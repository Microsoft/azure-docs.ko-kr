---
title: 복사 작업 성능 및 확장성 가이드
description: 복사 작업을 사용할 때 Azure Data Factory에서 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 28d0da369083d75bc175111d808828e186a366fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444128"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>복사 작업 성능 및 확장성 가이드

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)

빅 데이터 분석을 위해 data lake 또는 EDW (엔터프라이즈 데이터 웨어하우스)에서 대규모 데이터 마이그레이션을 수행할지 또는 여러 원본에서 Azure로 대규모로 데이터를 수집 하려는 경우에는 최적의 성능을 구현 하는 것이 중요 합니다. 향상.  Azure Data Factory은 데이터를 대규모로 수집할 수 있는 성능, 복원 력 및 비용 효율적인 메커니즘을 제공 하 여 데이터 엔지니어가 뛰어난 성능 및 확장성이 뛰어난 데이터 수집 파이프라인을 구축 하는 데 적합 합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- 데이터 마이그레이션 및 데이터 수집 시나리오에서 ADF 복사 작업을 사용 하 여 어떤 수준의 성능 및 확장성을 달성할 수 있나요?

- ADF 복사 작업의 성능을 조정 하기 위해 수행 해야 하는 단계는 무엇 인가요?
- 단일 복사 작업 실행의 성능을 최적화 하기 위해 활용할 수 있는 ADF 성능 최적화 노브 무엇 인가요?
- ADF 외부에서 복사 성능을 최적화할 때 고려할 다른 요소는 무엇 인가요?

> [!NOTE]
> 일반적으로 복사 작업에 익숙하지 않은 경우이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md) 를 참조 하세요.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF를 사용 하 여 달성 가능한 복사 성능 및 확장성

ADF는 서로 다른 수준에서 병렬 처리를 허용 하는 서버 리스 아키텍처를 제공 합니다 .이 아키텍처를 통해 개발자는 네트워크 대역폭과 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다.  즉, 원본 및 대상 사이에서 원본 데이터 저장소, 대상 데이터 저장소 및 네트워크 대역폭에서 제공 되는 최소 처리량을 측정 하 여 달성할 수 있는 처리량을 예상할 수 있습니다.  아래 표에서는 사용자 환경에 대한 데이터 크기 및 대역폭 제한에 따라 복사 기간을 계산 합니다. 

| 데이터 크기/ <br/> bandwidth | 50Mbps    | 100Mbps  | 500Mbps  | 1Gbps   | 5Gbps   | 10Gbps  | 50Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1GB**                    | 2.7 분    | 1.4 분   | 0.3 분   | 0.1 분  | 0.03 분 | 0.01 분 | 0.0 분   |
| **10gb**                   | 27.3 분   | 13.7 분  | 2.7 분   | 1.3 분  | 0.3 분  | 0.1 분  | 0.03 분  |
| **100 GB**                  | 4.6 시간    | 2.3 시간   | 0.5 시간   | 0.2 시간  | 0.05 시간 | 0.02 시간 | 0.0 시간   |
| **1 TB**                    | 46.6 시간   | 23.3 시간  | 4.7 시간   | 2.3 시간  | 0.5 시간  | 0.2 시간  | 0.05 시간  |
| **10TB**                   | 19.4 일  | 9.7 일  | 1.9 일  | 0.9 일 | 0.2 일 | 0.1 일 | 0.02 일 |
| **100 TB**                  | 194.2 일 | 97.1 일 | 19.4 일 | 9.7 일 | 1.9 일 | 1 일   | 0.2 일  |
| **1 PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10gb**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |

ADF 복사본은 다양 한 수준에서 확장 가능 합니다.

![ADF 복사본 크기 조정 방법](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 제어 흐름은 [For each 루프](control-flow-for-each-activity.md)를 사용 하는 등의 여러 복사 작업을 병렬로 시작할 수 있습니다.
- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. Azure Integration Runtime를 사용 하는 경우 서버를 사용 하지 않는 방식으로 각 복사 작업에 대해 [최대 256 DIUs를](#data-integration-units) 지정할 수 있습니다. 자체 호스트 된 Integration Runtime를 사용 하는 경우 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](create-self-hosted-integration-runtime.md#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다.
- 단일 복사 작업은 여러 스레드를 [병렬로](#parallel-copy)사용 하 여 데이터 저장소에서 읽고 씁니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

이러한 단계를 수행 하 여 복사 작업으로 Azure Data Factory 서비스의 성능을 조정 합니다.

1. **테스트 데이터 집합을 선택 하 고 기준선을 설정 합니다.** 개발 단계에서 대표 데이터 샘플에 대한 복사 작업을 사용 하 여 파이프라인을 테스트 합니다. 선택한 데이터 집합은 일반적인 데이터 패턴 (폴더 구조, 파일 패턴, 데이터 스키마 등)을 나타내고 복사 성능을 평가할 만큼 충분히 큽니다. 예를 들어 복사 작업을 완료 하는 데 10 분 이상 소요 됩니다. [복사 작업 모니터링](copy-activity-overview.md#monitoring)을 수행 하 여 실행 세부 정보 및 성능 특징을 수집 합니다.

2. **단일 복사 작업의 성능을 최대화 하는 방법**:

   먼저 단일 복사 작업을 사용 하 여 성능을 최대화 하는 것이 좋습니다.

   **복사 작업이 Azure Integration Runtime에서 실행 되는 경우:**

   [DIU (데이터 통합 단위](#data-integration-units) ) 및 [병렬 복사](#parallel-copy) 설정에 대한 기본값을 사용 하 여 시작 합니다.  성능 테스트 실행을 수행 하 고, DIUs 및 parallel 복사본에 사용 되는 실제 값 뿐만 아니라 성능을 확인 합니다.  사용 되는 실행 결과 및 성능 설정을 수집 하는 방법에 대한 [복사 작업 모니터링](copy-activity-overview.md#monitoring) 을 참조 하세요.

   이제 추가 성능 테스트 실행을 수행 하 고, 각 시간에 DIU 설정 값을 두 배로 설정 합니다.  또는 기본 설정을 사용 하 여 달성 하는 성능이 예상 보다 훨씬 낮은 것으로 생각 되 면 이후 테스트 실행에서 DIU 설정을 보다 크게 늘릴 수 있습니다.

   DIU 설정에 따라 복사 작업은 거의 완전히 정확 하 게 조정 해야 합니다.  DIU 설정을 두 배로 늘리면 처리량 배가 표시 되지 않고 다음 두 가지 현상이 발생할 수 있습니다.

   - 실행 하는 특정 복사 패턴은 더 많은 DIUs를 추가 하는 것을 유용 하 게 사용할 수 없습니다.  더 큰 DIU 값을 지정 했더라도 실제 DIU는 동일 하 게 유지 되므로 이전과 동일한 처리량을 얻을 수 있습니다.  이 경우 3 단계를 동시에 참조 하는 여러 복사본을 실행 하 여 집계 처리량을 최대화 합니다.
   - 더 많은 DIUs (더 많은 마 력)를 추가 하 여 데이터 추출, 전송 및 로드 속도가 더 높아질 수 있습니다. 원본 데이터 저장소, 간의 네트워크 또는 대상 데이터 저장소는 병목 상태에 도달 했을 수 있으며 제한 될 수 있습니다.  이 경우 데이터 저장소 관리자 또는 네트워크 관리자에 게 문의 하 여 상한 값을 높이 거 나 또는 조정을 중지할 때까지 DIU 설정을 줄이십시오.

   **복사 작업이 자체 호스트 된 Integration Runtime에서 실행 되는 경우:**

   Integration runtime을 호스트 하기 위해 데이터 저장소를 호스트 하는 서버와 별도의 전용 컴퓨터를 사용 하는 것이 좋습니다.

   [병렬 복사](#parallel-copy) 설정의 기본값을 사용 하 여 시작 하 고 자체 호스팅 IR에 단일 노드를 사용 합니다.  성능 테스트 실행을 수행 하 고 달성 한 성능을 적어 둡니다.

   높은 처리량을 얻으려면 자체 호스팅 IR을 강화 하거나 규모를 확장할 수 있습니다.

   - 자체 호스팅 IR 노드의 CPU 및 사용 가능한 메모리가 완전히 활용 되지 않지만 동시 작업의 실행이 한도에 도달 하는 경우에는 노드에서 실행할 수 있는 동시 작업 수를 늘려서 확장 해야 합니다.  지침은 [여기](create-self-hosted-integration-runtime.md#scale-up) 를 참조 하세요.
   - 반면에 CPU가 자체 호스팅 IR 노드에 있는 경우 또는 사용 가능한 메모리가 부족 한 경우에는 새 노드를 추가 하 여 여러 노드 간에 부하를 확장할 수 있습니다.  지침은 [여기](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 를 참조 하세요.

   자체 호스팅 IR의 용량을 확장 하거나 규모를 확장 하는 동안 성능 테스트 실행을 반복 하 여 처리량이 점차 증가 하는지 확인 합니다.  처리량이 향상 되 면 원본 데이터 저장소, between의 네트워크 또는 대상 데이터 저장소가 병목 상태에 도달 하 여 제한 되기 시작 합니다. 이 경우 데이터 저장소 관리자 또는 네트워크 관리자에 게 문의 하 여 상한을 높이 거 나 또는 자체 호스팅 IR에 대한 이전 크기 조정 설정으로 돌아갑니다. 

3. **여러 복사본을 동시에 실행 하 여 집계 처리량을 최대화 하는 방법:**

   이제 단일 복사 작업의 성능을 최대화 했으므로 (네트워크, 원본 데이터 저장소 및 대상 데이터 저장소) 환경의 처리량 상한을 아직 획득 하지 않은 경우 [For each 루프](control-flow-for-each-activity.md)와 같은 ADF 제어 흐름 구문을 사용 하 여 여러 개의 복사 작업을 병렬로 실행할 수 있습니다.

4. **성능 튜닝 팁 및 최적화 기능.** 경우에 따라 복사 작업을 Azure Data Factory 실행 하면 다음 예제와 같이 [복사 작업 모니터링](copy-activity-overview.md#monitor-visually)위에 "성능 튜닝 팁" 메시지가 표시 됩니다. 메시지는 지정 된 복사 실행에 대해 확인 된 병목 상태를 알려 줍니다. 또한 복사 처리량을 향상 시키기 위해 변경 해야 하는 사항에 대해 안내 합니다. 성능 튜닝 팁은 현재 다음과 같은 제안 사항을 제공 합니다.

   - 데이터를 Azure SQL Data Warehouse에 복사할 때 PolyBase를 사용 합니다.
   - 데이터 저장소 쪽의 리소스가 병목 상태일 때 Azure Cosmos DB 요청 단위 또는 Azure SQL Database Dtu (데이터베이스 처리량 단위)를 늘립니다.
   - 불필요 하 게 준비 된 복사본을 제거 합니다.

   성능 튜닝 규칙도 점진적으로 보강됩니다.

   **예: 성능 튜닝 팁을 사용 하 여 Azure SQL Database에 복사**

   이 샘플에서 복사를 실행 하는 동안 싱크 Azure SQL Database 높은 DTU 사용률에 도달 하 여 쓰기 작업의 속도를 저하 시키는 Azure Data Factory 합니다. 더 많은 Dtu를 사용 하 여 Azure SQL Database 계층을 늘리는 것이 좋습니다. 

   ![성능 튜닝 팁을 사용한 복사 모니터링](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   또한 다음과 같은 몇 가지 성능 최적화 기능을 알고 있어야 합니다.

   - [병렬 복사](#parallel-copy)
   - [데이터 통합 단위](#data-integration-units)
   - [준비된 복사](#staged-copy)
   - [자체 호스팅 통합 런타임 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **전체 데이터 집합에 대한 구성을 확장 합니다.** 실행 결과 및 성능에 만족 하는 경우 정의 및 파이프라인을 확장 하 여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="copy-performance-optimization-features"></a>성능 최적화 기능 복사

Azure Data Factory는 다음과 같은 성능 최적화 기능을 제공 합니다.

- [병렬 복사](#parallel-copy)
- [데이터 통합 단위](#data-integration-units)
- [준비된 복사](#staged-copy)

### <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory의 단일 단위에 대한 전원 (CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 척도입니다. 데이터 통합 단위는 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용 되 고 [자체 호스팅 통합 런타임에](concepts-integration-runtime.md#self-hosted-integration-runtime)는 적용 되지 않습니다.

**사용 된 DIUs \* 복사 기간 \* 단가/DIUS-시간에 대한**요금이 청구 됩니다. [여기](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)에서 현재 가격을 참조 하세요. 로컬 통화 및 별도의 크기 구독 유형별로 적용 될 수 있습니다.

복사 작업 실행을 강화 하는 데 허용 되는 DIUs는 **2에서 256 사이**입니다. 지정 하지 않거나 UI에서 "Auto"를 선택 하면 소스 싱크 쌍 및 데이터 패턴에 따라 최적의 DIU 설정을 동적으로 적용할 Data Factory. 다음 표에서는 다양 한 복사 시나리오에서 사용 되는 기본 DIUs를 나열 합니다.

| 복사 시나리오 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |
| 파일 기반 저장소 간의 데이터 복사 | 파일의 수와 크기에 따라 4에서 32 사이 |
| Azure SQL Database 또는 Azure Cosmos DB에 데이터 복사 |싱크 Azure SQL Database의 또는 Cosmos DB 계층 (Dtu/RUs 수)에 따라 4에서 16 사이 |
| 다른 모든 복사 시나리오 | 4 |

이 기본값을 재정의하려면 **dataIntegrationUnits** 속성의 값을 다음과 같이 지정합니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수*는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다.

작업 실행을 모니터링할 때 복사 작업 출력에서 각 복사 실행에 사용 되는 DIUs를 확인할 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-overview.md#monitoring)을 참조 하세요.

> [!NOTE]
> Azure Blob/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud Storage/cloud FTP/cloud SFTP에서 또는 파티션 옵션 사용 클라우드 관계형 데이터 저장소 ( [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)포함)에서 다른 클라우드 데이터 저장소로 여러 파일을 복사 하는 경우에만 현재 4 개 보다 큰 dius 설정이 적용 됩니다.

**예:**

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

### <a name="parallel-copy"></a>병렬 복사

**ParallelCopies** 속성을 사용 하 여 복사 작업에서 사용 하려는 병렬 처리를 나타낼 수 있습니다. 이 속성은 소스에서 읽거나 싱크 데이터 저장소에 동시에 쓸 수 있는 복사 작업 내의 최대 스레드 수로 간주할 수 있습니다.

각 복사 작업 실행에 대해 Azure Data Factory는 데이터를 원본 데이터 저장소에서 대상 데이터 저장소로 복사 하는 데 사용할 병렬 복사본 수를 결정 합니다. 사용 하는 병렬 복사의 기본 수는 사용 중인 원본 및 싱크의 유형에 따라 달라 집니다.

| 복사 시나리오 | 서비스에 의해 결정되는 기본 병렬 복사 개수 |
| --- | --- |
| 파일 기반 저장소 간의 데이터 복사 |는 파일의 크기와 두 클라우드 데이터 저장소 간에 데이터를 복사 하는 데 사용 된 DIUs의 수와 자체 호스팅 통합 런타임 컴퓨터의 물리적 구성에 따라 달라 집니다. |
| 파티션 옵션이 설정 된 관계형 데이터 저장소에서 복사 ( [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [sap 테이블](connector-sap-table.md#sap-table-as-source)및 [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)포함)|4 |
| 모든 원본 저장소에서 Azure Table storage로 데이터 복사 |4 |
| 그 밖의 모든 복사 시나리오 |1 |

> [!TIP]
> 파일 기반 저장소 간에 데이터를 복사 하는 경우 기본 동작은 일반적으로 최상의 처리량을 제공 합니다. 기본 동작은 소스 파일 패턴에 따라 자동으로 결정 됩니다.

데이터 저장소를 호스트 하는 컴퓨터에서 로드를 제어 하거나 복사 성능을 조정 하기 위해 기본값을 재정의 하 고 **parallelCopies** 속성에 대한 값을 지정할 수 있습니다. 값은 1 이상의 정수여야 합니다. 런타임 시 최상의 성능을 위해 복사 작업은 사용자가 설정한 값 보다 작거나 같은 값을 사용 합니다.

**주의할 사항:**

- 파일 기반 저장소 간에 데이터를 복사 하는 경우 **parallelCopies** 는 파일 수준에서 병렬 처리를 결정 합니다. 단일 파일 내의 청크는 자동으로 투명 하 게 발생 합니다. 지정 된 원본 데이터 저장소 형식에 가장 적합 한 청크 크기를 사용 하 여 데이터를 병렬로 로드 하 고 **parallelCopies**에 직교 하도록 디자인 되었습니다. 런타임 시 데이터 이동 서비스에서 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 파일의 수를 넘지 않습니다. 복사 동작이 **Mergefile**인 경우 복사 작업은 파일 수준 병렬 처리를 사용할 수 없습니다.
- 파일 기반이 아닌 저장소 ( [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [sap 테이블](connector-sap-table.md#sap-table-as-source)및 [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) 커넥터를 사용 하는 원본으로 데이터 분할을 사용 하는 원본으로)의 데이터를 파일 기반 저장소로 복사 하는 경우 데이터 이동 서비스는 **parallelCopies** 속성을 무시 합니다. 병렬 처리를 지정하더라도 이 경우에는 적용되지 않습니다.
- **ParallelCopies** 속성은 **dataIntegrationUnits**와 직교 합니다. 전자는 모든 데이터 통합 단위에서 계산됩니다.
- **ParallelCopies** 속성의 값을 지정 하는 경우 원본 및 싱크 데이터 저장소에 대한 부하 증가를 고려해 야 합니다. 또한 하이브리드 복사의 경우와 같이 복사 작업을 통해 권한을 부여 하는 경우 자체 호스팅 통합 런타임에 대한 부하 증가를 고려해 야 합니다. 이러한 부하가 증가 하는 것은 특히 동일한 데이터 저장소에 대해 실행 되는 동일한 활동의 여러 활동 또는 동시 실행이 있는 경우에 발생 합니다. 데이터 저장소 또는 자체 호스팅 통합 런타임이 부하가 많은 경우에는 부하를 완화 하기 위해 **parallelCopies** 값을 줄입니다.

**예:**

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

### <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양 한 데이터 저장소의 데이터를 SQL Data Warehouse으로 수집 하려고 합니다.** SQL Data Warehouse는 많은 양의 데이터를 SQL Data Warehouse에 로드하는 처리량이 높은 메커니즘인 PolyBase를 사용합니다. 원본 데이터는 Blob storage 또는 Azure Data Lake Store에 있어야 하 고 추가 조건을 충족 해야 합니다. Blob Storage 또는 Azure Data Lake Store가 아닌 데이터 저장소에서 데이터를 로드하는 경우 중간 준비 Blob Storage를 통해 데이터 복사를 활성화할 수 있습니다. 이 경우 Azure Data Factory는 필요한 데이터 변환을 수행 하 여 PolyBase의 요구 사항을 충족 하는지 확인 합니다. 그런 다음 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 효과적으로 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
- **저속 네트워크 연결을 통해 하이브리드 데이터 이동 (즉, 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사)을 수행 하는 데 시간이 오래 걸리는 경우가 있습니다.** 성능을 향상 시키려면 준비 된 복사본을 사용 하 여 온-프레미스로 데이터를 압축 하 여 클라우드의 준비 데이터 저장소로 데이터를 이동 하는 데 걸리는 시간을 줄일 수 있습니다. 그런 다음 대상 데이터 저장소에 로드 하기 전에 준비 저장소에서 데이터의 압축을 해제할 수 있습니다.
- **회사 IT 정책 때문에 방화벽에서 포트 80 및 포트 443 이외의 포트를 열지 않으려고 합니다.** 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크에 데이터를 복사할 경우 Windows 방화벽 및 회사 방화벽 모두에 대한 포트 1433에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서 준비 된 복사는 자체 호스팅 integration runtime을 활용 하 여 포트 443에서 HTTP 또는 HTTPS를 통해 Blob storage 준비 인스턴스로 데이터를 먼저 복사할 수 있습니다. 그런 다음 Blob storage 스테이징에서 SQL Database 또는 SQL Data Warehouse에 데이터를 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.

#### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 Blob Storage(직접 준비)로 복사됩니다. 그 다음, 데이터가 준비 데이터 저장소에서 싱크 데이터 저장소로 복사됩니다. Azure Data Factory은 2 단계 흐름을 자동으로 관리 합니다. 데이터 이동이 완료 된 후에도 준비 저장소에서 임시 데이터를 정리 하는 Azure Data Factory.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용 하 여 데이터 이동을 활성화 하는 경우 원본 데이터 저장소에서 중간 또는 준비 데이터 저장소로 데이터를 이동 하기 전에 데이터를 압축할지 여부를 지정 하 고, 중간 또는 준비 dat에서 데이터를 이동 하기 전에 압축을 해제할 수 있습니다. 싱크 데이터 저장소에 대한 저장소입니다.

현재 다른 자체 호스팅 IRs을 통해 연결 된 두 데이터 저장소 간에 데이터를 복사할 수 없습니다. 이러한 시나리오의 경우 원본에서 준비로 복사 하 고 스테이징에서 싱크로 복사 하기 위해 명시적으로 연결 된 복사 활동 두 개를 구성할 수 있습니다.

#### <a name="configuration"></a>구성

복사 작업에서 **Enablestaging** 설정을 구성 하 여 데이터를 대상 데이터 저장소에 로드 하기 전에 Blob 저장소에 준비 해야 하는지 여부를 지정 합니다. **Enablestaging** 을 `TRUE`로 설정 하는 경우 다음 표에 나열 된 추가 속성을 지정 합니다. 또한 준비에 대한 Azure Storage 또는 저장소 공유 액세스 서명 연결 된 서비스가 없는 경우 만들어야 합니다.

| 속성 | Description | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |거짓 |아닙니다. |
| linkedServiceName |중간 준비 저장소로 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 연결 서비스로 지정합니다. <br/><br/> PolyBase를 통해 SQL Data Warehouse에 데이터를 로드 하는 데 공유 액세스 서명이 포함 된 저장소를 사용할 수 없습니다. 다른 모든 시나리오에서는 사용할 수 있습니다. |N/A |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| 경로 |준비 데이터를 포함할 Blob Storage 경로를 지정합니다. 경로를 제공 하지 않으면 서비스에서 임시 데이터를 저장 하는 컨테이너를 만듭니다. <br/><br/> 공유 액세스 서명을 포함한 스토리지를 사용하거나 특정 위치에 임시 데이터가 필요한 경우에만 경로를 지정합니다. |N/A |아닙니다. |
| enableCompression |대상에 복사 하기 전에 데이터를 압축 해야 하는지 여부를 지정 합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |거짓 |아닙니다. |

>[!NOTE]
> 압축을 사용 하는 준비 된 복사를 사용 하는 경우 스테이징 blob 연결 된 서비스에 대한 서비스 주체 또는 MSI 인증이 지원 되지 않습니다.

위의 표에서 설명 하는 속성을 사용 하는 복사 작업의 샘플 정의는 다음과 같습니다.

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

#### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

복사 기간 및 복사 형식의 두 단계를 기준으로 요금이 청구 됩니다.

* 클라우드 복사 중에 준비를 사용 하 여 클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소로 데이터를 복사 하는 경우, 두 단계 모두 Azure integration runtime에 의해 사용 되는 경우 [1 단계 및 2 단계에 대한 복사 기간의 합계 합계] x [클라우드 복사 단가]에 대해 요금이 청구 됩니다.
* 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사 하는 하이브리드 복사 중에 준비를 사용 하는 경우 1 단계는 자체 호스팅 통합 런타임으로 사용 되며, [하이브리드 복사 기간] x [하이브리드 복사 단가] + [클라우드 복사 기간]에 대한 요금이 청구 됩니다. x [클라우드 복사 단가].

## <a name="references"></a>참조

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Azure Blob storage: blob 저장소에 [대한 확장성 및 성능 목표](../storage/blobs/scalability-targets.md) 와 [blob 저장소에 대한 성능 및 확장성 검사 목록](../storage/blobs/storage-performance-checklist.md)입니다.
* Azure 테이블 저장소: 테이블 저장소에 [대한 확장성 및 성능 목표](../storage/tables/scalability-targets.md) 와 [테이블 저장소에 대한 성능 및 확장성 검사 목록](../storage/tables/storage-performance-checklist.md)입니다.
* Azure SQL Database: [성능을 모니터링](../sql-database/sql-database-single-database-monitor.md) 하 고 DTU (데이터베이스 트랜잭션 단위) 비율을 확인할 수 있습니다.
* Azure SQL Data Warehouse: 해당 기능은 DWUs (데이터 웨어하우스 단위)로 측정 됩니다. [Azure SQL Data Warehouse에서 계산 능력 관리 (개요)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조 하세요.
* Azure Cosmos DB: [Azure Cosmos DB의 성능 수준](../cosmos-db/performance-levels.md)입니다.
* 온-프레미스 SQL Server: [성능을 모니터링 하 고 조정](https://msdn.microsoft.com/library/ms189081.aspx)합니다.
* 온-프레미스 파일 서버: [파일 서버에 대한 성능 조정](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조 하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)
- [Amazon s 3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
