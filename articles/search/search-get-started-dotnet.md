---
title: '빠른 시작: .NET에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 C# 빠른 시작에서는 Azure.Search.Documents 클라이언트 라이브러리를 사용하여 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 설명합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7901254463ef052f3c13b2c9fc49c31bd8ebc454
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020867"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>빠른 시작: Azure.Search.Documents 클라이언트 라이브러리를 사용하여 검색 인덱스 만들기

새로운 [Azure.Search.Documents(버전 11) 클라이언트 라이브러리](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)를 사용하여 C#에서 검색 인덱스를 만들고 로드하고 쿼리하는 .NET Core 콘솔 애플리케이션을 만듭니다.

[소스 코드를 다운로드](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v11)하여 완성된 프로젝트로 시작하거나 이 문서의 단계에 따라 새 프로젝트를 만듭니다.

> [!NOTE]
> 이전 버전을 찾고 있나요? [Microsoft.Azure.Search v10을 사용하여 검색 인덱스 만들기](search-get-started-dotnet-v10.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에, 다음과 같은 도구 및 서비스를 준비해야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스 [서비스를 만들거나](search-create-service-portal.md) [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드는 Visual Studio 2019의 Community 평가판 버전에서 테스트되었습니다.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>키 및 엔드포인트 가져오기

서비스를 호출하려면 모든 요청에서 URL 엔드포인트 및 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키**에서 서비스에 대한 모든 권한이 있는 관리자 키를 가져옵니다. 개체를 만들거나 삭제할 때 필요합니다. 상호 교환 가능한 기본 키와 보조 키가 있습니다. 둘 중 아무 키나 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="set-up-your-project"></a>프로젝트 설정

Visual Studio를 시작하고 .NET Core에서 실행할 수 있는 새 콘솔 앱 프로젝트를 만듭니다. 

### <a name="install-the-nuget-package"></a>NuGet 패키지 설치

프로젝트를 만든 후 클라이언트 라이브러리를 추가합니다. [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)는 .NET에서 검색 서비스를 사용하는 데 필요한 모든 API를 제공하는 하나의 클라이언트 라이브러리로 구성됩니다.

1. **도구** > **NuGet 패키지 관리자**에서 **솔루션의 NuGet 패키지 관리...** 를 선택합니다. 

1. **찾아보기**를 클릭합니다.

1. `Azure.Search.Documents`를 검색하고 11.0.0 버전을 선택합니다.

1. 오른쪽에서 **설치**를 클릭하여 프로젝트 및 솔루션에 어셈블리를 추가합니다.

### <a name="create-a-search-client"></a>검색 클라이언트 만들기

1. **Program.cs**에서 네임스페이스를 `AzureSearch.SDK.Quickstart.v11`로 변경하고 다음 `using` 지시문을 추가합니다.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 다음 두 클라이언트를 만듭니다. [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient)는 인덱스를 만들고, [SearchClient](/dotnet/api/azure.search.documents.searchclient)는 기존 인덱스와 함께 작동합니다. 둘 다 만들기/삭제 권한 인증을 위한 서비스 엔드포인트와 관리 API 키가 필요합니다.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

이 빠른 시작에서는 호텔 데이터와 함께 로드하고 쿼리를 실행할 호텔 인덱스를 작성합니다. 이 단계에서는 인덱스의 필드를 정의합니다. 각 필드 정의에는 이름, 데이터 형식 및 필드가 사용되는 방식을 결정하는 특성이 포함됩니다.

이 예제에서는 간단한 설명과 가독성을 위해 Azure.Search.Documents 라이브러리의 동기 메서드를 사용합니다. 그러나 프로덕션 시나리오의 경우 비동기 메서드를 사용하여 앱의 확장성 및 응답성을 유지해야 합니다. 예를 들어 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 대신 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync)를 사용해야 합니다.

1. 다음과 같이 프로젝트에 빈 클래스 정의를 추가합니다. **Hotel.cs**

1. **Hotel.cs**에서 호텔 문서 구조를 정의합니다.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. **Program.cs**에서 필드 및 특성을 지정합니다. [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 및 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)는 인덱스를 만드는 데 사용됩니다.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

필드의 특성은 필드가 애플리케이션에서 사용되는 방식을 결정합니다. 예를 들어 `IsFilterable` 특성은 필터 식을 지원하는 모든 필드에 할당해야 합니다.

검색 가능한 문자열 필드에 [IsSearchable](/dotnet/api/microsoft.azure.search.models.field.issearchable)이 필요한 이전 버전의 .NET SDK와 달리, [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) 및 [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)를 사용하여 필드 정의를 간소화할 수 있습니다.

그 외의 특성은 이전 버전과 마찬가지로 여전히 정의 자체에 필요합니다. 예를 들어 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 및 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable)은 위의 샘플처럼 명시적으로 특성을 지정해야 합니다. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

Azure Cognitive Search는 서비스에 저장된 콘텐츠를 검색합니다. 이 단계에서는 방금 만든 호텔 인덱스를 따르는 JSON 문서를 로드합니다.

Azure Cognitive Search에서 문서는 인덱싱에 대한 입력인 동시에 쿼리의 출력인 데이터 구조입니다. 외부 데이터 소스에서 가져온, 문서 입력은 데이터베이스의 행, Blob Storage의 Blob 또는 디스크의 JSON 문서일 수 있습니다. 이 예제에서는 손쉬운 방법을 사용하여 5개 호텔에 대한 JSON 문서를 코드 자체에 포함합니다. 

문서를 업로드할 때 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 개체를 사용해야 합니다. IndexDocumentsBatch는 [작업](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions) 컬렉션을 포함하고, 각 작업은 Azure Cognitive Search에 수행할 작업([업로드, 병합, 삭제 및 mergeOrUpload](search-what-is-data-import.md#indexing-actions))을 알려주는 문서와 속성을 포함합니다.

1. **Program.cs**에서 문서 및 인덱스 작업 배열을 만든 다음, 이 배열을 `ndexDocumentsBatch`에 전달합니다. 아래 문서는 호텔 클래스에서 정의한 대로 hotels-quickstart-v11 인덱스를 준수합니다.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 개체를 초기화 한 후에는 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 개체에서 [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments)를 호출하여 이 개체를 인덱스에 전송할 수 있습니다.

1. 이 앱은 모든 명령을 순차적으로 실행하는 콘솔 앱이므로 인덱싱과 쿼리 사이에 2초 대기 시간을 추가합니다.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2초 지연은 비동기식 인덱싱을 보정합니다. 따라서 쿼리가 실행되기 전에 모든 문서를 인덱싱할 수 있습니다. 지연 시 코딩은 일반적으로 데모, 테스트, 샘플 애플리케이션에서만 필요합니다.

## <a name="3---search-an-index"></a>3 - 인덱스 검색

첫 번째 문서의 인덱싱이 완료되는 즉시 쿼리 결과를 얻을 수 있지만 인덱스에 대한 실제 테스트는 모든 문서의 인덱싱이 완료될 때까지 기다려야 합니다.

이 섹션에서는 쿼리 논리 및 결과라는 두 가지 기능을 추가합니다. 쿼리에는 [Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드를 사용합니다. 이 메서드는 검색 텍스트(쿼리 문자열)뿐 아니라 다른 [옵션](/dotnet/api/azure.search.documents.searchoptions)을 사용합니다.

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) 클래스는 결과를 나타냅니다.

1. **Program.cs**에서 검색 결과를 콘솔에 출력하는 WriteDocuments 메서드를 만듭니다.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. 쿼리를 실행하고 결과를 반환하는 RunQueries 메서드를 만듭니다. 결과는 Hotel 개체입니다.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

이 예제에서는 [쿼리에서 용어를 매칭하는 두 가지 방법](search-query-overview.md#types-of-queries)인 전체 텍스트 검색 및 필터를 보여줍니다.

+ 전체 텍스트 검색은 인덱스의 검색 가능 필드에서 하나 이상의 용어를 쿼리합니다. 첫 번째 쿼리는 전체 텍스트 검색입니다. 전체 텍스트 검색은 결과의 순위를 매기는 데 사용되는 관련성 점수를 생성합니다.

+ 필터는 인덱스의 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 필드를 통해 평가되는 부울 식입니다. 필터는 포함 또는 제외 값을 쿼리합니다. 따라서 필터 쿼리와 관련된 관련성 점수가 없습니다. 마지막 두 개의 쿼리는 필터 검색을 보여줍니다.

전체 텍스트 검색과 필터를 함께 사용하거나 별도로 사용할 수 있습니다.

검색 및 필터는 모두 [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드를 사용하여 수행됩니다. 검색 쿼리는 `searchText` 문자열로 전달할 수 있는 반면, 필터 식은 [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) 클래스의 [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) 속성으로 전달할 수 있습니다. 검색하지 않고 필터링하려면 [Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드의 `searchText` 매개 변수에 대한 `"*"`를 전달합니다. 필터링하지 않고 검색하려면 `Filter` 속성을 설정하지 않고 그대로 두거나 `SearchOptions` 인스턴스에 전달하지 않아야 합니다.

## <a name="run-the-program"></a>프로그램 실행

F5 키를 눌러서 애플리케이션을 다시 빌드하고 프로그램 전체를 실행합니다. 

출력에는 [Console.WriteLIne](/dotnet/api/system.console.writeline)의 메시지가 포함되며, 쿼리 정보와 결과가 추가됩니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

C# 빠른 시작에서는 인덱스를 만들어서 문서와 함께 로드하고 쿼리를 실행하는 일련의 작업을 수행했습니다. 여러 단계에서, 가독성과 이해를 돕기 위해 손쉬운 방법을 사용하여 코드를 간소화했습니다. 기본 개념에 익숙한 경우, 지식의 깊이를 더할 수 있도록 대안적인 접근법과 개념을 알아볼 수 있는 다음 문서를 살펴보는 것이 좋습니다. 

> [!div class="nextstepaction"]
> [.NET으로 개발하는 방법](search-howto-dotnet-sdk.md)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

> [!div class="nextstepaction"]
> [Cost Management를 통한 비용 분석 시작](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)