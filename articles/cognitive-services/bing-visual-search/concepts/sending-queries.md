---
title: 검색 쿼리를 Bing Visual Search API로 보내기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 응답 개체뿐만 아니라 Bing Visual Search API로 전송된 요청의 매개 변수와 특성에 대해서도 설명합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 5d27aa80a63232694e1c9951f98b2191ba575e74
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913063"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>검색 쿼리를 Bing Visual Search API로 보내기

이 문서에서는 응답 개체뿐만 아니라 Bing Visual Search API로 전송된 요청의 매개 변수와 특성에 대해서도 설명합니다. 

다음 세 가지 방법으로 이미지에 대한 인사이트를 가져올 수 있습니다.

- [Bing Image Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 엔드포인트 중 하나에 대 한 이전 호출의 이미지에서 가져온 정보 토큰을 사용 합니다.
- 이미지의 URL 보내기
- 이미지 업로드 (이진 형식)

## <a name="bing-visual-search-requests"></a>Bing Visual Search 요청

이미지 토큰 또는 URL Visual Search를 전송 하는 경우 다음 코드 조각에서는 POST 본문에 포함 해야 하는 JSON 개체를 보여 줍니다.

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` 개체에는 `url` 또는 `imageInsightsToken` 필드 중 하나만 포함되어야 합니다. `url` 필드를 인터넷에서 액세스할 수 있는 이미지의 URL로 설정 합니다. 지원되는 최대 이미지 크기는 1MB입니다.

`imageInsightsToken`을 인사이트 토큰으로 설정해야 합니다. 인사이트 토큰을 가져오려면 Bing Image API를 호출합니다. 응답에는 `Image` 개체 목록이 포함됩니다. 각 `Image` 개체에는 토큰을 포함하는 `imageInsightsToken` 필드가 포함됩니다.

`cropArea` 필드는 선택 사항입니다. 자르기 영역은 관심 영역의 왼쪽 위 모퉁이와 오른쪽 아래 모퉁이를 지정 합니다. 0\.0~1.0 범위의 값을 지정합니다. 값은 전체 너비 또는 높이의 백분율입니다. 예를 들어 위 예제에서는 이미지의 오른쪽 절반을 관심 영역으로 표시합니다. 인사이트 요청을 관심 영역으로 제한하려면 포함합니다.

`filters` 개체에는 유사한 이미지 및 유사한 제품 결과를 특정 도메인으로 제한하는 데 사용할 수 있는 사이트 필터(`site` 필드 참조)가 포함되어 있습니다. 예를 들어 이미지가 Surface Book 인 경우 `site`를 `www.microsoft.com`으로 설정할 수 있습니다.

이미지의 로컬 사본에 대한 인사이트를 가져오려면 이미지를 이진 데이터로 업로드합니다.

POST 본문에 이러한 옵션을 포함하는 방법에 대한 자세한 내용은 [콘텐츠 양식 유형](#content-form-types)을 참조하세요.

### <a name="search-endpoint"></a>Search 엔드포인트

Visual Search 엔드포인트는 https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch입니다.

요청은 HTTP POST 요청으로만 전송되어야 합니다.

### <a name="query-parameters"></a>쿼리 매개 변수

다음은 요청에서 지정해야 하는 쿼리 매개 변수입니다. 최소한 `mkt` 쿼리 매개 변수를 포함 해야 합니다.

| 이름 | 값 | 유형 | 필수 |
| --- | --- | --- | --- |
| <a name="cc" />cc  | 결과를 가져올 위치를 나타내는 두 자리 국가 코드입니다.<br /><br /> 이 매개 변수를 설정하는 경우 [Accept-Language](#acceptlanguage) 헤더도 지정해야 합니다. Bing은 언어 목록에서 찾은 첫 번째 지원되는 언어를 사용하고, 지정한 국가 코드와 언어를 결합하여 결과를 반환할 지역/국가를 결정합니다. 언어 목록에 지원되는 언어가 없으면 Bing은 요청을 지원하는 가장 가까운 언어와 지역/국가를 찾습니다. 또는 지정된 지역/국가 대신 집계 또는 기본 지역/국가를 결과에 사용할 수 있습니다.<br /><br /> 여러 언어를 지정하는 경우에만 이 쿼리 매개 변수와 `Accept-Language` 쿼리 매개 변수를 사용해야 합니다. 여러 언어를 지정하지 않는 경우 `mkt` 및 `setLang` 쿼리 매개 변수를 사용해야 합니다.<br /><br /> 이 매개 변수와 [mkt](#mkt) 쿼리 매개 변수는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다. | String | 아닙니다.       |
| <a name="mkt" />mkt   | 결과가 나오는 지역/국가입니다. <br /><br /> **참고:** 알려진 경우 항상 시장을 지정 해야 합니다. 지역/국가를 지정하면 Bing이 요청을 라우팅하고 최적 응답을 반환하는 데 도움이 됩니다.<br /><br /> 이 매개 변수와 [cc](#cc) 쿼리 매개 변수는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다. | String | 예      |
| <a name="safesearch" />safeSearch | 성인 콘텐츠에 대 한 필터입니다. 다음은 대/소문자를 구분하지 않는 가능한 필터 값입니다.<br /><ul><li>Off&mdash;성인 텍스트 또는 이미지를 포함하는 웹 페이지를 반환합니다.<br /><br/></li><li>Moderate&mdash;성인 텍스트만 포함하고 성인 이미지는 포함하지 않는 웹 페이지를 반환합니다.<br /><br/></li><li>Strict&mdash;성인 텍스트 또는 이미지를 포함하는 웹 페이지를 반환하지 않습니다.</li></ul><br /> 기본값은 Moderate입니다.<br /><br /> **참고:** Bing의 성인 정책에 따라 `safeSearch`가 Strict로 설정되어야 하는 지역/국가에서 요청이 나온 경우 Bing은 `safeSearch` 값을 무시하고 Strict를 사용합니다.<br/><br/>**참고:** `site:` 쿼리 연산자를 사용 하는 경우 `safeSearch` 쿼리 매개 변수가 설정 된 항목에 관계 없이 응답에 성인 콘텐츠가 포함 될 수 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 컨텐츠를 지원하는 경우에만 `site:`를 사용합니다.  | String | 아닙니다.       |
| <a name="setlang" />setLang  | 사용자 인터페이스 문자열에 사용할 언어입니다. ISO 639-1 2 자 언어 코드를 사용 하 여 언어를 지정 합니다. 예를 들어 영어의 언어 코드는 EN입니다. 기본값은 EN(영어)입니다.<br /><br /> 선택 사항이지만, 언어를 항상 지정해야 합니다. 일반적으로, 사용자가 사용자 인터페이스 문자열을 다른 언어로 표시하려는 경우가 아니면 `setLang`을 `mkt`에 지정된 것과 동일한 언어로 설정합니다.<br /><br /> 이 매개 변수와 [Accept-Language](#acceptlanguage) 헤더는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다.<br /><br /> 사용자 인터페이스 문자열은 사용자 인터페이스에서 레이블로 사용되는 문자열입니다. JSON 응답 개체에는 몇 개의 사용자 인터페이스 문자열이 있습니다. 또한, 응답 개체에서 Bing.com 속성에 대한 링크는 지정된 언어를 적용합니다. | String | 아닙니다.   |

## <a name="headers"></a>headers

다음은 요청에서 지정해야 하는 헤더입니다. `Content-Type` 및 `Ocp-Apim-Subscription-Key` 헤더는 유일 하 게 필요한 헤더 이지만 `User-Agent`, `X-MSEdge-ClientID`, `X-MSEdge-ClientIP`및 `X-Search-Location`도 포함 해야 합니다.

| 헤더 | Description |
| --- | --- |
| <a name="acceptlanguage" />Accept-Language  | 선택적 요청 헤더입니다.<br /><br /> 사용자 인터페이스 문자열에 사용할 언어의 쉼표로 구분된 목록입니다. 목록은 기본 설정의 내림차순으로 표시됩니다. 필요한 형식을 포함하여 자세한 내용은 [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조하세요.<br /><br /> 이 헤더와 [setLang](#setlang) 쿼리 매개 변수는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다.<br /><br /> 이 헤더를 설정하는 경우, [cc](#cc) 쿼리 매개 변수도 지정해야 합니다. 결과를 반환할 지역/국가를 결정하기 위해 Bing은 목록에서 찾은 첫 번째 지원되는 언어를 사용하고, 이 언어를 `cc` 매개 변수 값과 결합합니다. 목록에 지원되는 언어가 없으면 Bing은 요청을 지원하는 가장 가까운 언어와 지역/국가를 찾거나 집계 또는 기본 지역/국가를 결과에 사용합니다. Bing에서 사용한 시장을 확인 하려면 `BingAPIs-Market` 헤더를 참조 하세요.<br /><br /> 여러 언어를 지정하는 경우에만 이 헤더와 `cc` 쿼리 매개 변수를 사용합니다. 여러 언어를 지정하지 않는 경우 [mkt](#mkt) 및 [setLang](#setlang) 쿼리 매개 변수를 사용합니다.<br /><br /> 사용자 인터페이스 문자열은 사용자 인터페이스에서 레이블로 사용되는 문자열입니다. JSON 응답 개체에는 몇 개의 사용자 인터페이스 문자열이 있습니다. 응답 개체에서 Bing.com 속성에 대한 링크는 지정된 언어를 적용합니다.  |
| <a name="contenttype" />Content-Type  |     |
| <a name="market" />BingAPIs-Market    | 응답 헤더입니다.<br /><br /> 요청에서 사용하는 지역/국가입니다. 형식은 \<languageCode\>-\<countryCode\>입니다. 예를 들어 en-US입니다.  |
| <a name="traceid" />BingAPIs-TraceId  | 응답 헤더입니다.<br /><br /> 요청의 세부 정보를 포함하는 로그 항목의 ID입니다. 오류가 발생하면 이 ID를 캡처합니다. 문제를 확인하고 해결할 수 없는 경우 지원 팀에게 제공할 다른 정보와 함께 이 ID를 기재합니다. |
| <a name="subscriptionkey" />Ocp-Apim-Subscription-Key | 필수 요청 헤더입니다.<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/)에서 이 서비스에 등록할 때 받은 구독 키입니다. |
| <a name="pragma" />Pragma |   |
| <a name="useragent" />User-Agent  | 선택적 요청 헤더입니다.<br /><br /> 요청을 시작하는 사용자 에이전트입니다. Bing은 사용자 에이전트를 사용하여 모바일 사용자에게 최적화된 환경을 제공합니다. 선택 사항이지만, 이 헤더를 항상 지정하는 것이 좋습니다.<br /><br /> user-agent는 일반적으로 사용되는 브라우저에서 보내는 것과 동일한 문자열이어야 합니다. 사용자 에이전트에 대 한 자세한 내용은 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조 하십시오.<br /><br /> 다음은 user-agent 문자열의 예입니다.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid" />X-MSEdge-ClientID  | 선택적 요청 및 응답 헤더입니다.<br /><br /> Bing은 이 헤더를 사용하여 Bing API 호출에서 일관된 동작을 사용자에게 제공합니다. Bing은 새로운 기능과 개선 사항을 자주 플라이트하며, 다른 플라이트의 트래픽을 할당하기 위해 클라이언트 ID를 키로 사용합니다. 여러 요청에서 사용자에 대해 동일한 클라이언트 ID를 사용하지 않는 경우 Bing은 여러 개의 충돌하는 플라이트에 사용자를 할당할 수 있습니다. 여러 개의 충돌하는 플라이트에 할당되면 일관되지 않은 사용자 경험이 발생할 수 있습니다. 예를 들어 두 번째 요청의 플라이트 할당이 첫 번째 요청과 다른 경우 예기치 않은 경험이 발생할 수 있습니다. 또한 Bing은 클라이언트 ID를 사용하여 웹 결과를 해당 클라이언트 ID의 검색 기록에 맞게 조정하고 사용자에게 보다 풍부한 경험을 제공할 수 있습니다.<br /><br /> 또한 Bing은 이 헤더를 통해 클라이언트 ID에서 생성된 활동을 분석하여 결과 순위를 개선합니다. 관련성 개선은 Bing API에서 제공하는 결과 품질 향상에 도움이 되며, API 소비자의 클릭 광고율도 높아집니다.<br /><br /> **중요:** 선택 사항이지만, 이 헤더를 필수로 간주해야 합니다. 동일한 일반 사용자 및 디바이스 조합에 대해 여러 요청에서 클라이언트 ID를 유지하면 1) API 소비자에게 일관된 사용자 환경이 수신되고, 2) Bing API의 결과 품질 향상을 통해 클릭 광고율이 높아집니다.<br /><br /> 다음은 이 헤더에 적용되는 기본 사용 규칙입니다.<br /><ul><li>디바이스에서 애플리케이션을 사용하는 각 사용자에게 Bing에서 생성된 고유한 클라이언트 ID가 있어야 합니다.<br /><br/>이 헤더를 요청에 포함하지 않으면 Bing은 ID를 생성하고 X-MSEdge-ClientID 응답 헤더에 반환합니다. 요청에 이 헤더를 포함해서는 안 되는 경우는 사용자가 해당 디바이스에서 앱을 처음 사용할 때뿐입니다.<br /><br/></li><li>**주의:** 이 클라이언트 ID를 인증된 사용자 계정 정보에 연결할 수 없도록 해야 합니다.</li><li>앱이 디바이스에서 이 사용자와 관련하여 수행하는 각 Bing API 요청에 대해 클라이언트 ID를 사용합니다.<br /><br/></li><li>클라이언트 ID를 유지합니다. 브라우저 앱에서 ID를 유지하려면 영구적 HTTP 쿠키를 사용하여 모든 세션에서 해당 ID가 사용되도록 합니다. 세션 쿠키를 사용하면 안 됩니다. 모바일 앱과 같은 다른 앱의 경우 디바이스의 영구적 스토리지를 사용하여 ID를 유지합니다.<br /><br/>다음에 사용자가 해당 디바이스에서 앱을 사용하는 경우 유지된 클라이언트 ID를 가져옵니다.</li></ul><br /> **참고:** Bing 응답에 이 헤더가 포함될 수도 있고, 포함되지 않을 수도 있습니다. 응답에 이 헤더가 포함된 경우, 클라이언트 ID를 캡처하여 해당 디바이스에서 사용자에 대해 수행되는 모든 후속 Bing 요청에 사용합니다.<br /><br /> **참고:** X-MSEdge-ClientID를 포함하는 경우 요청에 쿠키를 포함하면 안 됩니다. |
| <a name="clientip" />X-MSEdge-ClientIP   | 선택적 요청 헤더입니다.<br /><br /> 클라이언트 디바이스의 IPv4 또는 IPv6 주소입니다. IP 주소는 사용자 위치를 검색하는 데 사용됩니다. Bing은 위치 정보를 사용하여 유해 정보 차단 동작을 결정합니다.<br /><br /> **참고:** 선택 사항이지만, 이 헤더와 X-Search-Location 헤더를 항상 지정하는 것이 좋습니다.<br /><br /> 주소를 난독 처리(예: 마지막 8진수를 0으로 변경)하면 안 됩니다. 주소를 난독 처리하면 디바이스의 실제 위치 근처에 있는 위치로 지정되지 않으므로 Bing이 잘못된 결과를 제공할 수 있습니다. |
| <a name="location" />X-Search-Location   | 선택적 요청 헤더입니다.<br /><br /> 클라이언트의 지리적 위치를 설명하는 키/값 쌍의 세미콜론으로 구분된 목록입니다. Bing은 위치 정보를 사용하여 유해 정보 차단 동작을 결정하고 관련된 로컬 콘텐츠를 반환합니다. 키/값 쌍을 \<키\>:\<값\>으로 지정합니다. 다음은 사용자 위치를 지정하는 데 사용하는 키입니다.<br /><br /><ul><li>lat&mdash;필수. 클라이언트 위치의 위도(도)입니다. 위도는 -90.0보다 크거나 같고 +90.0보다 작거나 같아야 합니다. 음수 값은 남위를 나타내고, 양수 값은 북위를 나타냅니다.<br /><br /></li><li>long&mdash;필수. 클라이언트 위치의 경도(도)입니다. 경도는 -180.0보다 크거나 같고 +180.0보다 작거나 같아야 합니다. 음수 값은 서경을 나타내고, 양수 값은 동경을 나타냅니다.<br /><br /></li><li>re&mdash;필수. 좌표의 가로 정확도를 지정하는 반경(미터)입니다. 디바이스의 위치 서비스에서 반환된 값을 전달합니다. 일반적인 값은 GPS/Wi-fi의 경우 22 m, 셀 타워 삼각 측정의 경우 380 m, 역방향 IP 조회의 경우 18000 m 일 수 있습니다.<br /><br /></li><li>ts&mdash;선택 사항. 클라이언트가 위치에 있었던 시간의 UTC UNIX 타임스탬프입니다. UNIX 타임스탬프는 1970년 1월 1일 이후의 시간(초)입니다.<br /><br /></li><li>head&mdash;선택 사항입니다. 클라이언트 상대적인 이동 방향입니다. 정북을 기준으로 시계 반대 방향으로 계산해서, 이동 방향을 0에서 360 사이의 도로 지정합니다. `sp` 키가 0이 아닌 경우에만 이 키를 지정합니다.<br /><br /></li><li>sp&mdash;선택 사항. 클라이언트 디바이스가 이동하는 가로 벡터(속도)(초당 미터)입니다.<br /><br /></li><li>alt&mdash;선택 사항. 클라이언트 디바이스의 고도(미터)입니다.<br /><br /></li><li>are&mdash;선택 사항입니다. 좌표의 세로 정확도를 지정하는 반경(미터)입니다. `alt` 키를 지정하는 경우에만 이 키를 지정합니다.<br /><br /></li></ul> **참고:** 많은 키는 선택 사항이지만, 제공하는 정보가 많을수록 위치 결과가 정확해집니다.<br /><br /> **참고:** 선택 사항이지만, 사용자의 지리적 위치를 항상 지정하는 것이 좋습니다. 클라이언트의 IP 주소가 사용자의 실제 위치를 정확하게 반영하지 않는 경우(예: 클라이언트가 VPN을 사용하는 경우) 위치를 제공하는 것이 특히 중요합니다. 최적의 결과를 얻으려면이 헤더와 `X-MSEdge-ClientIP` 헤더를 포함 해야 하지만 최소한이 헤더를 포함 해야 합니다.       |

> [!NOTE]
> [BING SEARCH API 사용 및 디스플레이 요구 사항은](../../bing-web-search/use-display-requirements.md) 이러한 헤더 사용과 관련 하 여 모든 관련 법률을 준수 해야 한다는 점에 주의 해야 합니다. 예를 들어 유럽 등의 특정 관할지에서는 사용자 디바이스에 특정 추적 디바이스를 배치하기 전에 사용자 동의를 받아야 하는 요구 사항이 있습니다.

<a name="content-form-types" />

### <a name="content-form-types"></a>콘텐츠 양식 유형

각 요청에는 `Content-Type` 헤더가 포함 되어야 합니다. 헤더는 `multipart/form-data; boundary=\<boundary string\>`로 설정 되어야 합니다. 여기서 \<경계 문자열\>은 폼 데이터의 경계를 식별 하는 고유 하 고 불투명 한 문자열입니다. `boundary=boundary_1234-abcd`)을 입력합니다.

이미지 토큰 또는 URL Visual Search를 전송 하는 경우 다음 코드 조각은 게시물의 본문에 포함 해야 하는 폼 데이터를 보여 줍니다. 양식 데이터는 `Content-Disposition` 헤더를 포함 해야 하며 `name` 매개 변수를 "knowledgeRequest"로 설정 해야 합니다. `imageInfo` 개체에 대 한 자세한 내용은 요청을 참조 하십시오.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

웹 및 특성 정보에 대 한 링크를 포함 하 여 업로드 하는 이미지의 주 엔터티에 대 한 자세한 정보를 보려면 헤더의 `enableEntityData` 특성을 `true`으로 설정 합니다. 이 필드는 `false` 기본적으로 합니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

로컬 이미지를 업로드 하는 경우 다음 코드 조각은 게시물의 본문에 포함 해야 하는 폼 데이터를 보여 줍니다. 양식 데이터는 `Content-Disposition` 헤더를 포함 해야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. `Content-Type` 헤더는 일반적으로 사용 되는 이미지 mime 형식으로 설정할 수 있습니다. 폼의 내용은 이미지의 이진 데이터입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다. 가장 큰 너비 또는 높이는 1,500픽셀 미만이어야 합니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

다음 코드 조각에서는 업로드 된 이미지의 관심 영역을 지정 하는 방법을 보여 줍니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>요청 예

다음 코드 조각은 이미지 토큰과 관심 영역을 전달 하는 전체 이미지 정보 요청을 보여 줍니다. /Images/search에 대 한 이전 호출에서 insights 토큰을 가져옵니다.

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Bing Visual Search 응답


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

이미지에 사용할 수 있는 인사이트가 있는 경우 응답에는 인사이트를 포함하는 `tags`가 하나 이상 포함됩니다. `image` 필드는 입력 이미지에 대 한 정보 토큰을 포함 합니다.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` 필드에는 표시 이름 및 작업(인사이트) 목록이 포함됩니다. 태그 중 하나에는 빈 문자열로 설정된 `displayName` 필드가 있습니다. 이 태그에는 이미지를 포함하는 웹 페이지, 시각적으로 유사한 이미지, 이미지에서 찾은 항목에 대한 쇼핑 소스 등의 기본 인사이트가 포함됩니다. 전체 이미지를 사용할 수 있기 때문에 기본 insights 태그는 관심 영역에 대 한 경계 상자를 포함 하지 않습니다.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

기본 정보 목록은 [기본 insights 태그](../default-insights-tag.md)를 참조 하세요.

나머지 태그에는 사용자가 관심을 가질 수 있는 기타 인사이트가 포함됩니다. 예를 들어 이미지에 텍스트가 포함된 경우 태그 중 하나에는 인식된 텍스트를 포함하는 TextResults 인사이트가 포함될 수 있습니다. 또는 이미지에서 엔터티 (즉, 문화권을 잘 알려진/인기 있는 사람, 장소 또는 사물)를 인식 하는 경우 태그 중 하나에서 엔터티를 식별할 수 있습니다. Visual Search는 입력 이미지에서 파생된 다양한 용어(태그) 집합도 반환합니다. 이러한 태그를 통해 사용자는 이미지에 있는 개념을 탐색할 수 있습니다. 예를 들어 입력 이미지가 유명 운동 선수인 경우 태그 중 하나는 스포츠 이미지에 대한 링크가 포함된 스포츠일 수 있습니다.

각 태그에는 인사이트를 분류하는 데 사용할 수 있는 표시 이름, 인사이트가 적용되는 관심 영역을 식별하는 경계 상자, 인사이트 자체 및 이미지 썸네일이 포함됩니다. 예를 들어 이미지가 운동복을 착용한 사람인 경우 태그 중 하나에는 운동복의 경계를 표시하고 VisualSearch 및 ProductVisualSearch 인사이트를 포함하는 경계 상자가 포함될 수 있습니다. 다른 태그에는 관련된 토픽의 이미지를 가져오는 /images/search API 요청의 URL 또는 사용자를 Bing.com 이미지 검색 결과로 이동하는 Bing.com 검색 URL을 포함하는 ImageResults 인사이트가 포함될 수 있습니다.

기본 인사이트 태그 이외의 모든 태그에는 이미지에서 관심 영역을 식별하는 경계 상자가 포함됩니다. 예를 들어 이미지에 인식된 사람이 여러 명 포함된 경우 태그에는 각 사람의 경계 상자가 포함될 수 있습니다. 또는 이미지에 인식된 의류 항목이 포함된 경우 태그에는 각 인식된 의류 항목의 경계 상자가 포함될 수 있습니다. 경계 상자를 사용하여 클릭 시 이미지의 해당 영역에 있는 콘텐츠에 대한 세부 정보를 제공하는 핫 스폿을 이미지 위에 만들 수 있습니다. 전체 이미지를 식별하는 경계 상자의 핫 스폿을 이미지에 포함하면 안 됩니다.

### <a name="text-recognition"></a>텍스트 인식

이미지에 서비스가 인식하는 텍스트가 포함된 경우 태그 중 하나에는 TextResults 인사이트(작업)가 포함됩니다. 인식 된 텍스트를 포함 하는 `displayName`입니다.

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

태그의 `displayName` 필드에는 ##TextRecognition이 포함되므로 UX에서 이 필드를 범주 제목으로 사용하면 안 됩니다. ##으로 시작하는 모든 표시 이름이 여기에 해당합니다. 대신, 작업의 표시 이름을 사용 합니다.

텍스트 인식은 전화 번호, 메일 주소 등 명함의 연락처 정보를 인식할 수도 있습니다. 경계 상자는 카드에서 연락처 정보의 위치를 식별합니다.

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

이미지에 문화권 잘 알려진/인기 있는 사람, 장소 또는 사물과 같은 인식 된 엔터티가 포함 된 경우 태그 중 하나에 엔터티 정보를 포함할 수 있습니다. `mainEntity` 및 `data` 필드는 `Content-Type` 헤더의 `enableEntityData` 특성이 `true`로 설정 된 경우에만 사용할 수 있습니다.

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>참고 항목

- [Bing Visual Search API란?](../overview.md)
- [자습서: 단일 페이지 웹 앱 Visual Search 만들기](../tutorial-bing-visual-search-single-page-app.md)
