---
title: Azure Data Lake Storage Gen2 소개
description: Azure Data Lake Storage Gen2 소개를 읽어보세요. 주요 기능에 대해 알아봅니다. 지원되는 Blob 스토리지 기능, Azure 서비스 통합 및 플랫폼을 검토합니다.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d85b0cd2f9fa7eb81f5c39bd5d163188e3cd7106
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835768"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 소개

Azure Data Lake Storage Gen2는 [Azure Blob 스토리지](storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. Data Lake Storage Gen2는 기존의 두 가지 스토리지 서비스인 Azure Blob 스토리지와 Azure Data Lake Storage Gen1의 기능을 통합한 결과입니다. 파일 시스템 의미 체계, 디렉터리 및 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능이 [Azure Blob 스토리지](storage-blobs-introduction.md)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.

## <a name="designed-for-enterprise-big-data-analytics"></a>엔터프라이즈 빅 데이터 분석에 맞게 설계됨

Data Lake Storage Gen2는 Azure에서 Azure Storage를 엔터프라이즈 데이터 레이크를 구축하기 위한 기반으로 만듭니다. 처음부터 수백 기가비트의 처리량을 유지하면서 수 페타바이트의 정보에 대한 서비스를 제공하도록 설계된 Data Lake Storage Gen2는 방대한 양의 데이터를 쉽게 관리할 수 있습니다.

Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](data-lake-storage-namespace.md)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다. 일반적인 개체 저장소 명명 규칙은 이름에 슬래시를 사용하여 계층적 디렉터리 구조를 모방합니다. 이 구조는 Data Lake Storage Gen2에서 구현됩니다. 디렉터리 이름 바꾸기 또는 삭제와 같은 작업은 디렉터리의 이름 접두사를 공유하는 모든 개체를 열거 및 처리하는 대신 디렉터리에 대한 단일 원자성 메타데이터 작업이 됩니다.

Data Lake Storage Gen2는 Blob 스토리지를 기반으로 하며 다음과 같은 방법으로 성능, 관리 및 보안을 향상시킵니다.

-   분석의 필수 구성 요소로 데이터를 복사하거나 변환할 필요가 없으므로 **성능**이 최적화됩니다. Blob 스토리지의 단일 구조 네임스페이스와 비교할 때 계층 구조 네임스페이스는 디렉터리 관리 작업의 성능을 크게 향상시켜 전반적인 작업 성능을 향상시킵니다.

-   디렉터리 및 하위 디렉터리를 통해 파일을 구성하고 조작할 수 있으므로 **관리**가 더 쉽습니다.

-   **보안**은 디렉터리 또는 개별 파일에 대한 POSIX 권한을 정의할 수 있으므로 적용할 수 있습니다.

Data Lake Storage Gen2는 저비용 [Azure Blob 스토리지](storage-blobs-introduction.md) 위에 빌드되므로 매우 비용 효과적입니다. 추가 기능을 통해 Azure에서 빅 데이터 분석을 실행하기 위한 총 소유 비용이 더 낮아집니다.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2의 주요 기능

-   **Hadoop 호환 액세스**: Data Lake Storage Gen2를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)에서와 마찬가지로 데이터를 관리하고 액세스할 수 있습니다. 새 [ABFS 드라이버](data-lake-storage-abfs-driver.md)는 [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) 및 [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)를 포함한 모든 Apache Hadoop 환경 내에서 Data Lake Storage Gen2에 저장된 데이터에 액세스하는 데 사용할 수 있습니다.

-   **POSIX 권한 상위 세트**: Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 지원합니다. 설정은 Storage Explorer 또는 프레임워크(예: Hive 및 Spark)를 통해 구성할 수 있습니다.

-   **비용 효율성**: Data Lake Storage Gen2는 낮은 비용의 스토리지 용량 및 트랜잭션을 제공합니다. 데이터가 전체 수명 주기를 통해 전환됨에 따라 청구 요금이 바뀌므로 [Azure Blob Storage 수명 주기](storage-lifecycle-management-concepts.md)와 같은 기본 제공 기능을 통해 비용이 최소한으로 유지됩니다.

-   **최적화된 드라이버**: ABFS 드라이버는 빅 데이터 분석을 위해 [특별히 최적화](data-lake-storage-abfs-driver.md)되었습니다. 해당 REST API는 `dfs.core.windows.net` 엔드포인트를 통해 표시됩니다.

### <a name="scalability"></a>확장성

Azure Storage는 Data Lake Storage Gen2 또는 Blob Storage 인터페이스를 통해 액세스하는지 여부에 관계없이 계획적으로 확장 가능합니다. ‘많은 엑사바이트의 데이터’를 저장하고 제공할 수 있습니다.** 이 스토리지 양은 높은 IOPS(초당 입출력 작업 수) 수준에서 Gbps(초당 기가비트 수)로 측정되는 처리량에 사용할 수 있습니다. 지속성 외에도 서비스, 계정 및 파일 수준에서 측정되는 거의 지속적인 요청별 대기 시간으로 처리가 실행됩니다.

### <a name="cost-effectiveness"></a>비용 효과

Azure Blob 스토리지에 기반하여 Data Lake Storage Gen2를 구축하는 경우 얻을 수 있는 많은 이점 중 하나는 스토리지 용량 및 트랜잭션의 비용이 저렴하다는 것입니다. 다른 클라우드 스토리지 서비스와 달리 Data Lake Storage Gen2에 저장된 데이터는 분석하기 전에 이동하거나 변환할 필요가 없습니다. 가격 책정에 대한 자세한 내용은 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage)을 참조하세요.

또한 [계층 구조 네임스페이스](data-lake-storage-namespace.md)와 같은 기능은 많은 분석 작업의 전반적인 성능을 크게 개선합니다. 이 성능 개선은 동일한 양의 데이터를 처리하는 데 필요한 컴퓨팅 능력이 감소하므로 엔드투엔드 분석 작업에 대한 TCO(총 소유 비용)가 낮아짐을 의미합니다.

### <a name="one-service-multiple-concepts"></a>하나의 서비스, 여러 개념

Data Lake Storage Gen2는 빅데이터 분석을 위한 추가 기능이며, Azure Blob 스토리지를 기반으로 하여 구축됩니다. Blob의 기존 플랫폼 구성 요소를 활용하여 분석을 위한 데이터 레이크를 만들고 운영하면 많은 이점이 있지만, 동일한 공유 항목을 설명하는 여러 개념으로 이어집니다.

여러 개념으로 설명되는 동등한 실체는 다음과 같습니다. 달리 지정하지 않는 한 이러한 실체는 직접적인 동의어입니다.

| 개념                                | 최상위 수준 조직 | 낮은 수준의 조직                                            | 데이터 컨테이너 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob – 범용 개체 스토리지 | 컨테이너              | 가상 디렉터리(SDK만 해당 - 원자성 조작을 제공하지 않음) | Blob           |
| Azure Data Lake Storage Gen2 - Analytics Storage          | 컨테이너            | 디렉터리                                                           | 파일           |

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

 [진단 로깅](../common/storage-analytics-logging.md),  [액세스 계층](storage-blob-storage-tiers.md) 및  [Blob Storage 수명 주기 관리 정책](storage-lifecycle-management-concepts.md)과 같은 Blob 스토리지 기능은 이제 계층 구조 네임스페이스가 있는 계정으로 작동합니다. 따라서 이러한 기능에 대한 액세스를 잃지 않고 Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정할 수 있습니다. 

지원되는 Blob 스토리지 기능 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조하세요.

## <a name="supported-azure-service-integrations"></a>지원되는 Azure 서비스 통합

Data Lake Storage gen2는 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원합니다. 지원되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)


