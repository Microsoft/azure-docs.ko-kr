---
title: Azure Time Series Insights Gen2의 long 데이터 형식에 대 한 지원 | Microsoft Docs
description: Azure Time Series Insights Gen2의 long 데이터 형식에 대 한 지원.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: dpalled
ms.openlocfilehash: 8d9b166d4ac9290a0389eac23f9d6882f667e1a0
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856096"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2의 long 데이터 형식에 대 한 지원 추가

Long 데이터 형식에 대 한 지원 추가는 Azure Time Series Insights Gen2 환경 에서만 숫자 데이터를 저장 하 고 인덱싱하는 방법에 영향을 줍니다. Gen1 환경이 있는 경우 이러한 변경 내용을 무시할 수 있습니다.

사용자의 지역에 따라 2020 년 6 월 29 일부 터 6 월 30 일까 지 데이터는 **길고** **두 배가**인덱싱됩니다.  이 변경 내용에 대 한 질문이 나 궁금한 사항이 있는 경우 Azure Portal를 통해 지원 티켓을 제출 하 고이 통신을 언급 합니다.

다음의 경우에 영향을 받는 경우 권장 사항을 변경 합니다.

- **사례 1**: 현재 시계열 모델 변수를 사용 하 고 원격 분석 데이터에서 정수 계열 데이터 형식만 보냅니다.
- **사례 2**: 현재 시계열 모델 변수를 사용 하 고 원격 분석 데이터에 정수 계열 및 비정 수 데이터 형식을 모두 보냅니다.
- **사례 3**: 범주 변수를 사용 하 여 정수 값을 범주에 매핑합니다.
- **사례 4**: JavaScript SDK를 사용 하 여 사용자 지정 프런트 엔드 응용 프로그램을 빌드합니다.
- **사례 5**: 웜 저장소에서 1000-속성 이름 제한을 임박 하 고 정수 계열 및 비정 수 데이터를 모두 보냅니다. 속성 수는 [Azure Portal](https://portal.azure.com/)에서 메트릭으로 볼 수 있습니다.

모든 사례가 적용 되는 경우 모델을 변경 합니다. 변수 정의에서 TSX (시계열 식)를 권장 변경 내용으로 업데이트 합니다. 모두 업데이트:

- Azure Time Series Insights TSI 탐색기
- Api를 사용 하는 모든 사용자 지정 클라이언트

IoT 솔루션 및 제약 조건에 따라 Azure Time Series Insights Gen2 환경으로 전송 되는 데이터를 볼 수 없습니다. 데이터가 정수 계열 전용 이거나 정수 계열 및 비정 수 모두 인지 확실 하지 않은 경우 다음과 같은 몇 가지 옵션을 사용할 수 있습니다.

- 기능이 해제 될 때까지 기다릴 수 있습니다. 그런 다음 탐색기 UI에서 원시 이벤트를 탐색 하 여 두 개의 개별 열에 저장 되는 속성을 파악 합니다.
- 모든 숫자 태그에 대해 권장 되는 변경 작업을 미리 수 있습니다.
- 이벤트의 하위 집합을 저장소로 임시로 라우트 하 여 스키마를 보다 잘 이해 하 고 탐색할 수 있습니다.

이벤트를 저장 하려면 Azure Event Hubs에 대 한 [이벤트 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) 를 설정 하거나 IoT Hub에서 azure Blob storage로 [라우팅합니다](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) .

이벤트 [허브 탐색기](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)를 통하거나 [이벤트 프로세서 호스트](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)를 사용 하 여 데이터를 관찰할 수도 있습니다.

IoT Hub 사용 하는 경우 기본 제공 끝점에 액세스 하는 방법에 대 한 [기본 제공 끝점에서 장치-클라우드 메시지 읽기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) 로 이동 합니다.

> [!NOTE]
> 권장 사항을 변경 하지 않는 경우 중단이 발생할 수 있습니다. 예를 들어 쿼리 Api 또는 Time Series Insights 탐색기를 통해 액세스 되는 영향을 받는 Time Series Insights 변수는 **null** 을 반환 합니다. 즉, 탐색기에서 데이터를 표시 하지 않습니다.

## <a name="recommended-changes"></a>권장 변경 내용

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>사례 1: 시계열 모델 변수를 사용 하 여 원격 분석 데이터에서 정수 계열 데이터 형식만 보내기

사례 1의 권장 변경 사항은 사례 2의 경우와 동일 합니다. 사례 2에 대 한 섹션의 지침을 따릅니다.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>사례 2: 시계열 모델 변수를 사용 하 여 원격 분석 데이터에서 정수 계열 및 비정 두 형식 모두 보내기

현재 정수 원격 분석 데이터를 전송 하는 경우 데이터는 두 개의 열로 나뉩니다.

- **propertyValue_double**
- **propertyValue_long**

정수 데이터는 **propertyValue_long**에 기록 됩니다. 이전의 수집 (및 이후 수집) 숫자 데이터는 **propertyValue_double** 복사 되지 않습니다.

이 두 열에서 **propertyValue** 속성에 대해 데이터를 쿼리하려면 TSX에서 **병합 ()** 스칼라 함수를 사용 해야 합니다. 함수는 동일한 **데이터** 형식의 인수를 허용 하 고 인수 목록에서 null이 아닌 첫 번째 값을 반환 합니다. 자세한 내용은 [Azure Time Series Insights Gen2 data access 개념](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)을 참조 하세요.

#### <a name="variable-definition-in-tsx---numeric"></a>TSX의 변수 정의-numeric

*이전 변수 정의:*

[![이전 변수 정의](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*새 변수 정의:*

[![새 변수 정의](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

또한 사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)으로 **병합 ($event toDouble ($event))** 을 사용할 수 있습니다.

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>TSX 쿼리 Api를 사용 하 여 인라인 변수 정의-numeric

*이전 변수 정의:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*새 변수 정의:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

또한 사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)으로 **병합 ($event toDouble ($event))** 을 사용할 수 있습니다.

> [!NOTE]
> 사용할 수 있는 모든 위치에서 이러한 변수를 업데이트 하는 것이 좋습니다. 이러한 위치에는 시계열 모델, 저장 된 쿼리 및 Power BI 커넥터 쿼리가 포함 됩니다.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>사례 3: 범주 변수를 사용 하 여 정수 값을 범주에 매핑

현재 정수 값을 범주에 매핑하는 범주 변수를 사용 하는 경우 **toLong** 함수를 사용 하 여 **Double** 형식에서 **Long** 형식으로 데이터를 변환할 가능성이 높습니다. 사례 1 및 2와 마찬가지로 **Double** 및 **Long** **DataType** 열을 병합 해야 합니다.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>시계열 탐색기의 변수 정의-범주

*이전 변수 정의:*

[![이전 변수 정의](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*새 변수 정의:*

[![새 변수 정의](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

또한 사용자 지정 [시계열 식](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)으로 **병합 ($event toDouble ($event))** 을 사용할 수 있습니다.

범주 변수에는 여전히 값이 정수 형식 이어야 합니다. **병합 ()** 의 모든 인수에 대 한 **데이터 형식은** 사용자 지정 시계열 식의 **Long** 형식 이어야 합니다 [.](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>TSX 쿼리 Api-범주를 사용 하 여 인라인 변수 정의

*이전 변수 정의:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*새 변수 정의:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

범주 변수에는 여전히 값이 정수 형식 이어야 합니다. **병합 ()** 의 모든 인수에 대 한 **데이터 형식은** 사용자 지정 시계열 [식](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)의 **Long** 형식 이어야 합니다.

> [!NOTE]
> 사용할 수 있는 모든 위치에서 이러한 변수를 업데이트 하는 것이 좋습니다. 이러한 위치에는 시계열 모델, 저장 된 쿼리 및 Power BI 커넥터 쿼리가 포함 됩니다.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>사례 4: JavaScript SDK를 사용 하 여 사용자 지정 프런트 엔드 응용 프로그램 빌드

사례 1 ~ 3의 영향을 받고 사용자 지정 응용 프로그램을 빌드하는 경우 이전 예제에서 설명한 대로 **병합 ()** 함수를 사용 하도록 쿼리를 업데이트 해야 합니다.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>사례 5: 근접 웜 저장소 1000 속성 제한

많은 속성을 가진 웜 저장소 사용자 인 경우이 변경으로 인해 1000 웜 저장소 속성 이름 제한을 초과 하 여 환경을 푸시하는 경우 Azure Portal를 통해 지원 티켓을 제출 하 고이 통신을 언급 합니다.

## <a name="next-steps"></a>다음 단계

- [지원 되는 데이터 형식의](concepts-supported-data-types.md)전체 목록을 봅니다.
