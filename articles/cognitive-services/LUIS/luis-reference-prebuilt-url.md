---
title: URL 미리 빌드된 엔터티- LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 URL 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535256"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 URL 미리 빌드된 엔터티
URL 엔터티는 도메인 이름 또는 IP 주소를 사용하여 URL을 추출합니다. 이 엔터티를 이미 학습했기 때문에 URL을 애플리케이션에 포함하는 예제 발언을 추가할 필요가 없습니다. URL 엔터티는 `en-us` 문화권에서만 지원됩니다.

## <a name="types-of-urls"></a>URL 유형
URL은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-url-entity"></a>미리 빌드된 URL 엔터티의 해결

쿼리에 대해 반환되는 엔터티 개체는 다음과 같습니다.

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON의 `verbose` 매개 변수가 `false`로 설정되어 있습니다.

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)

다음 JSON의 `verbose` 매개 변수가 `true`로 설정되어 있습니다.

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

다음 예에서는 https://www.luis.ai 확인이 인공 지능의 뛰어난 Cognitive Services 예임을 보여 줍니다.

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다.
