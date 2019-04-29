---
title: Azure Data Lake Storage Gen2 소개
description: Azure Data Lake Storage Gen2에 대한 개요 제공
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 8777a7504c48b22d0e670dd9f0d28016ac8918db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627914"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 소개

Azure Data Lake Storage Gen2는 [Azure Blob 스토리지](storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. Data Lake Storage Gen2는 기존의 두 가지 스토리지 서비스인 Azure Blob 스토리지와 Azure Data Lake Storage Gen1의 기능을 통합한 결과입니다. 파일 시스템 의미 체계, 디렉터리 및 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능이 [Azure Blob 스토리지](storage-blobs-introduction.md)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.

## <a name="designed-for-enterprise-big-data-analytics"></a>엔터프라이즈 빅 데이터 분석에 맞게 설계됨

Data Lake Storage Gen2는 Azure에서 Azure Storage를 엔터프라이즈 데이터 레이크를 구축하기 위한 기반으로 만듭니다. 처음부터 수백 기가비트의 처리량을 유지하면서 수 페타바이트의 정보에 대한 서비스를 제공하도록 설계된 Data Lake Storage Gen2는 방대한 양의 데이터를 쉽게 관리할 수 있습니다.

Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](data-lake-storage-namespace.md)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다. 일반적인 개체 저장소 명명 규칙은 이름에 슬래시를 사용하여 계층적 디렉터리 구조를 모방합니다. 이 구조는 Data Lake Storage Gen2에서 구현됩니다. 디렉터리 이름 바꾸기 또는 삭제와 같은 작업은 디렉터리의 이름 접두사를 공유하는 모든 개체를 열거 및 처리하는 대신 디렉터리에 대한 단일 원자성 메타데이터 작업이 됩니다.

과거 클라우드 기반 분석은 성능, 관리 및 보안 영역을 양보해야 했습니다. Data Lake Storage Gen2는 이러한 각 측면을 다음과 같은 방법으로 해결합니다.

-   분석의 필수 구성 요소로 데이터를 복사하거나 변환할 필요가 없으므로 **성능**이 최적화됩니다. 계층 구조 네임스페이스는 디렉터리 관리 작업의 성능을 크게 향상시켜 전반적인 작업 성능을 향상시킵니다.

-   디렉터리 및 하위 디렉터리를 통해 파일을 구성하고 조작할 수 있으므로 **관리**가 더 쉽습니다.

-   **보안**은 디렉터리 또는 개별 파일에 대한 POSIX 권한을 정의할 수 있으므로 적용할 수 있습니다.

-   Data Lake Storage Gen2가 저비용 [Azure Blob Storage](storage-blobs-introduction.md) 위에 빌드되므로 **비용 효과**를 얻을 수 있습니다. 추가 기능을 통해 Azure에서 빅 데이터 분석을 실행하기 위한 총 소유 비용이 더 낮아집니다.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2의 주요 기능

-   **Hadoop 호환 액세스**: Data Lake Storage Gen2를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)에서와 마찬가지로 데이터를 관리하고 액세스할 수 있습니다. 새 [ABFS 드라이버](data-lake-storage-abfs-driver.md)는 [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) 및 [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/)를 포함한 모든 Apache Hadoop 환경 내에서 Data Lake Storage Gen2에 저장된 데이터에 액세스하는 데 사용할 수 있습니다.

-   **POSIX 권한 상위 세트**: Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 지원합니다. 설정은 Storage 탐색기 또는 프레임워크(예: Hive 및 Spark)를 통해 구성할 수 있습니다.

-   **비용 효율성**: Data Lake Storage Gen2는 낮은 비용의 스토리지 용량 및 트랜잭션을 제공합니다. 데이터가 전체 수명 주기를 통해 전환됨에 따라 청구 요금이 바뀌므로 [Azure Blob Storage 수명 주기](storage-lifecycle-management-concepts.md)와 같은 기본 제공 기능을 통해 비용이 최소한으로 유지됩니다.

-   **최적화된 드라이버**: ABFS 드라이버가 [특히 최적화](data-lake-storage-abfs-driver.md) 빅 데이터 분석에 대 한 합니다. 해당 REST Api 끝점을 통해 표시 됩니다 `dfs.core.windows.net`합니다.

### <a name="scalability"></a>확장성

Azure Storage는 Data Lake Storage Gen2 또는 Blob Storage 인터페이스를 통해 액세스하는지 여부에 관계없이 계획적으로 확장 가능합니다. ‘많은 엑사바이트의 데이터’를 저장하고 제공할 수 있습니다. 이 저장소 양은 높은 IOPS(초당 입출력 작업 수) 수준에서 Gbps(초당 기가비트 수)로 측정되는 처리량에 사용할 수 있습니다. 지속성 외에도 서비스, 계정 및 파일 수준에서 측정되는 거의 지속적인 요청별 대기 시간으로 처리가 실행됩니다.

### <a name="cost-effectiveness"></a>비용 효과

Azure Blob 스토리지에 기반하여 Data Lake Storage Gen2를 구축하는 경우 얻을 수 있는 많은 이점 중 하나는 스토리지 용량 및 트랜잭션의 비용이 저렴하다는 것입니다. 다른 클라우드 스토리지 서비스와 달리 Data Lake Storage Gen2에 저장된 데이터는 분석하기 전에 이동하거나 변환할 필요가 없습니다. 가격 책정에 대한 자세한 내용은 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage)을 참조하세요.

또한 [계층 구조 네임스페이스](data-lake-storage-namespace.md)와 같은 기능은 많은 분석 작업의 전반적인 성능을 크게 개선합니다. 이 성능 개선은 동일한 양의 데이터를 처리하는 데 필요한 계산 능력이 감소하므로 종단 간 분석 작업에 대한 TCO(총 소유 비용)가 낮아짐을 의미합니다.

### <a name="one-service-multiple-concepts"></a>하나의 서비스, 여러 개념

Data Lake Storage Gen2는 빅데이터 분석을 위한 추가 기능이며, Azure Blob 스토리지를 기반으로 하여 구축됩니다. Blob의 기존 플랫폼 구성 요소를 활용하여 분석을 위한 데이터 레이크를 만들고 운영하면 많은 이점이 있지만, 동일한 공유 항목을 설명하는 여러 개념으로 이어집니다.

여러 개념으로 설명되는 동등한 실체는 다음과 같습니다. 달리 지정하지 않는 한 이러한 실체는 직접적인 동의어입니다.

| 개념                                | 최상위 수준 조직 | 낮은 수준의 조직                                            | 데이터 컨테이너 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob – 범용 개체 스토리지 | 컨테이너              | 가상 디렉터리(SDK만 해당 - 원자성 조작을 제공하지 않음) | Blob           |
| ADLS Gen2 – 분석 스토리지          | 파일 시스템             | 디렉터리                                                           | 파일           |

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 다음 표에는 이러한 플랫폼이 나와 있습니다.

> [!NOTE]
> 이 표에 나오는 버전만 지원됩니다.

| 플랫폼 |  지원되는 버전 | 추가 정보 |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 이상 | [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전은?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 이상 | [Apache Hadoop 릴리스 보관 파일](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 이상 | [Cloudera Enterprise 6.x 릴리스 정보](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 이상 | [Databricks Runtime 버전](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x 이상 | [클라우드 데이터 액세스 구성](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Data Lake Storage Gen2의 기본 개념을 설명하고 데이터를 저장, 액세스, 관리하고 데이터에서 인사이트를 얻는 방법을 자세히 살펴봅니다.

-   [계층 구조 네임스페이스](data-lake-storage-namespace.md)
-   [저장소 계정을 만드는](data-lake-storage-quickstart-create-account.md)
-   [Azure Databricks에서 Data Lake Storage Gen2 계정 사용](data-lake-storage-quickstart-create-databricks-account.md)
