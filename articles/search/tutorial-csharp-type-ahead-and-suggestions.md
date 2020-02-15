---
title: 자동 완성 및 제안에 대한 C# 자습서
titleSuffix: Azure Cognitive Search
description: 이 자습서에서는 드롭다운 목록을 사용하여 사용자로부터 검색어 입력을 수집하는 방법으로 자동 완성 및 제안을 보여줍니다. 기존 호텔 프로젝트를 기반으로 빌드됩니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 8f244d64fe33a1529cf66314515bbe16e05ccffb
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121539"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C# 자습서: 자동 완성 및 제안 기능 추가 - Azure Cognitive Search

사용자가 검색창에 입력을 시작할 때 자동 완성(자동 완성 및 제안)을 구현하는 방법을 알아봅니다. 이 자습서에서는 자동 완성 결과 및 제안 결과를 개별적으로 표시한 다음, 자동 완성과 제안을 결합하여 보다 풍부한 사용자 환경을 만드는 방법을 보여줍니다. 사용자는 핵심 글자 두세 개만 입력하면 사용 가능한 모든 결과를 찾을 수 있습니다. 이 자습서는 [C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md) 자습서에서 만든 페이지 매김 프로젝트를 기반으로 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 제안 추가
> * 제안에 강조 표시 추가
> * 자동 완성 기능 추가
> * 자동 완성과 제안을 결합

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

[C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md) 프로젝트를 가동하여 실행합니다. 이전 자습서에서 완료한 개발자 고유의 버전을 이 프로젝트에 사용해도 되고, 또는 GitHub: [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 버전을 설치해도 됩니다.

## <a name="add-suggestions"></a>제안 추가

사용자에게 대안을 제공하는 가장 간단한 방법인 드롭다운 제안 목록부터 시작하겠습니다.

1. Index.cshtml 파일에서 **TextBoxFor** 문을 다음과 같이 변경합니다.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    여기서 핵심은 검색창의 ID를 **azureautosuggest**로 설정하는 것입니다.

2. 이 명령문의 닫는 **&lt;/div&gt;** 뒤에 다음 스크립트를 입력합니다.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    같은 ID를 통해 이 스크립트를 검색창에 연결했습니다. 또한 검색을 트리거하려면 두 개 이상의 문자가 필요한데, 홈 컨트롤러에서 두 쿼리 매개 변수 **highlights** 및 **fuzzy**를 사용하여 **Suggest** 작업을 호출합니다. 이 예에서는 두 매개 변수를 모두 false로 설정합니다.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>보기에 jquery 스크립트에 대한 참조 추가

위의 스크립트에서 호출되는 자동 완성 함수는 jquery 라이브러리에 제공되므로 직접 작성할 필요가 없습니다. 

1. jquery 라이브러리에 액세스하려면 보기 파일의 &lt;head&gt; 섹션을 다음 코드로 변경합니다.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. 또한 _Layout.cshtml 파일(**Views/Shared** 폴더에 있음)의 jquery를 참조하는 줄을 제거하거나 주석으로 처리해야 합니다. 다음 줄을 찾아서 다음과 같이 첫 번째 스크립트 줄을 주석 처리합니다. 이렇게 변경하면 jquery 참조가 충돌하는 것을 방지할 수 있습니다.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    이제 미리 정의된 자동 완성 jquery 함수를 사용할 수 있습니다.

### <a name="add-the-suggest-action-to-the-controller"></a>컨트롤러에 제안 작업 추가

1. 홈 컨트롤러에서 **제안** 작업을 추가합니다(**페이지** 작업 뒤로 가정).

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    **Top** 매개 변수는 반환할 결과 수를 지정합니다(지정하지 않으면 기본값은 5). _제안기_는 Azure 인덱스에 지정되는데, 이 자습서 같은 클라이언트 앱이 아닌 데이터를 설정할 때 지정됩니다. 이 예제에서는 제안기를 "sg"라고 부르며, **HotelName** 필드만 검색하고 그 외에는 아무 것도 검색하지 않습니다. 

    유사 일치를 사용하면 출력에 "니어 미스"가 포함됩니다. **highlights** 매개 변수를 true로 설정하면 볼드 HTML 태그가 출력에 추가됩니다. 다음 섹션에서는 이 두 매개 변수를 true로 설정하겠습니다.

2. 구문 오류가 발생할 수 있습니다. 이 경우 다음 두 **using** 문을 파일 맨 위에 추가합니다.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. 앱을 실행합니다. "po"를 입력하면 다양한 옵션이 제공되나요? 이번에는 "pa"를 입력해 봅시다.

    !["po"를 입력하면 두 가지 제안이 표시됩니다.](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    단순히 단어의 중간에 있는 문자를 입력하면 안 되고 _반드시_ 단어의 시작 문자를 입력해야 합니다.

4. 보기 스크립트에서 **&fuzzy**를 true로 설정하고, 앱을 다시 실행합니다. 이제 "po"를 입력합니다. 검색에서는 한 글자가 잘못 입력된 것으로 가정합니다.
 
    ![fuzzy를 true로 설정하고 "Pa" 입력](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    관심이 있는 분들은 유사 항목 검색에 사용되는 논리에 대해 설명하는 [Azure Cognitive Search의 Lucene 쿼리 구문](https://docs.microsoft.com/azure/search/query-lucene-syntax)을 읽어보세요.

## <a name="add-highlighting-to-the-suggestions"></a>제안에 강조 표시 추가

**highlights** 매개 변수를 true로 설정하여 사용자에게 표시되는 제안의 모양을 약간 개선할 수 있습니다. 그러나 먼저 볼드 텍스트를 표시하도록 보기에 일부 코드를 추가해야 합니다.

1. 보기(index.cshtml)에서, 위에서 입력한 **azureautosuggest** 스크립트 뒤에 다음 스크립트를 추가합니다.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. 텍스트 상자의 ID를 변경하면 다음과 같습니다.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 앱을 다시 실행하면 입력한 텍스트가 제안에 굵은 글씨로 표시되는 것을 볼 수 있습니다. "pa"를 입력해 봅시다.
 
    !["pa"를 입력하면 강조 표시됨](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. 위에서 스크립트를 강조 표시하는 데 사용된 논리는 완벽하지 않습니다. 같은 이름에 두 번 표시되는 용어를 입력하면 볼드가 원하는 대로 적용되지 않습니다. "mo"를 입력해 봅시다.

    개발자는 스크립트가 언제 "잘" 작동하는지, 특이점이 언제 해결되는지 파악해야 합니다. 이 자습서에서는 강조 표시를 더 이상 개선하지 않을 것이지만, 강조 표시를 더 개선하려면 정확한 알고리즘을 찾아야 합니다.

## <a name="add-autocompletion"></a>자동 완성 기능 추가

제안과 약간 다른 또 다른 변형이 자동 완성("미리 입력"이라고도 함)입니다. 마찬가지로, 간단한 구현부터 시작한 후 사용자 환경을 개선하겠습니다.

1. 이전 스크립트에 이어서 다음 스크립트를 보기에 입력합니다.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. 텍스트 상자의 ID를 변경하면 다음과 같습니다.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 홈 컨트롤러에서 **Autocomplete** 작업을 입력해야 합니다. 예를 들어 **Suggest** 작업 아래에 입력합니다.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    앞에서 제안에 했던 것처럼, "sg"라고 하는 동일한 *제안기* 함수를 자동 완성 검색에 사용합니다(즉, 호텔 이름만 자동 완성을 시도).

    다양한 **AutocompleteMode** 설정이 있는데, 여기서는 **OneTermWithContext**를 사용하겠습니다. 여기서 제공하는 옵션 범위에 대한 설명은 [Azure 자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete)을 참조하세요.

4. 앱을 실행합니다. 드롭다운 목록에 표시되는 옵션의 범위가 단일 단어라는 것을 알 수 있습니다. "re"로 시작하는 단어를 입력합니다. 더 많은 문자를 입력할수록 옵션 수가 감소합니다.

    ![기본 자동 완성을 사용하여 입력](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    앞에서 실행한 제안 스크립트는 그 이름처럼 이 자동 완성 스크립트보다 더 유용합니다. 보다 사용자 친화적인 자동 완성을 만드는 가장 좋은 방법은 제안 검색에 추가하는 것입니다.

## <a name="combine-autocompletion-and-suggestions"></a>자동 완성과 제안을 결합

자동 완성과 제안을 결합하는 것은 가장 복잡한 옵션이며, 아마도 최상의 사용자 환경을 제공할 것입니다. 우리가 원하는 것은 입력하는 텍스트에 따라 Azure Cognitive Search가 텍스트 자동 완성을 위해 선택하는 첫 번째 항목을 표시하는 것입니다. 또한 다양한 제안을 드롭다운 목록으로 제공하려 합니다.

이 기능을 제공하는 라이브러리가 있는데, "인라인 자동 완성" 또는 이와 비슷한 이름으로 부르기도 합니다. 하지만 여기서는 이 기능을 기본적으로 구현하고 어떻게 되는지 살펴보도록 하겠습니다. 이 예제에서는 컨트롤러부터 작업하겠습니다.

1. 지정된 수의 제안과 함께 자동 완성 결과를 딱 하나만 반환하는 작업을 컨트롤러에 추가해야 합니다. 이 작업을 **AutocompleteAndSuggest**라고 부르겠습니다. 홈 컨트롤러에서 다음 작업과 기타 새 작업을 추가합니다.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    자동 완성 옵션 중 하나가 **결과** 목록의 맨 위에 반환되고, 그 뒤에는 모든 제안이 표시됩니다.

2. 보기에서, 사용자가 입력한 볼드 텍스트 바로 아래에 밝은 회색 자동 완성 단어가 렌더링되도록 트릭을 먼저 구현합니다. HTML에는 이 용도로 사용되는 상대 위치가 포함되어 있습니다. **TextBoxFor** 문(및 주변 &lt;div&gt; 문)을 다음과 같이 변경하면 **underneath**로 식별되는 두 번째 검색창이 일반 검색창 바로 아래에 있고, 이 검색창을 기본 위치로부터 39픽셀만큼 끌어옵니다.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    이 예에서는 ID를 다시 **azureautocomplete**로 변경합니다.

3. 또한 보기에서 지금까지 입력한 모든 스크립트 뒤에 다음 스크립트를 입력합니다. 처리할 양이 꽤 많습니다.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    **interval** 함수를 현명하게 사용하는 방법으로는 첫째, 더 이상 일치하지 않는 기본 텍스트를 지우고, 둘째, 오버레이된 텍스트가 깔끔하게 표시되도록 사용자가 입력할 때 대/소문자를 똑같이 설정하는 것입니다(검색할 때 "pa"는 "PA", "pA", "Pa"와 일치하므로).

    스크립트의 주석을 모두 읽고 완전히 이해하시기 바랍니다.

4. 마지막으로, 두 HTML 클래스를 약간 조정하여 투명하게 만들어야 합니다. hotels.css 파일의 **searchBoxForm** 및 **searchBox** 클래스에 다음 줄을 추가합니다.

    ```html
        background: rgba(0,0,0,0);
    ```

5. 이제 앱을 실행합니다. 검색창에 "pa"를 입력합니다. "pa"가 들어 있는 두 호텔과 함께 "palace"가 자동 완성 제안으로 제공되나요?

    ![인라인 자동 완성 및 제안을 사용하여 입력](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. 탭하여 자동 완성 제안을 수락하고, 화살표 키와 Tab 키를 사용하여 제안을 선택하고, 마우스로 한 번 클릭하여 다시 시도해 보세요. 스크립트가 이 모든 상황을 깔끔하게 처리하는지 확인합니다.

    이 기능을 자동으로 제공하는 라이브러리에 로드하는 것이 더 간단하다고 결정할 수도 있지만, 이제 여러분은 인라인 자동 완성을 작동하는 방법을 적어도 한 가지는 알고 있습니다.

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* 자동 완성("미리 입력"이라고도 함) 및 제안을 사용하면 사용자가 핵심 글자 몇 개만 입력하여 원하는 내용을 정확하게 찾을 수 있습니다.
* 자동 완성과 제안은 함께 작동하여 풍부한 사용자 환경을 제공할 수 있습니다.
* 항상 모든 형식의 입력으로 자동 완성 기능을 테스트해야 합니다.
* UI 요소를 확인하고 수정할 때 **setInterval** 함수를 사용하면 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 사용자 환경을 개선하는 또 다른 방법으로 패싯을 사용하여 클릭 한 번으로 검색 범위를 좁히는 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [C# 자습서: 패싯을 사용하여 탐색 지원 - Azure Cognitive Search](tutorial-csharp-facets.md)


