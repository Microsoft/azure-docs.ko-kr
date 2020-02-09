---
title: Bing Custom Search 엔드포인트
titleSuffix: Azure Cognitive Services
description: 관심이 있는 항목에 대 한 맞춤형 검색 환경을 만듭니다. 사용자는 관심 있는 콘텐츠에 맞게 조정 된 검색 결과를 볼 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072780"
---
# <a name="custom-search"></a>사용자 지정 검색
Bing Custom Search를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다. 사용자는 관련 없는 콘텐츠가 포함된 검색 결과를 페이징할 필요 없이 관심 있는 콘텐츠에 맞게 조정된 검색 결과를 보게 됩니다.

## <a name="custom-search-endpoint"></a>Custom Search 엔드포인트
Bing Custom Search API를 사용하여 결과를 가져오려면 `GET` 요청을 다음 엔드포인트에 전송합니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

엔드포인트: `?q=""`에서 정의된 사용자 입력과 관련된 JSON 결과로 검색 제안을 반환합니다.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Custom Search 원본을 설정하는 방법을 설명하는 예제는 [자습서](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)를 참조하세요. 헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Custom Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) 참조를 참조하세요.

## <a name="custom-search-response-json"></a>Custom Search 응답 JSON
사용자 지정 검색 요청은 결과를 JSON 개체로 반환합니다. [응답 개체](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects)를 참조하세요. 

## <a name="custom-autosuggest"></a>Custom Autosuggest
Custom Autosuggest API를 사용하면 부분 검색 쿼리 용어를 Bing에 전송하고 구성할 수 있는 제안된 쿼리 목록을 받을 수 있습니다. Custom Autosuggest를 사용하는 경우 API에서 반환된 제안을 추가하고, 선택적으로 Bing에서 생성된 제안을 포함할지 여부를 지정합니다.

## <a name="custom-autosuggest-endpoint"></a>Custom Autosuggest 엔드포인트
사용자 지정 쿼리 제안을 요청하려면 GET 요청을 다음에 전송합니다.

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

사용자 지정 제안 정의에 대한 자세한 내용은 [사용자 지정 검색 제안 정의](define-custom-suggestions.md)를 참조하세요.

## <a name="custom-image-search"></a>Custom Image Search
Custom Image Search API를 사용하면 검색 쿼리를 Bing에 전송하고 Custom Search 인스턴스에서 관련 이미지 목록을 받을 수 있습니다.

## <a name="custom-image-search-endpoint"></a>Custom Image Search 엔드포인트
Custom Search 인스턴스에서 이미지를 요청하려면 GET 요청을 다음 URL에 전송합니다.

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Custom Search 인스턴스 구성에 대한 자세한 내용은 [사용자 지정 검색 환경 구성](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view)을 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트은 결과를 JSON 응답 개체로 반환 합니다.  모든 엔드포인트은 경도, 위도 및 검색 반지름을 기준으로 특정 언어 및/또는 위치를 반환 하는 쿼리를 지원 합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Custom Search API를 사용하는 기본 요청의 예는 [Custom Search 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)을 참조하세요.
