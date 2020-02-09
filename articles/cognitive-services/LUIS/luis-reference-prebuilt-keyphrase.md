---
title: 키 구 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 keyPhrase 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 51d1bd515651824545d486207ad4a74476aa7092
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491284"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 keyPhrase 미리 빌드된 엔터티
키 구 엔터티는 utterance에서 다양 한 키 구를 추출 합니다. 키 구를 포함 하는 예제 길이 발언를 응용 프로그램에 추가할 필요가 없습니다. 키 구 엔터티는 [텍스트 분석](../text-analytics/overview.md) 기능의 일부로 [많은 문화권](luis-language-support.md#languages-supported) 에서 지원 됩니다. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>미리 빌드된 keyPhrase 엔터티의 해결

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-responsetabv3"></a>[V3 응답](#tab/V3)

다음 JSON은 `false`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON은 `true`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

다음 예제에서는 **builtin.keyPhrase** 엔터티의 해결을 보여 줍니다.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [age](luis-reference-prebuilt-age.md) 엔터티에 대해 알아봅니다.
