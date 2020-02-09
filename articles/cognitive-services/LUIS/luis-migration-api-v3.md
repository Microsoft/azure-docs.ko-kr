---
title: V3 API의 예측 엔드포인트 변경 내용
titleSuffix: Azure Cognitive Services
description: 쿼리 예측 엔드포인트 V3 Api가 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 엔드포인트 Api로 마이그레이션하는 방법을 이해할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 8756d8e60e7612c1610e07b0567465e3a0ea8884
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531499"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3에 대 한 예측 엔드포인트 변경

쿼리 예측 엔드포인트 V3 Api가 변경 되었습니다. 이 가이드를 사용 하 여 버전 3 엔드포인트 Api로 마이그레이션하는 방법을 이해할 수 있습니다. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**일반적으로 사용 가능한 상태** -이 V3 api에는 V2 api의 중요 한 JSON 요청 및 응답 변경 내용이 포함 되어 있습니다.

V3 API는 다음과 같은 새로운 기능을 제공 합니다.

* [외부 엔터티](#external-entities-passed-in-at-prediction-time)
* [동적 목록](#dynamic-lists-passed-in-at-prediction-time)
* [미리 작성 한 엔터티 JSON 변경 내용](#prebuilt-entity-changes)

예측 엔드포인트 [요청](#request-changes) 및 [응답](#response-changes) 은 다음을 포함 하 여 위에 나열 된 새 기능을 지원 하기 위해 중요 한 변경 사항이 있습니다.

* [응답 개체 변경](#top-level-json-changes)
* [엔터티 이름 대신 엔터티 역할 이름 참조](#entity-role-name-instead-of-entity-name)
* [길이 발언에 엔터티를 표시 하는 속성](#marking-placement-of-entities-in-utterances)

[참조 설명서](https://aka.ms/luis-api-v3) 는 V3에서 사용할 수 있습니다.

## <a name="v3-changes-from-preview-to-ga"></a>V3이 미리 보기에서 GA로 변경 됩니다.

V 3에서 GA로 이동 하는 과정의 일부로 다음과 같이 변경 했습니다. 

* 다음 미리 작성 된 엔터티에는 서로 다른 JSON 응답이 있습니다. 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * `units`에서 `unit`까지 측정 가능한 단위 키 이름

* 요청 본문 JSON 변경:
    * `preferExternalEntities`에서 `preferExternalEntities`
    * 외부 엔터티에 대 한 선택적 `score` 매개 변수

* 응답 본문 JSON 변경 내용:
    * 제거 `normalizedQuery`

## <a name="suggested-adoption-strategy"></a>제안 된 채택 전략

Bot Framework를 사용 하거나 V7를 Bing Spell Check 하거나 LUIS 앱 제작만 마이그레이션하려면 V2 엔드포인트을 계속 사용 합니다. 

클라이언트 응용 프로그램 또는 통합 (Bot Framework 및 Bing Spell Check V7)이 영향을 받지 않고 LUIS 앱 작성과 예측 엔드포인트을 동시에 마이그레이션하는 것을 알고 있는 경우 V3 예측 엔드포인트 사용을 시작 합니다. V2 예측 엔드포인트은 계속 사용할 수 있으며 좋은 복구 전략입니다. 

## <a name="not-supported"></a>지원하지 않음

* Bing Spell Check API은 V3 예측 엔드포인트에서 지원 되지 않습니다.-계속 해 서 V2 API 예측 엔드포인트을 사용 하 여 맞춤법을 수정 합니다.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework 및 Azure Bot Service 클라이언트 응용 프로그램

Bot Framework의 V 4.7이 릴리스될 때까지 V2 API 예측 엔드포인트을 계속 사용 합니다. 

## <a name="v2-api-deprecation"></a>V2 API 사용 중단 

V2 예측 API는 V3 preview 이후 최소 9 개월 동안 (6 월 8 일, 2020)에는 사용 되지 않습니다. 

## <a name="endpoint-url-changes"></a>엔드포인트 URL 변경 

### <a name="changes-by-slot-name-and-version-name"></a>슬롯 이름 및 버전 이름 변경

V3 엔드포인트 HTTP 호출의 형식이 변경 되었습니다.

버전을 기준으로 쿼리하려면 먼저 `"directVersionPublish":true`를 사용 하 여 [API를 통해 게시](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) 해야 합니다. 슬롯 이름 대신 버전 ID를 참조 하는 엔드포인트을 쿼리 합니다.

|예측 API 버전|METHOD|URL|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v 3.0</b>/apps/<b>{앱 ID}</b>/slots/<b>{슬롯-이름}</b>/predict? query =<b>{query}</b>|
|V3|POST|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v 3.0</b>/apps/<b>{응용 프로그램 ID}</b>/slots/<b>{슬롯-이름}</b>/예측|
|V2|GET|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v 3.0</b>/apps/<b>{APP id}</b>/versions/<b>{VERSION-id}</b>/predict? query =<b>{query}</b>|
|V2|POST|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>예측</b>/<b>v 3.0</b>/apps/<b>{앱 id}</b>/versions/<b>{버전 id}</b>/예측|

|`SLOT-NAME` 유효한 값|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>변경 내용 요청 

### <a name="query-string-changes"></a>문자열 변경 내용 쿼리

V3 API에는 다른 쿼리 문자열 매개 변수가 있습니다.

|매개 변수 이름|유형|버전|기본값|용도|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|로그 파일에 쿼리를 저장 합니다. 기본값은 False입니다.| 
|`query`|문자열|V3만|기본값 없음-GET 요청에 필요 합니다.|V 2 **에서**예측할 utterance은 `q` 매개 변수입니다. <br><br>**V3에서**기능은 `query` 매개 변수에 전달 됩니다.|
|`show-all-intents`|boolean|V3만|false|**예측과** 개체의 해당 점수를 사용 하 여 모든 의도를 반환 합니다. 의도는 부모 `intents` 개체에서 개체로 반환 됩니다. 이렇게 하면 배열에서 의도를 찾을 필요 없이 프로그래밍 방식으로 액세스할 수 있습니다. `prediction.intents.give`. V 2에서는 배열에이 반환 되었습니다. |
|`verbose`|boolean|V2 & V3|false|V 2 **에서**true로 설정 하면 예측 된 모든 것이 반환 됩니다. 모든 예측 의도를 필요로 하는 경우 `show-all-intents`의 V3 매개 변수를 사용 합니다.<br><br>**V3에서**이 매개 변수는 엔터티 예측의 엔터티 메타 데이터 정보만 제공 합니다.  |
|`timezoneOffset`|문자열|V2|-|DatetimeV2 엔터티에 적용 되는 표준 시간대입니다.|
|`datetimeReference`|문자열|V3|-|DatetimeV2 엔터티에 적용 되는 [표준 시간대](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) 입니다. V 2에서 `timezoneOffset`를 바꿉니다.|


### <a name="v3-post-body"></a>V3 POST 본문

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|속성|유형|버전|기본값|용도|
|--|--|--|--|--|
|`dynamicLists`|array|V3만|필요하지 않습니다.|[동적 목록을](#dynamic-lists-passed-in-at-prediction-time) 사용 하면 이미 LUIS 앱에 있는 기존의 학습 및 게시 된 목록 엔터티를 확장할 수 있습니다.|
|`externalEntities`|array|V3만|필요하지 않습니다.|[외부 엔터티](#external-entities-passed-in-at-prediction-time) 를 통해 LUIS 앱은 런타임 중에 엔터티를 식별 하 고 레이블을 지정 하는 기능을 기존 엔터티에 대 한 기능으로 사용할 수 있습니다. |
|`options.datetimeReference`|문자열|V3만|기본값 없음|[DatetimeV2 오프셋](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)을 확인 하는 데 사용 됩니다. DatetimeReference의 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)입니다.|
|`options.preferExternalEntities`|boolean|V3만|false|사용자의 [외부 엔터티 (기존 엔터티와 이름이 같은)](#override-existing-model-predictions) 를 사용 하거나 모델의 기존 엔터티를 예측에 사용 하는지 여부를 지정 합니다. |
|`query`|문자열|V3만|필수 사항입니다.|V 2 **에서**예측할 utterance은 `q` 매개 변수입니다. <br><br>**V3에서**기능은 `query` 매개 변수에 전달 됩니다.|



## <a name="response-changes"></a>응답 변경

쿼리 응답 JSON은 가장 자주 사용 되는 데이터에 대 한 프로그래밍 방식의 액세스를 허용 하도록 변경 되었습니다. 

### <a name="top-level-json-changes"></a>최상위 수준의 JSON 변경 내용



V 2의 상위 JSON 속성은 `verbose` true로 설정 된 경우 `intents` 속성에서 모든 의도 및 해당 점수를 반환 합니다.

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3의 top JSON 속성은 다음과 같습니다.

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` 개체는 순서가 지정 되지 않은 목록입니다. `intents`의 첫 번째 자식이 `topIntent`에 해당 하는 것으로 가정 하지 마십시오. 대신 `topIntent` 값을 사용 하 여 점수를 찾으십시오.

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

응답 JSON 스키마 변경 내용:

* 원래 utterance와 `query`를 구분 하 고 `prediction`반환 된 예측을 반환 합니다.
* 예측 된 데이터에 대 한 프로그래밍 방식의 액세스를 용이 하 게 합니다. V 2에서 배열을 열거 하는 대신 **이름** 및 엔터티 모두에 대 한 값에 액세스할 수 있습니다. 예측 된 엔터티 역할의 경우 전체 앱에서 고유 하므로 역할 이름이 반환 됩니다.
* 데이터 형식이 결정 되 면 적용 됩니다. 숫자는 더 이상 문자열로 반환 되지 않습니다.
* 첫 번째 우선 순위 예측 정보와 추가 메타 데이터를 구분 하 여 `$instance` 개체에 반환 합니다. 

### <a name="entity-response-changes"></a>엔터티 응답 변경

#### <a name="marking-placement-of-entities-in-utterances"></a>길이 발언에서 엔터티 배치 표시

V 2 **에서**엔터티는 `startIndex` 및 `endIndex`를 사용 하 여 utterance로 표시 되었습니다. 

**V3에서**엔터티는 `startIndex` 및 `entityLength`표시 됩니다.

#### <a name="access-instance-for-entity-metadata"></a>엔터티 메타 데이터에 대 한 액세스 `$instance`

엔터티 메타 데이터가 필요한 경우 쿼리 문자열은 `verbose=true` 플래그를 사용 해야 하 고 응답에는 `$instance` 개체의 메타 데이터가 포함 됩니다. 다음 섹션의 JSON 응답에 예제가 나와 있습니다.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>예측 된 각 엔터티는 배열로 표시 됩니다.

Utterance에서 각 엔터티를 두 번 이상 예측할 수 있으므로 `prediction.entities.<entity-name>` 개체는 배열을 포함 합니다. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>미리 작성 한 엔터티 변경

V3 response 개체에는 미리 빌드된 엔터티에 대 한 변경 내용이 포함 됩니다. 미리 작성 한 [특정 엔터티](luis-reference-prebuilt-entities.md) 를 검토 하 여 자세히 알아보세요. 

#### <a name="list-entity-prediction-changes"></a>엔터티 예측 변경 내용 나열

목록 엔터티 예측에 대 한 JSON이 배열의 배열로 변경 되었습니다.

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
각 내부 배열은 utterance 내부의 텍스트에 해당 합니다. 동일한 텍스트가 목록 엔터티의 여러 하위 목록에 나타날 수 있기 때문에 내부 개체는 배열입니다. 

`entities` 개체를 `$instance` 개체로 매핑하면 목록 엔터티 예측에 대해 개체의 순서가 유지 됩니다.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>엔터티 이름 대신 엔터티 역할 이름 

V 2에서 `entities` 배열은 엔터티 이름이 고유 식별자가 되는 예측 된 엔터티를 모두 반환 합니다. V3에서 엔터티가 역할을 사용 하 고 예측을 엔터티 역할에 사용 하는 경우 기본 식별자는 역할 이름입니다. 이는 엔터티 역할 이름이 다른 모델 (의도, 엔터티) 이름을 포함 하 여 전체 앱에서 고유 해야 하기 때문에 가능 합니다.

다음 예제에서는 `Yellow Bird Lane`텍스트를 포함 하는 utterance을 고려 합니다. 이 텍스트는 사용자 지정 `Location` 엔터티의 `Destination`역할로 예측 됩니다.

|Utterance 텍스트|엔터티 이름|역할 이름|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V 2에서 엔터티는 역할을 개체의 속성으로 사용 하 여 _엔터티 이름_ 으로 식별 됩니다.

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

V3에서 엔터티는 역할에 대 한 예측 인 경우 _엔터티 역할_에서 참조 됩니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3에서 다음과 같이 `verbose` 플래그를 사용 하 여 엔터티 메타 데이터를 반환 합니다.

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>예측 시 전달 되는 외부 엔터티

외부 엔터티를 통해 LUIS 앱은 런타임 중에 엔터티를 식별 하 고 레이블을 지정 하는 기능을 기존 엔터티에 대 한 기능으로 사용할 수 있습니다. 이렇게 하면 예측 엔드포인트에 쿼리를 보내기 전에 별도의 고유한 사용자 지정 엔터티 추출기를 사용할 수 있습니다. 이는 쿼리 예측 엔드포인트에서 수행 되므로 모델을 다시 학습 하 고 게시할 필요가 없습니다.

클라이언트 응용 프로그램은 엔터티 일치를 관리 하 고 일치 하는 엔터티의 utterance 내에서 위치를 확인 한 다음 요청을 통해 해당 정보를 전송 하 여 자체 엔터티 추출기를 제공 합니다. 

외부 엔터티는 역할, 복합 및 기타 모델에 대 한 신호로 계속 사용 되는 동안 엔터티 형식을 확장 하는 메커니즘입니다.

이는 쿼리 예측 런타임에만 데이터를 사용할 수 있는 엔터티에 유용 합니다. 이러한 데이터 형식의 예는 지속적으로 데이터를 변경 하거나 사용자별로 특정 합니다. 사용자의 연락처 목록에서 외부 정보를 사용 하 여 LUIS contact 엔터티를 확장할 수 있습니다. 

### <a name="entity-already-exists-in-app"></a>엔터티가 앱에 이미 있습니다.

엔드포인트 요청 게시 본문에 전달 되는 외부 엔터티에 대 한 `entityName` 값은 요청 시 학습 된 앱과 게시 된 앱에 이미 존재 해야 합니다. 엔터티 형식이 중요 하지 않습니다. 모든 형식이 지원 됩니다.

### <a name="first-turn-in-conversation"></a>대화를 먼저 전환

사용자가 다음과 같은 불완전 한 정보를 입력 하는 chat bot 대화의 첫 번째 utterance을 고려 합니다.

`Send Hazem a new message`

채팅 봇에서 LUIS로의 요청은 게시 본문에 정보를 전달 하 여 사용자의 연락처 중 하 나와 직접 일치 하도록 `Hazem` 수 있습니다.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 외부 엔터티를 포함 합니다.  

### <a name="second-turn-in-conversation"></a>대화의 두 번째 설정

채팅 봇에 utterance 하는 다음 사용자는 더 모호한 용어를 사용 합니다.

`Send him a calendar reminder for the party.`

이전 utterance utterance는 `Hazem`에 대 한 참조로 `him`를 사용 합니다. 게시물 본문의 대화형 채팅 봇은 `him`를 첫 번째 utterance `Hazem`에서 추출 된 엔터티 값에 매핑할 수 있습니다.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 외부 엔터티를 포함 합니다.  

### <a name="override-existing-model-predictions"></a>기존 모델 예측 재정의

`preferExternalEntities` options 속성은 사용자가 동일한 이름의 예측 된 엔터티와 겹치는 외부 엔터티를 보내는 경우 LUIS에서 전달 된 엔터티를 선택 하거나 모델에서 기존 엔터티를 선택 하도록 지정 합니다. 

예를 들어 `today I'm free` 쿼리를 고려할 수 있습니다. LUIS는 다음 응답을 사용 하 여 `today`를 datetimeV2로 검색 합니다.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

사용자가 외부 엔터티를 전송 하는 경우:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

`preferExternalEntities` `false`으로 설정 된 경우 LUIS는 외부 엔터티가 전송 되지 않은 것 처럼 응답을 반환 합니다. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`preferExternalEntities` `true`으로 설정 된 경우 LUIS는 다음과 같은 응답을 반환 합니다.

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>해상도

_선택적_ `resolution` 속성은 예측 응답에서를 반환 하 여 외부 엔터티와 연결 된 메타 데이터를 전달한 다음 응답에서 다시 받을 수 있도록 합니다. 

기본 목적은 미리 작성 된 엔터티를 확장 하는 것 이지만 해당 엔터티 형식으로 제한 되지 않습니다. 

`resolution` 속성은 숫자, 문자열, 개체 또는 배열일 수 있습니다.

* 달라스
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>예측 시 전달 된 동적 목록

동적 목록을 사용 하면 이미 LUIS 앱에 있는 기존의 학습 및 게시 된 목록 엔터티를 확장할 수 있습니다. 

목록 엔터티 값을 주기적으로 변경 해야 하는 경우이 기능을 사용 합니다. 이 기능을 사용 하 여 이미 학습 되 고 게시 된 목록 엔터티를 확장할 수 있습니다.

* 쿼리 예측 엔드포인트 요청 시
* 단일 요청에 대 한입니다.

LUIS 앱에서 목록 엔터티는 비어 있을 수 있지만 존재 해야 합니다. LUIS 앱의 목록 엔터티는 변경 되지 않지만 엔드포인트의 예측 기능은 약 1000 항목을 포함 하는 최대 2 개의 목록을 포함 하도록 확장 됩니다.

### <a name="dynamic-list-json-request-body"></a>동적 목록 JSON 요청 본문

다음 JSON 본문으로 보내서 동의어를 사용 하 여 새 하위 목록을 목록에 추가 하 고 `POST` 쿼리 예측 요청을 사용 하 여 `LUIS`텍스트에 대 한 목록 엔터티를 예측할 수 있습니다.

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

예측 응답은 요청에 정의 되어 있기 때문에 다른 모든 예측 된 엔터티와 함께 해당 목록 엔터티를 포함 합니다. 

## <a name="deprecation"></a>사용 중단 

V2 API는 V3 미리 보기 후 9 개월 이상 사용 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

V3 API 설명서를 사용 하 여 LUIS [엔드포인트](https://aka.ms/luis-api-v3) api에 대 한 기존 REST 호출을 업데이트 합니다. 
