---
title: Bing Autosuggest API에 요청 보내기
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API는 검색 상자에 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. 요청을 보내는 방법에 대해 자세히 알아보세요.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072858"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Bing Autosuggest API에 요청 보내기

애플리케이션에서 Bing Search API에 쿼리를 보내는 경우 Bing Autosuggest API를 사용하여 사용자의 검색 환경을 개선할 수 있습니다. Bing Autosuggest API는 검색 상자에 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. 애플리케이션의 검색 상자에 문자를 입력하면 제안이 드롭다운 목록으로 표시될 수 있습니다. 이 문서에서는 이 API에 요청을 전송하는 방법을 자세히 알아봅니다. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Autosuggest API 엔드포인트

**Bing Autosuggest API**에는 부분 검색어에서 제안된 쿼리 목록을 반환하는 하나의 엔드포인트가 포함됩니다.

Bing API를 사용하여 제안된 쿼리를 가져오려면 `GET` 요청을 다음 엔드포인트에 전송합니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트:** `?q=""`에서 정의된 사용자 입력과 관련된 JSON 결과로 검색 제안을 반환합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 참조에서 확인할 수 있습니다.

**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트은 결과를 JSON 응답 개체로 반환 합니다.
모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Autosuggest API를 사용하는 기본 요청의 예는 [Autosuggest 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)을 참조하세요.

## <a name="bing-autosuggest-api-requests"></a>Bing Autosuggest API 요청

> [!NOTE]
> * Bing Autosuggest API에 대한 요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청은 서버에서 시작되는 것이 좋습니다. 클라이언트 애플리케이션의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 애플리케이션에 액세스할 가능성이 높아집니다. 또한 서버에서 호출하면 향후 업데이트를 위한 단일 업그레이드 지점이 제공됩니다.

요청에서 사용자의 부분 검색어가 포함된 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) 쿼리 매개 변수를 지정해야 합니다. 선택 사항이지만, 결과를 가져올 지역/국가를 식별하는 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) 쿼리 매개 변수도 요청에서 지정해야 합니다. 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)를 참조하세요. 모든 쿼리 매개 변수 값은 URL로 인코드되어야 합니다.

요청에서는 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

클라이언트 IP 및 위치 헤더는 위치 인식 콘텐츠를 반환하는 데 중요합니다.

모든 요청 및 응답 헤더 목록은 [헤더](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)를 참조하세요.

> [!NOTE]
> JavaScript에서 Bing Autosuggest API를 호출하는 경우 브라우저의 기본 제공 보안 기능으로 인해 이러한 헤더 값에 액세스하지 못할 수 있습니다.

이 문제를 해결하려면 CORS 프록시를 통해 Bing Autosuggest API 요청을 수행할 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 허용 목록에 추가하고 이를 JavaScript에서 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 있습니다.

[자습서 앱](../tutorials/autosuggest.md)이 선택적 클라이언트 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 프롬프트에서 다음 명령을 입력합니다.

    npm install -g cors-proxy-server

HTML 파일에서 Bing Autosuggest API 엔드포인트를 다음으로 변경합니다.

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

    cors-proxy-server

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

요청에는 제안된 모든 쿼리 매개 변수 및 헤더가 포함되어야 합니다. 

다음 예제에서는 *sail*에 대해 제안된 쿼리 문자열을 반환하는 요청을 보여 줍니다.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Bing API 중 하나를 처음으로 호출한 경우 클라이언트 ID 헤더를 포함하지 마십시오. 이전에 Bing API를 호출했으며 Bing에서 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우에만 클라이언트 ID 헤더를 포함합니다.

다음 웹 제안 그룹은 위 요청에 대한 응답입니다. 그룹에는 각각에 `displayText`, `query` 및 `url` 필드가 있는 검색 쿼리 제안의 목록이 포함됩니다.

`displayText` 필드에는 검색 상자의 드롭다운 목록을 채우는 데 사용하는 제안된 쿼리가 포함됩니다. 응답에 포함된 모든 제안을 지정된 순서대로 표시해야 합니다.  

사용자가 드롭다운 목록에서 쿼리를 선택하면 이 쿼리를 사용하여 [Bing Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) 중 하나를 호출하고 결과를 직접 표시하거나, 반환된 `url` 필드를 사용하여 사용자를 Bing 결과 페이지로 보낼 수 있습니다.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>다음 단계

- [Bing Autosuggest란?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Bing Autosuggest API에서 제안된 검색어 가져오기](get-suggestions.md)
