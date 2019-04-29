---
title: Azure Time Series Insights 쿼리에서 JSON을 셰이핑하는 방법 모범 사례 | Microsoft Docs
description: Azure Time Series Insights 쿼리 효율성을 개선하는 방법을 알아봅니다.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 2d42b7ebdee291e7c71351fa2c3a5583a121b79e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760669"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>JSON을 셰이핑하여 쿼리 성능을 극대화하는 방법 

이 아티클에서는 Azure TSI(Time Series Insights) 쿼리의 성능을 최대화하기 위해 JSON을 셰이핑하는 지침을 제공합니다.

## <a name="video"></a>비디오: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>이 비디오에서는 저장소 요구 사항에 맞게 JSON을 구성하는 모범 사례를 설명합니다.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>모범 사례

Time Series Insights에 이벤트를 보내는 방법을 이해해야 합니다. 즉, 다음을 수행해야 합니다.

1. 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
2. 시나리오에 적합한 집계를 수행할 수 있는 방식으로 데이터를 저장하도록 합니다.
3. 다음과 같은 TSI의 최대 속성 제한에 도달하지 않도록 합니다
   - S1 환경의 경우 600개의 속성(열)
   - S2 환경의 경우 800개의 속성(열)

다음 지침을 통해 최적의 쿼리 성능을 보장할 수 있습니다.

1. 최대 속성 한계에 도달하도록 기여하므로 태그 ID 등 동적 속성을 속성 이름으로 사용하지 않습니다.
2. 불필요한 속성을 보내지 않습니다. 쿼리 속성이 필요하지 않으면 해당 속성을 보내지 않고 저장소 제한 사항을 방지하는 것이 좋습니다.
3. 네트워크를 통해 고정 데이터를 전송하지 않도록 방지하기 위해 [참조 데이터](time-series-insights-add-reference-data-set.md)를 사용합니다.
4. 네트워크를 통해 데이터를 보다 효율적으로 보내기 위해 여러 이벤트에서 차원 속성을 공유합니다.
5. 여러 배열 중첩을 사용하지 않습니다. TSI에서는 개체가 포함된 최대 두 개 수준의 중첩된 배열을 지원합니다. TSI은 속성 값 쌍이 있는 여러 이벤트로 메시지의 배열을 평면화합니다.
6. 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 개별적으로 보내면 이벤트의 수를 줄일 수 있고 처리해야 하는 이벤트 수가 적을수록 쿼리 성능이 개선될 수 있습니다. 여러 측정값이 있는 경우 단일 속성의 값으로 보내면 최대 속성 제한에 도달할 가능성을 최소화합니다.

## <a name="examples"></a>예

다음 두 가지 예제에서는 이전의 권장 사항을 강조 표시하기 위해 보내는 이벤트를 보여줍니다. 각 예제를 따라 권장 사항을 적용하는 방법을 볼 수 있습니다.

예제는 여러 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정값 또는 신호는 흐름 속도, 엔진 오일 압력, 온도 및 습도일 수 있습니다. 첫 번째 예제에는 모든 디바이스에 대한 몇 가지 측정값이 있습니다. 두 번째 예제에는 여러 디바이스가 있으며 각각 여러 개의 고유한 값을 보냅니다.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>시나리오: 몇 가지 측정값/신호만이 존재함

**권장 사항:** 측정값을 별도 속성/열로 전송합니다.

다음 예제에는 단일 IoT Hub 메시지가 있습니다. 여기에서 외부 배열에는 일반적인 차원 값의 공유 섹션이 포함됩니다. 외부 배열은 참조 데이터를 사용하여 메시지의 효율성을 높입니다. 참조 데이터에는 모든 이벤트에서 변경되지 않는 디바이스 메타데이터가 포함되지만 데이터 분석에 유용한 속성을 제공합니다. 일반적인 차원 값을 일괄 처리하는 작업 및 참조 데이터를 사용하는 작업은 모두 온라인으로 전송된 바이트를 절약하므로 메시지를 더 효율적으로 만듭니다.

예제 JSON 페이로드:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

참조 데이터 테이블(주요 속성은 deviceId임):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | LINE\_DATA | EU |
| FYYY | LINE\_DATA | US |

Time Series Insights 이벤트 테이블(평면화 이후):

| deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

이전 예제를 따라 다음을 수행합니다.

- **deviceId** 열은 다수의 다양한 디바이스에서 열 헤더로 사용합니다. deviceId 값을 고유한 속성 이름으로 만들려고 시도하면 다른 5개의 열을 포함하여 총 디바이스를 595개(S1 환경) 또는 795개(S2 환경)로 제한합니다.

- 제조업체 및 모델 정보 등 불필요한 속성이 방지됩니다. 해당 속성이 나중에 쿼리될 수 없으므로 제거하여 네트워크 및 저장소 효율성을 개선할 수 있습니다.

- 참조 데이터는 네트워크를 통해 전송된 바이트 수를 줄이는 데 사용됩니다. **messageId** 및 **deviceLocation**이라는 두 개의 특성은 **deviceId**라는 주요 속성을 사용하여 조인됩니다. 이 데이터는 수신 시 원격 분석 데이터와 조인되고 쿼리하기 위해 나중에 TSI에 저장됩니다.

- TSI에서 지원하는 중첩의 최대 크기인 두 개의 중첩 레이어가 사용됩니다. 여러 중첩된 배열을 방지해야 합니다.

- 측정값은 몇 가지 측정값이 있으므로 동일한 개체 내에서 별도의 속성으로 전송됩니다. 여기에서 **series.Flow Rate psi** 및 **series.Engine Oil Pressure ft3/s**는 고유한 열입니다.

### <a name="scenario-several-measures-exist"></a>시나리오: 여러 측정값이 존재함

**권장 사항:** 측정값을 "형식", "단위", "값" 튜플로 보냅니다.

예제 JSON 페이로드:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

참조 데이터(주요 속성은 deviceId series.tagId임):

| deviceId | series.tagId | messageId | deviceLocation | 형식 | 단위 |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
| FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s |
| FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

Time Series Insights 이벤트 테이블(평면화 이후):

| deviceId | series.tagId | messageId | deviceLocation | 형식 | 단위 | timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | Psi | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

이전 예제를 따르면 첫 번째 예제와 비합니다.

- **deviceId** 및 **series.tagId** 열은 다수의 다양한 디바이스 및 태그에 대한 열 헤더로 사용합니다. 각각을 고유 특성으로 사용하면 6개의 열이 포함된 594(S1 환경)개 또는 794(S2 환경)개의 총 디바이스로 쿼리가 제한됩니다.

- 첫 번째 예제에서 설명한 이유로 인해 불필요한 속성이 방지되었습니다.

- 참조 데이터는 **messageId** 및 **deviceLocation**이라는 고유한 쌍에 대해 **deviceId**를 도입하여 네트워크를 통해 전송된 바이트 수를 줄이기 위해 사용됩니다.  **형식** 및 **단위**라는 고유 쌍에 대해 **series.tagId**라는 복합 키를 사용합니다. 복합 키를 사용하면 **messageId, deviceLocation, type** 및 **unit**이라는 4개의 값을 참조하는 데 **deviceId** 및 **series.tagId** 쌍을 사용할 수 있습니다. 이 데이터는 수신 시 원격 분석 데이터와 조인되고 쿼리하기 위해 나중에 TSI에 저장됩니다.

- 첫 번째 예제에서 설명한 이유로 인해 두 개의 중첩 레이어를 사용합니다.

### <a name="for-both-scenarios"></a>두 시나리오 모두의 경우

많은 가능한 값을 포함하는 속성이 있는 경우 각 값에 새 열을 만드는 대신 단일 열 내에서 고유 값으로 보내는 것이 가장 좋습니다. 앞의 두 예제에서:
  - 첫 번째 예제에는 여러 값이 포함된 몇 가지 속성이 있습니다. 따라서 각각 별도 속성으로 만들기에 적합합니다. 
  - 그러나 두 번째 예제에서 측정값이 개별 속성으로 지정되지 않지만 대신 일반적인 일련의 속성에서 값/측정값 배열로 지정된 것을 확인할 수 있습니다. **tagId**라는 새 키가 전송됩니다. 여기서는 일반 테이블에 **series.tagId**라는 새 열을 만듭니다. 참조 데이터를 사용하여 **형식** 및 **단위**라는 새 속성이 만들어지므로 속성 제한에 도달하지 않도록 방지합니다.

## <a name="next-steps"></a>다음 단계

이러한 지침을 사용하려면 [Azure Time Series Insights 쿼리 구문](/rest/api/time-series-insights/ga-query-syntax)을 참조하여 TSI 데이터 액세스 REST API의 쿼리 구문에 대해 자세히 알아봅니다.