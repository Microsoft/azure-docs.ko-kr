---
title: 통화 미리 빌드된 엔터티 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 currency 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534339"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 미리 빌드된 Currency 엔터티
currency 미리 빌드된 엔터티는 LUIS 앱 문화권에 관계없이 많은 액면가 및 국가/지역에서 통화를 검색합니다. 이 엔터티를 이미 학습했기 때문에 currency를 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Currency 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="types-of-currency"></a>Currency 유형
Currency는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-currency-entity"></a>Currency 엔터티의 해결

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON의 `verbose` 매개 변수가 `false`로 설정되어 있습니다.

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON의 `verbose` 매개 변수가 `true`로 설정되어 있습니다.

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.currency** 엔터티의 해결을 보여 줍니다.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md) 및 [email](luis-reference-prebuilt-email.md) 엔터티에 대해 알아봅니다.
