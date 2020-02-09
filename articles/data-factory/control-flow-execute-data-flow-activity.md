---
title: 데이터 흐름 작업
description: 데이터 팩터리 파이프라인 내에서 데이터 흐름을 실행 하는 방법입니다.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 01/02/2020
ms.openlocfilehash: d0b9c59852175b91b4bf799a366ae5124fa0ae42
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644796"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory의 데이터 흐름 작업

데이터 흐름 작업을 사용 하 여 데이터 흐름 매핑을 통해 데이터를 변환 하 고 이동 합니다. 데이터 흐름을 처음 접하는 경우 [데이터 흐름 매핑 개요](concepts-data-flow-overview.md) 를 참조 하세요.

## <a name="syntax"></a>구문

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
데이터 | 실행 되는 데이터 흐름에 대한 참조입니다. | DataFlowReference | 예
integrationRuntime | 데이터 흐름이 실행 되는 계산 환경입니다. 지정 하지 않으면 자동 확인 Azure 통합 런타임이 사용 됩니다. | IntegrationRuntimeReference | 아닙니다.
compute. coreCount | Spark 클러스터에서 사용 되는 코어 수입니다. Azure Integration runtime 자동 확인이 사용 되는 경우에만 지정할 수 있습니다. | 8, 16, 32, 48, 80, 144, 272 | 아닙니다.
계산. | Spark 클러스터에서 사용 되는 계산의 유형입니다. Azure Integration runtime 자동 확인이 사용 되는 경우에만 지정할 수 있습니다. | "일반", "서는 E최적화 됨", "MemoryOptimized" | 아닙니다.
linkedService | SQL DW 원본 또는 싱크를 사용 하는 경우 PolyBase 스테이징에 사용 되는 저장소 계정 | LinkedServiceReference | 데이터 흐름이 SQL DW를 읽거나 쓰는 경우에만
스테이징. folderPath | SQL DW 원본 또는 싱크를 사용 하는 경우 PolyBase 스테이징에 사용 되는 blob 저장소 계정의 폴더 경로 | String | 데이터 흐름이 SQL DW를 읽거나 쓰는 경우에만

![데이터 흐름 실행](media/data-flow/activity-data-flow.png "데이터 흐름 실행")

### <a name="data-flow-integration-runtime"></a>데이터 흐름 통합 런타임

데이터 흐름 활동 실행에 사용할 Integration Runtime를 선택 합니다. 기본적으로 Data Factory은 4 개의 작업자 코어와 함께 Azure Integration runtime 자동 확인을 사용 하 고 TTL (time to live)은 사용 하지 않습니다. 이 IR은 범용 계산 형식이 며 팩터리와 동일한 지역에서 실행 됩니다. 데이터 흐름 활동 실행에 대한 특정 지역, 계산 유형, 코어 수 및 TTL을 정의 하는 고유한 Azure 통합 런타임을 만들 수 있습니다.

파이프라인 실행의 경우 클러스터는 작업 클러스터로, 실행이 시작 되기 전에 몇 분 정도 걸릴 수 있습니다. TTL을 지정 하지 않으면 모든 파이프라인 실행에이 시작 시간이 필요 합니다. TTL을 지정 하면 웜 클러스터 풀은 마지막 실행 후에 지정 된 시간 동안 활성 상태를 유지 하므로 시작 시간이 짧아집니다. 예를 들어 TTL이 60 분이 고 한 시간에 한 번씩 데이터 흐름을 실행 하는 경우 클러스터 풀은 활성 상태로 유지 됩니다. 자세한 내용은 [Azure integration runtime](concepts-integration-runtime.md)을 참조 하세요.

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> 데이터 흐름 작업의 Integration Runtime 선택은 파이프라인의 *트리거된 실행* 에만 적용 됩니다. 데이터 흐름을 사용 하 여 파이프라인을 디버깅 하면 디버그 세션에 지정 된 클러스터에서 실행 됩니다.

### <a name="polybase"></a>PolyBase

Azure SQL Data Warehouse를 싱크 또는 원본으로 사용 하는 경우 PolyBase 일괄 처리 로드를 위한 스테이징 위치를 선택 해야 합니다. PolyBase를 사용 하면 데이터를 행 단위로 로드 하는 대신 일괄 처리를 대량으로 로드할 수 있습니다. PolyBase는 로드 시간을 SQL DW로 대폭 줄입니다.

## <a name="parameterizing-data-flows"></a>데이터 흐름 매개 변수화

### <a name="parameterized-datasets"></a>매개 변수가 있는 데이터 집합

데이터 흐름에서 매개 변수가 있는 데이터 집합을 사용 하는 경우 **설정** 탭에서 매개 변수 값을 설정 합니다.

![데이터 흐름 매개 변수 실행](media/data-flow/params.png "매개 변수")

### <a name="parameterized-data-flows"></a>매개 변수가 있는 데이터 흐름

데이터 흐름이 매개 변수화 된 경우 **매개 변수** 탭에서 데이터 흐름 매개 변수의 동적 값을 설정 합니다. ADF 파이프라인 식 언어 (문자열 형식에만 해당) 또는 데이터 흐름 식 언어를 사용 하 여 동적 또는 리터럴 매개 변수 값을 할당할 수 있습니다. 자세한 내용은 [데이터 흐름 매개 변수](parameters-data-flow.md)를 참조 하세요.

![데이터 흐름 매개 변수 실행 예제](media/data-flow/parameter-example.png "매개 변수 예")

### <a name="parameterized-compute-properties"></a>매개 변수가 있는 계산 속성

Azure Integration runtime 자동 확인을 사용 하 고 compute. coreCount 및 compute. 계산 Etype의 값을 지정 하는 경우 코어 수 또는 계산 형식을 매개 변수화 할 수 있습니다.

![데이터 흐름 매개 변수 실행 예제](media/data-flow/parameterize-compute.png "매개 변수 예")

## <a name="pipeline-debug-of-data-flow-activity"></a>데이터 흐름 작업의 파이프라인 디버그

데이터 흐름 작업을 사용 하 여 디버그 파이프라인 실행을 실행 하려면 위쪽 표시줄의 **데이터 흐름 디버그** 슬라이더를 통해 데이터 흐름 디버그 모드를 전환 해야 합니다. 디버그 모드를 사용 하면 활성 Spark 클러스터에 대해 데이터 흐름을 실행할 수 있습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조 하세요.

![디버그 단추](media/data-flow/debugbutton.png "디버깅 단추")

디버그 파이프라인은 데이터 흐름 작업 설정에 지정 된 통합 런타임 환경이 아닌 활성 디버그 클러스터에 대해 실행 됩니다. 디버그 모드를 시작할 때 디버그 계산 환경을 선택할 수 있습니다.

## <a name="monitoring-the-data-flow-activity"></a>데이터 흐름 작업 모니터링

데이터 흐름 작업은 분할, 단계 시간 및 데이터 계보 정보를 볼 수 있는 특별 한 모니터링 환경을 포함 합니다. **작업**아래의 안경 아이콘을 통해 모니터링 창을 엽니다. 자세한 내용은 [데이터 흐름 모니터링](concepts-data-flow-monitoring.md)을 참조 하세요.

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>후속 작업에서 데이터 흐름 활동 결과 사용

데이터 흐름 작업은 각 싱크에 쓰여진 행 수와 각 원본에서 읽은 행에 대한 메트릭을 출력 합니다. 이러한 결과는 활동 실행 결과의 `output` 섹션에서 반환 됩니다. 반환 되는 메트릭은 아래 json의 형식으로 되어 있습니다.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

예를 들어 이름이 ' dataflowActivity ' 인 활동에서 ' sink1 ' 라는 싱크에 기록 된 행 수를 가져오려면 `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`를 사용 합니다.

해당 싱크에 사용 된 ' source1 ' 라는 소스에서 읽은 행 수를 가져오려면 `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`를 사용 합니다.

> [!NOTE]
> 싱크에 기록 된 행이 없으면 메트릭에 표시 되지 않습니다. `contains` 함수를 사용 하 여 존재 여부를 확인할 수 있습니다. 예를 들어 `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`는 sink1에 기록 된 행이 있는지 여부를 확인 합니다.

## <a name="next-steps"></a>다음 단계

Data Factory에서 지 원하는 제어 흐름 작업을 참조 하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
