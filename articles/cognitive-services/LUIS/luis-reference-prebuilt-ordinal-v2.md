---
title: 서 수 V2 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding (LUIS)의 서 수 V2 미리 작성 된 엔터티 정보가 포함 되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 02bc6657126cb1cf241c2ca4668e62bd49608d4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491264"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱에 대 한 서 수 V2 미리 작성 된 엔터티
서 수 V2 숫자는 [서 수](luis-reference-prebuilt-ordinal.md) 를 확장 하 여 `next`, `last`및 `previous`와 같은 상대 참조를 제공 합니다. 이러한 항목은 미리 작성 된 서 수 엔터티를 사용 하 여 추출 되지 않습니다.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>미리 빌드된 서 수 V2 엔터티에 대 한 해결 방법

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`what is the second to last choice in the list`

#### <a name="v3-responsetabv3"></a>[V3 응답](#tab/V3)

다음 JSON은 `false`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)

다음 JSON은 `true`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

다음 예제에서는 ordinalV2 엔터티를 확인 하는 방법을 보여 줍니다 **.**

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

[백분율](luis-reference-prebuilt-percentage.md), [전화 번호](luis-reference-prebuilt-phonenumber.md)및 [온도](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 
