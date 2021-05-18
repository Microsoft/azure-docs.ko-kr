---
title: Bing Video Search API에 검색 요청 전송
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 반환하는 JSON 응답 개체뿐만 아니라 Bing Video Search API로 전송된 요청의 매개 변수와 특성에 대해서도 설명합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: aahi
ms.openlocfilehash: 78a3c3c2936bfcaf58ea9e46bd3d6a610cdfe436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341498"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Bing Video Search API에 검색 요청 전송

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 문서에서는 반환하는 JSON 응답 개체뿐만 아니라 Bing Video Search API로 전송된 요청의 매개 변수와 특성에 대해서도 설명합니다. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Bing Autosuggest API를 사용한 검색어 제안

사용자가 자신의 검색 용어를 입력할 수 있는 검색 상자를 제공하는 경우 [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md)를 사용하여 환경을 개선합니다. API는 부분 검색 용어 기반의 제안된 쿼리 문자열을 사용자 형식으로 반환합니다.

사용자가 검색어를 입력하면 [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) 쿼리 매개 변수를 설정하기 전에 이를 URL로 인코딩합니다. 예를 들어 사용자가 입력 *소형 범선* 을 입력한 경우 `q`를 `sailing+dinghies` 또는 `sailing%20dinghies`로 설정합니다.

## <a name="sending-a-request"></a>요청 보내기

비디오 검색 결과를 가져오려면 다음 엔드포인트로 GET 요청을 보냅니다.  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청은 서버에서 시작되는 것이 좋습니다. 클라이언트 애플리케이션의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 애플리케이션에 액세스할 가능성이 높아집니다. 또한 서버에서 호출하면 향후 API 버전을 위한 단일 업그레이드 지점이 제공됩니다.

  
요청에서 사용자의 검색어가 포함된 [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) 쿼리 매개 변수를 지정해야 합니다. 선택 사항이지만, 요청에서 결과를 가져올 지역/국가를 식별하는 [mkt](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) 쿼리 매개 변수도 지정해야 합니다. `pricing` 같은 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters)를 참조하세요. 모든 쿼리 매개 변수 값은 URL로 인코드되어야 합니다.  
  
요청에서 [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

클라이언트 IP 및 위치 헤더는 위치 인식 콘텐츠를 반환하는 데 중요합니다.  

모든 요청 및 응답 헤더 목록은 [헤더](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers)를 참조하세요.

## <a name="example-search-request"></a>예제 검색 요청

다음은 모든 제안된 쿼리 매개 변수 및 헤더를 포함하는 검색 요청을 보여 줍니다. Bing API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 전에 Bing API를 호출하고 Bing이 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>예제 JSON 응답

다음은 이전 요청에 대한 응답을 보여줍니다. 또한 이 예제는 Bing 관련 응답 헤더를 보여 줍니다.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

API를 사용해 봅니다. [Video Search API 테스트 콘솔](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8)로 이동합니다. 

응답 개체 사용에 대한 자세한 내용은 [웹에서 비디오 검색](../overview.md)을 참조하세요.

관련 검색 등 비디오에 대한 인사이트를 가져오는 방법에 대한 자세한 내용은 [비디오 인사이트](../video-insights.md)를 참조하세요.  
  
소셜 미디어에서 유행하는 비디오에 대한 자세한 내용은 [인기 비디오](../trending-videos.md)를 참조하세요.