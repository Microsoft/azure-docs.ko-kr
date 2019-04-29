---
title: 검색 상자-Azure Search에서에서 제안 사항 및 자동 완성 기능 추가
description: 확인 기를 만들고 사용 하 여 완료 된 단어 또는 구 검색 상자에 입력 하는 요청을 공식화 하 여 Azure Search의 미리 입력 쿼리 작업을 사용 합니다.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: ed2e0bd352823a932cfea719c18e05ae6c913621
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126745"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>예제: Azure Search 응용 프로그램에 제안 사항 또는 자동 완성 기능 추가

이 문서의 사용 방법 알아보기 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 및 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 검색---입력할 때 동작을 지 원하는 강력한 검색 상자를 빌드할 수 있습니다.

+ *제안* 제안 결과가 입력할 때 각 제안이 지금까지 입력 한 내용을 일치 하는 인덱스에서 단일 결과 생성 합니다. 

+ *자동 완성*, [미리 보기 기능](search-api-preview.md)를 "완료" 단어 또는 구 사용자가 현재 입력입니다. 결과 반환 하는 대신 실행할 결과 반환할 수 있습니다 하는 쿼리를 완료 합니다. 제안 사항이 있는 경우와 마찬가지로 완료 된 단어 또는 구 쿼리에서 않음에서 서술 된 인덱스에서 일치 항목을 합니다. 서비스는 인덱스에 0 개 결과 반환 하는 쿼리를 제공 하지 않습니다.

다운로드 하 고 샘플 코드 실행할 수 **DotNetHowToAutocomplete** 이러한 기능을 평가 합니다. 채워진 미리 작성 된 인덱스를 대상으로 하는 샘플 코드 [NYCJobs 데모 데이터](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)입니다. NYCJobs 인덱스에 포함 되어는 [Suggester 구문을](index-add-suggesters.md), 제안 또는 자동 완성을 사용 하 여에 대 한 요구 사항인 합니다. 샌드박스 서비스에서 호스트 되는 준비 된 인덱스를 사용할 수 있습니다 또는 [고유한 인덱스를 채우지](#configure-app) NYCJobs 샘플 솔루션에서 데이터 로더를 사용 합니다. 

합니다 **DotNetHowToAutocomplete** 샘플에서는 제안 및 둘 다에 자동 완성 기능을 모두 보여 줍니다. C# 및 JavaScript 언어 버전입니다. C#개발자는 ASP.NET MVC 기반 응용 프로그램을 단계별로 실행할 수는 [Azure Search.NET SDK](https://aka.ms/search-sdk)합니다. 자동 완성 및 제안 된 쿼리 호출 하기 위한 논리는 HomeController.cs 파일에 있습니다. JavaScript 개발자에 대 한 직접 호출을 포함 하는 IndexJavaScript.cshtml에서 동일한 쿼리 논리를 찾을 수 합니다 [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)합니다. 

프런트 엔드 사용자 환경을 기반으로 두 언어 버전에 대해 합니다 [jQuery UI](https://jqueryui.com/autocomplete/) 하 고 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) 라이브러리입니다. 이러한 라이브러리 빌드 제안 및 자동 완성을 지 원하는 검색 상자를 사용 합니다. 검색 상자에 수집 된 입력은 HomeController.cs 또는 IndexJavaScript.cshtml에 정의 된 대로 제안과 같은 자동 완성 작업을 사용 하 여 쌍을 이룹니다.

이 연습에서는 다음 작업을 안내합니다.

> [!div class="checklist"]
> * 제안 된 일치 항목 또는 자동 완성 사용 약관에 대 한 JavaScript 및 문제 요청에서 검색 입력된 상자 구현
> * C#를 HomeController.cs에서 제안 사항 및 자동 완성 동작을 정의 합니다.
> * JavaScript에서 동일한 기능을 제공 하는 직접 REST Api를 호출 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Search 서비스를 준비 된 NYCJobs 데모 인덱스를 호스트 하는 라이브 샌드박스 서비스를 사용 하는 솔루션 이므로이 연습의 선택 사항입니다. 이 예제에서는 사용자 고유의 search 서비스에서 실행 하려는 경우 참조 [NYC 작업 구성 인덱스](#configure-app) 지침에 대 한 합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

* 다운로드 합니다 [DotNetHowToAutoComplete 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)합니다.

샘플은 포괄적인, 포괄적 제안, 자동 완성, 패싯 탐색 및 클라이언트 쪽 캐싱 전체 설명은 샘플에서 제공 하는 기능에 대 한 주석을 확인 하 고 추가 정보를 검토 해야 합니다.

## <a name="run-the-sample"></a>샘플 실행

1. 오픈 **AutocompleteTutorial.sln** Visual Studio에서. 솔루션에는 NYC 작업 데모 인덱스에 대 한 연결을 사용 하 여 ASP.NET MVC 프로젝트를 포함합니다.

2. 원하는 브라우저에서 F5를 눌러 프로젝트를 실행하고 페이지를 로드합니다.

맨 위에 C# 또는 JavaScript를 선택하는 옵션이 표시됩니다. C# 옵션은 브라우저에서 HomeController를 호출 하 고 Azure Search.NET SDK를 사용 하 여 결과 검색 합니다. 

브라우저에서 직접 Azure Search REST API를 호출하는 JavaScript 옵션입니다. 이 옵션은 컨트롤러를 흐름 밖으로 꺼내기 때문에 일반적으로 성능이 눈에 띄게 높습니다. 요구 사항 및 언어 기본 설정에 부합하는 옵션을 선택할 수 있습니다. 각각에 대한 지침이 있는 페이지에 몇 가지 자동 완성 예제가 있습니다. 예제마다 테스트해볼 수 있는 몇 가지 권장 샘플 텍스트가 있습니다.  

각 검색 상자에 몇 자 입력해보고 결과를 확인합니다.

## <a name="search-box"></a>검색 상자

둘 다에 대해 C# JavaScript 버전에서 검색 상자 구현을 정확히 동일 합니다. 

엽니다는 **Index.cshtml** 폴더 \Views\Home 코드를 보려면 아래에 있는 파일:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

이 스타일 지정, JavaScript 및 자리 표시자 텍스트에서 참조 하는 ID에 대 한 클래스를 사용 하 여 간단한 입력된 텍스트 상자입니다.  매직은 포함 된 JavaScript입니다.

C# 언어 샘플 Index.cshtml에서 활용 하 여 JavaScript를 사용 합니다 [jQuery UI 자동 완성 라이브러리](https://jqueryui.com/autocomplete/)합니다. 이 라이브러리는 추천 단어를 검색 하는 MVC 컨트롤러에 대 한 비동기 호출 하 여 검색 상자에 자동 완성 환경을 추가 합니다. JavaScript 언어 버전은 IndexJavaScript.cshtml입니다. 검색 표시줄 뿐만 아니라 Azure search REST API 호출에 대 한 아래 스크립트를 포함합니다.

JQuery UI 자동 완성 함수를 제안에 대 한 요청을 전달를 호출 하는 첫 번째 예제에서는 JavaScript 코드를 살펴보겠습니다.

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

위의 코드는 jQuery UI 자동 완성 "example1a" 입력된 상자에 대 한 구성 페이지 로드 시 브라우저에서 실행 됩니다.  `minLength: 3`은 검색 상자에 3자 이상이 입력되었을 때만 권장 사항을 표시하도록 합니다.  원본 값이 중요합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

위의 줄 위치 검색 상자에서 표시할 항목의 목록을 가져오려면 jQuery UI 자동 완성 함수에 알려줍니다. MVC 프로젝트 이므로 쿼리 제안 (다음 섹션에서 제안 하는 방법에 대 한 자세한 내용은)를 반환 하는 것에 대 한 논리가 포함 된 HomeController.cs에서 제안 함수를 호출 합니다. 이 함수는 또한 컨트롤 강조 표시, 유사 항목 일치 및 용어에는 몇 가지 매개 변수를 전달 합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>샘플을 확장하여 유사 일치 지원

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 필수는 미리 입력 환경의 견고성 크게 개선 합니다. 소스 줄을 변경하여 유사 일치를 사용하도록 테스트해 보겠습니다.

다음 줄을

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

다음과 같이 변경합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

F5를 눌러 애플리케이션을 시작합니다.

"execative" 등의 항목을 입력하면 입력한 글자가 정확히 일치하지 않더라도 "executive"에 대한 결과가 불러지는 것을 확인합니다.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Azure Search 자동 완성에서 jQuery 자동 완성 지원

지금 UX 코드 검색을 중심에 제안으로 노출 되지 않도록 되었습니다. 다음 코드 블록을 Azure Search 자동 완성에 대 한 요청을 전달 jQuery UI 자동 완성 함수 (줄 91에 index.cshtml)를 보여 줍니다.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# 예제

이제 웹 페이지의 JavaScript 코드를 검토 했습니다 살펴보겠습니다 합니다 C# 실제로 Azure Search.NET SDK를 사용 하 여 제안 된 일치 항목을 검색 하는 서버 쪽 컨트롤러 코드입니다.

엽니다는 **HomeController.cs** 컨트롤러 디렉터리 아래에 있는 파일입니다. 

가장 먼저 알 수 있듯이 호출 되는 클래스의 맨 위에 있는 메서드인지 `InitSearch`합니다. 이 항목은 Azure Search 서비스에 대해 인증된 HTTP 인덱스 클라이언트가 만듭니다. 자세한 내용은 [.NET 응용 프로그램에서 Azure Search를 사용 하는 방법을](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)합니다.

41 줄에 Suggest 함수로를 확인할 수 있습니다. 기반이 되는 [DocumentsOperationsExtensions.Suggest 메서드](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview)합니다.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다. 메서드를 [SuggestParameters 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), 제안 API에 전달 되는 합니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

69 번 줄에서 자동 완성 기능을 확인할 수 있습니다. 기반이 되는 [DocumentsOperationsExtensions.Autocomplete 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview)합니다.

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

자동 완성 함수는 검색 용어 입력 합니다. 메서드를 [AutoCompleteParameters 개체](https://docs.microsoft.com/rest/api/searchservice/autocomplete)합니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

(선택 사항) Suggest 함수의 시작 부분에 중단점을 추가하고 코드를 단계별로 확인합니다. 메서드에서 반환 된 결과로 변환 되는 방법을 SDK에서 반환 된 응답을 확인 합니다.

페이지의 다른 예제 패싯 자동 완성 결과의 클라이언트쪽 캐싱을 지원 하 고 적중 항목 강조 표시를 추가 하려면 동일한 패턴을 따릅니다. 각각을 검토하여 작동 방식과 검색 환경에서의 활용 방법을 살펴봅니다.

## <a name="javascript-example"></a>JavaScript 예제

Javascript 자동 완성 기능 및 제안을 구현의 원본으로 URI를 사용 하 여 인덱스 및 작업을 지정 하는 REST API를 호출 합니다. 

JavaScript 구현은 검토 하려면 엽니다 **IndexJavaScript.cshtml**합니다. JQuery UI 자동 완성 함수는 검색 용어 입력을 수집, 검색 상자에도 사용을 검색 하려면 Azure Search에 대 한 비동기 호출 일치를 제안 하거나 용어 완료를 확인 합니다. 

첫 번째 예제의 JavaScript 코드를 살펴보겠습니다.

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

이 코드를, Home 컨트롤러를 호출하는 위의 에제와 비교해 보면 몇 가지 유사점을 확인할 수 있습니다.  `minLength` 및 `position`에 대한 자동 완성 구성은 정확히 동일합니다. 

여기서 큰 변화는 소스입니다. Home 컨트롤러의 제안 메서드를 호출 하는 대신 REST 요청을 JavaScript 함수에서 생성 되 고 Ajax를 사용 하 여 실행. 그러면 응답이 "성공" 처리되고 소스로 사용됩니다.

REST 호출 Uri를 사용 하 여 지정할 수 있는지 여부를 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 또는 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 호출이 수행 되 합니다. 다음 Uri는 9 및 10 줄에 각각입니다.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

148 줄에서 호출 하는 스크립트를 찾을 수 있습니다는 `autocompleteUri`합니다. 첫 번째 호출은 `suggestUri` 39 줄에 표시 됩니다.

> [!Note]
> JavaScript에서 REST 서비스를 호출 하는 제공 되는 REST api를 편리 하 게 설명을 위해 여기 있지만 모범 사례 또는 권장으로 해석 될 수 없습니다. API 키 및 끝점을 스크립트에 포함 된 스크립트에 해당 값을 읽을 수 있는 모든 사용자에 게 서비스 공격 거부가까지 서비스를 엽니다. 학습 목적으로 JavaScript를 사용 하는 안전 하 고 하는 동안 아마도 무료 서비스를 호스트 하는 인덱스의 권장 Java를 사용 하 여 또는 C# 프로덕션 코드에서 인덱싱 및 쿼리 작업에 대 한 합니다. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>서비스에서 실행 된 NYCJobs 구성

지금까지 호스 티 드 NYCJobs 데모 인덱스를 사용 했습니다. 전체 하려는 경우 모든 인덱스를 포함 하 여 코드에 대 한 가시성을 만들고 고유한 검색 서비스에서 인덱스를 로드 하려면 다음이 지침을 따릅니다.

1. [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 예제에 대 한 무료 서비스를 사용할 수 있습니다. 

   > [!Note]
   > 체험 Azure Search 서비스를 사용하는 경우 세 가지 인덱스로 제한됩니다. NYCJobs 데이터 로더는 두 개의 인덱스를 만듭니다. 서비스에 새 인덱스를 허용하는 공간이 있는지 확인합니다.

1. 다운로드 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 코드입니다.

1. NYCJobs 샘플 코드의 DataLoader 폴더를 엽니다 **DataLoader.sln** Visual Studio에서.

1. Azure Search 서비스에 대 한 연결 정보를 추가 합니다. DataLoader 프로젝트 안에서 App.config를 열고 Azure Search 서비스 및 Azure Search Service API 키에 맞게 TargetSearchServiceName 및 TargetSearchServiceApiKey appSettings를 변경합니다. 이 정보는 Azure Portal에서 찾을 수 있습니다.

1. F5 키를 눌러 두 개의 인덱스를 만들고 NYCJob 샘플 데이터를 가져오는 응용 프로그램을 시작 합니다.

1. 오픈 **AutocompleteTutorial.sln** 에서 Web.config를 편집 합니다 **AutocompleteTutorial** 프로젝트. SearchServiceName 및 SearchServiceApiKey 값 검색 서비스에 대 한 유효한 값으로 변경 합니다.

1. F5 키를 눌러 애플리케이션을 실행합니다. 샘플 웹 앱이 기본 브라우저에서 열립니다. 환경은 샌드박스 버전과 동일, 인덱스 및 데이터 서비스에서 호스팅됩니다.

## <a name="next-steps"></a>다음 단계

이 예제에서는 검색 상자 자동 완성 기능 및 제안을 지를 구축 하기 위한 기본 단계를 보여 줍니다. ASP.NET MVC 응용 프로그램을 빌드 및 Azure Search.NET SDK 또는 REST API를 사용 하 여 추천 단어를 검색할 수 있습니다 하는 방법을 살펴보았습니다.

다음 단계로, 제안 및 자동 완성 검색 환경을 통합을 시도 합니다. 다음 참조 문서를 사용 하는 데 도움이 됩니다.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Create Index REST API에서 패싯 인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index)

