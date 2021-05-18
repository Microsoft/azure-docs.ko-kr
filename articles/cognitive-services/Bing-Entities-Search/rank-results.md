---
title: 순위를 사용하여 대답 표시 - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: 순위를 사용하여 Bing Entity Search API가 반환하는 응답을 표시하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94365638"
---
# <a name="using-ranking-to-display-entity-search-results"></a>순위를 사용하여 엔터티 검색 결과 표시  

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

각 엔터티 검색 응답에는 Bing Entity Search API에서 반환하는 검색 결과의 표시 방법을 지정하는 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) 답변이 포함됩니다. 순위 응답은 결과를 폴, 메인라인 및 사이드바 콘텐츠로 그룹화합니다. 폴 결과는 가장 중요하거나 두드러진 결과로, 맨 처음에 표시됩니다. 나머지 결과를 기존의 메인라인 및 사이드바 형식으로 표시하지 않을 경우 사이드바 콘텐츠보다 가시성이 뛰어난 메인라인 콘텐츠를 제공해야 합니다. 
  
각 그룹 내에서 [항목](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) 배열은 콘텐츠를 표시해야 하는 순서를 식별합니다. 각 항목은 답변 내에서 결과를 식별하는 두 가지 방법을 제공합니다.  
 

|필드 | Description  |
|---------|---------|
|`answerType` 및 `resultIndex` | `answerType`은 응답(엔터티 또는 장소)을 식별하고 `resultIndex`는 응답 내의 결과(예: 엔터티)를 식별합니다. 인덱스는 0부터 시작합니다.|
|`value`    | `value`는 답변 또는 답변 내 결과의 ID와 일치하는 ID를 포함합니다. 답변 또는 결과 중 하나만 ID를 포함합니다. |
  
`answerType` 및 `resultIndex`의 사용은 두 단계로 진행되는 프로세스입니다. 먼저 `answerType`을 사용하여 표시할 결과가 포함된 답변을 식별합니다. 그 후 `resultIndex`를 사용하여 응답 결과로 인덱싱하여 표시할 결과를 가져옵니다. (`answerType` 값은 [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 개체의 필드 이름입니다.) 모든 대답의 결과를 함께 표시해야 하는 경우 순위 응답 항목에 `resultIndex` 필드가 포함되지 않습니다.

해당 ID를 사용하려면 순위 ID가 응답 또는 결과 중 하나의 ID와 일치해야 합니다. 답변 개체에 `id` 필드가 포함된 경우 모든 답변의 결과를 함께 표시합니다. 예를 들어, `Entities` 개체에 `id` 필드가 포함되면 모든 엔터티 아티클을 함께 표시합니다. `Entities` 개체에 `id` 필드가 포함되어 있지 않으면 각 엔터티는 `id` 필드를 포함하고 순위 응답은 엔터티와 장소 결과를 혼합합니다.  
  
## <a name="ranking-response-example"></a>순위 응답 예제

다음은 예제 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)를 보여 줍니다.
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

이 순위 응답에 따라 사이드바에는 Jimi Hendrix와 관련된 두 가지 엔터티 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](tutorial-bing-entities-search-single-page-app.md)