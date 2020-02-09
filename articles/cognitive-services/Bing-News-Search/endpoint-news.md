---
title: Bing News Search 엔드포인트
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 News search API 엔드포인트에 대 한 요약을 제공 합니다. 뉴스, 인기 뉴스 및 추세 뉴스.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111492"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API 엔드포인트

**News Search API**는 뉴스 기사, 웹 페이지, 이미지, 비디오 및 [엔터티](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)를 반환합니다. 엔터티에는 사람, 장소 또는 토픽에 대한 요약 정보가 포함됩니다.

## <a name="endpoints"></a>엔드포인트

Bing News Search API를 사용하여 뉴스 검색 결과를 가져오려면 다음 엔드포인트 중 하나로 `GET` 요청을 보냅니다. 헤더 및 URL 매개 변수는 추가 사양을 정의합니다.

### <a name="news-items-by-search-query"></a>검색 쿼리의 뉴스 항목

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

검색 쿼리를 기준으로 뉴스 항목을 반환합니다. 검색 쿼리가 비어 있으면 API는 다른 범주의 상위 뉴스 기사를 반환합니다. 검색어를 인코딩하고 `q=""` 매개 변수에 추가하여 URL에 따라 쿼리를 전송합니다. 가용성은 [지원 되는 국가/지역 및 시장을](language-support.md#supported-markets-for-news-search-endpoint)참조 하세요.

### <a name="top-news-items-by-category"></a>범주별 상위 뉴스 항목

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

범주별 상위 뉴스 항목을 반환합니다. `category=business`, `category=sports` 또는 `category=entertainment`를 사용하여 상위 비즈니스, 스포츠 또는 엔터테인먼트 기사를 구체적으로 요청할 수 있습니다.  `category` 매개 변수는 `/news` URL 에서만 사용할 수 있습니다. 범주를 지정하기 위한 몇 가지 공식적인 요구 사항이 있습니다. 자세한 내용은 `category`쿼리 매개 변수[ 설명서의 ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)를 참조하세요. 검색어를 인코딩하고 `q=""` 매개 변수에 추가하여 URL에 따라 쿼리를 전송합니다. 가용성은 [지원 되는 국가/지역 및 시장을](language-support.md#supported-markets-for-news-endpoint)참조 하세요.

### <a name="trending-news-topics"></a>최신 뉴스 토픽 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

현재 소셜 네트워크에서 유행하고 있는 최신 토픽을 반환합니다. `/trendingtopics` 옵션이 포함되면 Bing 검색은 `freshness` 및 `?q=""` 같은 여러 가지 다른 매개 변수를 무시합니다. 가용성은 [지원 되는 국가/지역 및 시장을](language-support.md#supported-markets-for-news-trending-endpoint)참조 하세요.

## <a name="next-steps"></a>다음 단계

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 참조에서 확인할 수 있습니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
News Search API를 사용하는 기본 요청의 예는 [Bing News Search 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)을 참조하세요.
