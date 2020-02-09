---
title: 미리 작성 한 엔터티 비율-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 percentage 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e9aa3fc7c0ff76f6b93a8acc67681d1ed7cf4ad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491225"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Percentage 미리 빌드된 엔터티
Percentage 수치는 분수, `3 1/2` 또는 백분율, `2%`로 표시될 수 있습니다. 이 엔터티를 이미 학습했기 때문에 percentage를 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Percentage 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-percentage"></a>Percentage 유형
Percentage는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-percentage-entity"></a>미리 빌드된 Percentage 엔터티의 해결

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`set a trigger when my stock goes up 2%`

#### <a name="v3-responsetabv3"></a>[V3 응답](#tab/V3)

다음 JSON은 `false`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON은 `true`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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
#### <a name="v2-responsetabv2"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.percentage** 엔터티의 해결을 보여 줍니다.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

[ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 
