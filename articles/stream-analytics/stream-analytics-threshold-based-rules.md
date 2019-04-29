---
title: Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙 처리
description: 이 문서에서는 Azure Stream Analytics를 사용하여 Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙이 있는 경고 솔루션을 얻는 방법을 설명합니다.
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 08/20/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761729"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙 처리
이 문서에서는 Azure Stream Analytics를 사용하여 Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙을 사용하는 경고 솔루션을 얻는 방법을 설명합니다.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>시나리오: 조정 가능한 규칙 임계값 기반 경고
들어오는 스트리밍 이벤트가 특정 값에 도달했거나, 들어오는 스트리밍 이벤트를 기준으로 하는 집계 값이 특정 임계값을 초과할 경우 출력으로 경고를 생성해야 할 수 있습니다. 고정되고 미리 결정된 정적 임계값에 값을 비교하는 Stream Analytics 쿼리는 간단히 설정할 수 있습니다. 고정 임계값은 단순 숫자 비교(보다 큼, 보다 작음 및 같음)를 사용하여 스트리밍 쿼리 구문으로 하드 코드될 수 있습니다.

어떤 경우에는 임계값이 변경될 때마다 쿼리 구문을 편집하지 않아도 되도록, 임계값을 구성하는 것이 더 쉬워야 할 수 있습니다. 각 종류의 디바이스에서 서로 다른 임계값을 갖는 같은 쿼리로 다양한 디바이스 또는 사용자를 처리해야 하는 경우도 있습니다. 

이 패턴은 임계값을 동적으로 구성하고, 입력 데이터를 필터링하여 임계값이 적용되는 하는 디바이스 종류를 선택적으로 고르고, 출력에 포함할 필드를 선택적으로 고르는 데 사용할 수 있습니다.

## <a name="recommended-design-pattern"></a>권장되는 디자인 패턴
Stream Analytics 작업에 대한 참조 데이터 입력을 사용하여 경고 임계값을 조회합니다.
- 참조 데이터에 임계값을 키당 값 하나씩 저장합니다.
- 참조 데이터에 대한 스트리밍 데이터 입력 이벤트를 키 열에 조인합니다.
- 참조 데이터의 키 지정 값을 임계값으로 사용합니다.

## <a name="example-data-and-query"></a>예제 데이터 및 쿼리
이 예제에서는 1분 기간 동안의 데이터 스트리밍 집계가 참조 데이터로 제공된 규칙의 규정된 값과 일치할 때 경고가 생성됩니다.

쿼리에서 각 deviceId 및 deviceId 아래의 각 metricName에 대해 GROUP BY로 0~5 차원을 구성할 수 있습니다. 해당하는 필터 값이 있는 이벤트만 그룹화됩니다. 일단 그룹화되면 Min, Max, Avg의 기간 이동 집계가 60초 연속 기간에 대해 계산됩니다. 그런 후 집계된 값에 대한 필터가 참조에 구성된 임계값에 따라 계산되어 경고 출력 이벤트를 생성합니다.

예를 들어, **rules**라는 참조 데이터 입력과, **metrics**라는 스트리밍 데이터 입력이 있는 Stream Analytics 작업이 있다고 가정합니다. 

## <a name="reference-data"></a>참조 데이터
이 예제에서 참조 데이터는 임계값 기반 규칙이 표시되는 방법을 보여줍니다. JSON 파일은 참조 데이터를 포함하고 Azure Blob Storage에 저장되며, 해당 Blob Storage **rules**라는 참조 데이터 입력으로 사용됩니다. 스트리밍 작업을 중지하거나 시작하지 않고도, 시간이 지남에 따라 이 JSON 파일을 덮어쓰고 규칙 구성을 바꿀 수 있습니다.

- 예제 규칙은 CPU가 `90`% 값을 초과할 때(평균보다 크거나 같음) 조정 가능한 경고를 나타내는 데 사용됩니다. `value` 필드는 필요에 따라 구성할 수 있습니다.
- 규칙에는 나중에 `AVGGREATEROREQUAL`의 쿼리 구문에서 동적으로 해석되는 **연산자** 필드가 있습니다. 
- 규칙은 `C1` 값을 갖는 특정 차원 키 `2`의 데이터를 필터링합니다. 다른 필드는 빈 문자열로, 이벤트 필드를 기준으로 입력 스트림을 필터링하지 않을 것임을 나타냅니다. 필요에 따라 일치하는 다른 필드를 필터링하도록 추가 CPU 규칙을 설정할 수 있습니다.
- 모든 열을 출력 경고 이벤트에 포함해야 하는 것은 아닙니다. 이 경우 스트림에 있는 이벤트 데이터의 필드 번호 2가 정식 출력 이벤트에 포함될 것임을 나타내기 위해 `includedDim` 키 번호 `2`가 `TRUE`로 설정됩니다. 다른 필드는 경고 출력에 포함되지 않지만 필드 목록을 조정할 수 있습니다.

```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>스트리밍 쿼리 예제
이 예제 Stream Analytics 쿼리는 위 예제의 **rules** 참조 데이터를 **metrics**라는 데이터의 입력 스트림에 조인합니다.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>스트리밍 입력 이벤트 데이터 예제
이 예제 JSON 데이터는 위의 스트리밍 쿼리에서 사용되는 **metrics** 입력 데이터를 나타냅니다. 

- 3개의 예제 이벤트가 `T14:50` 값을 갖는 1분의 시간 범위 내에 나열됩니다. 
- 3개의 이벤트 모두 동일한 `deviceId` 값 `978648`을 갖습니다.
- 각 이벤트에서 CPU 메트릭 값은 `98`, `95`, `80`으로 각각 다릅니다. 처음 2개의 예제 이벤트만 규칙에 설정된 CPU 경고 규칙을 초과합니다.
- 경고 규칙의 includeDim 필드는 키 번호 2였습니다. 예제 이벤트의 해당 키 2 필드 이름은 `NodeName`입니다. 3개의 예제 이벤트 값은 각각 `N024`, `N024` 및 `N014`입니다. 노드 `N024`가 높은 CPU에 대한 경고 조건에 맞는 유일한 데이터이므로 출력에 이 노드만 표시됩니다. `N014`는 높은 CPU 임계값을 충족하지 않습니다.
- 경고 규칙은 샘플 이벤트에서 `cluster` 필드에 해당하는 키 번호 2의 `filter`로만 구성됩니다. 3개의 예제 이벤트 모두 `C1` 값을 가지며 필터 조건과 일치합니다.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>예제 출력
이 예제 출력 JSON 데이터는 단일 경고 이벤트가 참조 데이터에 정의된 CPU 임계값 규칙을 기준으로 생성되었음을 보여줍니다. 출력 이벤트에는 경고의 이름과 고려되는 필드의 집계값(평균, 최소값, 최대값)이 포함됩니다. 출력 이벤트 데이터는 규칙 구성 때문에 필드 키 번호 2 `NodeName` 값 `N024`가 포함됩니다. (JSON은 가독성을 높이기 위해 줄 바꿈을 표시하도록 변경되었습니다.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
<!--Update_Description: updat meta properties, wording update-->