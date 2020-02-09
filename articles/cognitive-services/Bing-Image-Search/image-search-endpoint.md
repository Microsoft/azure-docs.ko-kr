---
title: Bing Image Search API에 대한 엔드포인트
titleSuffix: Azure Cognitive Services
description: Image Search API에는 세 개의 엔드포인트이 포함 되어 있습니다. 엔드포인트 1은 웹에서 이미지를 반환 합니다. 엔드포인트 2는 ImageInsights를 반환 합니다. 엔드포인트 3은 최신 이미지를 반환합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072638"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing Image Search API에 대한 엔드포인트

**Image Search API**는 세 개의 엔드포인트를 포함합니다.  엔드포인트 1은 쿼리를 기반으로 하는 웹에서 이미지를 반환합니다. 엔드포인트 2는 [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)를 반환합니다.  엔드포인트 3은 최신 이미지를 반환합니다.

## <a name="endpoints"></a>엔드포인트

Bing API를 사용하여 이미지 결과를 가져오려면 다음 엔드포인트 중 하나로 요청을 보냅니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트 1:** `?q=""`에서 정의한 사용자의 검색 쿼리와 관련된 이미지를 반환합니다.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**엔드포인트 2:** `GET` 또는 `POST` 중 하나를 사용하여 이미지에 대한 정보를 반환합니다.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 요청은 이미지가 포함된 웹 페이지와 같이 이미지에 대한 정보를 반환합니다. [ 요청에 ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)insightsToken`GET` 매개 변수를 포함합니다.

또는 `POST` 요청의 본문에 이진 이미지를 포함하고 [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 매개 변수를 `RecognizedEntities`로 설정할 수 있습니다. 그러면 후속 [ 요청에서 매개 변수로 사용할 ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken)insightsToken`GET`이 반환되며 이미지에 있는 인물에 대한 정보가 반환됩니다.  `modules`을 사용하여 다른 호출을 하지 않고 `All`의 결과에서 `RecognizedEntities`을 제외한 모든 정보를 얻으려면 `POST`를 `insightsToken`로 설정합니다.


**엔드포인트 3:** 다른 사용자가 한 검색 요청을 기반으로 최신 이미지를 반환합니다. 이미지는 예를 들어 주목할 만한 사람이나 이벤트를 기반으로 다양한 범주로 구분됩니다.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

최신 이미지를 지원하는 지역/국가 목록은 [최신 이미지](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)를 참조하세요.

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 참조를 참조하세요.
## <a name="response-json"></a>응답 JSON
이미지 검색 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과 구문 분석의 예제는 [자습서](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) 및 [소스 코드](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)를 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트은 결과를 JSON 응답 개체로 반환 합니다.  모든 엔드포인트은 경도, 위도 및 검색 반지름을 기준으로 특정 언어 및/또는 위치를 반환 하는 쿼리를 지원 합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Image Search API를 사용하는 기본 요청의 예는 [Image Search 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)을 참조하세요.
