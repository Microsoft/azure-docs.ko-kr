---
title: Azure Data Factory의 복사 작업
description: Azure Data Factory의 복사 작업에 대해 알아봅니다. 지원 되는 원본 데이터 저장소에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 데 사용할 수 있습니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: jingwang
ms.openlocfilehash: 0e138e954501df3cf3c3c8819d0198ad9a9288f0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754467"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업

> [!div class="op_single_selector" title1="사용 중인 Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-data-movement-activities.md)
> * [현재 버전](copy-activity-overview.md)

Azure Data Factory에서 복사 작업을 사용 하 여 온-프레미스 및 클라우드에 있는 데이터 저장소 간에 데이터를 복사할 수 있습니다. 데이터를 복사한 후 다른 활동을 사용 하 여 추가 변환 및 분석을 수행할 수 있습니다. 또한 복사 작업을 사용 하 여 BI (비즈니스 인텔리전스) 및 응용 프로그램 사용에 대한 변환 및 분석 결과를 게시할 수 있습니다.

![복사 활동의 역할](media/copy-activity-overview/copy-activity.png)

복사 작업은 [통합 런타임에서](concepts-integration-runtime.md)실행 됩니다. 다양 한 데이터 복사 시나리오에 대해 다양 한 유형의 통합 런타임을 사용할 수 있습니다.

* 모든 IP에서 인터넷을 통해 공개적으로 액세스할 수 있는 두 데이터 저장소 간에 데이터를 복사 하는 경우 복사 작업에 Azure integration runtime을 사용할 수 있습니다. 이 통합 런타임은 안전 하 고 안정적 이며 확장 가능 하며 [전역적으로 사용할 수](concepts-integration-runtime.md#integration-runtime-location)있습니다.
* 온-프레미스에 있는 데이터 저장소에서 데이터를 복사 하는 경우 또는 액세스 제어를 사용 하는 네트워크 (예: Azure virtual network)에서 데이터를 복사 하는 경우 자체 호스팅 통합 런타임을 설정 해야 합니다.

통합 런타임을 각 원본 및 싱크 데이터 저장소와 연결 해야 합니다. 복사 작업에서 사용할 통합 런타임을 결정 하는 방법에 대한 자세한 내용은 [사용할 IR 결정](concepts-integration-runtime.md#determining-which-ir-to-use)을 참조 하세요.

원본에서 싱크로 데이터를 복사 하기 위해 복사 작업을 실행 하는 서비스는 다음 단계를 수행 합니다.

1. 원본 데이터 저장소에서 데이터를 읽습니다.
2. Serialization/deserialization, 압축/압축 해제, 열 매핑 등을 수행 합니다. 입력 데이터 집합, 출력 데이터 집합 및 복사 작업의 구성에 따라 이러한 작업을 수행 합니다.
3. 싱크/대상 데이터 저장소에 데이터를 씁니다.

![복사 작업 개요](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>지원 파일 형식

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

복사 작업을 사용 하 여 두 파일 기반 데이터 저장소 간에 파일을 있는 그대로 복사할 수 있습니다 .이 경우 데이터는 직렬화 나 deserialization 없이 효율적으로 복사 됩니다. 또한 지정 된 형식의 파일을 구문 분석 하거나 생성할 수도 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* 온-프레미스 SQL Server 데이터베이스에서 데이터를 복사 하 고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트 (CSV) 형식의 파일을 복사 하 여 Avro 형식으로 Azure Blob 저장소에 씁니다.
* 온-프레미스 파일 시스템에서 압축 된 파일을 복사 하 여 즉시 압축을 풀고 압축을 푼 파일을 Azure Data Lake Storage Gen2에 기록 합니다.
* Azure Blob storage에서 Gzip CSV (압축 텍스트) 형식으로 데이터를 복사 하 여 Azure SQL Database에 기록 합니다.
* Serialization/deserialization, 압축/압축 해제가 필요한 많은 작업이 있습니다.

## <a name="supported-regions"></a>지원되는 지역

복사 작업을 사용 하도록 설정 하는 서비스는 [Azure integration runtime 위치](concepts-integration-runtime.md#integration-runtime-location)에 나열 된 지역 및 지역에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. 특정 지역에서 Data Factory 및 데이터 이동의 가용성을 확인 하려면 [지역별 제품](https://azure.microsoft.com/regions/#services) 을 참조 하세요.

## <a name="configuration"></a>구성

Azure Data Factory에서 복사 작업을 사용 하려면 다음을 수행 해야 합니다.

1. **원본 데이터 저장소 및 싱크 데이터 저장소에 대한 연결 된 서비스를 만듭니다.** 구성 정보 및 지원 되는 속성은 커넥터 문서의 "연결 된 서비스 속성" 섹션을 참조 하세요. 지원 되는 커넥터 목록은이 문서의 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats) 섹션에서 찾을 수 있습니다.
2. **원본 및 싱크에 대한 데이터 집합을 만듭니다.** 구성 정보 및 지원 되는 속성은 원본 및 싱크 커넥터 문서의 "데이터 집합 속성" 섹션을 참조 하세요.
3. **복사 작업을 사용 하 여 파이프라인을 만듭니다.** 다음 섹션에서 예제를 제공합니다.

### <a name="syntax"></a>구문

복사 작업의 다음 템플릿에는 지원 되는 속성의 전체 목록이 포함 되어 있습니다. 시나리오에 적합한 속성을 지정하세요.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>구문 세부 정보

| 속성 | Description | 필수 여부 |
|:--- |:--- |:--- |
| type | 복사 활동의 경우를 `Copy`으로 설정 합니다. | 예 |
| inputs | 원본 데이터를 가리키는 만든 데이터 집합을 지정 합니다. 복사 작업은 단일 입력만 지원 합니다. | 예 |
| outputs | 싱크 데이터를 가리키는 만든 데이터 집합을 지정 합니다. 복사 작업은 단일 출력만 지원 합니다. | 예 |
| typeProperties | 속성을 지정 하 여 복사 작업을 구성 합니다. | 예 |
| source | 복사 원본 유형 및 데이터 검색을 위한 해당 속성을 지정 합니다.<br/>자세한 내용은 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 나열 된 커넥터 문서의 "복사 작업 속성" 섹션을 참조 하세요. | 예 |
| 싱크 | 데이터를 쓰기 위한 복사 싱크 유형과 해당 속성을 지정 합니다.<br/>자세한 내용은 [지원 되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 나열 된 커넥터 문서의 "복사 작업 속성" 섹션을 참조 하세요. | 예 |
| 번역기 | 원본에서 싱크로의 명시적 열 매핑을 지정합니다. 이 속성은 기본 복사 동작이 사용자의 요구를 충족 하지 않는 경우에 적용 됩니다.<br/>자세한 내용은 [복사 활동의 스키마 매핑](copy-activity-schema-and-type-mapping.md)을 참조 하세요. | 아닙니다. |
| dataIntegrationUnits | [Azure integration runtime](concepts-integration-runtime.md) 에서 데이터 복사에 사용 하는 전력의 양을 나타내는 측정값을 지정 합니다. 이러한 단위는 이전에는 DMU (클라우드 데이터 이동 단위)로 알려져 있었습니다. <br/>자세한 내용은 [데이터 통합 단위](copy-activity-performance.md#data-integration-units)를 참조 하세요. | 아닙니다. |
| parallelCopies | 원본에서 데이터를 읽고 싱크에 데이터를 쓸 때 복사 작업에서 사용할 병렬 처리를 지정 합니다.<br/>자세한 내용은 [병렬 복사](copy-activity-performance.md#parallel-copy)를 참조 하세요. | 아닙니다. |
| 보존 | 데이터를 복사 하는 동안 메타 데이터/Acl을 유지할지 여부를 지정 합니다. <br/>자세한 내용은 [메타 데이터 유지](copy-activity-preserve-metadata.md)를 참조 하세요. |아닙니다. |
| enableStaging<br/>stagingSettings | 원본에서 싱크로 데이터를 직접 복사 하는 대신 Blob 저장소에서 중간 데이터를 준비할 지 여부를 지정 합니다.<br/>유용한 시나리오 및 구성 세부 정보에 대한 자세한 내용은 [준비 된 복사](copy-activity-performance.md#staged-copy)를 참조 하세요. | 아닙니다. |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 원본에서 싱크로 데이터를 복사할 때 호환 되지 않는 행을 처리 하는 방법을 선택 합니다.<br/>자세한 내용은 [내결함성](copy-activity-fault-tolerance.md)을 참조 하세요. | 아닙니다. |

## <a name="monitoring"></a>모니터링

Azure Data Factory **작성자 & 모니터** UI를 통해 또는 프로그래밍 방식으로 복사 작업 실행을 모니터링할 수 있습니다.

### <a name="monitor-visually"></a>시각적으로 모니터링

복사 작업 실행을 시각적으로 모니터링 하려면 데이터 팩터리로 이동한 다음 **작성자 & 모니터로**이동 합니다. **모니터** 탭의 **작업** 열에 **작업 실행 보기** 단추가 있는 파이프라인 실행 목록이 표시 됩니다.

![파이프라인 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

**작업 실행 보기** 를 선택 하 여 파이프라인 실행의 작업 목록을 표시 합니다. **작업** 열에 복사 작업 입력, 출력, 오류 (복사 작업 실행이 실패 한 경우) 및 세부 정보에 대한 링크가 표시 됩니다.

![작업 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

**작업** 열에서 **세부 정보** 단추를 선택 하 여 복사 작업의 실행 세부 정보 및 성능 특성을 확인 합니다. 볼륨/행 수/원본에서 싱크로 복사 된 데이터의 파일 수, 처리량, 복사 작업에서 해당 기간으로 이동 하는 단계, 복사 시나리오에 사용 되는 구성 등의 정보가 표시 됩니다.

>[!TIP]
>일부 시나리오에서는 모니터링 복사 페이지의 맨 위에 **성능 튜닝 팁** 도 표시 됩니다. 이러한 팁은 식별 된 병목 상태에 대한 정보를 제공 하 고 복사 처리량을 향상 시키기 위해 변경 해야 하는 사항을 제공 합니다. 예제는이 문서의 [성능 및 튜닝](#performance-and-tuning) 단원을 참조 하세요.

**예: Amazon s 3에서 Azure Data Lake Store
로 복사** ![작업 실행 세부 정보를 모니터링](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**예: Azure SQL Database에서 준비 된 복사
Azure SQL Data Warehouse으로 복사 하** ![작업 실행 세부 정보를 모니터링](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>프로그래밍 방식으로 모니터링

복사 작업 실행 세부 정보 및 성능 특성은 **복사 작업 실행 결과** > **출력** 섹션에도 반환 됩니다. 다음은 반환 될 수 있는 속성의 전체 목록입니다. 복사 시나리오에 적용 되는 속성만 볼 수 있습니다. 작업 실행을 모니터링 하는 방법에 대한 자세한 내용은 [파이프라인 실행 모니터링](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)을 참조 하세요.

| 속성 이름  | Description | 단위 |
|:--- |:--- |:--- |
| DataRead | 원본에서 읽은 데이터의 양입니다. | Int64 값 (바이트) |
| DataWritten | 싱크에 쓴 데이터의 양입니다. | Int64 값 (바이트) |
| filesRead | 파일 저장소에서 복사 하는 동안 복사 된 파일 수입니다. | Int64 값(단위 없음) |
| filesWritten | 파일 저장소에 복사 하는 동안 복사 된 파일 수입니다. | Int64 값(단위 없음) |
| sourcePeakConnections | 복사 작업을 실행 하는 동안 원본 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| sinkPeakConnections | 복사 작업을 실행 하는 동안 싱크 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| rowsRead | 원본에서 읽은 행 수입니다 (이진 복사에는 적용 되지 않음). | Int64 값(단위 없음) |
| rowsCopied | 싱크로 복사 된 행 수입니다 (이진 복사에는 적용 되지 않음). | Int64 값(단위 없음) |
| rowsSkipped | 건너뛴 호환 되지 않는 행의 수입니다. `enableSkipIncompatibleRow`를 true로 설정 하 여 호환 되지 않는 행을 건너뛰도록 설정할 수 있습니다. | Int64 값(단위 없음) |
| copyDuration | 복사 실행의 지속 시간입니다. | Int32 값 (초) |
| throughput | 데이터 전송 률입니다. | 부동 소수점 수 (KBps) |
| sourcePeakConnections | 복사 작업을 실행 하는 동안 원본 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int32 값 (단위 없음) |
| sinkPeakConnections| 복사 작업을 실행 하는 동안 싱크 데이터 저장소에 설정 된 최대 동시 연결 수입니다.| Int32 값 (단위 없음) |
| sqlDwPolyBase | 데이터를 SQL Data Warehouse에 복사할 때 PolyBase를 사용할지 여부를 지정 합니다. | 부울 |
| redshiftUnload | Redshift에서 데이터를 복사할 때 언로드가 사용 되는지 여부입니다. | 부울 |
| hdfsDistcp | HDFS에서 데이터를 복사할 때 DistCp를 사용할지 여부를 지정 합니다. | 부울 |
| effectiveIntegrationRuntime | 작업을 실행 하는 데 사용 되는 IR (통합 런타임) 또는 런타임이 `<IR name> (<region if it's Azure IR>)`형식으로 되어 있습니다. | 텍스트(문자열) |
| usedDataIntegrationUnits | 복사 중 효율적인 데이터 통합 단위입니다. | Int32 값 |
| usedParallelCopies | 복사 동안 유효한 parallelCopies입니다. | Int32 값 |
| redirectRowPath | `redirectIncompatibleRowSettings` 속성에서 구성 하는 blob 저장소에서 건너뛴 호환 되지 않는 행의 로그 경로입니다. 이 문서의 뒷부분에 나오는 [내결함성](#fault-tolerance) 을 참조 하세요. | 텍스트(문자열) |
| executionDetails | 복사 작업을 수행 하는 단계 및 해당 단계, 기간, 구성 등에 대해 자세히 설명 합니다. 이 섹션은 변경 될 수 있으므로이 섹션을 구문 분석 하지 않는 것이 좋습니다.<br/><br/>Data Factory은 `detailedDurations`에서 다양 한 단계에 소요 된 자세한 기간 (초)도 보고 합니다. 이러한 단계의 기간은 제외 됩니다. 지정 된 복사 작업 실행에 적용 되는 기간만 표시 됩니다.<br/>**큐 기간** (`queuingDuration`): 통합 런타임에 복사 작업을 실제로 시작 하기 전 까지의 시간입니다. 자체 호스팅 IR을 사용 하는 경우이 값이 크면 IR 용량과 사용량을 확인 하 고 워크 로드에 따라 규모를 확장 하거나 축소 합니다. <br/>**복사 전 스크립트 지속 시간** (`preCopyScriptDuration`): 복사 작업이 IR에서 시작 하 고 복사 작업에서 싱크 데이터 저장소의 사전 복사 스크립트 실행을 완료 하는 시점 사이에 경과 된 시간입니다. 사전 복사 스크립트를 구성할 때 적용 됩니다. <br/>**첫 번째 바이트 까지의 시간** (`timeToFirstByte`): 이전 단계의 끝과 IR이 원본 데이터 저장소에서 첫 번째 바이트를 받는 시간 사이에 경과 된 시간입니다. 파일 기반이 아닌 소스에 적용 됩니다. 이 값이 크면 쿼리 또는 서버를 확인 하 고 최적화 합니다.<br/>**전송 기간** (`transferDuration`): 이전 단계의 끝과 IR이 원본에서 싱크로 모든 데이터를 전송 하는 시간 사이에 경과 된 시간입니다. | Array |
| perfRecommendation 사항 | 성능 튜닝 팁을 복사 합니다. 자세한 내용은 [성능 및 튜닝](#performance-and-tuning) 을 참조 하세요. | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="incremental-copy"></a>증분 복사

Data Factory를 사용 하면 원본 데이터 저장소에서 싱크 데이터 저장소로 델타 데이터를 증분 복사할 수 있습니다. 자세한 내용은 [자습서: 데이터 증분 복사](tutorial-incremental-copy-overview.md)를 참조 하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

[복사 작업 성능 및 확장성 가이드](copy-activity-performance.md) 에서는 Azure Data Factory의 복사 작업을 통한 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 설명 합니다. 또한 테스트 중 관찰 된 성능 값을 나열 하 고 복사 작업의 성능을 최적화 하는 방법을 설명 합니다.

일부 시나리오에서는 Data Factory 복사 작업을 실행할 때 다음 예제와 같이 [복사 작업 모니터링 페이지](#monitor-visually)의 맨 위에 **성능 튜닝 팁** 이 표시 됩니다. 이 팁은 지정 된 복사 실행에 대해 식별 된 병목 상태를 알려 줍니다. 또한 복사 처리량을 향상 시키기 위해 변경 해야 하는 사항에 대한 정보도 제공 합니다. 성능 튜닝 팁은 현재 데이터를 Azure SQL Data Warehouse로 복사 하는 경우 PolyBase 사용, 데이터 저장소 쪽의 리소스가 병목 상태인 경우 Azure Cosmos DB RUs 또는 Azure SQL Database Dtu 사용과 같은 제안 사항을 제공 하 고, 제거 하는 등의 제안 사항을 제공 합니다. 불필요 하 게 준비 된 복사본입니다.

**예: 성능 조정 팁을 사용 하 여 Azure SQL Database에 복사**

이 샘플에서 복사를 실행 하는 동안 Data Factory는 싱크 Azure SQL Database에서 높은 DTU 사용률을 추적 합니다. 이 경우 쓰기 작업이 느려집니다. 권장 사항은 Azure SQL Database 계층에서 Dtu를 늘리는 것입니다.

![성능 튜닝 팁을 사용한 복사 모니터링](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="resume-from-last-failed-run"></a>마지막으로 실패 한 실행에서 다시 시작

복사 작업은 파일 기반 저장소 간에 이진 형식을 사용 하 여 큰 파일 크기를 그대로 복사 하 고, Amazon s 3에서 Azure Data Lake Storage Gen2로 데이터를 마이그레이션하기 위해 원본에서 싱크로 폴더/파일 계층 구조를 유지 하도록 선택 하는 경우 마지막으로 실패 한 실행에서 다시 시작을 지원 합니다. 이는 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)및 [SFTP](connector-sftp.md)와 같은 파일 기반 커넥터에 적용 됩니다.

다음 두 가지 방법으로 복사 작업 다시 시작을 활용할 수 있습니다.

- **작업 수준 다시 시도:** 복사 작업에 대해 재시도 횟수를 설정할 수 있습니다. 파이프라인을 실행 하는 동안이 복사 작업이 실행 되지 않으면 다음 자동 다시 시도가 마지막 평가판의 오류 지점에서 시작 됩니다.
- **실패 한 작업에서 다시 실행:** 파이프라인 실행이 완료 된 후 ADF UI 모니터링 보기의 실패 한 작업에서 다시 실행을 트리거하거나 프로그래밍 방식을 실행할 수도 있습니다. 실패 한 작업이 복사 작업 인 경우 파이프라인은이 작업에서 다시 실행 될 뿐만 아니라 이전 실행의 오류 지점에서 다시 시작 합니다.

    ![이력서 복사](media/copy-activity-overview/resume-copy.png)

유의 사항:

- 다시 시작은 파일 수준에서 발생 합니다. 파일을 복사할 때 복사 작업이 실패 하는 경우 다음 실행에서이 특정 파일이 다시 복사 됩니다.
- 다시 시작이 제대로 작동 하려면 다시 실행 간에 복사 작업 설정을 변경 하지 마십시오.
- Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 및 Google Cloud Storage에서 데이터를 복사 하는 경우 복사 작업은 복사 된 파일의 임의 수에서 다시 시작할 수 있습니다. 원본으로 사용 되는 파일 기반 커넥터의 나머지 부분에서는 현재 복사 작업이 제한 된 개수의 파일에서 다시 시작을 지원 합니다 .이는 일반적으로 수십의 범위에 있고 파일 경로의 길이에 따라 달라 집니다. 이 값을 초과 하는 파일은 다시 실행 하는 동안 다시 복사 됩니다.

이진 파일 복사 이외의 다른 시나리오의 경우 복사 작업 다시 실행은 처음부터 시작 됩니다.

## <a name="preserve-metadata-along-with-data"></a>데이터와 함께 메타 데이터 유지

Data lake migration과 같은 시나리오에서 원본에서 싱크로 데이터를 복사 하는 동안 복사 작업을 사용 하 여 데이터와 함께 메타 데이터와 Acl을 유지 하도록 선택할 수도 있습니다. 자세한 내용은 [메타 데이터 유지](copy-activity-preserve-metadata.md) 를 참조 하세요.

## <a name="schema-and-data-type-mapping"></a>스키마 및 데이터 형식 매핑

복사 작업에서 원본 데이터를 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md) 을 참조 하세요.

## <a name="fault-tolerance"></a>내결함성

기본적으로 복사 작업은 원본 데이터 행이 싱크 데이터 행과 호환 되지 않는 경우 데이터 복사를 중지 하 고 오류를 반환 합니다. 복사를 성공적으로 수행 하려면 호환 되지 않는 행을 건너뛰고 기록 하 고 호환 되는 데이터만 복사 하도록 복사 작업을 구성할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](copy-activity-fault-tolerance.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 퀵 스타트, 자습서 및 샘플을 참조하세요.

- [동일한 Azure Blob storage 계정의 한 위치에서 다른 위치로 데이터를 복사 합니다.](quickstart-create-data-factory-dot-net.md)
- [Azure Blob 저장소에서 Azure SQL Database로 데이터 복사](tutorial-copy-data-dot-net.md)
- [온-프레미스 SQL Server 데이터베이스에서 Azure로 데이터 복사](tutorial-hybrid-copy-powershell.md)
