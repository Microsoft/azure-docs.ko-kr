---
title: 데이터 추출-LUIS
description: 의도 및 엔터티를 사용 하 여 utterance 텍스트에서 데이터를 추출 합니다. Language Understanding에서 추출할 수 있는 데이터의 종류를 알아봅니다 (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716299"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>의도 및 엔터티를 사용 하 여 utterance 텍스트에서 데이터 추출
LUIS는 사용자의 자연어 발화에서 정보를 가져오는 기능을 제공합니다. 정보는 프로그램, 애플리케이션 또는 챗봇에서 작업을 수행하는 데 사용할 수 있는 방법으로 추출됩니다. 다음 섹션에서는 JSON 예제와 함께 의도 및 엔터티에서 반환되는 데이터에 대해 알아봅니다.

정확히 일치하는 텍스트가 아니므로 추출하기 가장 어려운 데이터는 기계 학습 데이터입니다. 기계 학습 [엔터티](luis-concept-entity-types.md)의 데이터 추출은 사용자가 예상한 데이터를 받는다고 확신할 때까지 [작성 주기](luis-concept-app-iteration.md)에 포함되어야 합니다.

## <a name="data-location-and-key-usage"></a>데이터 위치 및 키 사용
LUIS는 게시된 [엔드포인트](luis-glossary.md#endpoint)의 데이터를 제공합니다. **HTTPS 요청**(POST 또는 GET)에는 스테이징 또는 프로덕션 환경과 같은 일부 선택적 구성과 발화가 포함됩니다.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 예측 엔드포인트 요청](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 예측 엔드포인트 요청](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

`appID`는 LUIS 앱을 편집하는 동안 URL의 일부(**뒤) 및 LUIS 앱의**설정`/apps/` 페이지에서 사용할 수 있습니다. `subscription-key`는 앱을 쿼리하는 데 사용되는 엔드포인트 키입니다. LUIS를 학습시키는 동안 체험 작성/시작 키를 사용할 수 있지만, 엔드포인트 키를 [필요한 LUIS 사용](luis-boundaries.md#key-limits)을 지원하는 키로 변경해야 합니다. `timezoneOffset` 단위는 분입니다.

**HTTPS 응답**에는 스테이징 또는 프로덕션 엔드포인트의 현재 게시된 모델을 기반으로 LUIS가 확인할 수 있는 모든 의도 및 엔터티 정보가 포함됩니다. 엔드포인트 URL은 [LUIS](luis-reference-regions.md) 웹 사이트의 **관리** 섹션에 있는 **키 및 엔드포인트** 페이지에서 찾을 수 있습니다.

## <a name="data-from-intents"></a>의도의 데이터
기본 데이터는 상위 점수 **의도 이름**입니다. 엔드포인트 응답은 다음과 같습니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

|데이터 개체|데이터 형식|데이터 위치|값|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|“GetStoreInfo”|

두 개 이상의 의도 점수에 따라 의사 결정 봇 또는 LUIS 호출 앱이 결정 되 면 모든 의도의 점수를 반환 합니다.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

Querystring 매개 변수를 `verbose=true`설정 합니다. 엔드포인트 응답은 다음과 같습니다.

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

Querystring 매개 변수를 `show-all-intents=true`설정 합니다. 엔드포인트 응답은 다음과 같습니다.

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

의도는 최고 점수에서 최하 점수 순으로 정렬됩니다.

|데이터 개체|데이터 형식|데이터 위치|값|점수|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|“GetStoreInfo”|0.984749258|
|Intent|String|intents[1].intent|“None”|0.0168218873|

미리 빌드된 도메인을 추가하는 경우, 의도 이름은 의도뿐 아니라 `Utilties` 또는 `Communication`와 같은 도메인을 나타냅니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

|도메인|데이터 개체|데이터 형식|데이터 위치|값|
|--|--|--|--|--|
|공공 시설|Intent|String|intents[0].intent|“<b>Utilities</b>.ShowNext”|
|통신|Intent|String|intents[1].intent|<b>Communication</b>.StartOver”|
||Intent|String|intents[2].intent|“None”|


## <a name="data-from-entities"></a>엔터티의 데이터
대부분의 챗봇 및 애플리케이션에는 의도 이름 외에 더 많은 것이 필요합니다. 이 추가적이고 선택적인 데이터는 발화에서 검색되는 엔터티에서 가져옵니다. 각 엔터티 형식은 서로 다른 일치 정보를 반환합니다.

발화의 단일 단어 또는 구문이 둘 이상의 엔터티와 일치할 수 있습니다. 이 경우, 각 일치하는 엔터티는 점수와 함께 반환됩니다.

모든 엔터티는 엔드포인트에서 응답의 **엔터티** 배열로 반환됩니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

## <a name="tokenized-entity-returned"></a>토큰화된 엔터티가 반환됨

LUIS에서 [토큰 지원을](luis-language-support.md#tokenization) 검토 합니다.

## <a name="simple-entity-data"></a>단순 엔터티 데이터

[단순 엔터티](reference-entity-simple.md)는 기계 학습 값입니다. 이 값은 단어 또는 구문일 수 있습니다.

## <a name="composite-entity-data"></a>복합 엔터티 데이터

[복합 엔터티](reference-entity-composite.md) 는 미리 작성 된 엔터티, 단순, 정규식, 목록 엔터티 등의 다른 엔터티로 구성 됩니다. 개별 엔터티가 전체 엔터티를 형성합니다.

## <a name="list-entity-data"></a>목록 엔터티 데이터

[목록 엔터티](reference-entity-list.md) 는 동의어와 함께 고정 된 닫힌 관련 단어 집합을 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다.

## <a name="prebuilt-entity-data"></a>미리 빌드된 엔터티 데이터
[미리 빌드된](luis-concept-entity-types.md) 엔터티는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트를 사용하여 정규식 일치를 기반으로 검색됩니다. 미리 빌드된 엔터티는 엔터티 배열로 반환되며 `builtin::` 접두사가 추가된 형식 이름을 사용합니다. 다음 텍스트는 반환된 미리 빌드된 엔터티가 포함된 예제 발화입니다.

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

Querystring 매개 변수를 사용 하지 않고 `verbose=true`합니다.

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Querystring 매개 변수를 사용 하 여 `verbose=true`합니다.

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *
## <a name="regular-expression-entity-data"></a>정규식 엔터티 데이터

[정규식 엔터티](reference-entity-regular-expression.md) 는 사용자가 제공 하는 정규식 패턴을 기반으로 엔터티를 추출 합니다.

## <a name="extracting-names"></a>이름 추출
이름은 거의 문자와 단어의 조합일 수 있기 때문에 발화에서 이름을 가져오는 것은 어렵습니다. 추출할 이름의 유형에 따라 몇 가지 옵션이 있습니다. 다음 제안 사항을 규칙이 아니라 자세한 지침입니다.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>미리 빌드된 PersonName 및 GeographyV2 엔터티 추가

[PersonName](luis-reference-prebuilt-person.md) 및 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 엔터티는 일부 [언어 문화권](luis-reference-prebuilt-entities.md)에서 사용할 수 있습니다.

### <a name="names-of-people"></a>사용자의 이름

사용자의 이름에는 언어 및 문화권에 따라 일부 약한 형식이 적용될 수 있습니다. 미리 작성 된 **[personName](luis-reference-prebuilt-person.md)** 엔터티 또는 [역할](luis-concept-roles.md) 을 성과 이름으로 사용 하는 **[간단한 엔터티](luis-concept-entity-types.md#simple-entity)** 를 사용 합니다.

단순 엔터티를 사용 하는 경우 utterance의 서로 다른 부분에 있는 성과 이름을 사용 하는 예제를 제공 하 고, 다른 길이의 길이 발언를 사용 하 여 모든 의도에 대 한 길이 발언를 제공 해야 합니다. 정기적으로 엔드포인트 발화를 [검토](luis-how-to-review-endoint-utt.md)하여 올바르게 예측되지 않은 이름에 레이블을 지정합니다.

### <a name="names-of-places"></a>위치 이름

위치 이름은 도시, 군, 주,도 및 국가/지역과 같은 설정 및 알려져 있습니다. 미리 작성 된 엔터티 **[geographyV2](luis-reference-prebuilt-geographyv2.md)** 를 사용 하 여 위치 정보를 추출 합니다.

### <a name="new-and-emerging-names"></a>새롭게 떠오르는 이름

일부 앱은 제품이나 회사와 같이 새롭게 떠오르는 이름을 찾을 수 있어야 합니다. 이러한 형식의 이름은 가장 어려운 데이터 추출 유형입니다. **[간단한 엔터티로](luis-concept-entity-types.md#simple-entity)** 시작 하 고 [구 목록을](luis-concept-feature.md)추가 합니다. 정기적으로 엔드포인트 발화를 [검토](luis-how-to-review-endoint-utt.md)하여 올바르게 예측되지 않은 이름에 레이블을 지정합니다.

## <a name="pattern-roles-data"></a>패턴 역할 데이터
역할은 엔터티의 컨텍스트 차이입니다.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

엔터티 이름은 `Location``Origin` 및 `Destination`라는 두 개의 역할이 있습니다.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

V3에서 **역할 이름은** 개체의 기본 이름입니다.

엔터티 이름은 `Location``Origin` 및 `Destination`라는 두 개의 역할이 있습니다.

Querystring 매개 변수를 사용 하지 않고 `verbose=true`합니다.

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Querystring 매개 변수를 사용 하 여 `verbose=true`합니다.

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

## <a name="patternany-entity-data"></a>Pattern.any 엔터티 데이터

[패턴. any](reference-entity-pattern-any.md) 는 패턴이 시작 되 고 끝나는 위치를 표시 하는 패턴의 템플릿 utterance에만 사용 되는 가변 길이 자리 표시자입니다.

## <a name="sentiment-analysis"></a>정서 분석
감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다. [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) 설명서에서 감정 분석에 대해 자세히 알아봅니다.

### <a name="sentiment-data"></a>감정 데이터
감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

문화권이 `en-us`인 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

다른 모든 문화권의 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>키 구문 추출 엔터티 데이터
키 구문 추출 엔터티는 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)에서 제공하는 발화의 키 구문을 반환합니다.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

Querystring 매개 변수를 사용 하지 않고 `verbose=true`합니다.

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Querystring 매개 변수를 사용 하 여 `verbose=true`합니다.

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *


## <a name="data-matching-multiple-entities"></a>여러 엔터티와 일치하는 데이터

LUIS는 발화에서 검색된 모든 엔터티를 반환합니다. 결과적으로 챗봇은 결과를 기반으로 결정해야 할 수 있습니다. 발화에는 많은 엔터티가 포함될 수 있습니다.

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS 엔드포인트은 서로 다른 엔터티에서 동일한 데이터를 검색할 수 있습니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

Querystring 매개 변수로 `verbose=true` 하지 않습니다.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

`verbose=true` querystring 매개 변수로 사용 합니다.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

## <a name="data-matching-multiple-list-entities"></a>여러 목록 엔터티와 일치하는 데이터

단어나 구가 둘 이상의 목록 엔터티와 일치하는 경우, 엔드포인트 쿼리는 각 목록 엔터티를 반환합니다.

`when is the best time to go to red rock?` 쿼리에서 앱이 둘 이상의 목록에 `red`라는 단어를 갖는 경우 LUIS는 모든 엔터티를 인식하고 엔터티의 배열을 JSON 엔드포인트 응답의 일부로 반환합니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

쿼리 문자열에 `verbose=true` 하지 않습니다.

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


쿼리 문자열에 `verbose=true`을 사용 합니다.

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

* * *

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
