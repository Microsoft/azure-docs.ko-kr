---
title: Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교
description: 스토리지 유형 및 Azure HDInsight에서 작동하는 원리에 대한 개요를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: ac1a0e4eadc0b84fdd2a170c2e0f6e0a2f2af3a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922002"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교

HDInsight 클러스터를 만들 때 몇 가지 서로 다른 Azure 저장소 서비스 간에 선택할 수 있습니다.

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

이 문서에서는 이러한 스토리지 유형 및 각 유형의 고유 기능에 대한 개요를 제공합니다.

다음 표에서 서로 다른 버전의 HDInsight 사용 하 여 지원 되는 Azure Storage 서비스를 보여 줍니다.

| 스토리지 서비스 | 계정 유형 | Namespace 형식 | 지원되는 서비스 | 지원되는 성능 계층 | 지원되는 액세스 계층 | HDInsight 버전 | 클러스터 유형 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 범용 V2 | 계층 구조 (파일 시스템) | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | 모두 |
|Azure Storage| 범용 V2 | Object | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | 모두 |
|Azure Storage| 범용 V1 | Object | Blob | Standard | N/A | 모두 | 모두 |
|Azure Storage| Blob Storage | Object | Blob | Standard | 핫, 쿨, 보관 | 모두 | 모두 |
|Azure Data Lake Storage Gen1| N/A | 계층 구조 (파일 시스템) | N/A | 해당 사항 없음 | N/A | 3.6에만 해당 | HBase를 제외한 모든 |

Azure Storage 액세스 계층에 대 한 자세한 내용은 참조 하세요. [Azure Blob storage: Premium (미리 보기), 핫, 쿨 및 보관 저장소 계층](../storage/blobs/storage-blob-storage-tiers.md)

기본 및 선택적 보조 저장소에 대 한 서비스의 다양 한 조합을 사용 하는 클러스터를 만들 수 있습니다. 다음 표에서 HDInsight에서 현재 지원 되는 클러스터 저장소 구성:

| HDInsight 버전 | 기본 스토리지 | 보조 저장소 | 지원됨 |
|---|---|---|---|
| 3.6 & 4.0 | 표준 Blob | 표준 Blob | 예 |
| 3.6 & 4.0 | 표준 Blob | Data Lake Storage Gen2 | 아닙니다. |
| 3.6 & 4.0 | 표준 Blob | Data Lake Storage Gen1 | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2* | 표준 Blob | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 아닙니다. |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | 표준 Blob | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 아닙니다. |
| 4.0 | Data Lake Storage Gen1 | 모두 | 아닙니다. |

* =으로 클러스터 액세스에 대 한 관리 되는 동일한 id를 사용 하려면 모든 설정 하는 하나 이상의 Data Lake 저장소 Gen2 계정 수 있습니다.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight의 Apache Hadoop에서 Azure Data Lake Storage Gen2 사용

Azure Data Lake Storage Gen2는 Azure Data Lake Storage Gen1의 핵심 기능을 가져와 Azure Blob 스토리지에 통합합니다. 이러한 기능에는 Hadoop, Azure AD(Azure Active Directory) 및 POSIX 기반 ACL(액세스 제어 목록)과 호환되는 파일 시스템이 포함됩니다. 이 조합을 사용하면 Azure Data Lake Storage Gen1의 성능을 활용하는 한편, Blob 스토리지의 계층화 및 데이터 수명 주기 관리 기능을 사용할 수 있습니다.

Azure Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)를 참조하세요.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2의 핵심 기능

* **Hadoop과 호환되는 액세스:** Azure Data Lake Storage Gen2에서는 HDFS(Hadoop 분산 파일 시스템)에서와 마찬가지로 데이터를 관리하고 액세스할 수 있습니다. ABFS(Azure Blob Filesystem) 드라이버는 Azure HDInsight 및 Azure Databricks를 포함한 모든 Apache Hadoop 환경 내에서 사용할 수 있습니다. ABFS를 사용하여 Data Lake Storage Gen2에 저장된 데이터에 액세스합니다.

* **POSIX 권한 상위 세트:** Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 지원합니다. 설정은 관리 도구 또는 Apache Hive 및 Apache Spark와 같은 프레임워크를 통해 구성할 수 있습니다.

* **비용 효과:** Data Lake Storage Gen2는 낮은 비용의 스토리지 용량 및 트랜잭션을 제공합니다. Azure Blob 스토리지 수명 주기와 같은 기능을 사용하면 데이터가 수명 주기를 통해 이동함에 따라 청구 요금을 조정하여 비용을 절감할 수 있습니다.

* **Blob 스토리지 도구, 프레임워크 및 앱과의 호환성:** Data Lake Storage Gen2는 Blob 스토리지를 위한 다양한 도구, 프레임워크 및 애플리케이션을 계속 사용합니다.

* **최적화된 드라이버:** ABFS 드라이버는 빅 데이터 분석을 위해 특별히 최적화되었습니다. 해당 REST API는 DFS(분산 파일 시스템) 엔드포인트(dfs.core.windows.net)를 통해 표시됩니다.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2의 새로운 기능

#### <a name="managed-identities-for-secure-file-access"></a>보안 파일 액세스용 관리 ID

Azure HDInsight는 관리 ID를 사용하여 Azure Data Lake Storage Gen2의 파일에 대한 클러스터 액세스를 보호합니다. 관리 ID는 자동으로 관리되는 일단의 자격 증명을 Azure 서비스에 제공하는 Azure Active Directory의 기능입니다. 이러한 자격 증명은 Active Directory 인증을 지원하는 모든 서비스에 인증하는 데 사용할 수 있습니다. 관리 ID를 사용하면 코드 또는 구성 파일에 자격 증명을 저장할 필요가 없습니다.

자세한 내용은 [Azure 리소스에 대한 ID 관리](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

#### <a name="azure-blob-file-system-driver"></a>Azure Blob 파일 시스템 드라이버

Apache Hadoop 애플리케이션은 기본적으로 로컬 디스크 스토리지에서 데이터를 읽고 쓰려고 합니다. ABFS와 같은 Hadoop 파일 시스템 드라이버는 Hadoop 애플리케이션에서 일반 Hadoop 파일 시스템 작업을 에뮬레이트하여 클라우드 스토리지를 사용할 수 있도록 합니다. 드라이버는 애플리케이션에서 받은 명령을 실제 클라우드 스토리지 플랫폼이 이해할 수 있는 작업으로 변환합니다.

이전에 Hadoop 파일 시스템 드라이버는 모든 파일 시스템 작업을 클라이언트 쪽에서 Azure Storage REST API 호출로 변환한 다음, 이 REST API를 호출했습니다. 그러나 이러한 클라이언트 쪽 변환으로 인해 파일 이름 바꾸기와 같은 단일 파일 시스템 작업에 대해 여러 번의 REST API가 호출되었습니다. ABFS는 일부 Hadoop 파일 시스템 논리를 클라이언트 쪽에서 서버 쪽으로 이동했습니다. Azure Data Lake Storage Gen2 API는 이제 Blob API와 동시에 실행됩니다. 일반적인 Hadoop 파일 시스템 작업이 한 번의 REST API 호출로 실행될 수 있으므로 이 마이그레이션은 성능을 향상시킵니다.

자세한 내용은 [ABFS(Azure Blob 파일 시스템) 드라이버: Hadoop 전용 Azure Storage 드라이버](../storage/blobs/data-lake-storage-abfs-driver.md)를 참조하세요.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2의 URI 체계 

Azure Data Lake Storage Gen2는 새 URI 체계를 사용하여 HDInsight에서 Azure Storage의 파일에 액세스합니다.

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI 체계는 SSL 암호화 액세스(`abfss://` 접두사) 및 암호화되지 않은 액세스(`abfs://` 접두사)를 제공합니다. Azure의 동일한 지역에 있는 데이터에 액세스하는 경우에도 가능하면 `abfss`를 사용합니다.

`<FILE_SYSTEM_NAME>`은 Data Lake Storage Gen2 파일 시스템의 경로를 식별합니다.

`<ACCOUNT_NAME>`은 Azure Storage 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.

`<PATH>`는 파일 또는 디렉터리 HDFS 경로 이름입니다.

`<FILE_SYSTEM_NAME>` 및 `<ACCOUNT_NAME>`에 대한 값을 지정하지 않으면 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용합니다. 예를 들어 HDInsight 클러스터와 함께 제공되는 `hadoop-mapreduce-examples.jar` 파일은 다음 경로 중 하나를 사용하여 참조할 수 있습니다.

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> HDInsight 버전 2.1 및 1.6 클러스터에서 파일 이름은 `hadoop-examples.jar`입니다. HDInsight 외부에서 파일을 사용할 때 대부분의 유틸리티는 ABFS 형식을 인식하지 않으며 대신 `example/jars/hadoop-mapreduce-examples.jar`과 같은 기본 경로 형식을 예상합니다.

자세한 내용은 [Azure Data Lake Storage Gen2 URI 사용](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)을 참조하세요.

## <a name="azure-storage"></a>Azure Storage

Azure Storage는 HDInsight와 매끄럽게 통합되는 강력한 범용 스토리지 솔루션입니다. HDInsight는 Azure Storage의 Blob 컨테이너를 클러스터의 기본 파일 시스템으로 사용합니다. HDInsight의 모든 구성 요소 집합은 HDFS 인터페이스를 통해 Blob에 저장된 구조적 또는 비구조적 데이터에서 직접 작동할 수 있습니다.

기본 클러스터 저장소 및 비즈니스 데이터를 위한 별도 저장소 컨테이너를 사용 하 여 HDInsight 로그 및 사용자 고유의 비즈니스 데이터에서 임시 파일을 격리 하기 좋습니다. 응용 프로그램 및 시스템 로그를 포함 하는 기본 blob 컨테이너를 삭제 하는 것이 좋습니다 저장소 비용을 줄이기 위해 각 사용 후 합니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

사용 하 여 저장소 계정을 보호 하려는 경우는 **방화벽 및 virtual network** 에 대 한 제한 **네트워크를 선택한**, 예외를 사용 하도록 설정 해야 **허용 Microsoft 신뢰할 수 있는 서비스 하는 중...**  HDInsight 저장소 계정에 액세스할 수 있도록 합니다.

### <a name="hdinsight-storage-architecture"></a>HDInsight 저장소 아키텍처

다음 다이어그램은 Azure Storage의 HDInsight 아키텍처 추상 보기를 제공합니다.

![Hadoop 클러스터가 HDFS API를 사용하여 Blob 스토리지의 구조적 및 비구조적 데이터에 액세스하고 저장하는 방식을 보여 주는 다이어그램](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight 스토리지 아키텍처")

HDInsight는 계산 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 정규화된 URI를 사용하여 이 파일 시스템에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

    hdfs://<namenodehost>/<path>

HDInsight를 통해 Azure Storage의 데이터에 액세스할 수도 있습니다. 구문은 다음과 같습니다.

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight 클러스터와 Azure Storage 계정을 사용하는 경우 다음 원칙을 고려하세요.

* **클러스터에 연결된 스토리지 계정의 컨테이너**: 계정 이름과 키는 만들기 중 클러스터와 연결되므로 사용자는 이러한 컨테이너의 Blob에 대한 모든 권한을 보유합니다.

* **클러스터에 연결되지 *않은* 스토리지 계정의 공용 컨테이너 또는 공용 Blob:** 컨테이너의 Blob에 대한 읽기 전용 권한을 가집니다.
  
  > [!NOTE]  
  > 공용 컨테이너를 사용하면 해당 컨테이너에서 사용할 수 있는 모든 Blob 목록 및 컨테이너 메타데이터를 가져올 수 있습니다. 공용 Blob을 사용하면 정확한 URL을 아는 경우에만 Blob에 액세스할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../storage/blobs/storage-manage-access-to-resources.md)를 참조하세요.

* **클러스터에 연결되지 *않은* 스토리지 계정의 개인 컨테이너:** WebHCat 작업을 제출할 때 스토리지 계정을 정의하지 않는 경우 컨테이너의 Blob에 액세스할 수 없습니다. 

만들기 프로세스에서 정의된 저장소 계정과 해당 키는 클러스터 노드의 %HADOOP_HOME%/conf/core-site.xml에 저장됩니다. 기본적으로 HDInsight는 core-site.xml 파일에 정의된 스토리지 계정을 사용합니다. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md)를 사용하여 이 설정을 수정할 수 있습니다.

Apache Hive, MapReduce, Apache Hadoop 스트리밍 및 Apache Pig를 비롯한 여러 WebHCat 작업은 스토리지 계정 및 메타데이터 설명을 포함할 수 있습니다. (이 방식은 현재 메타데이터가 아닌 스토리지 계정이 있는 Pig에 적합합니다.) 자세한 내용은 [대체 스토리지 계정 및 Metastore와 HDInsight 클러스터 사용](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)을 참조하세요.

구조적 및 비구조적 데이터에 대한 Blob을 사용할 수 있습니다. Blob 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 슬래시 문자(/)를 포함할 수 있습니다. 예를 들어, Blob 키는 `input/log1.txt`일 수 있습니다. 실제로 `input` 디렉터리는 없지만 키 이름에 슬래시 문자가 있으므로 키가 파일 경로처럼 보입니다.

### <a id="benefits"></a>Azure Storage의 이점
함께 배치되지 않은 컴퓨팅 클러스터와 스토리지 리소스는 성능 비용을 초래합니다. 이러한 비용은 컴퓨팅 클러스터를 Azure 지역 내의 스토리지 계정 리소스와 비슷한 방식으로 만들어 완화할 수 있습니다. 이 지역에서 컴퓨팅 노드는 Azure Storage 내의 고속 네트워크를 통해 데이터에 효율적으로 액세스할 수 있습니다.

HDFS 대신 Azure Storage에 데이터를 저장할 때 다음과 같은 몇 가지 이점이 있습니다.

* **데이터 다시 사용 및 공유:** HDFS의 데이터는 컴퓨팅 클러스터 내에 있습니다. 컴퓨팅 클러스터에 액세스할 수 있는 애플리케이션만 HDFS API를 통해 데이터를 사용할 수 있습니다. 반대로, Azure Storage의 데이터는 HDFS API 또는 Blob 스토리지 REST API를 통해 액세스할 수 있습니다. 이러한 정렬 방식 때문에 데이터의 생성과 소비에 더 많은 애플리케이션(기타 HDInsight 클러스터 포함)과 도구를 사용할 수 있습니다.

* **데이터 보관:** Azure Storage에 데이터를 저장하면 사용자 데이터를 손실하지 않고 컴퓨팅에 사용되는 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

* **데이터 스토리지 비용:** 데이터를 장기간 저장하는 경우 DFS에 저장하는 것이 Azure 스토리지에 저장하는 것보다 비용이 많이 드는데, 이는 컴퓨팅 클러스터의 비용이 Azure 스토리지의 비용보다 비싸기 때문입니다. 또한 컴퓨팅 클러스터를 생성할 때마다 데이터를 다시 로드할 필요가 없기 때문에 데이터 로드 비용도 절약됩니다.

* **탄력적인 규모 확장:** HDFS는 확장된 파일 시스템을 제공하지만, 크기 조정은 클러스터에 대해 만드는 노드의 수에 따라 결정됩니다. 규모를 변경하는 것이 Azure Storage에서 자동으로 수행되는 탄력적인 확장 기능에 의존하는 것보다 더 복잡해질 수 있습니다.

* **지역 복제:** Azure Storage를 지역에서 복제할 수 있습니다. 지역에서 복제는 지리적 복구와 데이터 중복 기능을 제공하지만, 지역에서 복제된 위치에 대한 장애 조치(failover)는 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 따라서 지역에서 복제 기능은 신중하게 선택해야 하며, 추가 비용을 감수할 만큼 가치 있는 데이터에만 사용하는 것이 좋습니다.

특정 MapReduce 작업과 패키지는 Azure Storage에 전혀 저장하고 싶지 않을 만한 중간 결과를 생성할 수 있습니다. 그런 경우에는 로컬 HDFS에 데이터를 저장하도록 선택할 수 있습니다. HDInsight는 Hive 작업 및 기타 프로세스에서 생성되는 이러한 중간 결과 중 일부에 DFS를 사용합니다.

> [!NOTE]  
> 대부분의 HDFS 명령(예: `ls`, `copyFromLocal` 및 `mkdir`)은 Azure Storage에서 여전히 예상대로 작동합니다. `fschk` 및 `dfsadmin`과 같은 기본 HDFS 구현(DFS라고 함)에 해당하는 명령만 Azure Storage에서 다르게 동작합니다.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 개요

Azure Data Lake Storage Gen1은 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure Data Lake를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

Data Lake Storage Gen1은 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스합니다. Data Lake Storage Gen1은 저장된 데이터를 분석하기 위해 설계되었으며, 데이터 분석 시나리오에 필요한 고성능을 제공하도록 튜닝되었습니다. 기본적으로 실제 엔터프라이즈 사용 사례에 반드시 필요한 기능을 포함합니다. 이러한 기능에는 보안, 관리 효율성, 확장성, 안정성 및 가용성이 포함됩니다.

Azure Data Lake Storage Gen1에 대한 자세한 내용은 [Azure Data Lake Storage Gen1 개요](../data-lake-store/data-lake-store-overview.md)를 참조하세요.

Data Lake Storage Gen1의 주요 기능은 다음과 같습니다.

### <a name="compatibility-with-hadoop"></a>Hadoop과의 호환

Data Lake Storage Gen1은 HDFS와 호환되는 Apache Hadoop 파일 시스템이며 Hadoop 에코시스템과 함께 작동됩니다.  WebHDFS API를 사용하는 기존 HDInsight 애플리케이션 또는 서비스는 Data Lake Storage Gen1과 쉽게 통합할 수 있습니다. Data Lake Storage Gen1은 또한 애플리케이션에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

Data Lake Storage Gen1에 저장된 데이터는 MapReduce 또는 Hive와 같은 Hadoop 분석 프레임워크를 사용하여 쉽게 분석될 수 있습니다. Azure HDInsight 클러스터는 Data Lake Storage Gen1에 저장된 데이터에 직접 액세스하도록 프로비전되고 구성될 수 있습니다.

### <a name="unlimited-storage-petabyte-files"></a>무제한 저장소, 페타바이트 파일

Data Lake Storage Gen1은 무제한 스토리지를 제공하며 분석에 대한 다양한 데이터를 저장하는 데 적합합니다. 데이터 레이크에 저장될 수 있는 계정 크기, 파일 크기 또는 데이터 양을 제한하지 않습니다. 개별 파일의 범위는 킬로바이트에서 페타바이트까지 다양하므로 Data Lake Storage Gen1은 모든 형식의 데이터를 저장할 수 있는 유용한 옵션입니다. 데이터는 여러 복사본을 만들어 영구적으로 저장되며 데이터가 Data Lake에 저장될 수 있는 기간에 제한이 없습니다.

### <a name="performance-tuning-for-big-data-analytics"></a>빅 데이터 분석에 대한 성능 조정

Data Lake Storage Gen1은 많은 양의 데이터에 대한 쿼리 및 분석을 위해 대규모 처리 능력이 필요한 대규모 분석 시스템을 실행할 수 있도록 제작되었습니다. 데이터 레이크는 몇 개의 개별 스토리지 서버에 파일의 일부분을 배포합니다. 데이터를 분석하는 경우 이러한 설정은 파일을 동시에 읽을 때의 읽기 처리량을 향상시킵니다.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>엔터프라이즈를 위한 준비: 고가용성 및 보안

Data Lake Storage Gen1은 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다. 기업에서는 기존 데이터 플랫폼의 중요한 부분으로 솔루션에서 Data Lake Storage Gen1을 사용할 수 있습니다.

또한 Data Lake Storage Gen1은 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 데이터 보안](#DataLakeStoreSecurity)을 참조하세요.

### <a name="flexible-data-structures"></a>유연한 데이터 구조

Data Lake Storage Gen1은 사전 변환 없이 모든 데이터를 원시 형식으로 그대로 저장할 수 있습니다. Data Lake Storage Gen1에서는 데이터를 로드하기 전에 먼저 스키마를 정의할 필요가 없습니다. 개별 분석 프레임워크는 데이터를 해석하고 분석 시에 스키마를 정의합니다. 임의 크기 및 형식의 파일을 저장할 수 있으므로 Data Lake Storage Gen1은 구조화, 반구조화 및 비구조화된 데이터를 처리할 수 있습니다.

데이터에 대한 Data Lake Storage Gen1 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure Portal 및 Azure Powershell을 사용하여 저장된 데이터에서 작동합니다. 이러한 인터페이스 및 적절한 컨테이너를 사용하여 저장소에 데이터를 저장하는 한 모든 종류의 데이터를 저장할 수 있습니다. Data Lake Storage Gen1은 저장하는 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.

## <a name="DataLakeStoreSecurity">Data Lake Storage Gen1의 데이터 보안</a>
Data Lake Storage Gen1은 인증을 위해 Azure Active Directory를 사용하고 데이터에 대한 액세스를 관리하기 위해 ACL(액세스 제어 목록)을 사용합니다.

| **기능** | **설명** |
| --- | --- |
| Authentication |Data Lake Storage Gen1은 Data Lake Storage Gen1에 저장된 모든 데이터에 대한 ID 및 액세스 관리를 위해 Azure Active Directory(Azure AD)와 통합합니다. 이러한 통합으로 인해 Data Lake Storage Gen1은 모든 Azure AD 기능의 이점을 얻습니다. 이러한 기능에는 다단계 인증, 조건부 액세스, 역할 기반 액세스 제어, 애플리케이션 사용 모니터링, 보안 모니터링 및 경고 등이 포함됩니다. Data Lake Storage Gen1은 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. [Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1에 인증](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)을 참조하세요.|
| Access Control |Data Lake Storage Gen1은 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. ACL은 루트 폴더, 하위 폴더 및 개별 파일에서도 사용할 수 있습니다. Data Lake Storage Gen1의 컨텍스트에서 ACL 작동 방식에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)를 참조하세요. |
| 암호화 |또한 Data Lake Storage Gen1은 계정에 저장된 데이터에 대한 암호화를 제공합니다. Data Lake Storage Gen1 계정을 만드는 동안 암호화 설정을 지정합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 자세한 내용은 [Data Lake Storage Gen1의 암호화](../data-lake-store/data-lake-store-encryption.md)를 참조하세요. 암호화 관련 구성을 제공하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요. |

Data Lake Storage Gen1의 데이터 보안 방법에 대해 자세히 알아보려면 [Azure Data Lake Storage Gen1에 저장된 데이터 보안](../data-lake-store/data-lake-store-secure-data.md)을 참조하세요.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1과 호환되는 애플리케이션
Data Lake Storage Gen1은 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와 원활하게 통합됩니다.  다른 Azure 서비스뿐만 아니라 오픈 소스 구성 요소와 함께 Data Lake Storage Gen1을 사용할 수 있는 방법을 자세히 알아보려면 아래 링크로 이동하세요.

* Data Lake Storage Gen1과 상호 운용 가능한 오픈 소스 애플리케이션 목록은 [Azure Data Lake Storage Gen1에서 작동하는 오픈 소스 빅 데이터 애플리케이션](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)을 참조하세요.
* Data Lake Storage Gen1을 다른 Azure 서비스와 사용하여 광범위한 시나리오를 활성화하는 방법을 이해하려면 [Azure Data Lake Storage Gen1을 다른 Azure 서비스와 통합](../data-lake-store/data-lake-store-integrate-with-other-services.md)을 참조하세요.
* [빅 데이터 요구 사항에 Azure Data Lake Storage Gen1 사용](../data-lake-store/data-lake-store-data-scenarios.md)을 참조하여 데이터 수집, 데이터 처리, 데이터 다운로드 및 데이터 시각화와 같은 시나리오에서 Data Lake Storage Gen1을 사용하는 방법을 알아봅니다.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 파일 시스템(adl://)
Hadoop 환경(HDInsight 클러스터에서 사용 가능)에서 새로운 파일 시스템인 AzureDataLakeFilesystem(adl://)을 통해 Data Lake Storage Gen1에 액세스할 수 있습니다. adl://을 사용하는 애플리케이션 및 서비스의 성능은 현재 WebHDFS에서 사용할 수 없는 방식으로 최적화할 수 있습니다. 그 결과 Data Lake Storage Gen1을 사용할 경우 권장되는 adl://를 사용하여 최상의 성능을 이용하거나 계속해서 WebHDFS API를 직접 사용하여 기존 코드를 유지할 수 있습니다. Azure HDInsight는 Data Lake Storage Gen1에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

다음을 사용하여 Data Lake Storage Gen1의 데이터에 액세스할 수 있습니다.

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage Gen1의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터에 대해 사용할 수 있는 작업](../data-lake-store/data-lake-store-get-started-portal.md#properties)을 참조하세요.



## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
