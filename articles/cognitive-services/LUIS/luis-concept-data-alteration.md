---
title: 데이터 변경-LUIS
description: Language Understanding(LUIS)에서 예측 전에 데이터를 변경하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c675ac246f397b5949c870ad91ab936bbd92c7ef
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934670"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>예측 이전 또는 도중에 발언 데이터 변경
LUIS는 예측 전이나 예측 중에 발화를 조작할 수 있는 방법을 제공합니다. 여기에는 미리 작성 한 [datetimeV2](luis-reference-prebuilt-datetimev2.md)에 대 한 [맞춤법 수정](luis-tutorial-bing-spellcheck.md), 표준 시간대 문제 해결 등이 포함 됩니다.

## <a name="correct-spelling-errors-in-utterance"></a>발화에서 맞춤법 오류 수정


### <a name="v3-runtime"></a>V3 런타임

Utterance을 LUIS로 보내기 전에 맞춤법 수정 텍스트를 전처리 합니다. 올바른 예측을 얻을 수 있도록 올바른 철자를 사용 하는 예제 길이 발언를 사용 합니다.

LUIS에 보내기 전에 [Bing Spell Check](../bing-spell-check/overview.md) 을 사용 하 여 텍스트를 수정 합니다.

### <a name="prior-to-v3-runtime"></a>V3 런타임 이전

LUIS는 [Bing Spell Check API V7](../Bing-Spell-Check/overview.md)을 사용하여 발화의 맞춤법 오류를 수정합니다. LUIS에는 해당 서비스와 연관된 키가 필요합니다. 키를 만든 다음, [엔드포인트](https://go.microsoft.com/fwlink/?linkid=2092356)에서 쿼리 문자열 매개 변수로 키를 추가합니다.

엔드포인트에서 맞춤법 수정이 작동하려면 두 개의 매개 변수가 필요합니다.

|매개 변수|값|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 엔드포인트 키|

[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)에서 오류를 검색하면 원래 발화와 수정된 발화가 엔드포인트의 예측과 함께 반환됩니다.

#### <a name="v2-prediction-endpoint-response"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 예측 엔드포인트 응답](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>허용 되는 단어 목록
LUIS에서 사용 되는 Bing 맞춤법 검사 API는 맞춤법 검사를 변경 하는 동안 무시할 단어 목록을 지원 하지 않습니다. 단어 또는 머리글자어 목록을 허용 해야 하는 경우에는 utterance를 LUIS로 보내기 전에 클라이언트 응용 프로그램에서 utterance를 처리 합니다.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>미리 빌드된 datetimeV2 엔터티의 표준 시간대 변경
LUIS 앱이 미리 작성 된 [datetimeV2](luis-reference-prebuilt-datetimev2.md) 엔터티를 사용 하는 경우 예측 응답에서 datetime 값을 반환할 수 있습니다. 요청의 표준 시간대는 반환할 올바른 datetime을 확인하는 데 사용됩니다. LUIS에 연결하기 전에 봇이나 다른 중앙 집중식 애플리케이션에서 요청을 가져오는 경우, LUIS에서 사용하는 표준 시간대를 수정하세요.

### <a name="v3-prediction-api-to-alter-timezone"></a>표준 시간대를 변경 하는 V3 예측 API

V3에서는 `datetimeReference` 표준 시간대 오프셋을 결정 합니다. [V3 예측](luis-migration-api-v3.md#v3-post-body)에 대해 자세히 알아보세요.

### <a name="v2-prediction-api-to-alter-timezone"></a>표준 시간대를 변경 하는 V2 예측 API
표준 시간대는 `timezoneOffset` API 버전에 따라 매개 변수를 사용 하 여 사용자의 표준 시간대를 끝점에 추가 하 여 수정 됩니다. 매개 변수 값은 시간을 변경 하는 양수 또는 음수 (분)입니다.

#### <a name="v2-prediction-daylight-savings-example"></a>V2 예측 일광 절약 예제
일광 절약 시간제를 조정 하기 위해 반환 된 미리 작성 된 datetimeV2이 필요한 경우에는 [끝점](https://go.microsoft.com/fwlink/?linkid=2092356) 쿼리에 대해 몇 분 안에 querystring 매개 변수를 사용 해야 합니다.

60분 추가:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60분 제거:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 예측 c # 코드에서 올바른 매개 변수 값을 확인 합니다.

다음 c # 코드는 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) 클래스의 [timezoneinfo.findsystemtimezonebyid 메서드의](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) 메서드를 사용 하 여 시스템 시간에 따라 올바른 오프셋 값을 결정 합니다.

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이 자습서에서 맞춤법 오류 수정](luis-tutorial-bing-spellcheck.md)
