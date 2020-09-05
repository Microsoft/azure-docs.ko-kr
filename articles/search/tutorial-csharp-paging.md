---
title: 검색 결과 페이지 매김에 대한 C# 자습서
titleSuffix: Azure Cognitive Search
description: 검색 결과에 페이지 매김 및 탐색 단추를 추가하여 첫 번째, 다음, 이전, 마지막 및 번호 매기기 단추를 추가하는 기존 호텔 프로젝트를 빌드합니다. 두 번째 페이징 시스템은 세로 스크롤 막대를 해당 하한값까지 이동하여 트리거되는 무한 스크롤을 사용합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 3223bf3e2b413940fd99404453b7f958655e6fff
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998478"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 검색 결과에 페이징 추가

서로 다른 두 가지 페이징 시스템을 구현하는 방법에 대해 알아봅니다. 첫 번째 페이징 시스템은 페이지 번호, 두 번째 페이징 시스템은 무한 스크롤을 기반으로 합니다. 두 페이징 시스템은 모두 널리 사용되고 있으며, 올바른 페이징을 선택하는 것은 결과와 관련하여 원하는 사용자 경험에 따라 달라집니다. 이 자습서에서는 페이징 시스템을 [C# 자습서: 첫 번째 앱 만들기 - Azure Cognitive Search](tutorial-csharp-create-first-app.md) 자습서에서 만든 프로젝트에 빌드합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 번호 매기기 페이징을 사용하여 앱 확장
> * 무한 스크롤을 사용하여 앱 확장

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

[C# 자습서: 첫 번째 앱 만들기 - Azure Cognitive Search](tutorial-csharp-create-first-app.md) 프로젝트를 가동하여 실행합니다. 이 프로젝트는 사용자 고유의 버전이거나 GitHub: [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 버전을 설치해도 됩니다.

## <a name="extend-your-app-with-numbered-paging"></a>번호 매기기 페이징을 사용하여 앱 확장

번호 매기기 페이징은 기본 인터넷 검색 엔진과 대부분의 다른 검색 웹 사이트를 선택할 수 있는 페이징 시스템입니다. 번호 매기기 페이징에는 일반적으로 실제 페이지 번호 범위 외에도 "다음" 및 "이전" 옵션이 포함됩니다. 또한 "첫 번째 페이지" 및 "마지막 페이지" 옵션도 사용할 수 있습니다. 이러한 옵션에서는 페이지 기반 결과를 탐색할 수 있는 사용자 정의 컨트롤을 반드시 제공합니다.

여기서는 첫 번째, 이전, 다음 및 마지막 옵션이 포함된 시스템을 1부터 시작하지 않고 대신 사용자가 현재 사용 중인 페이지를 둘러싼 페이지 번호와 함께 추가합니다(예: 사용자가 10페이지를 보고 있는 경우 아마도 8, 9, 10, 11 및 12의 페이지 번호가 표시될 수 있음).

시스템은 표시되는 페이지 번호의 숫자를 글로벌 변수에 설정할 수 있을 만큼 충분히 유연합니다.

시스템에서 가장 왼쪽 및 가장 오른쪽의 페이지 번호 단추를 특수한 것으로 처리합니다. 즉, 표시되는 페이지 번호의 범위에 대한 변경을 트리거합니다. 예를 들어 8, 9, 10, 11 및 12의 페이지 번호가 표시되고 사용자가 8을 클릭하면 표시되는 페이지 번호의 범위가 6, 7, 8, 9 및 10으로 변경됩니다. 그리고 12를 선택하면 오른쪽으로의 비슷한 이동이 있습니다.

### <a name="add-paging-fields-to-the-model"></a>모델에 페이징 필드 추가

기본 검색 페이지 솔루션을 엽니다.

1. SearchData.cs 모델 파일을 엽니다.

2. 먼저 몇 가지 글로벌 변수를 추가합니다. MVC에서 글로벌 변수는 자체의 정적 클래스로 선언됩니다. **ResultsPerPage**는 페이지당 결과의 수를 설정합니다. **MaxPageRange**는 보기에 표시되는 페이지 번호의 숫자를 결정합니다. **PageRangeDelta**는 가장 왼쪽 또는 가장 오른쪽의 페이지 번호를 선택하는 경우 페이지 범위를 왼쪽 또는 오른쪽으로 이동할 페이지의 수를 결정합니다. 일반적으로 이 두 번째 숫자는 **MaxPageRange**의 절반 정도입니다. 다음 코드를 네임스페이스에 추가합니다.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >랩톱과 같이 화면이 작은 디바이스에서 이 프로젝트를 실행하는 경우 **ResultsPerPage**를 2로 변경하는 것이 좋습니다.

3. 페이징 속성을 **searchText** 속성 뒤의 **SearchData** 클래스에 추가합니다.

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>보기에 페이징 옵션 테이블 추가

1. index.cshtml 파일을 열고, 다음 코드를 닫는 &lt;/body&gt; 태그 바로 뒤에 추가합니다. 이 새 코드는 첫 번째, 이전, 1, 2, 3, 4, 5, 다음, 마지막의 페이징 옵션 테이블을 제공합니다.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    HTML 테이블을 사용하여 페이지 옵션을 깔끔하게 정렬합니다. 그러나 모든 작업은 @Html.ActionLink 문의 결과이며, 각 문에서는 앞에서 추가한 **paging** 속성의 다른 항목을 사용하여 만든 **new** 모델을 통해 컨트롤러를 호출합니다.

    첫 번째 및 마지막 페이지 옵션은 "첫 번째" 및 "마지막"과 같은 문자열을 보내지 않고 대신 올바른 페이지 번호를 보냅니다.

2. 일부 페이징 클래스를 hotels.css 파일의 HTML 스타일 목록에 추가합니다. 여기서 **pageSelected** 클래스는 페이지 번호 목록에서 숫자를 굵게 표시하여 사용자가 현재 보고 있는 페이지를 식별하기 위한 것입니다.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>컨트롤러에 페이지 Page 추가

1. HomeController.cs 파일을 열고, **Page** 작업을 추가합니다. 이 작업은 선택한 페이지 옵션 중 하나에 응답합니다.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    이제 세 번째 매개 변수로 인해 **RunQueryAsync** 메서드에서 구문 오류를 표시합니다.

    > [!Note]
    > **TempData** 호출은 값(**object**)을 임시 스토리지에 저장하지만 이 스토리지는_하나_의 호출에서만 유지됩니다. 무언가를 임시 데이터에 저장하는 경우 이는 컨트롤러 작업에 대한 다음 호출에 사용할 수 있지만 그 후의 호출에서는 확실히 사라집니다. 이 짧은 수명으로 인해 하나도 빠짐없이 **Page**에 대한 모든 호출에서 검색 텍스트와 페이징 속성을 임시 스토리지에 다시 저장합니다.

2. **Index(model)** 작업은 임시 변수를 저장하고 가장 왼쪽 페이지 매개 변수를 **RunQueryAsync** 호출에 추가하도록 업데이트해야 합니다.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **RunQueryAsync** 메서드는 크게 업데이트해야 합니다. **SearchParameters** 클래스의 **Skip**, **Top** 및 **IncludeTotalResultCount** 필드를 사용하여 **Skip** 설정에서 시작하는 한 페이지 분량의 결과만 요청합니다. 또한 보기에 대한 페이징 변수를 계산해야 합니다. 전체 메서드를 다음 코드로 바꿉니다.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. 마지막으로, 보기를 약간 변경해야 합니다. 이제 **resultsList.Results.Count** 변수에는 총 수가 아니라 한 페이지에 반환되는 결과의 수(예제의 경우 3)가 포함됩니다. **IncludeTotalResultCount**를 true로 설정했으므로 **resultsList.Count** 변수에는 이제 결과의 총 수가 포함됩니다. 따라서 보기에서 결과의 수가 표시되는 위치를 찾아 다음 코드로 변경합니다.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > 이 합계는 Azure Cognitive Search에서 계산해야 하므로 **IncludeTotalResultCount**를 true로 설정하면 일반적으로 많지는 않지만 성능이 저하됩니다. 복잡한 데이터 세트를 사용하면 반환되는 값이 _근사값_이라는 경고가 표시됩니다. 이 호텔 데이터의 경우에는 정확합니다.

### <a name="compile-and-run-the-app"></a>앱 컴파일 및 실행

이제 **디버그하지 않고 시작**을 선택하거나 F5 키를 누릅니다.

1. 많은 양의 결과를 얻을 수 있는 일부 텍스트(예: "wifi")를 검색합니다. 이러한 결과를 깔끔하게 페이지징할 수 있나요?

    !["pool" 결과를 통한 번호 매기기 페이징](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. 먼저 가장 오른쪽, 다음으로 가장 왼쪽의 페이지 번호를 클릭합니다. 페이지 번호가 적절히 조정되어 현재 사용 중인 페이지의 가운데에 맞추어지는가요?

3. "첫 번째" 및 "마지막" 옵션이 유용한가요? 일부 인기 있는 웹 검색에서는 이러한 옵션을 사용하지만, 다른 웹 검색에서는 사용하지 않습니다.

4. 결과의 마지막 페이지로 이동합니다. 마지막 페이지는 **ResultsPerPage** 미만의 결과를 포함할 수 있는 유일한 페이지입니다.

    !["wifi"의 마지막 페이지 검사](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. "town"을 입력하고 검색을 클릭합니다. 한 페이지 분량보다 적은 결과가 있으면 페이징 옵션이 표시되지 않습니다.

    !["town" 검색](./media/tutorial-csharp-create-first-app/azure-search-town.png)

이제 이 프로젝트를 저장하고, 이 유형의 페이징 대신 다른 페이징을 시도해 보겠습니다.

## <a name="extend-your-app-with-infinite-scrolling"></a>무한 스크롤을 사용하여 앱 확장

무한 스크롤은 사용자가 세로 스크롤 막대를 표시되는 결과의 마지막까지 스크롤할 때 트리거됩니다. 이 이벤트에서 서버에 대한 호출은 다음 결과 페이지를 위해 수행됩니다. 결과가 더 이상 없으면 아무 것도 반환되지 않으며 세로 스크롤 막대가 변경되지 않습니다. 결과가 더 있으면 현재 페이지에 추가되고 스크롤 막대가 변경되어 더 많은 결과를 사용할 수 있음을 나타냅니다.

여기서 중요한 점은 표시되는 페이지가 바뀌지 않고 새 결과와 함께 추가된다는 것입니다. 사용자는 언제든지 검색의 첫 번째 결과까지 뒤로 스크롤할 수 있습니다.

무한 스크롤을 구현하기 위해 페이지 번호 스크롤 요소를 추가하기 전에 먼저 프로젝트부터 시작하겠습니다. 따라서 필요한 경우 GitHub: [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 버전을 설치해도 됩니다.

### <a name="add-paging-fields-to-the-model"></a>모델에 페이징 필드 추가

1. 먼저 **paging** 속성을 SearchData.cs 모델 파일의 **SearchData** 클래스에 추가합니다.

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    이 변수는 결과의 다음 페이지를 보내야 하는 경우 "다음"을 유지하는 문자열이거나 검색의 첫 번째 페이지에 대해 null이 됩니다.

2. 동일한 파일 및 네임스페이스 내에서 하나의 속성이 있는 글로벌 변수 클래스를 추가합니다. MVC에서 글로벌 변수는 자체의 정적 클래스로 선언됩니다. **ResultsPerPage**는 페이지당 결과의 수를 설정합니다. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>보기에 세로 스크롤 막대 추가

1. 결과를 표시하는 index.cshtml 파일의 섹션( **@if (Model != null)** 로 시작)을 찾습니다.

2. 섹션을 아래 코드로 바꿉니다. 새 **&lt;div&gt;** 섹션은 스크롤할 수 있어야 하는 영역 주위에 있으며, 다음과 같이 **overflow-y** 특성과 "scroted()"라는 **onscroll** 함수에 대한 호출을 모두 추가합니다.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. **scroled** 함수를 루프 바로 아래의 &lt;/div&gt; 태그 뒤에 추가합니다.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    위 스크립트의 **if** 문에서 테스트하여 사용자가 세로 스크롤 막대의 맨 아래까지 스크롤했는지 확인합니다. 이렇게 수행했으면 **Home** 컨트롤러에 대한 호출이 **Next**라는 작업에 수행됩니다. 컨트롤러에 다른 정보가 필요하지 않으며, 데이터의 다음 페이지를 반환합니다. 그런 다음, 이 데이터의 형식이 원래 페이지와 동일한 HTML 스타일을 사용하여 지정됩니다. 결과가 반환되지 않으면 아무것도 추가되지 않고 그대로 유지됩니다.

### <a name="handle-the-next-action"></a>Next 작업 처리

컨트롤러에 보내야 하는 작업에는 **Index()** 를 호출하는 앱의 첫 번째 실행, **Index(model)** 를 호출하는 사용자의 첫 번째 검색 및 **Next(model)** 를 통해 더 많은 결과를 요청하는 후속 호출의 세 가지 작업만 있습니다.

1. 홈 컨트롤러 파일을 열고, 원래 자습서의 **RunQueryAsync** 메서드를 삭제합니다.

2. **Index(model)** 작업을 다음 코드로 바꿉니다. 이제 null이거나 "next"로 설정된 **paging** 필드를 처리하고, Azure Cognitive Search에 대한 호출을 처리합니다.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    번호 매기기 페이징 메서드와 마찬가지로, **Skip** 및 **Top** 검색 설정을 사용하여 필요한 데이터만 반환되도록 요청합니다.

3. **Next** 작업을 홈 컨트롤러에 추가합니다. 각 호텔 데이터에 호텔 이름과 호텔 설명이라는 두 가지 요소를 추가하여 목록을 반환하는 방법을 확인합니다. 이 형식은 보기에서 반환된 데이터를 사용하는 **scrolled** 함수와 일치하도록 설정됩니다.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. **List&lt;문자열&gt;** 에 구문 오류가 발생하면 다음 **using** 지시문을 컨트롤러 파일의 헤드에 추가합니다.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>프로젝트 컴파일 및 실행

이제 **디버그하지 않고 시작**을 선택하거나 F5 키를 누릅니다.

1. 많은 결과를 얻을 수 있는 용어(예: "pool")를 입력한 다음, 세로 스크롤 막대를 테스트합니다. 결과의 새 페이지가 트리거되나요?

    !["pool" 결과를 통한 무한 스크롤](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > 스크롤 막대가 첫 번째 페이지에 표시되도록 하려면 결과의 첫 번째 페이지가 표시되는 영역의 높이를 약간 초과해야 합니다. 예제에서 **.box1**의 높이는 30픽셀이고, **.box2**의 높이는 100픽셀이며, 아래쪽 여백은 24픽셀입니다. 따라서 각 항목은 154픽셀을 사용합니다. 세 항목에서 모두 3 x 154 = 462셀을 차지합니다. 세로 스크롤 막대가 표시되도록 하려면 표시 영역의 높이를 462픽셀보다 작게 설정해야 합니다(461픽셀에서도 작동함). 이 문제는 스크롤 막대가 표시되어야 하는 첫 번째 페이지에서만 발생합니다. 업데이트할 줄은 **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** 입니다.

2. 결과의 맨 아래까지 완전히 스크롤합니다. 이제 모든 정보가 단일 보기 페이지에 표시되는 방법을 확인합니다. 서버 호출을 트리거하지 않고 맨 위까지 완전히 다시 스크롤할 수 있습니다.

더 정교한 무한 스크롤 시스템에서는 마우스 휠 또는 비슷한 다른 메커니즘을 사용하여 결과의 새 페이지 로드를 트리거할 수 있습니다. 이 자습서에서는 무한 스크롤을 더 자세히 수행하지 않지만, 마우스를 추가로 클릭하지 않아도 되므로 특정 매력이 있으며, 다른 옵션을 더 자세히 조사하는 것이 좋습니다!

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* 번호 매기기 페이징은 결과의 순서가 다소 임의적인 검색에 적합합니다. 즉 이후의 페이지에 사용자의 관심을 끄는 무언가가 있을 수 있습니다.
* 무한 스크롤은 결과의 순서가 특히 중요한 경우에 적합합니다. 예를 들어 대상 도시의 중심에서 떨어진 거리에 따라 결과가 정렬되는 경우입니다.
* 번호 매기기 페이징을 사용하면 탐색 기능이 향상됩니다. 예를 들어 사용자는 흥미로운 결과가 6페이지에 있다는 것을 기억할 수 있지만, 무한 스크롤에는 이러한 쉬운 참조가 없습니다.
* 무한 스크롤에는 클릭하기에는 너무 복잡한 페이지 번호를 사용하지 않고 위아래로 스크롤할 수 있는 편리한 매력이 있습니다.
* 무한 스크롤의 주요 기능은 결과를 기존 페이지에 추가하는 것이며, 해당 페이지를 바꾸는 것이 아닙니다. 이로 인해 효율적입니다.
* 임시 스토리지는 하나의 호출에서만 유지되며, 추가 호출에서도 유지하려면 다시 설정해야 합니다.


## <a name="next-steps"></a>다음 단계

페이징은 인터넷 검색의 기본입니다. 페이징이 잘 처리되면 다음 단계는 자동 완성 검색 기능을 추가하여 사용자 환경을 더욱 향상시키는 것입니다.

> [!div class="nextstepaction"]
> [C# 자습서: 자동 완성 및 제안 기능 추가 - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
