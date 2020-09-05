---
title: 검색 상자에 자동 완성 및 제안 추가
titleSuffix: Azure Cognitive Search
description: 완성 된 용어나 구를 사용 하 여 검색 상자를 자동으로 자동 완성 하는 요청을 확인 기 하 고 작성 하 여 Azure Cognitive Search에서 검색 형식 쿼리 작업을 사용 하도록 설정 합니다. 제안 된 일치 항목을 반환할 수도 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: c0031b09dbb3335113cb52c9b3ec5e4fd4fa2758
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011582"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>클라이언트 앱에 자동 완성 및 제안 추가

검색 형식은 사용자가 시작한 쿼리의 생산성을 개선 하는 일반적인 기술입니다. Azure Cognitive Search에서는이 환경이 *자동 완성*을 통해 지원 되며,이는 부분 입력 ("microsoft"로 "마이크로" 완료)을 기반으로 용어 또는 구를 완료 합니다. 또 다른 양식은 *제안*: 일치 하는 문서의 짧은 목록 (세부 정보 페이지에 연결할 수 있도록 ID가 있는 책 제목 반환)입니다. 인덱스의 일치 항목에 대 한 자동 완성 및 제안이 모두 예측 됩니다. 서비스는 결과를 0으로 반환 하는 쿼리를 제공 하지 않습니다.

Azure Cognitive Search에서 이러한 환경을 구현 하려면 다음이 필요 합니다.

+ 백 엔드에서 *확인 기* 입니다.
+ 요청에 대 한 [자동 완성](/rest/api/searchservice/autocomplete) 또는 [제안](/rest/api/searchservice/suggestions) API를 지정 하는 *쿼리입니다* .
+ 클라이언트 앱에서 검색 형식 상호 작용을 처리 하는 *UI 컨트롤* 입니다. 이 목적을 위해 기존 JavaScript 라이브러리를 사용 하는 것이 좋습니다.

Azure Cognitive Search에서 자동 완성 쿼리와 제안 된 결과가 검색 인덱스에서 검색 되 고 확인 기에 등록 한 선택한 필드에서 검색 됩니다. 확인 기는 인덱스의 일부 이며 쿼리를 완료 하거나 결과를 제안 하거나 둘 다 수행 하는 내용을 제공 하는 필드를 지정 합니다. 인덱스를 만들고 로드할 때 확인 기 데이터 구조는 내부적으로 생성 되어 부분 쿼리를 일치 시키는 데 사용 되는 접두사를 저장 합니다. 제안에 대해 고유한 필드를 선택 하거나 최소한의 반복을 선택 하지 않는 것이 좋습니다. 자세한 내용은 [Create a 확인 기](index-add-suggesters.md)을 참조 하세요.

이 문서의 나머지 부분에서는 쿼리와 클라이언트 코드에 대해 집중적으로 설명 합니다. JavaScript 및 c #을 사용 하 여 핵심 사항을 설명 합니다. REST API 예제는 각 작업을 간결 하 게 표시 하는 데 사용 됩니다. 종단 간 코드 샘플에 대 한 링크는 [다음 단계](#next-steps)를 참조 하세요.

## <a name="set-up-a-request"></a>요청 설정

요청 요소에는 검색 형식 Api, 부분 쿼리 및 확인 기 중 하나가 포함 됩니다. 다음 스크립트는 REST API 자동 완성을 사용 하 여 요청의 구성 요소를 보여 줍니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName** 는 용어 또는 제안을 완료 하는 데 사용 되는 확인 기 인식 필드를 제공 합니다. 특히, 필드 목록은 일치 결과 중에서 명확한 선택 항목을 제공 하는 항목으로 구성 되어야 합니다. 컴퓨터 게임을 판매 하는 사이트에서이 필드는 게임 타이틀이 될 수 있습니다.

**검색** 매개 변수는 JQuery 자동 완성 컨트롤을 통해 쿼리 요청에 문자를 공급 하는 부분 쿼리를 제공 합니다. 위의 예제에서 "minecraf"는 컨트롤이 전달 했을 수 있는 항목에 대 한 정적 그림입니다.

Api는 부분 쿼리에 최소 길이 요구 사항을 적용 하지 않습니다. 문자는 한 문자이 하 여야 합니다. 그러나 jQuery 자동 완성은 최소 길이를 제공 합니다. 일반적으로 2 ~ 3 자 이상입니다.

일치 항목은 입력 문자열의 모든 위치에서 시작 됩니다. "빠른 갈색 fox"를 고려 하 여 "the", "quick", "갈색" 또는 "fox"의 부분 버전에서는 "rown" 또는 "황소"와 같은 부분 사용 약관이 일치 하지 않습니다. 또한 각 일치 항목은 다운스트림 확장의 범위를 설정 합니다. "빠른 br"의 부분 쿼리는 "빠른 갈색" 또는 "빠른 이동 (quick)"에서 일치 하지만 "" 갈색 "또는" 이동 "은" 빠른 "앞에 오는 경우를 제외 하 고 모두 일치 합니다.

### <a name="apis-for-search-as-you-type"></a>검색을 위한 Api (입력 형식)

REST 및 .NET SDK 참조 페이지에 대 한 다음 링크를 따르세요.

+ [제안 REST API](/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 메서드](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 메서드](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>응답 구성

자동 완성 및 제안에 대 한 응답은 패턴에 대해 예측할 수 있습니다. [자동 완성](/rest/api/searchservice/autocomplete#response) 은 단어를 인출할 수 있도록 용어와 문서 ID [를 반환 합니다](/rest/api/searchservice/suggestions#response) . 그러면 문서를 인출할 수 있습니다 ( [조회 문서](/rest/api/searchservice/lookup-document) API를 사용 하 여 세부 정보 페이지에 대 한 특정 문서 페치).

응답은 요청에 대 한 매개 변수로 모양이 지정 됩니다. 자동 완성을 위해 [**autocompleteMode**](/rest/api/searchservice/autocomplete#autocomplete-modes) 를 설정 하 여 텍스트 완성이 한 두 항에서 발생 하는지 여부를 확인 합니다. 제안에 대해 선택한 필드에 따라 응답의 내용이 결정 됩니다.

제안 사항을 위해 중복을 방지 하기 위해 응답을 구체화 하거나 관련 되지 않은 결과를 표시 하는 것이 좋습니다. 결과를 제어 하려면 요청에 추가 매개 변수를 포함 합니다. 다음 매개 변수는 자동 완성 및 제안에 모두 적용 되지만 특히 확인 기에 여러 필드가 포함 된 경우 제안에 더 필요할 수 있습니다.

| 매개 변수 | 사용 |
|-----------|-------|
| **$select** | 확인 기에 여러 **sourcefields** 가 있는 경우 **$select** 를 사용 하 여 값을 제공 하는 필드를 선택 `$select=GameTitle` 합니다 (). |
| **searchFields** | 특정 필드에 대 한 쿼리를 제한 합니다. |
| **$filter** | 결과 집합에 일치 조건 ()을 적용 `$filter=Category eq 'ActionAdventure'` 합니다. |
| **$top** | 결과를 특정 숫자 ()로 제한 `$top=5` 합니다.|

## <a name="add-user-interaction-code"></a>사용자 상호 작용 코드 추가

쿼리 용어를 자동으로 채우거 나 일치 하는 링크 목록을 삭제 하려면 사용자 상호 작용 코드 (일반적으로 JavaScript)를 사용 해야 합니다 .이 코드는 자동 완성 또는 Azure Search 인식 인덱스에 대 한 제안 쿼리와 같은 외부 소스의 요청을 사용할 수 있습니다.

이 코드를 기본적으로 작성할 수 있지만 기존 JavaScript 라이브러리의 함수를 사용 하는 것이 훨씬 쉽습니다. 이 문서에서는 두 가지 (제안 및 자동 완성 용)를 보여 줍니다. 

+ [자동 완성 위젯 (JQUERY UI)](https://jqueryui.com/autocomplete/) 은 제안 예제에서 사용 됩니다. 검색 상자를 만든 다음 자동 완성 위젯을 사용 하는 JavaScript 함수에서 검색 상자를 참조할 수 있습니다. 위젯의 속성은 원본 (자동 완성 또는 제안 함수), 작업을 수행 하기 전의 최소 입력 문자 길이 및 위치 지정을 설정 합니다.

+ [XDSoft 자동 완성 플러그](https://xdsoft.net/jqplugins/autocomplete/) 인은 자동 완성 예제에 사용 됩니다.

이러한 라이브러리를 사용하여 제안과 자동 완성을 모두 지원하는 검색 상자를 빌드합니다. 검색 상자에 수집 된 입력은 제안 및 자동 완성 작업과 쌍을 이룹니다.

## <a name="suggestions"></a>제안

이 섹션에서는 검색 상자 정의부터 시작 하 여 제안 된 결과를 구현 하는 과정을 안내 합니다. 또한이 문서에서 참조 하는 첫 번째 JavaScript 자동 완성 라이브러리를 호출 하는 방법과 스크립트를 보여 줍니다.

### <a name="create-a-search-box"></a>검색 상자 만들기

[JQUERY UI 자동 완성 라이브러리](https://jqueryui.com/autocomplete/) 및 c #의 MVC 프로젝트를 가정 하 고, **Index. Cshtml** 파일에서 JavaScript를 사용 하 여 검색 상자를 정의할 수 있습니다. 라이브러리는 MVC 컨트롤러에 대 한 비동기 호출을 수행 하 여 제안을 검색 하 고 검색 상자에 검색 형식 상호 작용을 추가 합니다.

\Views\Home 폴더 아래의 **Index. cshtml** 에서 검색 상자를 만드는 줄은 다음과 같을 수 있습니다.

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

이 예제는 스타일 지정에 대한 클래스, JavaScript에서 참조할 ID 및 자리 표시자 텍스트가 있는 간단한 입력 텍스트 상자입니다.  

동일한 파일 내에서 검색 상자를 참조 하는 JavaScript를 포함 합니다. 다음 함수는 부분 용어 입력에 따라 제안 된 일치 문서를 요청 하는 제안 API를 호출 합니다.

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

는 `source` 검색 상자 아래에 표시할 항목 목록을 가져오는 JQUERY UI 자동 완성 기능을 제공 합니다. 이 프로젝트는 MVC 프로젝트 이므로 쿼리 제안을 반환 하는 논리를 포함 하는 **HomeController.cs** 의 **제안** 함수를 호출 합니다. 또한 이 함수는 강조 표시, 유사 일치, 용어를 제어하는 몇 가지 매개 변수도 전달합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

는 `minLength: 3` 검색 상자에 세 개 이상의 문자가 있는 경우에만 권장 사항이 표시 되도록 합니다.

### <a name="enable-fuzzy-matching"></a>유사 항목 일치 사용

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 편집 거리는 1입니다. 즉, 사용자 입력과 일치 하는 문자 사이에는 한 문자의 최대 차이가 있을 수 있습니다. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>강조 표시 사용

강조 표시는 입력에 해당 하는 결과의 문자에 글꼴 스타일을 적용 합니다. 예를 들어 부분 입력이 "마이크로" 이면 결과는 **마이크로**소프트, **마이크로**범위 등으로 표시 됩니다. 강조 표시는 제안 함수를 사용 하 여 인라인으로 정의 된 HighlightPreTag 및 HighlightPostTag 매개 변수를 기반으로 합니다.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>제안 함수

C # 및 MVC 응용 프로그램을 사용 하는 경우 Controller 디렉터리 아래의 **HomeController.cs** 파일은 제안 된 결과에 대 한 클래스를 만들 수 있습니다. .NET에서 제안 함수는 [DocumentsOperationsExtensions 메서드](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)를 기반으로 합니다.

`InitSearch`메서드는 Azure Cognitive Search 서비스에 대 한 인증 된 HTTP 인덱스 클라이언트를 만듭니다. .NET SDK에 대 한 자세한 내용은 [.Net 응용 프로그램에서 Azure Cognitive Search를 사용 하는 방법](./search-howto-dotnet-sdk.md)을 참조 하세요.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
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

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다. 메서드는 제안 API에 전달 되는 [SuggestParameters 개체](/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="autocomplete"></a>자동 완성

지금까지 검색 UX 코드는 제안을 중심으로 합니다. 다음 코드 블록은 XDSoft jQuery UI 자동 완성 함수를 사용 하 여 Azure Cognitive Search 자동 완성에 대 한 요청을 전달 하는 자동 완성 기능을 보여 줍니다. 제안과 마찬가지로 c # 응용 프로그램에서는 사용자 상호 작용을 지 원하는 코드가 **인덱스 cshtml**로 이동 합니다.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>자동 완성 함수

자동 완성은 [DocumentsOperationsExtensions 메서드](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)를 기반으로 합니다. 제안과 마찬가지로이 코드 블록은 **HomeController.cs** 파일로 이동 합니다.

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 함수는 검색 용어 입력을 사용합니다. 이 메서드는 [AutoCompleteParameters 개체](/rest/api/searchservice/autocomplete)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 따라 포괄적인 검색 환경을 보여 주는 종단 간 지침 또는 코드를 참조 하세요. 두 코드 예제 모두에는 제안 및 자동 완성의 하이브리드 구현이 포함 됩니다.

+ [자습서: c #에서 첫 번째 앱 만들기 (3 단원)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C # 코드 샘플: azure-검색-dotnet-samples/create-first-app/3-추가 형식 미리/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [REST side-by-side 코드 샘플을 사용 하는 c # 및 JavaScript](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)