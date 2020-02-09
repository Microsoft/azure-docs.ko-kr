---
title: 감정 분석 - LUIS
titleSuffix: Azure Cognitive Services
description: 감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501069"
---
# <a name="sentiment-analysis"></a>정서 분석
감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다. [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) 설명서에서 감정 분석에 대해 자세히 알아봅니다.


## <a name="resolution-for-sentiment"></a>감정에 대 한 해결 방법

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

#### <a name="english-languagetabenglish"></a>[영어](#tab/english)

문화권이 `en-us`인 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[기타 언어](#tab/other-languages)

다른 모든 문화권의 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

