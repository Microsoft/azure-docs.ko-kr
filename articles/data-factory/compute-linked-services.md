---
title: Azure Data Factory에서 지원하는 컴퓨팅 환경
description: '데이터의 변환 또는 처리를 위해 Azure Data Factory 파이프라인(예: Azure HDInsight)에서 사용할 수 있는 컴퓨팅 환경을 알아봅니다.'
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 4545a75cc2082c21dcb87986eba819ebe39adf7b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208763"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory에서 지원하는 컴퓨팅 환경
이 문서는 프로세스 또는 변환 데이터에 사용할 수 있는 다양한 컴퓨팅 환경을 설명합니다. 또한 이러한 컴퓨팅 환경을 Azure 데이터 팩터리에 연결하는 연결된 서비스를 구성하는 경우 데이터 팩터리에서 지원하는 다른 구성(주문형 vs. 사용자 고유)에 대한 자세한 내용을 제공합니다.

다음 표는 Data Factory 및 실행할 수 있는 작업에서 지원하는 컴퓨팅 환경 목록을 제공합니다. 

| 컴퓨팅 환경                                          | 작업                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [주문형 HDInsight 클러스터](#azure-hdinsight-on-demand-linked-service) 또는 [사용자 고유의 HDInsight 클러스터](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop 스트리밍](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Custom](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning 작업: Batch 실행 및 업데이트 리소스](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [파이프라인 실행 Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [파이프라인 실행 Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [데이터 레이크 분석 U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [저장 프로시저](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure Function](#azure-function-linked-service)         | [Azure 함수 활동](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>주문형 HDInsight 컴퓨팅 환경
이 구성의 형식에서는 컴퓨팅 환경은 Azure 데이터 팩터리 서비스에 의해 완벽하게 관리됩니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 사용자는 주문형 컴퓨팅 환경에 대한 연결된 서비스를 만들고 구성하며 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 세부적인 설정을 제어할 수 있습니다.

> [!NOTE]
> 주문형 구성은 현재 Azure HDInsight 클러스터에 대해서만 지원됩니다. Azure Databricks는 작업 클러스터를 사용하는 주문형 작업도 지원합니다. 자세한 내용은 [Azure Databricks 연결된 서비스](#azure-databricks-linked-service)를 참조하세요.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 주문형 연결된 서비스
Azure Data Factory 서비스는 데이터를 처리하는 주문형 HDInsight 클러스터를 자동으로 만들 수 있습니다. 클러스터는 클러스터와 연결된 스토리지 계정(JSON에서 linkedServiceName 속성)과 동일한 하위 지역에 만들어집니다. 스토리지 계정은 범용 표준 Azure Storage 계정이어야 합니다. 

주문형 HDInsight 연결된 서비스에 대해 다음 **중요한** 점에 유의하십시오.

* 주문형 HDInsight 클러스터는 Azure 구독에 따라 생성됩니다. 클러스터가 실행 중인 경우 Azure Portal에서 클러스터를 볼 수 있습니다. 
* 주문형 HDInsight 클러스터에서 실행하는 작업에 대한 로그는 HDInsight 클러스터와 연결된 스토리지 계정에 복사됩니다. 연결된 서비스 정의에 정의된 clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword는 클러스터의 수명 주기 동안 자세한 문제 해결을 위해 클러스터에 로그인하는 데 사용됩니다. 
* HDInsight 클러스터가 작업을 실행 중인 경우에 대해서만 청구됩니다.
* Azure HDInsight 주문형 연결된 서비스와 함께 **스크립트 동작**을 사용할 수 없습니다.  

> [!IMPORTANT]
> 주문형 Azure HDInsight 클러스터를 프로비전하는 데 일반적으로 **20분** 이상이 걸립니다.

### <a name="example"></a>예제
다음 JSON는 Linux 기반 주문형 HDInsight 연결된 서비스를 정의합니다. Data Factory 서비스는 **Linux 기반** HDInsight 클러스터를 자동으로 만들어 필요한 작업을 처리합니다. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight 클러스터는 JSON(**linkedServiceName**)에서 지정한 Blob Storage에 **기본 컨테이너**를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 한 슬라이스를 처리해야 할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 삭제됩니다. 
>
> 더 많은 작업이 실행될수록 Azure Blob Storage에 컨테이너가 많아집니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 스토리지 비용을 줄이기 위해 삭제할 수 있습니다. 이러한 컨테이너의 이름은 `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 패턴을 따릅니다. [Microsoft Storage Explorer](https://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob Storage에서 컨테이너를 삭제합니다.
>
> 

### <a name="properties"></a>속성
| 속성                     | Description                              | 필수 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | 형식 속성은 **HDInsightOnDemand**로 설정해야 합니다. | yes      |
| clusterSize                  | 클러스터의 작업자/데이터 노드 수 HDInsight 클러스터는 속성에 지정한 작업자 노드의 수와 함께 2개의 헤드 노드로 생성됩니다. 노드의 크기는 4개 코어를 포함한 Standard_D3이므로, 4개 작업자 노드 클러스터에서 24개 코어(작업자 노드용 4\*4 = 16코어 및 헤드 노드용 2\*4 = 8코어)를 사용합니다. 자세한 내용은 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터를 설정](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요. | yes      |
| linkedServiceName            | 데이터를 저장 및 처리하기 위해 주문형 클러스터에서 사용하는 Azure Storage 연결 서비스입니다. HDInsight 클러스터는 Azure Storage 계정과 동일한 지역에 생성됩니다. Azure HDInsight에는 지원하는 각 Azure 지역에서 사용할 수 있는 총 코어 수에 대한 제한이 있습니다. 필요한 clustersize를 충족하기에 해당 Azure 지역의 코어 할당량이 충분한지 확인합니다. 자세한 내용은 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터를 설정](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.<p>현재 Azure Data Lake Store를 스토리지로 사용하는 주문형 HDInsight 클러스터를 만들 수 없습니다. HDInsight 처리의 결과 데이터를 Azure Data Lake Store에 저장하려면 복사 작업을 사용하여 Azure Blob Storage의 데이터를 Azure Data Lake Store로 복사합니다. </p> | yes      |
| clusterResourceGroup         | HDInsight 클러스터는 이 리소스 그룹에 생성됩니다. | yes      |
| timetolive                   | 주문형 HDInsight 클러스터에 대한 허용된 유휴 시간입니다. 클러스터에 다른 활성 작업이 없으면 작업이 완료된 후에 주문형 HDInsight 클러스터가 유지될 기간을 지정합니다. 최소 허용 값은 5분(00:05:00)입니다.<br/><br/>예를 들어 활동 실행에 6분이 걸리고 timetolive이 5분으로 설정된 경우 클러스터는 활동을 처리하는 6분 동안 실행된 후에 5분 동안 유지됩니다. 다른 활동 실행이 6분 창을 실행하는 경우 동일한 클러스터에 의해 처리됩니다.<br/><br/>주문형 HDInsight 클러스터를 만드는 데는 많은 시간이 걸려 값 비싼 작업이 되므로 필요할 때마다 이 설정을 사용하여 주문형 HDInsight 클러스터를 다시 사용함으로써 데이터 팩터리의 성능을 향상시킵니다.<br/><br/>timetolive 값을 0으로 설정한 경우 클러스터는 작업 실행이 완료되는 즉시 삭제됩니다. 반면 높은 값을 설정하는 경우 몇 가지 문제 해결 목적으로 로그온하려는 사용자를 위해 클러스터는 많은 비용이 발생하는 유휴 상태에 머무를 수 있습니다. 따라서 필요에 따라 적절한 값을 설정하는 것이 중요합니다.<br/><br/>timetolive 속성 값이 적절하게 설정되는 경우 여러 파이프라인은 주문형 HDInsight 클러스터의 인스턴스를 공유할 수 있습니다. | yes      |
| clusterType                  | 만들 HDInsight 클러스터의 형식입니다. 허용되는 값은 "hadoop" 및 "spark"입니다. 지정하지 않으면 기본값은 hadoop입니다. Enterprise Security Package 지원 클러스터는 주문형으로 만들 수 없으며, 대신 [기존 클러스터/사용자 고유의 컴퓨팅 가져오기](#azure-hdinsight-linked-service)를 사용합니다. | 예       |
| 버전                      | HDInsight 클러스터의 버전입니다. 지정하지 않으면 현재 HDInsight에 정의된 기본 버전을 사용하는 것입니다. | 예       |
| hostSubscriptionId           | HDInsight 클러스터를 만드는 데 사용되는 Azure 구독 ID입니다. 지정하지 않으면 Azure 로그인 컨텍스트의 구독 ID를 사용합니다. | 예       |
| clusterNamePrefix           | HDI 클러스터 이름의 접두사로, 클러스터 이름 끝에 타임스탬프가 자동으로 추가됩니다.| 예       |
| sparkVersion                 | 클러스터 형식이 "Spark"인 경우 Spark 버전입니다. | 예       |
| additionalLinkedServiceNames | HDInsight 연결된 서비스에 대한 추가 스토리지 계정을 지정하므로 데이터 팩터리 서비스가 사용자를 대신해 계정을 등록할 수 있습니다. 이러한 스토리지 계정은 linkedServiceName에 지정된 스토리지 계정과 동일한 지역에 생성된 HDInsight 클러스터와 동일한 지역에 있어야 합니다. | 예       |
| osType                       | 운영 체제 유형입니다. 허용되는 값은 Linux 및 Windows(HDInsight 3.3에만 해당)입니다. 기본값은 Linux입니다. | 예       |
| hcatalogLinkedServiceName    | HCatalog 데이터베이스를 가리키는 Azure SQL 연결된 서비스 이름입니다. 주문형 HDInsight 클러스터는 Azure SQL 데이터베이스를 metastore로 사용하여 만들어집니다. | 예       |
| connectVia                   | 이 HDInsight 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. 주문형 HDInsight 연결된 서비스의 경우 Azure 통합 런타임만 지원합니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예       |
| clusterUserName                   | 클러스터에 액세스하기 위한 사용자 이름입니다. | 예       |
| clusterPassword                   | 클러스터에 액세스하기 위한 보안 문자열 형식의 암호입니다. | 예       |
| clusterSshUserName         | SSH에 대 한 사용자 이름은 클러스터의 노드에 원격으로 연결 됩니다 (Linux의 경우). | 예       |
| clusterSshPassword         | SSH를 통해 클러스터 노드에 원격으로 연결하기 위한 보안 문자열 형식의 암호입니다(Linux용). | 예       |
| scriptActions | 주문형 클러스터를 만드는 동안 [HDInsight 클러스터 사용자 지정](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)을 위한 스크립트를 지정합니다. <br />현재 Azure Data Factory의 사용자 인터페이스 제작 도구는 1개의 스크립트 동작만 지정하도록 지원하지만 JSON에서는 이 제한을 무시할 수 있습니다(JSON에서 여러 스크립트 동작 지정). | 예 |


> [!IMPORTANT]
> HDInsight는 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포에 포함된 구성 요소 집합이 만들어집니다. 지원되는 HDInsight 버전 목록을 계속 업데이트하여 최신 Hadoop 에코시스템 구성 요소 및 수정 프로그램을 제공합니다. [지원되는 HDInsight 버전 및 OS 형식](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)의 최신 정보를 항상 참조하여 지원되는 HDInsight 버전을 사용 중인지 확인합니다. 
>
> [!IMPORTANT]
> 현재 HDInsight 연결된 서비스에서는 HBase, 대화형 쿼리(Hive LLAP), Storm을 지원하지 않습니다. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON 예제

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>서비스 주체 인증

주문형 HDInsight 연결된 서비스가 사용자를 대신해 HDInsight 클러스터를 만들려면 서비스 주체 인증이 필요합니다. 서비스 주체 인증을 사용하려면 Azure AD(Azure Active Directory)에 애플리케이션 엔터티를 등록하고 HDInsight 클러스터가 만들어진 리소스 그룹 또는 구독의 **참가자** 역할을 애플리케이션 엔터티에 부여합니다. 자세한 단계는 [포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)를 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

- 애플리케이션 UI
- 애플리케이션 키 
- 테넌트 ID

다음 속성을 지정하여 서비스 주체 인증을 사용합니다.

| 속성                | Description                              | 필수 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | 애플리케이션의 클라이언트 ID를 지정합니다.     | yes      |
| **servicePrincipalKey** | 애플리케이션의 키를 지정합니다.           | yes      |
| **테넌트**              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | yes      |

### <a name="advanced-properties"></a>고급 속성

또한 주문형 HDInsight 클러스터의 세부적인 구성에 다음 속성을 지정할 수 있습니다.

| 속성               | Description                              | 필수 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 만들어지는 HDInsight 클러스터에 대한 핵심 구성 매개 변수(core-site.xml에서 처럼)를 지정합니다. | 예       |
| hBaseConfiguration     | HDInsight 클러스터에 대한 HBase 구성 매개 변수(hbase-site.xml)를 지정합니다. | 예       |
| hdfsConfiguration      | HDInsight 클러스터에 대한 HDFS 구성 매개 변수(hdfs-site.xml)를 지정합니다. | 예       |
| hiveConfiguration      | HDInsight 클러스터에 대한 hive 구성 매개 변수(hive-site.xml)를 지정합니다. | 예       |
| mapReduceConfiguration | HDInsight 클러스터에 대한 MapReduce 구성 매개 변수(mapred-site.xml)를 지정합니다. | 예       |
| oozieConfiguration     | HDInsight 클러스터에 대한 Oozie 구성 매개 변수(oozie-site.xml)를 지정합니다. | 예       |
| stormConfiguration     | HDInsight 클러스터에 대한 Storm 구성 매개 변수(storm-site.xml)를 지정합니다. | 예       |
| yarnConfiguration      | HDInsight 클러스터에 대한 Yarn 구성 매개 변수(yarn-site.xml)를 지정합니다. | 예       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>예제 - 고급 속성을 포함하는 주문형 HDInsight 클러스터 구성

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>노드 크기
다음 속성을 사용하여 헤드, 데이터 및 Zookeeper 노드의 크기를 지정할 수 있습니다. 

| 속성          | Description                              | 필수 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | 헤드 노드의 크기를 지정합니다. 기본값은 Standard_D3입니다. 자세한 내용은 **노드 크기 지정** 섹션을 참조하세요. | 예       |
| dataNodeSize      | 데이터 노드의 크기를 지정합니다. 기본값은 Standard_D3입니다. | 예       |
| zookeeperNodeSize | Zookeeper 노드의 크기를 지정합니다. 기본값은 Standard_D3입니다. | 예       |

#### <a name="specifying-node-sizes"></a>노드 크기 지정
이전 섹션에 언급된 속성에 대해 지정해야 하는 문자열 값은 [Virtual Machines 크기](../virtual-machines/linux/sizes.md) 문서를 참조하세요. 값은 이 문서에서 참조된 **Cmdlet 및 API**를 준수해야 합니다. 이 문서에서 볼 수 있는 것처럼 크게(기본값) 크기의 데이터 노드는 메모리가 7GB이므로 시나리오에 맞지 않을 수 있습니다. 

D4 크기의 헤드 노드 및 작업자 노드를 만들려는 경우 headNodeSize 및 dataNodeSize 속성에 대한 값으로 **Standard_D4**를 지정합니다. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

이러한 속성에 잘못된 값을 지정하는 경우 다음과 같은 오류가 발생할 수 있습니다. **오류:** 클러스터를 만들지 못했습니다. 예외: 클러스터 만들기 작업을 완료할 수 없습니다. 작업이 실패했습니다. 오류 코드는 '400'입니다. 클러스터의 상태가 '오류'로 남아 있습니다. 메시지: 'PreClusterCreationValidationFailure'. 이 오류가 발생하면 **Virtual Machines 크기** 문서의 테이블에서 [CMDLET 및 API](../virtual-machines/linux/sizes.md) 이름을 사용하고 있는지 확인합니다.        

## <a name="bring-your-own-compute-environment"></a>사용자 고유의 컴퓨팅 환경 가져오기
이 구성의 형식에서는 사용자가 이미 기존 컴퓨팅 환경을 데이터 팩터리에서 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다.

이 구성의 형식은 다음의 컴퓨팅 환경에 대해 지원됩니다.

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure 데이터 레이크 분석
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 연결된 서비스
Azure HDInsight 연결된 서비스를 만들어서 데이터 팩터리를 사용하는 사용자 고유의 HDInsight 클러스터를 등록할 수 있습니다.

### <a name="example"></a>예제

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>속성
| 속성          | Description                                                  | 필수 |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | 형식 속성은 **HDInsight**로 설정해야 합니다.            | yes      |
| clusterUri        | HDInsight 클러스터의 URI입니다.                            | yes      |
| 사용자 이름          | 기존 HDInsight 클러스터에 연결하는데 사용할 사용자의 이름을 지정합니다. | yes      |
| password          | 사용자 계정으로 password를 지정합니다.                       | yes      |
| linkedServiceName | HDInsight 클러스터에서 사용하는 Azure Blob Storage를 참조하는 Azure Storage 연결된 서비스의 이름입니다. <p>현재 이 속성에 대한 Azure Data Lake Store 연결된 서비스를 지정할 수 없습니다. HDInsight 클러스터가 Data Lake Store에 액세스할 경우 Hive/Pig 스크립트의 Azure Data Lake Store에 있는 데이터에 액세스할 수 있습니다. </p> | yes      |
| isEspEnabled      | HDInsight 클러스터에서 *Enterprise Security Package*가 사용하도록 설정되어 있으면 '[true](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture)'를 지정합니다. 기본값은 ‘*false*’입니다. | 예       |
| connectVia        | 이 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. <br />ESP (Enterprise Security Package)를 사용 하도록 설정 된 HDInsight 클러스터는 클러스터에 대 한 시야를 포함 하는 자체 호스팅 integration runtime을 사용 하 여 ESP HDInsight 클러스터와 동일한 Virtual Network 내에 배포 해야 합니다. | 예       |

> [!IMPORTANT]
> HDInsight는 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포에 포함된 구성 요소 집합이 만들어집니다. 지원되는 HDInsight 버전 목록을 계속 업데이트하여 최신 Hadoop 에코시스템 구성 요소 및 수정 프로그램을 제공합니다. [지원되는 HDInsight 버전 및 OS 형식](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)의 최신 정보를 항상 참조하여 지원되는 HDInsight 버전을 사용 중인지 확인합니다. 
>
> [!IMPORTANT]
> 현재 HDInsight 연결된 서비스에서는 HBase, 대화형 쿼리(Hive LLAP), Storm을 지원하지 않습니다. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch 연결된 서비스

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Batch 연결된 서비스를 만들어 데이터 팩터리에 가상 머신(VM)의 일괄 처리 풀을 등록합니다. Azure 배치를 사용하여 사용자 지정 작업을 실행할 수 있습니다.

Azure Batch 서비스가 처음이라면 다음 문서를 참조하세요.

* [Azure Batch 기본 사항](../batch/batch-technical-overview.md) 입니다.
* [AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet을 사용 하 여 Azure Batch 계정 (또는) [Azure Portal](../batch/batch-account-create-portal.md) 를 만들어 Azure Portal를 사용 하 여 Azure Batch 계정을 만들 수 있습니다. 이 cmdlet 사용에 관한 자세한 지침은 [PowerShell을 사용하여 Azure Batch 계정 관리](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 문서를 참조하세요.
* Azure Batch 풀을 만드는 [AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet

### <a name="example"></a>예제

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>속성
| 속성          | Description                              | 필수 |
| ----------------- | ---------------------------------------- | -------- |
| type              | 형식 속성은 **AzureBatch**로 설정해야 합니다. | yes      |
| accountName       | Azure Batch 계정의 이름         | yes      |
| accessKey         | Azure Batch 계정에 대한 선택키  | yes      |
| batchUri          | Azure 배치 계정에 대한 URL로, https://*batchaccountname.region*.batch.azure.com 형식을 사용합니다. | yes      |
| poolName          | 가상 머신의 풀 이름입니다.    | yes      |
| linkedServiceName | Azure Batch 연결된 서비스와 관련된 Azure Storage 연결된 서비스의 이름입니다. 이 연결된 서비스는 작업을 실행하는 데 필요한 파일을 준비하는 데 사용됩니다. | yes      |
| connectVia        | 이 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio 연결 된 서비스
Azure Machine Learning Studio 연결 된 서비스를 만들어 데이터 팩터리에 Machine Learning 일괄 처리 점수 매기기 끝점을 등록 합니다.

### <a name="example"></a>예제

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>속성
| 속성               | Description                              | 필수                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | 형식 속성은 **AzureML**로 설정해야 합니다. | yes                                      |
| mlEndpoint             | 일괄 처리 점수 매기기 URL입니다.                   | yes                                      |
| apiKey                 | 게시된 작업 영역 모델의 API입니다.     | yes                                      |
| updateResourceEndpoint | 학습 된 모델 파일로 예측 웹 서비스를 업데이트 하는 데 사용 되는 Azure Machine Learning 웹 서비스 끝점에 대 한 업데이트 리소스 URL입니다. | 예                                       |
| servicePrincipalId     | 애플리케이션의 클라이언트 ID를 지정합니다.     | UpdateResourceEndpoint가 지정된 경우에 필요합니다. |
| servicePrincipalKey    | 애플리케이션의 키를 지정합니다.           | UpdateResourceEndpoint가 지정된 경우에 필요합니다. |
| tenant                 | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | UpdateResourceEndpoint가 지정된 경우에 필요합니다. |
| connectVia             | 이 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning 연결된 서비스
Azure Machine Learning 연결 된 서비스를 만들어 Azure Machine Learning 작업 영역을 데이터 팩터리에 연결 합니다.

> [!NOTE]
> 현재 Azure Machine Learning 연결 된 서비스에 대 한 서비스 주체 인증만 지원 됩니다.

### <a name="example"></a>예제

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>속성
| 속성               | Description                              | 필수                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | Type 속성은 **AzureMLService**로 설정 해야 합니다. | yes                                      |
| subscriptionId         | Azure 구독 ID입니다.              | yes                                      |
| resourceGroupName      | name | yes                                      |
| mlWorkspaceName        | Azure Machine Learning 작업 영역 이름 | yes  |
| servicePrincipalId     | 애플리케이션의 클라이언트 ID를 지정합니다.     | 예 |
| servicePrincipalKey    | 애플리케이션의 키를 지정합니다.           | 예 |
| tenant                 | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | UpdateResourceEndpoint가 지정된 경우에 필요합니다. | 예 |
| connectVia             | 이 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예 |    

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 연결된 서비스
Azure 데이터 레이크 분석 컴퓨팅 서비스와 Azure Data Factory에 연결하는 **Azure 데이터 레이크 분석** 연결 서비스를 만듭니다. 파이프라인에서 데이터 레이크 분석 U-SQL 작업은 이 연결된 서비스를 가리킵니다. 

### <a name="example"></a>예제

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>속성

| 속성             | Description                              | 필수                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | type 속성은 **AzureDataLakeAnalytics**로 설정해야 합니다. | yes                                      |
| accountName          | Azure 데이터 레이크 분석 계정 이름입니다.  | yes                                      |
| dataLakeAnalyticsUri | Azure 데이터 레이크 분석 URI입니다.           | 예                                       |
| subscriptionId       | Azure 구독 ID입니다.                    | 예                                       |
| resourceGroupName    | Azure 리소스 그룹 이름                | 예                                       |
| servicePrincipalId   | 애플리케이션의 클라이언트 ID를 지정합니다.     | yes                                      |
| servicePrincipalKey  | 애플리케이션의 키를 지정합니다.           | yes                                      |
| tenant               | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | yes                                      |
| connectVia           | 이 연결된 서비스에 작업을 디스패치하는 데 사용할 통합 런타임입니다. Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks 연결된 서비스
Databricks 워크 로드를 실행 하는 데 사용할 Databricks 작업 영역을 등록 하는 **Azure Databricks 연결 된 서비스** 를 만들 수 있습니다 (노트북, jar, python). 
> [!IMPORTANT]
> Databricks 연결 된 서비스는 [인스턴스 풀](https://aka.ms/instance-pools)을 지원 합니다. 

### <a name="example---using-new-job-cluster-in-databricks"></a>예 - Databricks에서 새 작업 클러스터 사용

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>예 - Databricks에서 기존의 대화형 클러스터 사용

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>속성

| 속성             | Description                              | 필수                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | 연결된 서비스의 이름입니다.               | yes   |
| type                 | 형식 속성은 **Azure Databricks**로 설정 되어야 합니다. | yes                                      |
| 도메인               | Databricks 작업 영역의 지역을 기준으로 Azure 지역을 적절히 지정합니다. 예: https://eastus.azuredatabricks.net | yes                                 |
| accessToken          | 데이터 팩터리가 Azure Databricks에서 인증을 받으려면 액세스 토큰이 필요합니다. 액세스 토큰은 Databricks 작업 영역에서 생성해야 합니다. 액세스 토큰을 찾는 보다 자세한 단계는 [여기](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)에서 확인할 수 있습니다.  | yes                                       |
| existingClusterId    | 해당하는 모든 작업을 실행할 기존 클러스터의 클러스터 ID입니다. 이미 만들어진 대화형 클러스터여야 합니다. 클러스터가 응답을 중지하는 경우 수동으로 다시 시작해야 합니다. 안정성을 높이기 위해서는 새 클러스터에서 작업을 실행하는 것이 좋습니다. Databricks 작업 영역 -> 대화형 클러스터 이름 -> 구성 -> 태그에서 대화형 클러스터의 클러스터 ID를 찾을 수 있습니다. [자세한 정보](https://docs.databricks.com/user-guide/clusters/tags.html) | 예 
| instancePoolId    | Databricks 작업 영역에 있는 기존 풀의 인스턴스 풀 ID입니다.  | 예  |
| newClusterVersion    | 클러스터의 Spark 버전입니다. Databricks에서 작업 클러스터를 만듭니다. | 예  |
| newClusterNumOfWorker| 이 클러스터에 있어야 하는 작업자 노드의 수입니다. 하나의 클러스터에 총 num_workers + 1개의 Spark 노드에 대해 하나의 Spark 드라이버와 num_workers개의 실행기가 있습니다. 문자열 형식 Int32 (예: "1")는 numOfWorker가 1 또는 "1:10" 임을 의미 합니다. 1에서 min 및 10을 max로 자동 크기 조정을 의미 합니다.  | 예                |
| newClusterNodeType   | 이 필드는 단일 값을 통해 이 클러스터의 각 Spark 노드에서 사용할 수 있는 리소스를 인코딩합니다. 예를 들어, Spark 노드는 메모리 또는 계산 집약적 워크 로드에 대해 프로 비전 되 고 최적화 될 수 있습니다. 이 필드는 새 클러스터에 필요 합니다.                | 예               |
| newClusterSparkConf  | 선택적인 사용자 지정 Spark 구성 키-값 쌍의 집합입니다. 또한 사용자는 각각 spark.driver.extraJavaOptions 및 spark.executor.extraJavaOptions를 통해 드라이버와 실행기에 추가 JVM 옵션 문자열을 전달할 수 있습니다. | 예  |
| newClusterInitScripts| 새 클러스터에 대 한 선택적 사용자 정의 초기화 스크립트의 집합입니다. Init 스크립트에 대 한 DBFS 경로를 지정 합니다. | 예  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스
Azure SQL 연결된 서비스를 만들고 [저장 프로시저 활동](transform-data-using-stored-procedure.md) 에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 이 연결된 서비스에 대한 자세한 내용은 [Azure SQL 커넥터](connector-azure-sql-database.md#linked-service-properties) 문서를 참조하세요.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse 연결된 서비스
Azure SQL Data Warehouse 연결된 서비스를 만들고 [저장 프로시저 활동](transform-data-using-stored-procedure.md) 에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 이 연결된 서비스에 대한 자세한 내용은 [Azure SQL Data Warehouse 커넥터](connector-azure-sql-data-warehouse.md#linked-service-properties) 문서를 참조하세요.

## <a name="sql-server-linked-service"></a>SQL Server 연결된 서비스
SQL Server 연결된 서비스를 만들고 [저장 프로시저 활동](transform-data-using-stored-procedure.md) 에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 이 연결된 서비스에 대한 자세한 내용은 [SQL Server 커넥터](connector-sql-server.md#linked-service-properties) 문서를 참조하세요.

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스
Azure Function 연결 된 서비스를 만들고 [Azure function 활동](control-flow-azure-function-activity.md) 에서 사용 하 여 Data Factory 파이프라인에서 Azure Functions를 실행 합니다. Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. [Jarray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) 는 `JObject`이 *아닙니다* . `JObject` 이외의 모든 반환 형식이 실패 하 고 사용자 오류 *응답 콘텐츠가 올바른 JObject가 아닙니다*.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| type   | Type 속성은 **AzureFunction** 로 설정 해야 합니다. | 예 |
| 함수 앱 URL | Azure 함수 앱의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 함수 앱을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | 예 |
| 기능 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키**를 복사합니다. [AZURE FUNCTIONS HTTP 트리거 및 바인딩을](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) 참조 하세요. | 예 |
|   |   |   |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 지원하는 변환 작업의 목록은 [데이터 변환](transform-data.md)을 참조하세요.
