---
title: Temperature 미리 빌드된 엔터티 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 아티클에는 LUIS(Language Understanding)의 미리 빌드된 온도 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 46161a83d261ae23ca45b7293e48ff15e435f42d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535342"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 미리 빌드된 온도 엔터티
온도는 다양한 온도 형식을 추출합니다. 이 엔터티를 이미 학습했기 때문에 온도를 포함하는 예제 발언을 애플리케이션에 추가할 필요가 없습니다. 온도 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="types-of-temperature"></a>온도 형식
온도는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-temperature-entity"></a>미리 빌드된 온도 엔터티의 해결 방법

쿼리에 대해 반환되는 엔터티 개체는 다음과 같습니다.

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON의 `verbose` 매개 변수가 `false`로 설정되어 있습니다.

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON의 `verbose` 매개 변수가 `true`로 설정되어 있습니다.

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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
#### <a name="v2-response"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.temperature** 엔터티의 해결 방법을 보여줍니다.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [age](luis-reference-prebuilt-age.md) 엔터티에 대해 알아봅니다.
