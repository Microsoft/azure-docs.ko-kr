---
title: 첫 번째 앱 만들기 C# 자습서
titleSuffix: Azure Cognitive Search
description: 첫 번째 C# 검색 앱을 단계별로 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 GitHub의 작동하는 앱 링크와 앱을 처음부터 빌드하는 전체 프로세스의 링크를 제공합니다. Azure Cognitive Search의 필수 구성 요소에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: adcd6aa2c232bf87dc82284acbe2815484660ca7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998563"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 첫 번째 검색 앱 만들기

Azure Cognitive Search를 사용하여 인덱스에서 검색 결과를 쿼리 및 표시하는 웹 인터페이스를 만드는 방법을 알아봅니다. 검색 페이지 작성에 집중할 수 있도록 이 자습서는 기존에 호스트한 인덱스로 시작됩니다. 인덱스에는 가상의 호텔 데이터가 포함되어 있습니다. 기본 페이지를 만든 후에는 후속 단원에서 페이징, 패싯 및 자동 완성 환경을 포함하도록 기본 페이지를 개선할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 구조 모델링
> * 웹 페이지 만들기
> * 메서드 정의
> * 앱 테스트

또한 검색 호출이 얼마나 간단한지 알아보겠습니다. 여러분이 개발할 코드의 핵심 명령문이 다음 몇 줄에 캡슐화되어 있습니다.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

이 호출은 Azure 데이터 검색을 시작하고 결과를 반환합니다.

!["풀" 검색](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

IDE로 사용할 [Visual Studio를 설치](https://visualstudio.microsoft.com/)합니다.

### <a name="install-and-run-the-project-from-github"></a>GitHub에서 프로젝트를 설치하고 실행

1. GitHub에서 [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 버전을 설치해도 됩니다.
1. **복제 또는 다운로드**를 선택하고 프로젝트의 프라이빗 로컬 복사본을 만듭니다.
1. Visual Studio를 사용하여 기본 검색 페이지의 솔루션으로 이동하여 솔루션을 열고, **디버깅 없이 시작**을 선택합니다(또는 F5 키).
1. 아무 단어를 입력하고(예: "wifi", "전망", "바", "주차"), 결과를 확인합니다.

    !["wifi" 검색](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

이 프로젝트가 원활하게 진행되어 Azure 앱이 실행되기를 바랍니다. 보다 세련된 검색 기능을 구현하기 위한 여러 필수 구성 요소가 이 앱 하나에 포함되어 있으므로, 이 앱을 살펴보고 단계별로 만들어 보는 것이 좋습니다.

이 프로젝트를 처음부터 새로 만들고, 마음 속에 그리고 있는 Azure Cognitive Search 구성 요소를 강화하려면 다음 단계를 수행하세요.

## <a name="set-up-a-development-environment"></a>개발 환경 설정

1. Visual Studio 2017 이상에서 **새로 만들기/프로젝트**를 선택한 다음, **ASP.NET Core 웹 애플리케이션**을 선택합니다. 프로젝트 이름을 입력합니다(예: "FirstAzureSearchApp").

    ![클라우드 프로젝트 만들기](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. 이 프로젝트 형식에 대해 **확인**을 클릭하면 이 프로젝트에 적용되는 두 번째 옵션 세트가 제공됩니다. **웹 애플리케이션(Model-View-Controller)** 을 선택합니다.

    ![MVC 프로젝트 만들기](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. 다음으로, **도구** 메뉴에서 **NuGet 패키지 관리자**, **...솔루션용 NuGet 패키지 관리**를 차례로 선택합니다. 꼭 설치해야 하는 패키지가 하나 있습니다. **찾아보기** 탭을 선택한 다음, 검색 상자에서 “Azure Cognitive Search”를 입력합니다. 목록에 **Microsoft.Azure.Search**(버전 9.0.1 이상)가 나타나면 설치합니다. 설치를 완료하려면 몇 가지 추가 대화 상자를 더 클릭해야 합니다.

    ![NuGet을 사용하여 Azure 라이브러리 추가](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search 초기화

이 샘플에서는 공개적으로 제공되는 호텔 데이터를 사용하겠습니다. 이 데이터는 오직 데모 데이터를 제공할 목적으로 만든 임의의 50개 가상 호텔 이름과 설명 컬렉션입니다. 이 데이터에 액세스하려면 데이터의 이름과 키를 지정해야 합니다.

1. 새 프로젝트에서 appsettings.json 파일을 열고 기본 줄을 다음 이름과 키로 바꿉니다. 여기에 표시된 API 키는 키 예제가 아니라, _정확하게_ 호텔 데이터에 액세스할 때 필요한 키입니다. 이제 appsettings.json 파일이 다음과 비슷할 것입니다.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. 아직 이 파일 작업이 끝나지 않았습니다. 이 파일의 속성을 선택하고, **출력 디렉터리에 복사** 설정을 **변경된 내용만 복사**로 변경합니다.

    ![앱 설정을 출력에 복사](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>데이터 구조 모델링

모델(C# 클래스)은 클라이언트(보기), 서버(컨트롤러), 그리고 MVC(모델, 보기, 컨트롤러) 아키텍처를 사용하는 Azure 클라우드 간에 데이터를 교환하는 데 사용됩니다. 일반적으로 이러한 모델은 액세스하는 데이터의 구조를 반영합니다. 또한 보기/컨트롤러 통신을 처리할 모델이 필요합니다.

1. 솔루션 탐색기를 사용하여 프로젝트의 **Models** 폴더를 열면 그 안에 있는 기본 모델 **ErrorViewModel.cs**가 보일 것입니다.

2. **Models** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **새 항목**을 선택합니다. 그런 다음, 나타나는 대화 상자에서 **ASP.NET Core**를 선택하고 첫 번째 옵션 **클래스**를 선택합니다. .cs 파일 이름을 Hotel.cs로 변경하고 **추가**를 클릭합니다. Hotel.cs의 모든 콘텐츠를 다음 코드로 바꿉니다. 클래스의 **Address** 및 **Room** 멤버를 보면, 필드 자체가 클래스이므로 필드에도 모델이 필요합니다.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. Address.cs 파일의 이름 지정을 제외하고, 똑같은 프로세스에 따라 **Address** 클래스에 대한 모델을 만듭니다. 콘텐츠를 다음으로 바꿉니다.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. 마찬가지로. 똑같은 프로세스에 따라 **Room** 클래스를 만들고 파일 이름을 Room.cs로 지정합니다. 역시 콘텐츠를 다음으로 바꿉니다.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. **Hotel**, **Address** 및 **Room** 클래스 세트를 Azure에서는 [_복합 형식_](search-howto-complex-data-types.md)이라고 하며, Azure Cognitive Search의 중요한 기능입니다. 복합 형식은 다양한 클래스 및 하위 클래스 레벨 깊이를 지원하며, _단순 형식_(기본 멤버만 포함하는 클래스)을 사용할 때보다 훨씬 복잡한 데이터 구조를 지원합니다. 모델이 하나 더 필요하므로 새 모델 클래스 만들기 프로세스를 다시 진행합니다. 단, 이번에는 SearchData.cs 클래스를 호출하고 기본 코드를 다음으로 바꿉니다.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    이 클래스에는 사용자의 입력(**searchText**)과 검색의 출력(**resultList**)이 포함되어 있습니다. 출력 형식 **DocumentSearchResult&lt;Hotel&gt;** 이 매우 중요합니다. 이 형식은 검색 결과와 정확하게 일치하므로 이 참조를 보기에 전달해야 합니다.



## <a name="create-a-web-page"></a>웹 페이지 만들기

여기서 만든 프로젝트는 기본적으로 여러 클라이언트 보기를 만듭니다. 정확한 보기는 사용하는 Core .NET 버전에 따라 달라집니다(이 샘플에서는 2.1 사용). 모든 보기는 프로젝트의 **Views** 폴더에 있습니다. Index.cshtml 파일만 수정해야 합니다(**Views/Home** 폴더에 있음).

Index.cshtml의 콘텐츠 전체를 삭제하고, 다음 단계에 따라 파일을 빌드합니다.

1. 작은 이미지 두 개를 보기에 사용합니다. 갖고 있는 이미지를 사용해도 되고, GitHub 프로젝트에서 azure logo.png 및 search.png 이미지를 복사해도 됩니다. 이 두 이미지를 **wwwroot/images** 폴더에 배치해야 합니다.

2. Index.cshtml의 첫 번째 줄은 클라이언트(보기)와 서버(컨트롤러) 간에 데이터를 교환하는 데 사용할 모델, 즉, 앞에서 만든 **SearchData** 모델을 참조해야 합니다. 이 줄을 Index.cshtml 파일에 추가합니다.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. 보기의 제목을 입력하는 표준 연습이므로, 다음 줄은 아래와 같아야 합니다.

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. 제목 뒤에 HTML 스타일 시트의 참조를 입력합니다. 잠시 후에 만들겠습니다.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. 이번에는 보기의 핵심입니다. 기억해야 할 핵심 사항은 보기에서 두 가지 상황을 처리해야 한다는 것입니다. 첫째, 앱을 처음 시작할 때 보기에서 디스플레이를 처리해야 하는데, 사용자가 아직 검색 텍스트를 입력하지 않았습니다. 둘째, 사용자가 반복해서 사용할 수 있도록 검색 텍스트 상자에 추가하는 것 외에도 결과 표시를 처리해야 합니다. 이러한 두 가지 상황을 처리하려면 보기에 제공된 모델이 Null인지 확인해야 합니다. Null 모델은 두 상황 중 첫 번째 상황(앱을 처음으로 실행)이라는 것을 나타냅니다. 다음 명령을 Index.cshtml 파일에 추가하고 주석을 읽어봅니다.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. 마지막으로, 스타일 시트를 추가합니다. Visual Studio의 **파일** 메뉴에서 **새로 만들기/파일**을 선택한 다음, **스타일 시트**를 선택합니다(**일반**이 강조 표시된 상태로). 기본 코드를 다음 코드로 바꿉니다. 이 파일을 자세히 살펴보지는 않겠습니다. 스타일은 표준 HTML입니다.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. 스타일 시트 파일을 기본 site.css 파일과 함께 wwwroot/css 폴더에 hotels.css로 저장합니다.

이것으로 보기가 완성되었습니다. 순조롭게 진행되고 있습니다. 모델과 보기가 완성되었고, 이제 컨트롤만 완성하면 모두 연결됩니다.

## <a name="define-methods"></a>메서드 정의

기본적으로 만들어지는 컨트롤러(**Home Controller**)의 콘텐츠를 수정해야 합니다.

1. HomeController.cs 파일을 열고 **using** 문을 다음과 같이 바꿉니다.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>인덱스 메서드 추가

두 개의 **인덱스** 메서드가 필요합니다. 하나는 매개 변수가 없고(앱을 처음으로 여는 경우), 다른 하나는 모델을 매개 변수로 사용합니다(사용자가 검색 텍스트를 입력한 경우). 첫 번째 메서드는 기본적으로 생성됩니다. 

1. 다음 메서드를 기본 **Index()** 메서드 뒤에 추가합니다.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    메서드의 **async** 선언과 **RunQueryAsync**에 대한 **await** 호출을 살펴봅니다. 두 키워드는 호출을 비동기적으로 처리하므로 서버의 스레드를 차단하지 않습니다.

    **catch** 블록은 기본적으로 생성되는 오류 모델을 사용합니다.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>오류 처리 및 기타 기본 보기와 메서드 확인

사용하는 .NET Core 버전에 따라 조금씩 다른 기본 보기 세트가 생성됩니다. .NET Core 2.1의 기본 보기는 인덱스, 정보, 연락처, 개인 정보 및 오류입니다. .NET Core 2.2의 기본 보기는 인덱스, 개인 정보 및 오류입니다. 두 버전 모두 앱을 실행할 때 이러한 기본 페이지를 볼 수 있으며, 컨트롤러에서 어떻게 처리되는지 검사할 수 있습니다.

이 자습서의 뒷부분에서 오류 보기를 테스트하겠습니다.

GitHub 샘플에서 사용하지 않는 보기와 관련 작업을 삭제했습니다.

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync 메서드 추가

Azure Cognitive Search 호출은 **RunQueryAsync** 메서드에 캡슐화됩니다.

1. 먼저 Azure 서비스를 설정하는 몇 가지 정적 변수를 추가하고, 이러한 정적 변수를 시작하는 호출을 추가합니다.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. 이제 **RunQueryAsync** 메서드 자체를 추가합니다.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    이 메서드에서는 먼저 Azure 구성이 시작되는지 확인한 다음, 검색 매개 변수를 설정합니다. **선택** 매개 변수의 필드 이름은 **hotel** 클래스의 속성 이름과 정확하게 일치합니다. **Select** 매개 변수를 생략할 수 있으며, 이 경우 모든 속성이 반환됩니다. 그러나 데이터의 하위 집합에만 관심이 있는 경우 **Select** 매개 변수를 설정하지 않는 것은 비효율적입니다. 관심이 있는 속성을 지정하면 해당 속성만 반환됩니다.

    검색 비동기 호출(**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** )은 이 자습서와 앱의 모든 것을 보여줍니다. **DocumentSearchResult**는 흥미로운 클래스이며, (앱이 실행 중일 때) 여기에 중단점을 설정하고 디버거를 사용하여 **model.resultList**의 콘텐츠를 검사하는 것이 좋습니다. 이 방법이 직관적이고, 요청한 데이터를 받을 수 있으며, 그 외의 데이터는 별로 반환되지 않는다는 것을 알 수 있습니다.

이제 진실의 순간입니다.

### <a name="test-the-app"></a>앱 테스트

앱이 올바르게 실행되는지 확인하겠습니다.

1. **디버그/디버깅 없이 시작**을 선택하거나 F5 키를 누릅니다. 코딩을 제대로 했다면 초기 인덱스 보기가 표시됩니다.

     ![앱 열기](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. "해변"(또는 떠오르는 아무 텍스트) 등의 텍스트를 입력하고 검색 아이콘을 클릭합니다. 결과가 표시될 것입니다.

     !["해변" 검색](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. "5성"을 입력합니다. 결과가 없는 이유를 확인합니다. 보다 세련된 검색은 "5성"을 그와 동의어인 "고급"으로 처리하고 해당 결과를 반환하는 것입니다. 동의어 사용은 Azure Cognitive Search에서 가능하지만, 이 첫 번째 자습서에서는 다루지 않겠습니다.
 
4. 검색 텍스트로 "hot"를 입력합니다. "hotel"이라는 단어가 포함된 항목이 반환되지 _않습니다_. 몇 가지 결과가 반환되기는 하지만, 현재 검색은 오직 전체 단어만 찾습니다.

5. "풀", "햇살", "보기" 등의 다른 단어를 입력해 봅니다. Azure Cognitive Search가 가장 간단하게 작동하지만, 여전히 설득력 있는 수준입니다.

## <a name="test-edge-conditions-and-errors"></a>에지 조건 및 오류 테스트

모든 것이 완벽하게 작동하더라도 오류 처리 기능이 정상적으로 작동하는지 확인해야 합니다. 

1. **Index** 메서드에서 **try {** 호출 뒤에 **Throw new Exception()** 줄을 입력합니다. 이 예외는 텍스트를 검색할 때 오류를 강제로 발생시킵니다.

2. 앱을 실행하고, 검색 텍스트로 "바"를 입력하고, 검색 아이콘을 클릭합니다. 예외가 오류 보기로 이어집니다.

     ![오류 발생](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > 오류 페이지의 내부 오류 번호를 반환하면 보안 위험이 높아집니다. 앱이 범용인 경우 오류 발생 시 무엇을 반환해야 하는지 보안 및 모범 사례를 조사하세요.

3. 오류 처리 작업에 만족하는 경우 **Throw new Exception()** 을 제거합니다.

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* Azure Cognitive Search 호출은 간결하며, 결과를 쉽게 해석할 수 있습니다.
* 비동기 호출은 컨트롤러의 복잡성이 살짝 증가하지만, 고품질의 앱을 개발하려는 경우 가장 좋은 방법입니다.
* 이 앱은 **searchParameters**에 설정된 정의에 따라 간단한 텍스트 검색을 수행했습니다. 그러나 이 클래스를 검색의 정교함을 높이는 여러 멤버로 채울 수 있습니다. 이 앱의 성능을 높이기 위해 그렇게 많은 추가 작업이 필요하지는 않습니다.

## <a name="next-steps"></a>다음 단계

Azure Cognitive Search를 사용하여 최상의 사용자 경험을 제공하려면 더 많은 기능, 특히 페이징(페이지 번호 또는 무한 스크롤을 사용하여) 기능과 자동 완성/제안 기능을 추가해야 합니다. 또한 보다 세련된 검색 매개 변수(예: 특정 지점에서 지정된 반경 내에 있는 호텔의 지리적 위치 검색 및 검색 결과 정렬)를 고민해야 합니다.

이러한 단계는 자습서 시리즈에서 다룹니다. 페이징부터 시작하겠습니다.

> [!div class="nextstepaction"]
> [C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md)


