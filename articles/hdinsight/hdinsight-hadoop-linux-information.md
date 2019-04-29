---
title: Linux 기반 HDInsight에서 Hadoop 사용 팁 - Azure
description: Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Linux 기반 HDInsight(Hadoop) 클러스터를 사용하기 위한 구현 팁을 제공합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2a7af59495966c76a47ea84311ab073eb594f82e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765720"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Linux에서 HDInsight 사용에 관한 정보

Azure HDInsight 클러스터는 Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Apache Hadoop을 제공합니다. 대부분의 작업에 대해 Linux 설치에서 모든 다른 Hadoop으로 정확하게 작동해야 합니다. 이 문서를 알고 있어야 하는 특정 차이점을 호출합니다.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계 대부분은 많은 시스템에 설치해야 할 수 있는 다음과 같은 유틸리티를 사용합니다.

* [cURL](https://curl.haxx.se/) - 웹 기반 서비스와 통신하는 데 사용됩니다.
* **jq**, 명령줄 JSON 프로세서.  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)를 참조하세요.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - Azure 서비스를 원격으로 관리하는 데 사용됩니다.
* **SSH 클라이언트** 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="users"></a>사용자

[도메인에 가입](./domain-joined/apache-domain-joined-introduction.md)되어 있지 않으면 HDInsight를 **단일 사용자** 시스템으로 간주하기 때문에 관리자 수준 권한으로 하나의 SSH 사용자 계정이 클러스터와 함께 만들어집니다. 추가 SSH 계정을 만들 수는 있지만 클러스터에 대한 관리자 액세스 권한이 있어야 합니다.

도메인 가입 HDInsight에서는 여러 사용자와 세분화된 권한 및 역할 설정을 지원합니다. 자세한 내용은 [도메인 가입 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-manage.md)을 참조하세요.

## <a name="domain-names"></a>도메인 이름

인터넷에서 클러스터에 연결할 때 사용할 정규화 된 도메인 이름 (FQDN)은 `CLUSTERNAME.azurehdinsight.net` 또는 `CLUSTERNAME-ssh.azurehdinsight.net` (예: SSH에만 해당).

내부적으로 클러스터의 각 노드 이름은 클러스터 구성 중에 할당됩니다. 클러스터 이름을 찾으려면 Ambari 웹 UI의 **호스트** 페이지를 참조하세요. 다음을 사용하여 Ambari REST API에서 호스트 목록을 반환할 수도 있습니다.

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

`CLUSTERNAME`을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 관리자 계정에 대한 암호를 입력합니다. 이 명령은 클러스터의 호스트 목록을 포함하는 JSON 문서를 반환합니다. [jq](https://stedolan.github.io/jq/) 추출 하는 데 사용 되는 `host_name` 각 호스트에 대 한 요소 값입니다.

특정 서비스에 대한 노드의 이름을 찾으려면 해당 구성 요소에 대해 Ambari를 쿼리하면 됩니다. 예를 들어 HDFS 이름 노드에 대한 호스트를 찾으려면 다음 명령을 사용합니다.

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

이 명령은 서비스를 설명 하는 JSON 문서를 반환한 차례로 [jq](https://stedolan.github.io/jq/) 만 추출 합니다 `host_name` 호스트에 대 한 값입니다.

## <a name="remote-access-to-services"></a>서비스에 대한 원격 액세스

* **Ambari (웹)** - https://CLUSTERNAME.azurehdinsight.net

    클러스터 관리자 사용자 이름 및 암호를 사용 하 여 인증 하 고 Ambari에 로그인 합니다.

    인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

    > [!IMPORTANT]  
    > 일부 웹 UI는 내부 도메인 이름을 사용하여 Ambari 액세스 일부를 통해 사용할 수 있습니다. 내부 도메인 이름은 인터넷을 통해 공개적으로 액세스할 수 없습니다. 인터넷을 통해 일부 기능에 액세스하려고 하면 "서버를 찾을 수 없음" 오류가 표시될 수 있습니다.
    >
    > Ambari 웹 UI의 모든 기능을 사용하려면 프록시 웹 트래픽에 대한 SSH 터널을 클러스터 헤드 노드에 사용합니다. [SSH 터널링을 사용하여 Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

* **Ambari (REST)** - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
    >
    > 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **WebHCat (Templeton)** - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
    >
    > 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **SSH** -CLUSTERNAME-ssh.azurehdinsight.net 포트 22 또는 23입니다. 포트 22는 기본 헤드 노드에 연결하는 데 사용되는 반면 포트 23은 보조 헤드 노드에 연결하는 데 사용됩니다. 헤드 노드에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)을 참조하세요.

    > [!NOTE]  
    > 클라이언트 컴퓨터에서 SSH를 통해 클러스터 헤드 노드에 액세스할 수 있습니다. 연결한 후 헤드 노드에서 SSH를 사용하여 작업자 노드에 액세스할 수 있습니다.

자세한 내용은 [HDInsight의 Apache Hadoop 서비스에서 사용하는 포트](hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

## <a name="file-locations"></a>파일 위치

Hadoop 관련 파일은 `/usr/hdp`의 클러스터 노드에서 찾을 수 있습니다. 이 디렉터리에는 다음과 같은 하위 디렉터리가 포함됩니다.

* **2.6.5.3006-29**: 디렉터리 이름은 HDInsight에서 사용되는 Hortonworks Data Platform의 버전입니다. 클러스터에 있는 숫자는 여기에 나열된 것과 다를 수 있습니다.
* **current**: 이 디렉터리 아래의 하위 디렉터리에 대 한 링크가 포함 된 **2.6.5.3006-29** 디렉터리입니다. 이 디렉터리가 있으므로 버전 번호를 기억할 필요가 없습니다.

예제 데이터 및 JAR 파일은 `/example` 및 `/HdiSamples`의 HDFS(Hadoop 분산 파일 시스템)에서 찾을 수 있습니다.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage 및 Data Lake Storage

대부분의 Hadoop 배포판에서 HDFS에 저장된 데이터는 클러스터의 머신에서 로컬 저장소에 의해 되돌아갑니다. 계산 리소스에 대해 시간당 또는 분당 비용이 부과되는 클라우드 기반 솔루션의 경우 로컬 스토리지를 사용하면 비용이 많이 들 수 있습니다.

HDInsight를 사용할 때는 Azure Blob Storage와, 선택적으로 Azure Data Lake Storage를 통해 데이터 파일이 확장성 있고 탄력적인 방식으로 클라우드에 저장됩니다. 이러한 서비스는 다음과 같은 이점을 제공합니다.

* 저렴한 장기 스토리지
* 웹 사이트, 파일 업로드/다운로드 유틸리티, 다양한 언어 SDK 및 웹 브라우저와 같은 외부 서비스에 액세스할 수 있음
* 대용량 파일 용량과 대규모 확장성 있는 스토리지

자세한 내용은 [Blob 이해](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 및 [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 참조하세요.

Azure Storage 또는 Data Lake Storage를 사용하는 경우 HDInsight에서 데이터에 액세스하기 위해 특별한 작업을 수행할 필요가 없습니다. 예를 들어 Azure Storage 또는 Data Lake Storage 중 어디에 저장되어 있든지 다음 명령은 `/example/data` 폴더에 있는 파일을 나열합니다.

    hdfs dfs -ls /example/data

HDInsight에서 데이터 스토리지 리소스(Azure Blob Storage 및 Azure Data Lake Storage)는 컴퓨팅 리소스와는 분리됩니다. 따라서 필요에 따라 계산을 수행하는 HDInsight 클러스터를 만들고 나중에 작업이 완료되면 클러스터를 삭제할 수 있습니다. 그 동안 데이터 파일은 클라우드 저장소에서 필요한 시간 동안 안전하게 유지됩니다.


### <a name="URI-and-scheme"></a>URI 및 구성표

일부 명령에서는 파일에 액세스할 때 URI의 일부로 구성표를 지정해야 할 수도 있습니다. 예를 들어 Storm-HDFS 구성 요소를 사용하려면 구성표를 지정해야 합니다. 기본값이 아닌 저장소(클러스터에 "추가" 저장소로 추가된 저장소)를 사용할 때는 항상 URI의 일부로 구성표를 사용해야 합니다.

__Azure Storage__를 사용하는 경우 다음 URI 체계 중 하나를 사용합니다.

* `wasb:///`: 암호화되지 않은 통신을 사용하여 기본 스토리지에 액세스합니다.

* `wasbs:///`: 암호화된 통신을 사용하여 기본 스토리지에 액세스합니다.  wasbs 구성표는 HDInsight 버전 3.6 이상에서만 지원됩니다.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: 기본이 아닌 스토리지 계정과 통신할 때 사용됩니다. 예를 들어 추가 저장소 계정이 있거나 공개적으로 액세스할 수 있는 저장소 계정에 저장된 데이터에 액세스하는 경우입니다.

__Azure Data Lake Storage Gen2__를 사용하는 경우 다음 URI 체계 중 하나를 사용합니다.

* `abfs:///`: 암호화되지 않은 통신을 사용하여 기본 스토리지에 액세스합니다.

* `abfss:///`: 암호화된 통신을 사용하여 기본 스토리지에 액세스합니다.  abfss 체계는 HDInsight 버전 3.6 이상에서만 지원됩니다.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: 기본이 아닌 스토리지 계정과 통신할 때 사용됩니다. 예를 들어 추가 저장소 계정이 있거나 공개적으로 액세스할 수 있는 저장소 계정에 저장된 데이터에 액세스하는 경우입니다.

__Azure Data Lake Storage Gen1__을 사용하는 경우 다음 URI 체계 중 하나를 사용합니다.

* `adl:///`: 클러스터의 기본 Data Lake Storage에 액세스합니다.

* `adl://<storage-name>.azuredatalakestore.net/`: 기본이 아닌 Data Lake Storage와 통신할 때 사용됩니다. HDInsight 클러스터의 루트 디렉터리 외부 데이터에 액세스하는 데도 사용됩니다.

> [!IMPORTANT]  
> HDInsight의 기본 저장소로 Data Lake Storage를 사용하는 경우 사용할 저장소 내의 경로를 HDInsight 스토리지의 루트로 지정해야 합니다. 기본 경로는 `/clusters/<cluster-name>/`입니다.
>
> `/` 또는 `adl:///`을 사용하여 데이터에 액세스하는 경우 클러스터의 루트(예: `/clusters/<cluster-name>/`)에 저장된 데이터에만 액세스할 수 있습니다. 저장소의 모든 위치에 있는 데이터에 액세스하려면 `adl://<storage-name>.azuredatalakestore.net/` 형식을 사용합니다.

### <a name="what-storage-is-the-cluster-using"></a>클러스터에서 사용하는 저장소

Ambari를 사용하면 클러스터에 대한 기본 저장소 구성을 검색할 수 있습니다. curl을 사용하여 HDFS 구성 정보를 검색하도록 다음 명령을 사용하고 [jq](https://stedolan.github.io/jq/)를 사용하여 필터링합니다.

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> 이 명령은 해당 정보가 있는 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환합니다. 최신 버전을 찾기 위해 모든 구성 버전을 나열해야 할 수 있습니다.

이 명령은 다음 URI와 유사한 값을 반환합니다.

* `wasb://<container-name>@<account-name>.blob.core.windows.net` - Azure Storage 계정을 사용하는 경우

    계정 이름은 Azure Storage 계정의 이름입니다. 컨테이너 이름은 클러스터 저장소의 루트인 Blob 컨테이너입니다.

* Azure Data Lake Storage를 사용하는 경우 `adl://home`입니다. Data Lake Storage 이름을 가져오려면 다음 REST 호출을 사용합니다.

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    이 명령은 `<data-lake-store-account-name>.azuredatalakestore.net` 호스트 이름을 반환합니다.

    저장소 내에서 HDInsight의 루트 디렉터리를 가져오려면 다음 REST 호출을 사용합니다.

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    이 명령은 `/clusters/<hdinsight-cluster-name>/`과 비슷한 경로를 반환합니다.

또한 다음 단계를 사용하여 Azure Portal에서 저장소 정보를 찾을 수도 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 HDInsight 클러스터를 선택합니다.

2. **속성** 섹션에서 **Storage 계정**을 선택합니다. 클러스터에 대한 저장소 정보가 표시됩니다.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>HDInsight 외부에서 파일에 액세스하는 방법

HDInsight 클러스터 외부에서 데이터에 액세스하는 다양한 방법이 있습니다. 다음은 데이터 작업에 사용할 수 있는 유틸리티 및 SDK에 대한 몇 가지 링크입니다.

__Azure Storage__를 사용하는 경우 다음 링크를 참조하여 데이터에 액세스할 수 있습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Azure로 작업하기 위한 명령줄 인터페이스 명령입니다. 설치 후 저장소 사용에 대한 도움말은 `az storage`를 참조하고 Blob 관련 명령에 대한 도움말은 `az storage blob`을 참조하세요.
* [blobxfer.py](https://github.com/Azure/blobxfer): Azure Storage의 Blob 작업을 위한 Python 스크립트입니다.
* 다양한 SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

__Azure Data Lake Storage__를 사용하는 경우 다음 링크를 참조하여 데이터에 액세스할 수 있습니다.

* [웹 브라우저](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>클러스터 크기 조정

클러스터 크기 조정 기능을 사용하면 클러스터에서 사용하는 데이터 노드 수를 동적으로 변경할 수 있습니다. 클러스터에서 다른 작업 또는 프로세스가 실행되는 동안 크기 조정 작업을 수행할 수 있습니다.  또한 참조 [확장 HDInsight 클러스터](./hdinsight-scaling-best-practices.md)

다른 클러스터 종류는 다음과 같이 크기 조정에 영향을 받습니다.

* **Hadoop**: 클러스터의 노드 수를 줄이면 클러스터 서비스 중 일부가 다시 시작됩니다. 크기 조정 작업을 수행하면 작업이 실행 중이거나 보류 중 상태가 되므로 크기 조정 작업이 완료되지 못하고 실패합니다. 작업이 완료되면 작업을 다시 제출할 수 있습니다.
* **HBase**: 지역 서버는 크기 조정 작업을 완료한 후 몇 분 안에 자동으로 균형을 맞춥니다. 지역 서버를 수동으로 조정하려면 다음 단계를 사용합니다.

    1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

    2. 다음을 사용하여 HBase 셸을 시작합니다.

            hbase shell

    3. HBase 셸이 로드되면 다음을 사용하여 지역 서버를 수동으로 조정합니다.

            balancer

* **Storm**: 크기 조정 작업을 수행한 후 실행 중인 모든 Storm 토폴로지 균형을 다시 맞추어야 합니다. 균형을 다시 조정하면 토폴로지를 새 클러스터의 노드 수에 따라 병렬 처리 설정을 다시 조정할 수 있습니다. 실행 중인 토폴로지의 균형을 다시 조정하려면 다음 옵션 중 하나를 사용합니다.

    * **SSH**: 서버에 연결하고 다음 명령을 사용하여 토폴로지 균형을 다시 맞춥니다.

            storm rebalance TOPOLOGYNAME

        매개 변수를 지정하여 원래 토폴로지로 제공된 병렬 처리 힌트를 재정의할 수도 있습니다. 예를 들어 `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10`은 토폴로지를 5개 작업자 프로세스, 파란색 spout 구성 요소를 3개 실행자 및 노란색 bolt 구성 요소를 10개 실행자로 다시 구성합니다.

    * **Storm UI**: Storm UI를 사용하여 토폴로지 균형을 다시 맞추려면 다음 단계를 사용합니다.

        1. 오픈 `https://CLUSTERNAME.azurehdinsight.net/stormui` 웹 브라우저에서 여기서 `CLUSTERNAME` Storm 클러스터의 이름입니다. 메시지가 표시되면 클러스터를 만들 때 지정한 HDInsight 클러스터 관리자(관리자) 이름 및 암호를 입력합니다.
        2. 균형을 다시 맞추려는 토폴로지를 선택한 다음 **균형 다시 맞추기** 단추를 선택합니다. 균형 재조정 작업이 수행되기 전에 지연 시간을 입력합니다.

* **Kafka**: 크기 조정 작업 후 파티션 복제본의 균형을 다시 조정해야 합니다. 자세한 내용은 [HDInsight에서 Apache Kafka를 사용한 데이터의 고가용성](./kafka/apache-kafka-high-availability.md) 문서를 참조하세요.

HDInsight 클러스터 크기 조정에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure PowerShell을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hue(또는 다른 Hadoop 구성 요소)를 어떻게 설치합니까?

HDInsight는 관리 서비스입니다. Azure에서 클러스터와 관련된 문제를 발견하면 실패한 노드를 삭제하고 이 노드를 대체할 노드를 만들 수 있습니다. 클러스터에 Hue(또는 다른 Hadoop 구성 요소)를 수동으로 설치하는 경우 이 작업이 수행될 때 유지되지 않습니다. 대신 [HDInsight 스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md)을 사용합니다. 스크립트 동작을 사용하여 다음과 같이 변경할 수 있습니다.

* 서비스 또는 웹 사이트를 설치하고 구성합니다.
* 클러스터의 여러 노드에 대한 구성을 변경할 필요가 있는 구성 요소를 설치하고 구성합니다.

스크립트 동작은 Bash 스크립트입니다. 스크립트는 클러스터를 만드는 동안 실행되며, 추가 구성 요소를 설치 및 구성하는 데 사용됩니다. 다음 구성 요소를 설치하기 위한 예제 스크립트가 제공됩니다.

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

사용자 고유의 스크립트 작업 개발에 대한 정보는 [HDInsight를 사용하여 스크립트 작업 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

### <a name="jar-files"></a>Jar 파일

일부 Hadoop 기술은 MapReduce 작업의 일부로 사용되거나 Pig 또는 Hive 내부에서 사용되는 함수를 포함하는 자체 포함된 jar 파일에 제공되어 있습니다. 설정이 필요 없는 경우가 많으며 만든 후에 클러스터에 업로드하여 바로 사용할 수 있습니다. 구성 요소에서 클러스터를 다시 이미징하여 유지할 수 있도록 하려면 클러스터의 기본 저장소(WASB 또는 ADL)에 jar 파일을 저장할 수 있습니다.

예를 들어 [Apache DataFu](https://datafu.incubator.apache.org/)의 최신 버전을 사용하려는 경우 프로젝트가 포함된 jar을 다운로드하고 HDInsight 클러스터에 업로드할 수 있습니다. 그런 다음 Pig 또는 Hive를 사용하는 방법에 대한 DataFu 설명서를 수행합니다.

> [!IMPORTANT]  
> 독립 실행형 jar 파일인 구성 요소 일부는 HDInsight와 함께 제공되지만 경로에 있지 않습니다. 특정 구성 요소를 찾으려면 다음을 사용하여 클러스터에서 검색할 수 있습니다.
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 이 명령은 일치하는 jar 파일의 경로를 모두 반환합니다.

다른 버전의 구성 요소를 사용하려면 필요한 버전을 업로드한 후 작업에서 사용합니다.

> [!IMPORTANT]
> HDInsight 클러스터와 함께 제공되는 구성 요소는 완벽하게 지원되며 Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하는 데 도움이 됩니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [https://apache.org](https://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/)).

## <a name="next-steps"></a>다음 단계

* [Windows 기반 HDInsight에서 Linux 기반 HDInsight로 마이그레이션](hdinsight-migrate-from-windows-to-linux.md)
* [Apache Ambari REST API를 사용 하 여 HDInsight 클러스터 관리](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hadoop/hdinsight-use-mapreduce.md)
