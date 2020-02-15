---
title: '자습서: 단일 페이지 웹앱 만들기 - Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: 이 단일 페이지 앱은 Bing Web Search API를 사용하여 단일 페이지 앱에서 관련 검색 결과를 검색, 구문 분석 및 표시하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1acc17f9c2fbeb53b992891174866433d14f128d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986664"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>자습서: Bing Web Search API를 사용하여 단일 페이지 앱 만들기

이 단일 페이지 앱은 Bing Web Search API에서 검색 결과를 검색, 구문 분석 및 표시하는 방법을 보여 줍니다. 자습서에서는 상용구 HTML 및 CSS를 사용하고 JavaScript 코드에 초점을 맞춥니다. HTML, CSS 및 JS 파일은 빠른 시작 지침이 있는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)에서 사용할 수 있습니다.

이 샘플 앱은 다음을 수행할 수 있습니다.

> [!div class="checklist"]
> * 검색 옵션을 사용하여 Bing Web Search API 호출
> * 웹, 이미지, 뉴스 및 비디오 결과 표시
> * 결과에 페이지 매기기
> * 구독 키 관리
> * 오류 처리

이 앱을 사용하려면 Bing Search API와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

앱을 실행하는 데 필요한 몇 가지 사항은 다음과 같습니다.

* Node.js 8 이상
* 구독 키

## <a name="get-the-source-code-and-install-dependencies"></a>소스 코드 가져오기 및 종속성 설치

첫 번째 단계는 샘플 앱의 소스 코드를 사용하여 리포지토리를 복제하는 것입니다.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

그런 다음, `npm install`을 실행합니다. 이 자습서에서는 Express.js가 유일한 종속성입니다.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>앱 구성 요소

빌드 중인 샘플 앱은 다음 4개 부분으로 구성됩니다.

* `bing-web-search.js` - Express.js 앱입니다. 이 앱은 요청/응답 논리 및 라우팅을 처리합니다.
* `public/index.html` - 앱의 뼈대로서, 사용자에게 데이터가 제공되는 방식을 정의합니다.
* `public/css/styles.css` - 글꼴, 색, 텍스트 크기 등의 페이지 스타일을 정의합니다.
* `public/js/scripts.js` - Bing Web Search API에 대한 요청을 수행하고, 구독 키를 관리하고, 응답을 처리 및 구문 분석하고, 결과를 표시하는 논리를 포함합니다.

이 자습서는 `scripts.js`와 함께 Bing Web Search API를 호출하고 응답을 처리하는 데 필요한 논리에 초점을 맞춥니다.

## <a name="html-form"></a>HTML 양식

`index.html`에는 사용자가 검색 옵션을 검색 및 선택할 수 있는 양식이 포함됩니다. `onsubmit` 특성은 양식이 제출될 때 실행되어 `scripts.js`에 정의된 `bingWebSearch()` 메서드를 호출합니다. 3가지 인수를 사용합니다.

* 검색 쿼리
* 선택된 옵션
* 구독 키

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>쿼리 옵션

HTML 양식에는 [Bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters)의 쿼리 매개 변수에 매핑되는 옵션이 포함됩니다. 이 표에서는 사용자가 샘플 앱을 사용하여 검색 결과를 필터링하는 방법을 구분합니다.

| 매개 변수 | Description |
|-----------|-------------|
| `query` | 쿼리 문자열을 입력할 텍스트 필드입니다. |
| `where` | 시장(위치 및 언어)을 선택하기 위한 드롭다운 메뉴입니다. |
| `what` | 특정 결과 형식의 수준을 올리기 위한 확인란입니다. 예를 들어 이미지의 수준을 올리면 검색 결과에서 이미지 순위가 높아집니다. |
| `when` | 사용자가 검색 결과를 오늘, 이번 주 또는 이번 달로 제한할 수 있는 드롭다운 메뉴입니다. |
| `safe` | 성인 콘텐츠를 필터링하는 Bing 유해 정보 차단을 사용하도록 설정하는 확인란입니다. |
| `count` | 숨겨진 필드입니다. 각 요청에서 반환할 검색 결과 수입니다. 페이지당 더 적거나 더 많은 결과를 표시하려면 이 값을 변경합니다. |
| `offset` | 숨겨진 필드입니다. 요청의 첫 번째 검색 결과에 대한 오프셋으로, 페이징에 사용됩니다. 각각의 새 요청을 사용하여 `0`으로 재설정됩니다. |

> [!NOTE]
> Bing Web Search API는 검색 결과를 구체화하는 데 도움이 되는 추가 쿼리 매개 변수를 제공합니다. 이 샘플에는 몇 가지만 사용됩니다. 사용 가능한 매개 변수의 전체 목록은 [Bing Web Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters)를 참조하세요.

`bingSearchOptions()` 함수는 Bing Search API에 필요한 형식과 일치하도록 이러한 옵션을 변환합니다.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`moderate`를 Bing Web Search의 기본 설정으로 사용하여 `SafeSearch`를 `strict`, `moderate` 또는 `off`로 설정할 수 있습니다. 이 양식은 `strict` 또는 `moderate`의 두 개의 상태가 있는 확인란을 사용합니다.

**수준 올리기** 확인란을 선택하면 `answerCount` 매개 변수가 쿼리에 추가됩니다. `promote` 매개 변수를 사용할 때 `answerCount`가 필요합니다. 이 코드 조각에서 값은 모든 사용 가능한 결과 형식을 반환하도록 `9`로 설정됩니다.
> [!NOTE]
> 결과 형식의 수준을 올려도 검색 결과에 포함되도록 *보장*되지 않습니다. 대신 프로모션은 일반적인 해당 순위를 기준으로 이러한 종류의 결과 순위를 높입니다. 특정 종류의 결과에 대한 검색을 제한하려면 `responseFilter` 쿼리 매개 변수를 사용하거나 Bing Image Search 또는 Bing News Search와 같은 구체적인 엔드포인트를 호출합니다.

`textDecoration` 및 `textFormat` 쿼리 매개 변수는 스크립트로 하드 코드되고 검색 용어가 검색 결과에 굵게 지정되도록 합니다. 이러한 매개 변수는 필요하지 않습니다.

## <a name="manage-subscription-keys"></a>구독 키 관리

Bing Search API 구독 키의 하드 코드를 방지하기 위해 이 샘플 앱은 브라우저의 영구적 스토리지를 사용하여 구독 키를 저장합니다. 구독 키가 저장되지 않으면 사용자에게 구독 키를 입력하라는 메시지가 표시됩니다. 구독 키가 API에서 거부되면 사용자에게 구독 키를 다시 입력하라는 메시지가 표시됩니다.

`getSubscriptionKey()` 함수는 `storeValue` 및 `retrieveValue` 함수를 사용하여 사용자 구독 키를 저장 및 검색합니다. 이러한 함수는 `localStorage` 개체(지원되는 경우) 또는 쿠키를 사용합니다.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

앞에서 살펴본 대로 양식이 제출되면 `onsubmit`이 실행되어 `bingWebSearch`를 호출합니다. 이 함수는 요청을 초기화하고 보냅니다. 요청을 인증하기 위해 제출할 때마다 `getSubscriptionKey`가 호출됩니다.

## <a name="call-bing-web-search"></a>Bing Web Search 호출

쿼리, 옵션 문자열 및 구독 키가 제공되면 `BingWebSearch` 함수는 `XMLHttpRequest` 개체를 만들어 Bing Web Search 엔드포인트를 호출합니다.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

요청이 성공한 후 `load` 이벤트 처리기가 실행되고 `handleBingResponse` 함수를 호출합니다. `handleBingResponse`는 결과 개체를 구문 분석하고, 결과를 표시하고, 실패한 요청에 대한 오류 논리를 포함합니다.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> 성공한 HTTP 요청이 검색 자체가 성공했음을 의미하는 것은 *아닙니다*. 검색 작업에 오류가 발생하는 경우 Bing Web Search API는 200 이외의 HTTP 상태 코드를 반환하고 JSON 응답에 오류 정보를 포함합니다. 요청의 속도가 제한된 경우 API에서 빈 응답을 반환합니다.

위의 두 함수에 포함된 코드는 대부분 오류 처리에만 사용됩니다. 다음 단계에서 오류가 발생할 수 있습니다.

| 단계 | 잠재적 오류 | 처리 방법 |
|-------|--------------------|------------|
| 요청 개체 빌드 | 잘못된 URL | `try` / `catch` 블록 |
| 요청하기 | 네트워크 오류, 중단된 연결 | `error` 및 `abort` 이벤트 처리기 |
| 검색 수행 | 잘못된 요청, 잘못된 JSON, 속도 제한 | `load` 이벤트 처리기에서 테스트 |

오류는 `renderErrorMessage()`를 호출하여 처리됩니다. 응답이 모든 오류 테스트에 성공하면 `renderSearchResults()`가 호출되어 검색 결과를 표시합니다.

## <a name="display-search-results"></a>검색 결과 표시

Bing Web Search API에서 반환된 결과에 대한 [사용 및 표시 요구 사항](useanddisplayrequirements.md)이 있습니다. 응답에는 다양한 결과 형식이 포함될 수 있으므로 최상위 `WebPages` 컬렉션을 반복하기에 충분하지 않습니다. 대신에 샘플 앱은 `RankingResponse`를 사용하여 결과를 사양에 맞게 정렬합니다.

> [!NOTE]
> 단일 결과 형식만 필요한 경우 `responseFilter` 쿼리 매개 변수를 사용하거나 Bing Image Search와 같은 다른 Bing Search 엔드포인트 중 하나를 사용하는 것이 좋습니다.

각 응답에는 최대 3개의 컬렉션인 `pole`, `mainline` 및 `sidebar`가 포함될 수 있는 `RankingResponse` 개체가 있습니다. `pole`(있는 경우)은 관련성이 가장 높은 검색 결과이므로 잘 보이게 표시되어야 합니다. `mainline`은 대부분의 검색 결과를 포함하고 `pole` 바로 뒤에 표시됩니다. `sidebar`에는 보조 검색 결과가 포함됩니다. 가능하면 이러한 결과는 사이드바에 표시되어야 합니다. 화면 제한으로 인해 사이드바가 실용적이지 않은 경우 이러한 결과는 `mainline` 결과 뒤에 표시되어야 합니다.

각 `RankingResponse`에는 결과 정렬 방법을 지정하는 `RankingItem` 배열이 포함됩니다. 샘플 앱은 `answerType` 및 `resultIndex` 매개 변수를 사용하여 결과를 식별합니다.

> [!NOTE]
> 결과를 식별하고 순위를 지정하는 추가적인 방법이 있습니다. 자세한 내용은 [순위를 사용하여 결과 표시](rank-results.md)를 참조하세요.

코드를 살펴보겠습니다.

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` 함수는 각 `RankingResponse` 컬렉션의 항목을 반복하고, `answerType` 및 `resultIndex` 값을 사용하여 검색 결과에 각 순위 결과를 매핑하고, 해당하는 렌더링 함수를 호출하여 HTML을 생성합니다. `resultIndex`가 항목에 지정되지 않으면 `renderResultsItems()`는 해당 형식의 모든 결과를 반복하고 각 항목에 대한 렌더링 함수를 호출합니다. 결과 HTML이 페이지의 `index.html`의 해당하는 `<div>` 요소에 삽입됩니다.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>렌더러 함수 검토

샘플 앱에서 `searchItemRenderers` 개체에는 각 검색 결과 형식에 대한 HTML을 생성하는 함수가 포함됩니다.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> 샘플 앱에는 웹 페이지, 뉴스, 이미지, 비디오 및 관련 검색의 렌더러가 있습니다. 사용자 애플리케이션에는 수신할 수 있는 결과 형식에 대한 렌더러가 필요합니다. 여기에는 계산, 맞춤법 제안, 엔터티, 표준 시간대 및 정의가 포함될 수 있습니다.

일부 렌더링 함수는 `item` 매개 변수만 허용합니다. 다른 함수는 다양한 컨텍스트에 따라 항목을 렌더링하는 데 사용할 수 있는 추가 매개 변수를 허용합니다. 이 정보를 사용하지 않는 렌더러는 이러한 매개 변수를 허용할 필요가 없습니다.

컨텍스트 인수는 다음과 같습니다.

| 매개 변수  | Description |
|------------|-------------|
| `section` | 항목이 표시되는 결과 섹션(`pole`, `mainline` 또는 `sidebar`) |
| `index`<br>`count` | `RankingResponse` 항목이 지정된 컬렉션에 모든 결과가 표시되도록 지정한 경우에 사용할 수 있습니다. 그렇지 않으면 `undefined`입니다. 해당 컬렉션 내에 있는 항목의 인덱스 및 해당 컬렉션에 있는 총 항목 수. 이 정보를 사용하여 결과를 세고 첫 번째 또는 마지막 결과에 대해 다른 HTML을 생성할 수 있습니다. |

샘플 앱에서 `images` 및 `relatedSearches` 렌더러는 모두 컨텍스트 인수를 사용하여 생성한 HTML을 사용자 지정합니다. `images` 렌더러를 좀 더 자세히 살펴보겠습니다.

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

이미지 렌더러:

* 이미지 썸네일 크기를 계산합니다(높이가 60픽셀에 고정된 반면 너비는 다름).
* 컨텍스트에 따라 이미지 결과 앞에 오는 HTML을 삽입합니다.
* 이미지가 포함된 페이지로 연결되는 HTML `<a>` 태그를 빌드합니다.
* HTML `<img>` 태그를 빌드하여 이미지 썸네일을 표시합니다.

이미지 렌더러는 `section` 및 `index` 변수를 사용하여 나타나는 위치에 따라 다르게 결과를 표시합니다. 줄 바꿈(`<br>` 태그)은 사이드바에서 이미지 결과 사이에 삽입되므로 사이드바가 이미지 열을 표시합니다. 다른 섹션에서 첫 번째 이미지 결과 `(index === 0)`는 `<p>` 태그가 앞에 옵니다.

썸네일 크기는 `<img>` 태그와 썸네일의 URL에 있는 `h` 및 `w` 필드 둘 다에 사용됩니다. `title` 및 `alt` 특성(이미지의 텍스트 설명)은 이미지의 이름 및 URL의 호스트 이름에서 생성됩니다.

이미지가 샘플 앱에 표시되는 방식의 예는 다음과 같습니다.

![[Bing 이미지 결과]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>클라이언트 ID 유지

Bing Search API의 응답에는 각 후속 요청과 함께 API로 다시 전송되어야 하는 `X-MSEdge-ClientID` 헤더가 포함될 수 있습니다. 앱에서 Bing Search API 중 두 개 이상을 사용할 경우 전체 서비스에서 각 요청과 함께 동일한 클라이언트 ID가 전송되는지 확인합니다.

`X-MSEdge-ClientID` 헤더를 제공하면 Bing API가 사용자 검색을 연결할 수 있습니다. 첫째, Bing 검색 엔진이 이전 컨텍스트를 검색에 적용하여 요청에 보다 만족스러운 결과를 찾을 수 있습니다. 예를 들어 사용자가 이전에 항해와 관련된 용어를 검색한 경우 나중에 “knots”를 검색하면 항해에 사용되는 노트에 대한 정보가 우선적으로 반환될 수 있습니다. 둘째, Bing을 통해 임의로 선택된 사용자가 새로운 기능이 폭넓게 사용되기 전에 이러한 새 기능을 경험해볼 수 있습니다. 각 요청에 동일한 클라이언트 ID를 제공하면 선택된 사용자에게 항상 이를 보는 기능이 표시됩니다. 클라이언트 ID가 없으면 사용자의 검색 결과에서 기능이 임의로 나타나거나 사라지는 것처럼 보일 수 있습니다.

CORS(원본 간 리소스 공유)와 같은 브라우저 보안 정책으로 인해 샘플 앱이 `X-MSEdge-ClientID` 헤더에 액세스하지 못할 수 있습니다. 이러한 제한은 검색 응답의 원본이 해당 응답을 요청한 페이지와 다른 경우에 발생합니다. 프로덕션 환경에서는 웹 페이지와 동일한 도메인에 대해 API 호출을 수행하는 서버 쪽 스크립트를 호스트하여 이 정책 문제를 해결해야 합니다. 스크립트의 원본은 웹 페이지와 동일하므로 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용할 수 있습니다.

> [!NOTE]
> 그래도 프로덕션 웹 애플리케이션의 경우 서버 쪽에서 요청을 수행해야 합니다. 그렇지 않은 경우 Bing Search API 구독 키를 웹 페이지에 포함해야만 원본을 보는 누구나 사용할 수 있게 됩니다. 권한 없는 사람이 수행한 요청을 포함하여 API 구독 키를 통한 모든 사용량에 요금이 청구되므로, 키를 노출하지 않는 것이 중요합니다.

개발 목적으로 CORS 프록시를 통해 요청을 만들 수 있습니다. 이 프록시 유형의 응답에는 응답 헤더를 허용 목록에 추가하고 JavaScript에서 응답 헤더를 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 포함됩니다.

샘플 앱이 클라이언트 ID 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 다음 명령을 실행합니다.

```console
npm install -g cors-proxy-server
```

다음으로, `script.js`에서 Bing Web Search 엔드포인트를 변경합니다.

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

다음 명령을 사용하여 CORS 프록시를 시작합니다.

```console
cors-proxy-server
```

샘플 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래 확장 가능한 HTTP 헤더 섹션에 `X-MSEdge-ClientID` 헤더가 표시되어야 합니다. 헤더가 각 요청에 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Web Search API v7 참조](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
