---
title: JSON Azure Time Series Insights 쿼리 모양에 대 한 모범 사례 | Microsoft Docs
description: JSON을 셰이핑 하 여 Azure Time Series Insights 쿼리 효율성을 향상 시키는 방법에 대해 알아봅니다.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1a7a88e0db38f399dc47c030f3b97f6b26f4da07
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168238"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>Gen1 환경의 쿼리 성능을 최대화 하기 위해 JSON 모양

이 문서에서는 Azure Time Series Insights 쿼리의 효율성을 최대화 하기 위해 JSON 셰이프를 만드는 방법에 대 한 지침을 제공 합니다.

## <a name="video"></a>비디오

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>저장소 요구 사항에 맞게 JSON을 셰이핑 하는 모범 사례를 알아봅니다.</br>

> [!VIDEO <https://www.youtube.com/embed/b2BD5hwbg5I>]

## <a name="best-practices"></a>모범 사례

Azure Time Series Insights로 이벤트를 보내는 방법에 대해 생각해 보십시오. 즉, 항상 다음 작업을 수행 합니다.

1. 네트워크를 통해 최대한 효율적으로 데이터를 보냅니다.
1. 시나리오에 적합 한 집계를 수행할 수 있도록 데이터가 방식으로 저장 되었는지 확인 합니다.
1. Azure Time Series Insights 최대 속성 제한에 도달 하지 않았는지 확인 합니다.
   - S1 환경의 경우 600개의 속성(열)
   - S2 환경의 경우 800개의 속성(열)

다음 지침은 가능한 최상의 쿼리 성능을 보장 하는 데 도움이 됩니다.

1. 태그 ID와 같은 동적 속성을 속성 이름으로 사용 하지 마세요. 이 사용은 최대 속성 한도에 도달 하는 데 기여 합니다.
1. 불필요한 속성을 보내지 않습니다. 쿼리 속성이 필요 하지 않은 경우에는 전송 하지 않는 것이 좋습니다. 이러한 방식으로 저장소 제한을 피할 수 있습니다.
1. [참조 데이터](time-series-insights-add-reference-data-set.md) 를 사용 하 여 네트워크를 통해 정적 데이터를 전송 하지 않도록 합니다.
1. 여러 이벤트 간에 차원 속성을 공유 하 여 네트워크를 통해 데이터를 보다 효율적으로 전송 합니다.
1. 여러 배열 중첩을 사용하지 않습니다. Azure Time Series Insights는 개체를 포함 하는 중첩 배열의 최대 두 수준을 지원 합니다. Azure Time Series Insights는 메시지의 배열을 속성 값 쌍을 사용 하는 여러 이벤트로 평면화 합니다.
1. 대부분 또는 모든 이벤트에 몇 가지 측정값이 존재하는 경우 이러한 측정값을 동일한 개체 내에서 별도 속성으로 전송하는 것이 좋습니다. 이러한 이벤트를 개별적으로 전송 하면 이벤트 수가 감소 하 고 처리 해야 하는 이벤트 수가 줄어들기 때문에 쿼리 성능이 향상 될 수 있습니다. 여러 측정값이 있는 경우 단일 속성에 값으로 보내면 최대 속성 제한에 도달할 가능성이 최소화 됩니다.

## <a name="example-overview"></a>예제 개요

다음 두 예제에서는 이벤트를 전송 하 여 이전 권장 사항을 강조 표시 하는 방법을 보여 줍니다. 각 예제에 따라 권장 사항이 적용 된 방법을 검토할 수 있습니다.

예제는 여러 디바이스가 측정값 또는 신호를 보내는 시나리오를 기반으로 합니다. 측정 또는 신호는 흐름 요금, 엔진 오일 압력, 온도 및 습도가 될 수 있습니다. 첫 번째 예제에는 모든 디바이스에 대한 몇 가지 측정값이 있습니다. 두 번째 예제에는 많은 장치가 있으며 각 장치는 여러 개의 고유한 측정값을 보냅니다.

## <a name="scenario-one-only-a-few-measurements-exist"></a>시나리오 1: 측정값이 몇 개만 있습니다.

> [!TIP]
> 각 측정 또는 신호를 별도의 속성 또는 열로 보내는 것이 좋습니다.

다음 예제에는 외부 배열에 공통 차원 값의 공유 섹션이 포함 된 단일 Azure IoT Hub 메시지가 있습니다. 외부 배열은 참조 데이터를 사용하여 메시지의 효율성을 높입니다. 참조 데이터는 모든 이벤트에 대해 변경 되지 않는 장치 메타 데이터를 포함 하지만 데이터 분석에 대 한 유용한 속성을 제공 합니다. 공통 차원 값을 일괄 처리 하 고 참조 데이터를 사용 하면 네트워크를 통해 전송 되는 바이트에 저장 되므로 메시지 효율성이 높아집니다.

Azure cloud로 전송 될 때 JSON으로 serialize 되는 [IoT 장치 메시지 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) 를 사용 하 여 Azure Time Series Insights GA 환경으로 전송 되는 다음 json 페이로드를 살펴보겠습니다.

```JSON
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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

- 키 속성이 **deviceId**인 참조 데이터 테이블:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

- Azure Time Series Insights 이벤트 테이블, 평면화 후:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]

> - **deviceId** 열은 다수의 다양한 디바이스에서 열 헤더로 사용합니다. **DeviceId** 값을 고유 속성 이름으로 설정 하면 총 장치 수가 595 (S1 환경) 또는 795 (S2 환경)에서 다른 5 개의 열로 제한 됩니다.
> - 불필요 한 속성 (예: 제조업체 및 모델 정보)은 사용 하지 않습니다. 속성은 나중에 쿼리하지 않기 때문에 제거 하면 네트워크 및 저장소 효율성이 향상 됩니다.
> - 참조 데이터는 네트워크를 통해 전송된 바이트 수를 줄이는 데 사용됩니다. Key 속성 **deviceId**를 사용 하 여 두 특성 **MessageId** 및 **devicelocation** 을 조인 합니다. 이 데이터는 수신 시 원격 분석 데이터와 조인 된 다음 쿼리를 위해 Azure Time Series Insights에 저장 됩니다.
> - Azure Time Series Insights에서 지 원하는 최대 중첩 크기 인 두 개의 중첩 계층이 사용 됩니다. 여러 중첩된 배열을 방지해야 합니다.
> - 측정값은 몇 가지 측정값이 있으므로 동일한 개체 내에서 별도의 속성으로 전송 됩니다. 여기에서 **series.Flow Rate psi** 및 **series.Engine Oil Pressure ft3/s**는 고유한 열입니다.

## <a name="scenario-two-several-measures-exist"></a>시나리오 2: 여러 측정값이 존재 합니다.

> [!TIP]
> "Type", "unit" 및 "value" 튜플로 측정을 전송 하는 것이 좋습니다.

예제 JSON 페이로드:

```JSON
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

- 키 속성이 **deviceId** 및 **tagId**인 참조 데이터 테이블:

   | deviceId | series.tagId | messageId | deviceLocation | type | 단위 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

- Azure Time Series Insights 이벤트 테이블, 평면화 후:

   | deviceId | series.tagId | messageId | deviceLocation | type | 단위 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 흐름 속도 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 흐름 속도 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]

> - **DeviceId** 및 **tagId** 열은 제에서 다양 한 장치 및 태그의 열 머리글로 사용 됩니다. 각각을 고유한 특성으로 사용 하 여 쿼리를 594 (S1 환경) 또는 794 (S2 환경)의 6 개 열이 있는 총 장치 수로 제한 합니다.
> - 첫 번째 예제에 명시 된 이유로 불필요 한 속성을 사용할 필요가 없습니다.
> - 참조 데이터는 **messageId** 및 **devicelocation**의 고유 쌍에 사용 되는 **deviceId**를 도입 하 여 네트워크를 통해 전송 되는 바이트 수를 줄이는 데 사용 됩니다. 복합 키 tagId는 **형식** 및 **단위의**고유 쌍에 사용 됩니다 **.** 복합 키를 사용 하면 **deviceId** 및 **tagId** 쌍을 사용 하 여 **messageId, devicelocation, type** 및 **unit**의 네 가지 값을 참조할 수 있습니다. 이 데이터는 수신 시 원격 분석 데이터와 조인 됩니다. 그런 다음 쿼리를 위해 Azure Time Series Insights에 저장 됩니다.
> - 첫 번째 예제에 명시 된 이유로 두 개의 중첩 계층이 사용 됩니다.

### <a name="for-both-scenarios"></a>두 시나리오 모두의 경우

가능한 값이 많은 속성의 경우에는 각 값에 대해 새 열을 만드는 대신 단일 열 내에서 고유한 값으로 보내는 것이 가장 좋습니다. 앞의 두 예제에서:

- 첫 번째 예제에서는 몇 가지 속성에 여러 값이 있으므로 별도의 속성을 설정 하는 것이 적절 합니다.
- 두 번째 예제에서는 측정값이 개별 속성으로 지정 되지 않습니다. 대신, 공통 계열 속성 아래에 있는 값 또는 측정값의 배열입니다. 새 키 **tagId** 가 전송 되며,이는 평면화 된 테이블에 새 열 **tagId** 를 만듭니다. 새 속성 **유형** 및 **단위** 는 속성 제한에 도달 하지 않도록 참조 데이터를 사용 하 여 생성 됩니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [클라우드로 IoT Hub 장치 메시지 보내기를](../iot-hub/iot-hub-devguide-messages-construct.md)참조 하세요.

- Azure Time Series Insights 데이터 액세스 REST API의 쿼리 구문에 대 한 자세한 내용은 [쿼리 구문 Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-syntax) 를 참조 하세요.

- [이벤트를 셰이핑 하는 방법을](./time-series-insights-send-events.md)알아봅니다.
