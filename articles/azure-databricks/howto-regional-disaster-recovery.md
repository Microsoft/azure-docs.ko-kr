---
title: Azure Databricks의 지역 재해 복구
description: 이 문서에서는 Azure Databricks에서 재해 복구를 수행하는 방법을 설명합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: bd91d9201e81c884b48b41de27146c186eeb9598
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784681"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Azure Databricks 클러스터의 지역 재해 복구

이 문서에서는 Azure Databricks 클러스터에 유용한 재해 복구 아키텍처 및 이 디자인을 구현하는 단계를 설명합니다.

## <a name="azure-databricks-architecture"></a>Azure Databricks 아키텍처

간략하게 설명하자면, Azure Portal에서 Azure Databricks 작업 영역을 만들면 선택한 Azure 지역(예: 미국 서부)에 [관리되는 어플라이언스](../managed-applications/overview.md)가 구독의 Azure 리소스로 배포됩니다. 이 어플라이언스는 구독에서 사용할 수 있는 [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md) 및 Azure Storage 계정을 사용하여 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 배포됩니다. 가상 네트워크는 Databricks 작업 영역에 경계 수준 보안을 제공하며 네트워크 보안 그룹을 통해 보호됩니다. 작업 영역 내에서, 작업자 및 드라이버 VM 유형과 Databricks 런타임 버전을 제공하여 Databricks 클러스터를 만들 수 있습니다. 지속형 데이터는 스토리지 계정에서 사용할 수 있으며, 스토리지 계정은 Azure Blob Storage 또는 Azure Data Lake Store입니다. 클러스터가 만들어지면 작업을 특정 클러스터에 연결하여 노트북, REST API, ODBC/JDBC 엔드포인트를 통해 작업을 실행할 수 있습니다.

Databricks 제어 평면은 Databricks 작업 영역 환경을 관리하고 모니터링합니다. 클러스터 만들기 같은 관리 작업은 제어 평면에서 시작됩니다. 예약된 작업 같은 모든 메타데이터는 내결함성을 위해 지역 복제 기능을 갖춘 Azure Database에 저장됩니다.

![Databricks 아키텍처](media/howto-regional-disaster-recovery/databricks-architecture.png)

이 아키텍처의 장점 중 하나는 사용자가 Azure Databricks를 본인 계정의 저장소 리소스에 연결할 수 있다는 것입니다. 주요 장점은 계산(Azure Databricks) 및 스토리지를 서로 독립적으로 크기 조정할 수 있다는 것입니다.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>지역 재해 복구 토폴로지를 만드는 방법

이전 아키텍처 설명에 표시 된 대로 Azure Databricks를 사용 하 여 빅 데이터 파이프라인에 사용 되는 구성 요소는 여러 가지가 있습니다.  Azure Storage, Azure 데이터베이스 및 기타 데이터 원본입니다. Azure Databricks는 빅 데이터 파이프라인을 위한 *계산*입니다. 본질적으로 *임시적*입니다. 다시 말해서, Azure Storage에서 데이터를 계속 사용할 수 있더라도 계산이 필요 없을 때 불필요한 비용이 발생하지 않도록 *계산*(Azure Databricks 클러스터)이 종료될 수 있습니다. 작업의 대기 시간이 길어지지 않도록 *계산*(Azure Databricks) 및 스토리지 소스가 같은 지역에 있어야 합니다.  

고유한 지역 재해 복구 토폴로지를 만들려면 다음 요구 사항을 따릅니다.

   1. 별도의 Azure 지역에 여러 Azure Databricks 작업 영역을 프로비전합니다. 예를 들어 미국 동부 2에 기본 Azure Databricks 작업 영역을 만듭니다. 미국 서부 같은 별도의 지역에 보조 재해 복구 Azure Databricks 작업 영역을 만듭니다.

   2. [지역 중복 저장소](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)를 사용합니다. Azure Databricks와 연결된 데이터는 기본적으로 Azure Storage에 저장됩니다. 또한 Databricks 작업의 결과가 기본적으로 Azure Blob Storage에 저장되므로, 처리된 데이터는 클러스터가 종료된 후에도 내구성과 고가용성을 유지합니다. Storage 및 Databricks 클러스터가 같이 배치되므로 기본 지역에 더 이상 액세스할 수 없을 때 보조 지역에서 데이터에 액세스할 수 있도록 지역 중복 스토리지를 사용해야 합니다.

   3. 보조 지역을 만든 후에는 사용자, 사용자 폴더, 노트북, 클러스터 구성, 작업 구성, 라이브러리, 저장소, init 스크립트를 마이그레이션하고 액세스 제어를 다시 구성해야 합니다. 자세한 내용은 다음 섹션에 나와 있습니다.

## <a name="detailed-migration-steps"></a>자세한 마이그레이션 단계

1. **컴퓨터에서 Databricks 명령줄 인터페이스 설정**

   이 문서에서는 Azure Databricks REST API보다 간편한 래퍼인 명령줄 인터페이스를 대부분의 자동화된 단계에 사용하는 다양한 예제를 보여줍니다.

   마이그레이션 단계를 수행하기 전에, 작업에 사용할 데스크톱 컴퓨터 또는 가상 머신에 databricks-cli를 설치합니다. 자세한 내용은 [Databricks CLI 설치](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요.

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > 이 문서에서 제공하는 모든 python 스크립트는 Python 2.7 이상 3.x 미만에서 작동합니다.

2. **두 개의 프로필 구성.**

   한 프로필은 기본 작업 영역에 대해 구성하고, 다른 프로필은 보조 작업 영역에 대해 구성합니다.

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   이 문서의 코드 블록은 해당 작업 영역 명령을 사용하여 각 후속 단계에서 프로필 간에 전환합니다. 만드는 프로필의 이름을 각 코드 블록으로 바꿔야 합니다.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   필요한 경우 명령줄에서 수동으로 전환할 수 있습니다.

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory 사용자 마이그레이션**

   기본 작업 영역에 있는 보조 작업 영역에 동일한 Azure Active Directory 사용자를 수동으로 추가합니다.

4. **사용자 폴더 및 노트북 마이그레이션**

   다음 python 코드를 사용하여 샌드박스가 적용된 사용자 환경을 마이그레이션합니다. 이 사용자 환경은 중첩된 폴더 구조체 및 사용자별 노트북을 포함하고 있습니다.

   > [!NOTE]
   > 라이브러리는 이 단계에서 복사되지 않습니다. 기본 API가 라이브러리를 지원하지 않기 때문입니다.

   다음 python 스크립트를 복사하여 파일에 저장하고, Databricks 명령줄에서 실행합니다. 예: `python scriptname.py`

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **클러스터 구성 마이그레이션**

   노트북을 마이그레이션한 후에는 필요에 따라 클러스터 구성을 새 작업 영역으로 마이그레이션할 수 있습니다. 모든 클러스터가 아닌 선택적 클러스터 구성 마이그레이션을 수행하려는 경우 외에는 거의 모든 단계가 databricks-cli를 사용하여 완전히 자동화되었습니다.

   > [!NOTE]
   > 클러스터 구성 만들기 엔드포인트는 없지만, 이 스크립트는 각 클러스터를 즉시 생성하려고 시도합니다. 구독에 사용 가능한 코어가 충분하지 않은 경우 클러스터 만들기가 실패할 수 있습니다. 구성이 성공적으로 전송되는 한, 오류를 무시할 수 있습니다.

   제공된 다음 스크립트는 기존 클러스터 ID에서 새 클러스터 ID로의 매핑을 인쇄하며, 나중에 작업 마이그레이션(기존 클러스터를 사용하도록 구성된 작업)에 사용할 수 있습니다.

   다음 python 스크립트를 복사하여 파일에 저장하고, Databricks 명령줄에서 실행합니다. 예: `python scriptname.py`

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **작업 구성 마이그레이션**

   이전 단계에서 클러스터 구성을 마이그레이션한 경우 작업 구성을 새 작업 영역으로 마이그레이션하도록 선택할 수 있습니다. 모든 작업이 아닌 선택적 작업 구성 마이그레이션을 수행하려는 경우 외에는 모든 단계가 databricks-cli를 사용하여 완전히 자동화되었습니다.

   > [!NOTE]
   > 예약된 작업의 구성에는 "일정" 정보도 포함되므로, 기본적으로 마이그레이션되는 즉시 구성된 타이밍에 따라 작동을 시작합니다. 그러므로 다음 코드 블록은 기존 작업 영역과 새 작업 영역 간에 실행이 중복되지 않도록 마이그레이션하는 동안 모든 일정 정보를 제거합니다. 중단 준비가 끝나면 이러한 작업의 일정을 구성합니다.

   작업 구성에는 새 클러스터 또는 기존 클러스터에 대한 설정이 필요합니다. 기존 클러스터를 사용하는 경우 아래의 스크립트/코드가 기존 클러스터 ID를 새 클러스터 ID로 바꾸려고 시도합니다.

   다음 python 스크립트를 복사하여 파일에 저장합니다. `old_cluster_id` 및 `new_cluster_id` 값을 이전 단계에서 수행한 클러스터 마이그레이션의 출력으로 바꿉니다. databricks-cli 명령줄에서 실행합니다(예: `python scriptname.py`).

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **라이브러리 마이그레이션**

   현재는 라이브러리를 한 작업 영역에서 다른 작업 영역으로 직접 마이그레이션할 수 없습니다. 대신, 해당 라이브러리를 새 작업 영역에 수동으로 설치해야 합니다. [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) 조합을 사용하여 작업 영역 및 [라이브러리 CLI](https://github.com/databricks/databricks-cli#libraries-cli)에 사용자 지정 라이브러리를 업로드하는 과정을 자동화할 수 있습니다.

8. **Azure Blob Storage 및 Azure Data Lake Store 탑재 마이그레이션**

   수동으로 모든 다시 탑재 [Azure Blob storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) 및 [Azure Data Lake Store (Gen 2)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) 노트북 기반 솔루션을 사용 하 여 요소를 탑재 합니다. 저장소 리소스는 기본 작업 영역에 탑재되었을 것이며, 보조 작업 영역에서도 반복해야 합니다. 탑재를 위한 외부 API는 없습니다.

9. **클러스터 init 스크립트 마이그레이션**

   [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples)를 사용하여 모든 클러스터 초기화 스크립트를 기존 작업 영역에서 새 작업 영역으로 마이그레이션할 수 있습니다. 먼저 필요한 스크립트를 `dbfs:/dat abricks/init/..`에서 로컬 데스크톱 또는 가상 머신으로 복사합니다. 다음으로, 해당 스크립트를 동일한 경로의 새 작업 영역에 복사합니다.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **액세스 제어를 수동으로 다시 구성 및 다시 적용**

    프리미엄 계층(SKU)을 사용하도록 기존 주 작업 영역을 구성한 경우 [액세스 제어 기능](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control)을 사용할 가능성이 높습니다.

    액세스 제어 기능을 사용하는 경우 리소스(노트북, 클러스터, 작업, 테이블)에 대한 액세스 제어를 수동으로 다시 적용합니다.

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Azure 에코 시스템에 대 한 재해 복구

다른 Azure 서비스를 사용 하는 경우에 너무 해당 서비스에 대 한 재해 복구 모범 사례를 구현 해야 합니다. 예를 들어 외부 Hive metastore 인스턴스를 사용 하려는 경우에 대 한 재해 복구 고려해 야 [Azure SQL Server](../sql-database/sql-database-disaster-recovery.md)하십시오 [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md), 및/또는 [Azure Database for MySQL ](../mysql/concepts-business-continuity.md). 재해 복구에 대 한 일반적인 정보를 참조 하세요 [Azure 응용 프로그램에 대 한 재해 복구](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Databricks 설명서](https://docs.azuredatabricks.net/user-guide/index.html)를 참조하세요.
