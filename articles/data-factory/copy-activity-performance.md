---
title: Azure Data Factory의 복사 작업 성능 및 조정 가이드 | Microsoft Docs
description: 복사 활동을 사용 하는 경우 Azure Data Factory에서 데이터 이동의 성능에 영향을 주는 주요 요소에 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 3ea89e9f6a6bb8a4c377c70bbe1b5540d3b74d44
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341240"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>복사 활동 성능 및 튜닝 가이드
> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)


Azure Data Factory 복사 작업은 최고 수준의 보안, 안정성 및 고성능 데이터 로드 솔루션을 제공 합니다. 복사본 수십 테라바이트의 데이터 일 마다 다양 한 다양 한 클라우드 및 온-프레미스 데이터 저장소를 사용할 수 있습니다. 빠른 데이터 로드 성능은 core 빅 데이터 문제에 집중할 수 있도록 키: 고급 분석 솔루션을 구축 하 고 모든 데이터에서 깊은 통찰을 얻는 합니다.

Azure는 엔터프라이즈급의 집합을 데이터 저장소 및 데이터 웨어하우스 솔루션을 제공합니다. 복사 작업 로드 환경을 쉽게 구성 및 설정할 수 있는 고도로 최적화 된 데이터를 제공 합니다. 단일 복사 작업을 사용 하 여 데이터를 로드할 수 있습니다.

* Azure SQL Data Warehouse에는 1.2gbps 합니다.
* 1\.0 GBps에서 azure Blob 저장소입니다.
* 1\.0 GBps에서 azure Data Lake Store입니다.

이 문서에서는 다음을 설명합니다.

* [성능 참조 번호](#performance-reference) 에 대 한 프로젝트를 계획 하려면 원본 및 싱크 데이터 저장소를 지원 합니다.
* 포함 하는 다양 한 시나리오로 복사 처리량을 높일 수 있는 기능 [데이터 통합 단위](#data-integration-units) (DIUs) [병렬 복사](#parallel-copy), 및 [준비 된 복사](#staged-copy)합니다.
* [성능 조정 지침](#performance-tuning-steps) 복사 성능이 영향을 주는 주요 요소 및 성능을 조정 하는 방법에 있습니다.

> [!NOTE]
> 모르는 경우 복사 활동을 사용 하 여 일반적을 참조 합니다 [복사 작업 개요](copy-activity-overview.md) 이 문서를 읽기 전에 합니다.
>

## <a name="performance-reference"></a>성능 참조

참조로 다음 표에서 수를 보여 줍니다는 복사 처리량 (mbps) 지정 된 소스의 및 싱크 쌍을 단일 복사 작업 실행에 사내 테스트에 따른 합니다. 비교를 보여 줍니다의 다른 설정이 어떻게 [데이터 통합 단위](#data-integration-units) 하거나 [자체 호스팅 integration runtime 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime) (여러 노드) 복사 성능에 도움이 됩니다.

![성능 매트릭스](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> 복사 작업이 Azure integration runtime에서 실행 되는 경우 최소 허용 된 데이터 통합 단위 (이전의 데이터 이동 단위)는 2입니다. 지정 하지 않으면 참조에 사용 되는 기본 데이터 통합 단위 [데이터 통합 단위](#data-integration-units)합니다.

**주의할 사항:**

* 처리량은 다음 수식을 사용 하 여 계산 됩니다. [원본에서 읽은 데이터의 크기] / [복사 작업 실행 기간].
* 테이블의 성능 참조 번호를 사용 하 여 측정 된 것을 [Tpc-h](http://www.tpc.org/tpch/) 단일 복사 작업 실행의 데이터 집합입니다. 파일 기반 저장소에 대 한 테스트 파일은 크기가 10GB를 사용 하 여 여러 파일은입니다.
* Azure 데이터 저장소에서는 원본 및 싱크는 동일한 Azure 지역에 있습니다.
* 온-프레미스와 클라우드 간의 하이브리드 복사에 대 한 데이터 저장소에서 각 자체 호스팅된 통합 런타임 노드는 사양 사용 하 여 데이터 저장소에서 분리 된 컴퓨터에서 실행 되었습니다. 단일 작업이 실행 중인 경우 복사 작업은 테스트 컴퓨터의 CPU, 메모리 또는 네트워크 대역폭의 작은 부분만 사용합니다.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 코어 2.20GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>메모리</td>
        <td>128GB</td>
    </tr>
    <tr>
        <td>네트워크</td>
        <td>인터넷 인터페이스: 10Gbps; 인트라넷 인터페이스: 40Gbps</td>
    </tr>
    </table>


> [!TIP]
> 자세한 DIUs를 사용 하 여 더 높은 처리량을 얻을 수 있습니다. 예를 들어 100 DIUs를 사용 하 여 데이터 복사할 수 있습니다 Azure Blob storage에서 1.0 GBps에서 Azure Data Lake Store로 합니다. 이 기능 및 지원 되는 시나리오에 대 한 자세한 내용은 참조는 [데이터 통합 단위](#data-integration-units) 섹션입니다. 

## <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory 내에서 단일 단위의 힘 (CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 척도입니다. 데이터 통합 단위에만 적용 됩니다 [Azure 통합 런타임은](concepts-integration-runtime.md#azure-integration-runtime), 하지 않고 [자체 호스팅 통합 런타임](concepts-integration-runtime.md#self-hosted-integration-runtime)합니다.

복사 작업 실행을 강화 하려면 최소 DIUs는 2입니다. 지정되지 않은 경우, 다양한 복사 시나리오에서 사용되는 기본 DIU가 나열된 다음 표를 참조하세요.

| 복사 시나리오 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |
| 파일 기반 저장소 간의 데이터 복사 | 4에서 파일의 크기와 수에 따라 32 사이 |
| 그 밖의 모든 복사 시나리오 | 4 |

이 기본값을 재정의하려면 **dataIntegrationUnits** 속성의 값을 다음과 같이 지정합니다. 합니다 *허용 되는 값* 에 대 한 합니다 **dataIntegrationUnits** 속성은 최대 길이 256입니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수*는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다. 특정 복사 원본 및 싱크에 대해 더 많은 단위를 구성할 때 얻을 수 있는 성능상 이점 수준에 대한 자세한 내용은 [성능 참조](#performance-reference)를 참조하세요.

각 복사 작업 실행을 모니터링 하는 경우 복사 활동 출력의 실행에 사용 되는 DIUs 볼 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-overview.md#monitoring)합니다.

> [!NOTE]
> 설정 DIUs의에 Azure Storage, Azure Data Lake Storage, Amazon S3, Google 클라우드 Storage에서 여러 파일을 복사 하는 경우에 현재 적용 4 보다 큰, FTP, 클라우드 또는 클라우드 SFTP 다른 클라우드 데이터 저장소로.
>

**예제**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>데이터 통합 단위 청구 영향

비용이 청구 기반 복사 작업의 총 시간에 기억 합니다. 데이터 이동에 대 한 요금이 청구 됩니다 총 기간 DIUs 간에 지속 시간의 합계가 표시 됩니다. 두 클라우드 단위로 1시간이 걸렸던 복사 작업이 이제 8개의 클라우드에서 15분이 소요된다면 전체 청구 금액은 거의 동일한 상태로 유지됩니다.

## <a name="parallel-copy"></a>병렬 복사

사용할 수는 **parallelCopies** 속성을 사용 하 여 복사 작업을 병렬 처리를 나타냅니다. 이 속성의 원본에서 읽거나 병렬로 싱크 데이터 저장소에 쓸 수 있는 복사 작업 내에서 스레드 최대 수로 생각할 수 있습니다.

각 복사 작업 실행에 대 한 Azure Data Factory는 원본 데이터 저장소와 대상 데이터 저장소에서 데이터를 복사 하는 데는 병렬 복사의 수를 결정 합니다. 사용 되는 병렬 복사의 기본 수는 원본 및 싱크에서 사용 하는 형식에 따라 달라 집니다.

| 복사 시나리오 | 서비스에 의해 결정되는 기본 병렬 복사 개수 |
| --- | --- |
| 파일 기반 저장소 간의 데이터 복사 |두 클라우드 데이터 저장소 또는 자체 호스팅된 통합 런타임 컴퓨터의 실제 구성 간에 데이터를 복사 하는 데 DIUs 수와 파일의 크기에 따라 달라 집니다. |
| 원본 데이터 저장소의 데이터를 Azure Table Storage에 복사 |4 |
| 그 밖의 모든 복사 시나리오 |1 |

> [!TIP]
> 파일 기반 저장소 간에 데이터를 복사할 때 기본 동작을 일반적으로 사용 하면 최상의 처리량을 합니다. 기본 동작은 자동 결정 합니다.

저장 데이터를 호스트 하는 컴퓨터에서 로드를 제어 하거나 복사 성능을 조정 하기 위해 기본 값을 재정의 값을 지정 합니다 **parallelCopies** 속성입니다. 값은 1 이상의 정수여야 합니다. 런타임 시의 복사 작업은 최상의 성능을 위해 사용자가 설정한 값 보다 작거나 하는 값입니다.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

**주의할 사항:**

* 파일 기반 저장소 간에 데이터를 복사 하는 경우 **parallelCopies** 파일 수준 병렬 처리를 결정 합니다. 단일 파일 내에서 청크를 아래에 있는 자동으로 발생 하며 투명 하 게 합니다. 가장 적합 한 청크를 병렬로 데이터를 로드 하는 지정 된 원본 데이터 저장소 유형에 대 한 크기 및 직각으로 사용 하도록 설계 되었습니다 **parallelCopies**합니다. 런타임 시 데이터 이동 서비스에서 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 파일의 수를 넘지 않습니다. 복사 동작이 **mergeFile**, 복사 작업 파일 수준 병렬 처리 활용을 사용할 수 없습니다.
* 에 대 한 값을 지정 하는 경우는 **parallelCopies** 속성을 원본에서 로드 증가 고려 하 고 싱크 데이터 저장소로. 또한 하이브리드 복사에 대 한 예를 들어에서 복사 작업은 제공 하는 경우에 자체 호스팅된 integration runtime 부하 증가 고려 합니다. 이 부하 증가 있는 경우 특히 여러 활동이 있거나 동일한 데이터 저장소에 대해 실행 되는 동일한 작업의 동시 실행 발생 합니다. 데이터 저장소나 자체 호스팅된 integration runtime을 가득 채워 부하를 감소 합니다 **parallelCopies** 값을 로드 합니다.
* 파일 기반 저장소에 파일 기반 하지 않는 저장소에서 데이터를 복사할 때 데이터 이동 서비스를 무시 합니다 **parallelCopies** 속성입니다. 병렬 처리를 지정하더라도 이 경우에는 적용되지 않습니다.
* 합니다 **parallelCopies** 속성에 수직인 **dataIntegrationUnits**합니다. 전자는 모든 데이터 통합 단위에서 계산됩니다.

## <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase 통해 SQL Data Warehouse로 다양 한 데이터 저장소에서 데이터를 수집 해야 합니다.** SQL Data Warehouse는 많은 양의 데이터를 SQL Data Warehouse에 로드하는 처리량이 높은 메커니즘인 PolyBase를 사용합니다. 원본 데이터가 Blob storage 또는 Azure Data Lake Store에 있어야 하 고 추가 조건을 충족 해야 합니다. Blob Storage 또는 Azure Data Lake Store가 아닌 데이터 저장소에서 데이터를 로드하는 경우 중간 준비 Blob Storage를 통해 데이터 복사를 활성화할 수 있습니다. 이 경우 Azure Data Factory는 PolyBase의 요구 사항을 충족 하도록 하려면 필요한 데이터 변환을 수행 합니다. 그런 다음 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 효과적으로 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
- **하이브리드 데이터 이동을 수행 하는 데 걸리는 경우도 있습니다 (즉, 온-프레미스에서 복사할 데이터 저장소는 클라우드 데이터 저장소로) 느린 네트워크 연결을 통해.** 성능 향상을 위해 클라우드의 준비 데이터 저장소로 데이터 이동 시간이 적게 소요 되는 온-프레미스 데이터를 압축 하려면 준비 된 복사를 사용할 수 있습니다. 다음 대상 데이터 저장소로 로드 하기 전에 준비 저장소에서 데이터 압축을 풀 수 있습니다.
- **기업 IT 정책 때문에 방화벽에서 포트 80과 포트 443 이외의 포트를 열려는 원하지입니다.** 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크에 데이터를 복사할 경우 Windows 방화벽 및 회사 방화벽 모두에 대한 포트 1433에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서는 준비 된 복사 먼저 준비 인스턴스에 HTTP 또는 포트 443에서 HTTPS를 통해 Blob 저장소로 데이터를 복사 하려면 자체 호스팅된 통합 런타임 활용을 걸릴 수 있습니다. 그런 다음 Blob storage 준비에서 SQL Database 또는 SQL Data Warehouse에 데이터를 로드할 수 것입니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 Blob Storage(직접 준비)로 복사됩니다. 그 다음, 데이터가 준비 데이터 저장소에서 싱크 데이터 저장소로 복사됩니다. Azure Data Factory를 2 단계 흐름을 자동으로 관리합니다. Azure Data Factory 데이터 이동이 완료 된 후에 준비 저장소에서 임시 데이터를 정리 합니다.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용 하 여 데이터 이동을 활성화 하면 원본 데이터에서 데이터를 이동 하기 전에 압축 데이터 저장소는 중간 또는 준비 데이터 저장 하는 중간 또는 준비 dat에서 데이터를 이동 하기 전에 압축할지 것인지 지정할 수 있습니다. 싱크 데이터 저장소에 저장 되 는입니다.

현재는 준비 저장소를 사용하여 두 온-프레미스 데이터 저장소 간에 데이터를 복사할 수 없습니다.

### <a name="configuration"></a>구성

구성 된 **enableStaging** 하려는 데이터를 대상 데이터 저장소로 로드 하기 전에 Blob 저장소에서 준비할 수 있는지 여부를 지정 하려면 복사 작업에서 설정 합니다. 설정한 경우 **enableStaging** 에 `TRUE`, 다음 표에 나열 된 추가 속성을 지정 합니다. Azure Storage를 만들어야 할 수도 또는 저장소 공유 액세스 서명 연결 된 서비스 계정이 없는 경우를 준비 합니다.

| 자산 | 설명 | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |아닙니다. |
| linkedServiceName |중간 준비 저장소로 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 연결 서비스로 지정합니다. <br/><br/> PolyBase 통해 SQL Data Warehouse로 데이터를 로드 하는 공유 액세스 서명을 사용 하 여 저장소를 사용할 수 없습니다. 다른 모든 시나리오에서는 사용할 수 있습니다. |해당 없음 |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| 경로 |준비 데이터를 포함할 Blob Storage 경로를 지정합니다. 경로 제공 하지 않으면 서비스는 임시 데이터를 저장할 컨테이너를 만듭니다. <br/><br/> 공유 액세스 서명을 포함한 저장소를 사용하거나 특정 위치에 임시 데이터가 필요한 경우에만 경로를 지정합니다. |해당 없음 |아닙니다. |
| enableCompression |대상에 복사 하기 전에 데이터를 압축할지 여부를 지정 합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |아닙니다. |

>[!NOTE]
> 압축 사용 시, 서비스 주체 또는 MSI 인증을 사용 하 여 준비 된 복사를 사용 하 여 스테이징 연결 된 blob에 대 한 서비스 지원 되지 않습니다.

앞의 표에 설명 되어 있는 속성을 사용 하 여 복사 작업의 샘플 정의 다음과 같습니다.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

두 단계에 따라 요금이 부과 됩니다: 복사 기간 및 복사 유형의 합니다.

* 사용 하는 경우 데이터를 복사 하는 클라우드 복사 중에 준비 클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소 모두 Azure 통합 런타임에 의해 기술 교육 단계에서 [총 1 단계 및 2 단계 복사 기간] x [클라우드 복사 단가] 요금이 부과 됩니다.
* 사용 하는 경우 자체 호스팅된 통합 런타임에서 기술 교육 한 단계는 온-프레미스 데이터 저장소에서에 데이터를 복사 클라우드 데이터 저장소는 하이브리드 복사 중에 준비, [하이브리드 복사 기간]에 대 한 요금이 부과 됩니다 x [하이브리드 복사 단가] + [클라우드 복사 기간] x [클라우드 복사 단가].

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

복사 작업을 사용 하 여 Azure Data Factory 서비스의 성능을 조정 하기 위해 다음이 단계를 수행 합니다.

1. **기준선을 설정 합니다.** 개발 단계 중 대표적인 샘플 데이터에 대 한 복사 작업을 사용 하 여 파이프라인을 테스트 합니다. 실행 세부 정보 및 성능 특성을 수집 [복사 작업 모니터링](copy-activity-overview.md#monitoring)합니다.

2. **진단 하 고 성능을 최적화 합니다.** 관찰 한 성능을 기대를 충족 하지 않습니다, 경우에 성능 병목 현상을 식별 합니다. 그런 다음 성능을 최적화하여 병목 현상의 효과를 제거하거나 줄입니다.

    경우에 따라 Azure Data Factory의 복사 작업을 실행 하면 "성능 튜닝 팁" 메시지가 표시 위쪽에 [복사 작업 모니터링 페이지](copy-activity-overview.md#monitor-visually)다음 예제에서와 같이 합니다. 메시지 지정된 복사 실행에 대 한 식별 된 병목을 알려 줍니다. 도 안내에 boost 복사 처리량을 변경 합니다. 성능 튜닝 팁은 현재와 같은 제안 사항을 제공 합니다.

    - Azure SQL Data Warehouse로 데이터를 복사할 때 PolyBase를 사용 하 여 합니다.
    - 데이터 저장소 쪽에서 리소스 병목 상태가 되 면 Azure Cosmos DB 요청 단위 또는 Azure SQL Database Dtu (데이터베이스 처리량 단위)를 늘립니다.
    - 불필요 한 준비 된 복사를 제거 합니다.

    성능 튜닝 규칙도 점진적으로 보강됩니다.

    **예제: 성능 튜닝 팁을 사용 하 여 Azure SQL Database로 복사 합니다.**

    이 샘플에서는 복사본을 실행 하는 동안 Azure Data Factory 정보는 Azure SQL Database에 쓰기 작업은 속도가 높은 DTU 사용률에 도달 하면 싱크 합니다. 제안이 더 많은 Dtu 사용 하 여 Azure SQL Database 계층 증가 하는 것입니다. 

    ![성능 튜닝 팁을 사용한 복사 모니터링](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    성능 튜닝과 함께 일반적으로 고려해야 하는 사항은 다음과 같습니다. 이 문서에서는 성능 진단에 대해 자세히 설명하지는 않습니다.

   * 성능 기능:
     * [병렬 복사](#parallel-copy)
     * [데이터 통합 단위](#data-integration-units)
     * [준비된 복사](#staged-copy)
     * [자체 호스팅된 integration runtime 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [자체 호스팅 통합 런타임](#considerations-for-self-hosted-integration-runtime)
   * [원본](#considerations-for-the-source)
   * [싱크](#considerations-for-the-sink)
   * [직렬화 및 역직렬화](#considerations-for-serialization-and-deserialization)
   * [압축](#considerations-for-compression)
   * [열 매핑](#considerations-for-column-mapping)
   * [기타 고려 사항](#other-considerations)

3. **전체 데이터 집합을 구성을 확장 합니다.** 실행 결과 및 성능에 만족 하면 정 및 전체 데이터 집합을 포함 하는 파이프라인을 확장할 수 있습니다.

## <a name="considerations-for-self-hosted-integration-runtime"></a>자체 호스팅된 통합 런타임에 대 한 고려 사항

복사 활동 자체 호스팅된 integration runtime에서 실행 되 면 다음 note:

**설정**: 전용된 호스트 통합 런타임 컴퓨터를 사용 하는 것이 좋습니다. 참조 [자체 호스팅 통합 런타임 사용 시 고려 사항](concepts-integration-runtime.md)합니다.

**규모 확장**: 하나 이상의 노드를 사용 하 여 단일 논리 자체 호스팅된 integration runtime을 동시에 한 번에 여러 개의 복사 작업 실행을 사용할 수 있습니다. 많은 동시 복사 작업 실행 수가 많은 또는 많은 양의 데이터 복사를 사용 하 여 하이브리드 데이터 이동에 있는 것이 좋습니다 [자체 호스팅된 통합 런타임을 확장](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 더 많은 리소스를 프로 비전 하려면 복사를 제공 합니다.

## <a name="considerations-for-the-source"></a>원본에 대한 고려 사항

### <a name="general"></a>일반

내부 데이터 저장소에서 또는에 대해 실행 하는 다른 워크 로드에서 전복 되지 않습니다는 해야 합니다.

Microsoft 데이터 저장소에 대 한 참조 [모니터링 및 튜닝 항목](#performance-reference) 데이터 저장소에 관련 되어 있습니다. 이러한 항목에서 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하고 처리량을 최대화하는 방법을 파악할 수 있습니다.

* Blob storage에서 SQL Data Warehouse로 데이터를 복사한 경우에 성능을 향상 시키기 위해 PolyBase를 사용 하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* HDFS에서 Azure Blob storage 또는 Azure Data Lake Store로 데이터를 복사 하는 경우에 성능을 향상 시키기 위해 DistCp를 사용 하는 것이 좋습니다. 자세한 내용은 [HDFS에서 데이터 복사를 사용 하 여 DistCp](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)합니다.
* Azure SQL Data Warehouse, Azure BLob storage 또는 Azure Data Lake Store를 Redshift에서 데이터를 복사 하는 경우에 UNLOAD를 사용 하 여 성능을 향상 시키기 위해 하는 것이 좋습니다. 자세한 내용은 [Amazon Redshift에서 데이터를 복사할 UNLOAD를 사용 하 여](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)입니다.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **평균 파일 크기 및 파일 개수**: 한 번에 하나의 파일 데이터를 전송 하는 복사 작업 합니다. 동일한 양의 데이터를 이동하는 경우 각 파일에 대한 부트스트랩 단계이기 때문에 적은 수의 큰 파일보다는 많은 수의 작은 파일로 데이터가 구성되는 경우 전체 처리량은 느려집니다. 가능한 경우 더 높은 처리량을 더 큰 파일에 작은 파일을 결합 합니다.
* **파일 형식 및 압축**: 성능을 향상하는 다양한 방법은 [직렬화 및 역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **데이터 패턴**: 테이블 스키마는 복사본 처리량에 영향을 줍니다. 행 크기가 크면 동일한 양의 데이터를 복사 하는 작은 행 크기 보다 더 나은 성능을 제공 합니다. 원인은 데이터베이스가 적은 수의 행을 포함하는 더 적은 배치의 데이터보다 더욱 효율적으로 검색할 수 있기 때문입니다.
* **쿼리 또는 저장 프로시저**: 데이터를 보다 효율적으로 가져오기 위해 복사 활동 원본에서 지정한 쿼리 또는 저장된 프로시저의 논리를 최적화 합니다.

## <a name="considerations-for-the-sink"></a>싱크에 대한 고려 사항

### <a name="general"></a>일반

내부 데이터 저장소에서 또는에 대해 실행 하는 다른 워크 로드에서 전복 되지 않습니다는 해야 합니다.

Microsoft 데이터 저장소에 대 한 참조 [모니터링 및 튜닝 항목](#performance-reference) 데이터 저장소에 관련 되어 있습니다. 이러한 항목에서 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하고 처리량을 최대화하는 방법을 파악할 수 있습니다.

* Azure SQL Data Warehouse로 데이터 저장소에서 데이터를 복사 하는 경우에 성능을 향상 시키기 위해 PolyBase를 사용 하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* HDFS에서 Azure Blob storage 또는 Azure Data Lake Store로 데이터를 복사 하는 경우에 성능을 향상 시키기 위해 DistCp를 사용 하는 것이 좋습니다. 자세한 내용은 [HDFS에서 데이터 복사를 사용 하 여 DistCp](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)합니다.
* Azure SQL Data Warehouse, Azure Blob storage 또는 Azure Data Lake Store를 Redshift에서 데이터를 복사 하는 경우에 UNLOAD를 사용 하 여 성능을 향상 시키기 위해 하는 것이 좋습니다. 자세한 내용은 [Amazon Redshift에서 데이터를 복사할 UNLOAD를 사용 하 여](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)입니다.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **복사 동작**: 복사 활동에 세 가지 옵션을 통해 다른 파일 기반 데이터 저장소에서 데이터를 복사 하는 경우는 **copyBehavior** 속성입니다. 계층 구조를 유지하고 평면화하며 파일을 병합합니다. 계층 구조를 유지 또는 평면화하는 작업은 성능 오버 헤드가 거의 또는 전혀 발생하지 않는 반면 파일을 병합하는 작업은 성능 오버 헤드가 증가합니다.
* **파일 형식 및 압축**: 성능을 향상하는 다양한 방법은 [직렬화 및 역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **복사 동작 및 성능 제한적인**: SQL 싱크에 데이터를 작성 하는 방법은 여러 가지입니다. 자세히 알아보세요 [모범 사례를 Azure SQL Database로 데이터를 로드 하기 위한](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)합니다.

* **데이터 패턴 및 배치 크기**:
  * 테이블 스키마는 복사본 처리량에 영향을 줍니다. 동일한 양의 데이터를 복사하려면 데이터베이스가 데이터에서 적은 배치를 보다 효율적으로 커밋할 수 있기 때문에 행 크기가 크면 행 크기가 작은 경우 보다 더 성능이 나아집니다.
  * 복사 작업의 일괄 처리는 일련의 데이터를 삽입합니다. **writeBatchSize** 속성을 사용하여 배치에서 행 수를 설정할 수 있습니다. 데이터에 작은 크기의 행이 있으면 높은 값을 가진 **writeBatchSize** 속성을 설정하여 적은 수의 배치 오버헤드와 높은 처리량의 혜택을 얻을 수 있습니다. 데이터의 행 크기가 큰 경우 **writeBatchSize**를 늘릴 때 주의하세요. 이 값이 높으면 데이터베이스에 오버로드가 발생하여 복사에 실패할 수 있습니다.

### <a name="nosql-stores"></a>NoSQL 저장소

* **Table Storage**:
  * **파티션**: 인터리브 파티션에 데이터를 작성하면 성능이 크게 저하됩니다. 데이터 삽입 되도록 효율적으로 하나의 파티션으로 다음에 다른 파티션 키로 원본 데이터를 정렬 합니다. 또는 단일 파티션으로 데이터를 작성 하는 논리를 조정할 수 있습니다.

## <a name="considerations-for-serialization-and-deserialization"></a>직렬화 및 역직렬화에 대한 고려 사항

Serialization 및 deserialization에는 입력된 데이터 집합 또는 출력 데이터 집합의 파일인 경우 발생할 수 있습니다. 복사 작업에서 지원 되는 파일 형식에 대 한 자세한 내용은 참조 하세요. [지원 되는 파일 및 압축 형식](supported-file-formats-and-compression-codecs.md)합니다.

**복사 동작**:

* 파일 기반 데이터 저장소 간에 파일 복사:
  * 실행 입력 및 출력 데이터 집합 모두에 있는 경우 동일한 또는 파일 형식 설정이 없을, 데이터 이동 서비스는 *이진 복사* 직렬화 / 역직렬화 없이 합니다. 원본 및 싱크 파일 형식 설정이 서로 다른 시나리오에 비해 더 나은 처리량이 나타납니다.
  * 입력 및 출력 데이터 집합 모두 텍스트 형식이 고 인코딩 형식만 다른 경우 데이터 이동 서비스는 인코딩 변환만 수행 합니다. 이진 복사에 비해 성능 오버헤드를 유발하는 어떠한 직렬화 및 역직렬화도 수행하지 않습니다.
  * 입력 및 출력 데이터 집합 모두에 있는 경우 다른 파일 형식 또는 구분 기호와 같이 구성이 다른, 데이터 이동 서비스가 원본 데이터 스트림, 변환 및 표시 하는 출력 형식으로 serialize 한 다음 deserialize 합니다. 이 작업으로 다른 시나리오에 비해 훨씬 더 심각한 성능 오버헤드가 발생합니다.
* 예를 들어, 관계형 저장소에 파일 기반 저장소에서 기반 파일에 없는 데이터 저장소에서 파일을 복사할 때 serialization 또는 deserialization 단계는 필수입니다. 이 단계로 상당한 성능 오버헤드가 발생합니다.

**파일 형식**: 선택한 파일 형식에 따라 복사 성능이 달라질 수 있습니다. 예를 들어 Avro는 데이터를 사용하여 메타데이터를 저장하는 간단한 이진 형식입니다. 처리 및 쿼리에 대한 Hadoop 에코시스템을 광범위하게 지원합니다. Avro는 serialization 및 deserialization을 수행 하는 텍스트 형식에 비해 복사 처리량이 낮은 경우에 대 한 비용이 더 듭니다. 

파일 형식의 선택은 처리 흐름 전체적으로 수행합니다. 시작:

- 데이터 폼 외부 시스템에서 추출할 또는 원본 데이터 저장소에 저장 됩니다.
- 저장소, 분석 처리 및 쿼리 하는 것에 대 한 최상의 형식입니다.
- 어떤 형식으로 보고 및 시각화 도구에 대 한 데이터 마트에 데이터를 내보내야 합니다.

경우에 따라 읽기 및 쓰기 성능에 대해 최적이 아닌 파일 형식은 전체 분석 프로세스를 고려할 때 적합한 선택일 수 있습니다.

## <a name="considerations-for-compression"></a>압축에 대한 고려 사항

입력 또는 출력 데이터 집합이 파일인 경우에 대상에 데이터를 쓰기 때문에 압축 하거나 압축을 푸는 수행 하려면 복사 작업을 설정할 수 있습니다. 압축을 선택할 때 입력/출력(I/O) 및 CPU 간에 균형을 유지합니다. 계산 리소스에서 데이터를 압축하는 데 추가 비용이 발생합니다. 대신에, 네트워크 I/O 및 저장소는 감소합니다. 데이터에 따라 전체 복사 처리량에서 향상을 볼 수 있습니다.

**코덱**: 압축 코덱에는 각각 장점이 있습니다. 예를 들어 bzip2는 가장 낮은 복사 처리량을 갖지만 처리를 위해 분할될 수 있으므로 bzip2로 최상의 Hive 쿼리 성능을 얻게 됩니다. Gzip는 가장 균형 있는 옵션 및 가장 자주 사용 합니다. 엔드투엔드 시나리오에 가장 적합한 코덱을 선택합니다.

**수준**: 각 압축 코덱의 경우 빠른 압축 및 최적 압축이라는 두 옵션 중에서 선택할 수 있습니다. 결과 파일이 최적으로 압축 되지 않으며 경우에 데이터를 최대한 빨리 압축 하는 가장 빠른 압축된 옵션입니다. 최적으로 압축된 옵션은 압축에 더 많은 시간을 사용하고 최소한의 데이터를 생성합니다. 두 옵션 모두 테스트하여 어떤 옵션이 사용자에게 더 나은 성능을 제공하는지 확인할 수 있습니다.

**고려 사항**: 온-프레미스 저장소와 클라우드 간에 더 많은 데이터를 복사하려면 압축을 활성화한 상태에서 [준비된 복사](#staged-copy)를 사용하는 것이 좋습니다. 중간 저장소를 사용 하 여 회사 네트워크와 Azure 서비스의 대역폭을 제한 하 고 입력된 데이터 집합을 출력 데이터 집합 모두에 압축 되지 않은 형태로 때 유용 합니다.

## <a name="considerations-for-column-mapping"></a>열 매핑에 대한 고려 사항

설정할 수 있습니다 합니다 **columnMappings** 모든 맵 또는 출력 열에 입력된 열의 하위 집합만 복사 활동의 속성입니다. 데이터 이동 서비스는 원본에서 데이터를 읽은 후에 데이터를 싱크에 쓰기 전에 데이터에 열 매핑을 수행해야 합니다. 이 추가 처리는 복사 처리량을 감소시킵니다.

원본 데이터 스토리지를 쿼리할 수 있는 경우 예를 들어 SQL Database 또는 SQL Server와 같은 관계형 스토리지이거나 Table Storage 또는 Azure Cosmos DB와 같은 NoSQL 스토리지인 경우 열 매핑을 사용하는 대신, 열 필터링 및 재정렬 논리를 **query** 속성에 푸시하는 것이 좋습니다. 이러한 방식으로 데이터 이동 서비스는 훨씬 효율적 원본 데이터 저장소에서 데이터를 읽는 동안 프로젝션이 발생 합니다.

자세히 알아보세요 [복사 작업 스키마 매핑](copy-activity-schema-and-type-mapping.md)합니다.

## <a name="other-considerations"></a>기타 고려 사항

복사 하려는 데이터 크기가 큰 경우 추가 데이터를 분할 하도록 비즈니스 논리를 조정할 수 있습니다. 실행 하는 각 복사 작업에 대 한 데이터 크기를 줄이기 위해 더 자주 실행 하려면 복사 작업을 예약할 수 있습니다.

데이터 집합의 수에 대 한 주의 해야 하 고 동시에 동일한 데이터 저장소에 연결할 Azure Data Factory를 필요로 하는 활동을 복사 합니다. 많은 동시 복사 작업은 데이터 저장소를 제한하고 성능 저하, 복사 작업 내부 재시도 및 일부 경우 실행 오류를 발생시킬 수 있습니다.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>샘플 시나리오: 온-프레미스 SQL server에서 Blob storage로 복사

**시나리오**: CSV 형식으로 Blob storage로 온-프레미스 SQL server에서 데이터를 복사할 파이프라인 작성 됩니다. 복사 작업을 더 빠르게 하려면 CSV 파일이 bzip2 형식으로 압축되어야 합니다.

**테스트 및 분석**: 복사 작업의 처리량은 2mbps 보다 적고 성능 벤치 마크 보다 훨씬 더 느립니다.

**성능 분석 및 튜닝**: 성능 문제를 해결하기 위해 데이터가 처리되고 이동되는 방법을 살펴보겠습니다.

- **데이터 읽기**: Integration runtime을 SQL Server 연결을 열고 쿼리를 보냅니다. SQL Server 데이터 스트림을 인트라넷을 통해 integration runtime을 전송 하 여 응답 합니다.
- **데이터 직렬화 및 압축**: Integration runtime 데이터 스트림을 CSV 형식으로 직렬화 하 고 데이터를 bzip2 스트림으로 압축 합니다.
- **데이터 쓰기**: Integration runtime 인터넷을 통해 Blob 저장소로 bzip2 스트림을 업로드합니다.

알 수 있듯이 데이터를 처리 하 고 스트리밍 순차적으로 이동: SQL Server > LAN > 통합 런타임 > WAN > Blob 스토리지 전반적인 성능은 파이프라인을 통해 최소 처리량에 의해 제어 됩니다.

![데이터 흐름](./media/copy-activity-performance/case-study-pic-1.png)

다음 중 하나 이상의 요인으로 성능 병목 현상이 발생할 수 있습니다.

* **원본**: SQL Server 자체가 과도한 로드로 인해 처리량이 낮아집니다.
* **자체 호스팅 통합 런타임**:
  * **LAN**: 통합 런타임이 SQL Server 머신에서 멀리 떨어져 있고 낮은 대역폭 연결을 사용합니다.
  * **통합 런타임**: 통합 런타임은 다음 작업을 수행하는 로드 제한에 도달했습니다.
    * **직렬화**: CSV 형식에 대한 데이터 스트림을 직렬화하면 처리량이 느려집니다.
    * **압축**: Core i7 2.8mbps는 느린 압축 코덱을, 예를 들어 bzip2 선택 합니다.
  * **WAN**: 회사 네트워크와 Azure 서비스 간에 대역폭 부족, 예: T1 = 1,544kbps; T2 = 6,312kbps 합니다.
* **싱크**: Blob 스토리지의 처리량이 낮습니다. 이 시나리오는 해당 서비스 수준 계약 (SLA)을 최소 60mbps를 보장 하므로 가능성이 없습니다.

이 경우 bzip2 데이터 압축은 전체 파이프라인을 느리게 만들 수 있습니다. gzip 압축 코덱을 전환하면 병목 상태를 완화할 수 있습니다.

## <a name="references"></a>참조

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Blob storage 및 Table storage를 포함 하는 azure Storage: [Azure Storage 확장성 대상](../storage/common/storage-scalability-targets.md) 하 고 [Azure Storage 성능 및 확장성 검사 목록](../storage/common/storage-performance-checklist.md)합니다.
* Azure SQL Database: 할 수 있습니다 [성능을 모니터링](../sql-database/sql-database-single-database-monitor.md) 데이터베이스 트랜잭션 단위 (DTU) 비율을 확인 합니다.
* Azure SQL Data Warehouse: 해당 기능은 Dwu (데이터 웨어하우스 단위) 단위로 측정 됩니다. 참조 [(개요) Azure SQL Data Warehouse의 계산 능력 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)합니다.
* Azure Cosmos DB는 [Azure Cosmos DB의 성능 수준](../cosmos-db/performance-levels.md)합니다.
* 온-프레미스 SQL Server: [모니터링 및 성능 튜닝](https://msdn.microsoft.com/library/ms189081.aspx)합니다.
* 온-프레미스 파일 서버: [파일 서버를 위한 성능 튜닝](https://msdn.microsoft.com/library/dn567661.aspx)합니다.

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조 하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 스키마 매핑](copy-activity-schema-and-type-mapping.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)
