---
title: '빠른 시작: .NET을 사용하여 C#에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 C# 빠른 시작에서 Azure Cognitive Search .NET SDK를 사용하여 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 설명합니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: e9f43f729fc9e08054cd160b82945ee4bcd5821c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121654"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>빠른 시작: .NET SDK를 사용하여 C#에서 Azure Cognitive Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [포털](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Visual Studio와 [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk)를 사용하여 Azure Cognitive Search 인덱스를 만들고, 로드하고, 쿼리하는 .NET Core C# 콘솔 애플리케이션을 만듭니다. 이 문서에서는 애플리케이션을 만드는 방법을 단계별로 설명합니다. 또는 [전체 애플리케이션을 다운로드하고 실행](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart)할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> 이 문서의 데모 코드는 간단히 하기 위해 Azure Cognitive Search .NET SDK의 동기 메서드를 사용합니다. 하지만 프로덕션 시나리오에서는 확장성과 응답성을 유지하기 위해 자체 애플리케이션에 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어, `Create` 및 `Delete` 대신`CreateAsync` 및 `DeleteAsync`를 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에 필요한 서비스와 도구는 다음과 같습니다.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

서비스를 호출하려면 모든 요청에서 URL 엔드포인트 및 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   쿼리 키도 가져옵니다. 쿼리 요청은 읽기 전용 액세스로 발급하는 것이 좋습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="set-up-your-environment"></a>환경 설정

먼저 Visual Studio를 열고 .NET Core에서 실행할 수 있는 새 콘솔 앱 프로젝트를 만듭니다.

### <a name="install-nuget-packages"></a>NuGet 패키지 설치

[Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk)는 NuGet 패키지로 배포되는 몇 가지 클라이언트 라이브러리로 구성됩니다.

이 프로젝트의 경우 `Microsoft.Azure.Search` NuGet 패키지 버전 9와 최신 `Microsoft.Extensions.Configuration.Json` NuGet 패키지를 사용합니다.

1. **도구** > **NuGet 패키지 관리자**에서 **솔루션의 NuGet 패키지 관리...** 를 선택합니다. 

1. **찾아보기**를 클릭합니다.

1. `Microsoft.Azure.Search`를 검색하고 버전 9.0.1 이상을 선택합니다.

1. 오른쪽에서 **설치**를 클릭하여 프로젝트 및 솔루션에 어셈블리를 추가합니다.

1. 버전 2.2.0 이상을 선택하고 `Microsoft.Extensions.Configuration.Json`에 대해 반복합니다.


### <a name="add-azure-cognitive-search-service-information"></a>Azure Cognitive Search 서비스 정보 추가

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목...** 을 선택합니다. 

1. 새 항목 추가에서 "JSON"을 검색하여 항목 종류의 JSON 관련 목록을 반환합니다.

1. **JSON 파일**을 선택하고 파일 이름을 "appsettings.json"으로 지정하고 **추가**를 클릭합니다. 

1. 출력 디렉터리에 파일을 추가합니다. appsettings.json을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **출력 디렉터리로 복사**에서 **변경된 내용만 복사**를 선택합니다.

1. 다음 JSON을 새 JSON 파일에 복사합니다. 검색 서비스 이름(YOUR-SEARCH-SERVICE-NAME)과 관리자 API 키(YOUR-ADMIN-API-KEY)를 유효한 값으로 바꿉니다. 서비스 엔드포인트가 `https://mydemo.search.windows.net`이면 서비스 이름은 "mydemo"가 됩니다.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>프로젝트에 클래스 ".Method" 파일 추가

콘솔 창에 결과를 출력할 때 Hotel 개체의 개별 필드를 문자열로 반환해야 합니다. 필요한 코드를 새 파일 2개에 복사하여, 이 작업을 수행하도록 [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8)을 구현할 수 있습니다.

1. 프로젝트에 빈 클래스 정의 2개를 추가합니다. Address.Methods.cs, Hotel.Methods.cs

1. Address.Methods.cs의 기본 콘텐츠를 다음 코드 [1~32줄](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)로 덮어씁니다.

1. Hotel.Methods.cs에서 [1~66줄](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)을 복사합니다.


## <a name="1---create-index"></a>1 - 인덱스 만들기

호텔 인덱스는 단순 및 복합 필드로 구성되어 있습니다. 단순 필드는 "HotelName" 또는 "Description"이고 복합 필드는 하위 필드가 있는 주소 또는 방의 컬렉션입니다. 인덱스에 복합 유형이 포함된 경우에는 복합 필드 정의를 별도의 클래스로 분리합니다.

1. 프로젝트에 빈 클래스 정의 2개를 추가합니다. Address.cs, Hotel.cs

1. Address.cs의 기본 콘텐츠를 다음 코드로 덮어씁니다.

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. Hotel.cs에서 클래스는 주소 클래스에 대한 참조를 포함하여 인덱스의 전체 구조를 정의합니다.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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
        }
    }
    ```

    필드의 특성은 필드가 애플리케이션에서 사용되는 방식을 결정합니다. 예를 들어 전체 텍스트 검색에 포함되어야 하는 모든 필드에는 `IsSearchable` 특성을 할당해야 합니다. 
    
    > [!NOTE]
    > .NET SDK에서 필드에는 명시적으로 [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) 및 [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet) 특성이 사용되어야 합니다. 이 동작은 데이터 형식에 따라 특성을 암시적으로 사용하도록 설정하는 REST API와는 대조적입니다(예를 들어 단순 문자열 필드는 자동으로 검색 가능함).

    `string` 형식의 인덱스에는 정확히 하나의 필드가 각 문서를 고유하게 식별하는 키(*key*) 필드여야 합니다. 이 스키마에서 키는 `HotelId`입니다.

    이 인덱스에서 설명 필드는 선택적 [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) 속성을 사용합니다. 이 속성은 기본 표준 Lucene 분석기를 재정의하려는 경우 지정합니다. `description_fr` 필드는 프랑스어 텍스트를 저장하기 때문에 프랑스어 Lucene 분석기([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet))를 사용합니다. `description`은 선택적 Microsoft 언어 분석기([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet))를 사용합니다.

1. Program.cs에서 애플리케이션의 구성 파일 (appsettings.json)에 저장되는 값을 사용하여 서비스에 연결되는 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 클래스 인스턴스를 만듭니다. 

   `SearchServiceClient`에는 Azure Cognitive Search 인덱스를 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공하는 [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) 속성이 있습니다. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    가능하면, 연결이 너무 많이 열리는 것을 방지하기 위해 애플리케이션에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 클래스 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.

   클래스에는 몇 가지 생성자가 있습니다. 검색 서비스 이름과 `SearchCredentials` 개체를 매개 변수로 사용할 생성자입니다. `SearchCredentials` 는 API 키를 래핑합니다.

    인덱스 정의에서, `Field` 개체를 만드는 가장 쉬운 방법은 `FieldBuilder.BuildForType` 메서드를 호출하여 형식 매개 변수에 대한 모델 클래스를 전달합니다. 모델 클래스에는 인덱스 필드에 매핑되는 속성이 있습니다. 이 매핑을 통해 모델 클래스의 인스턴스에 대한 검색 인덱스에서 문서를 바인딩할 수 있습니다.

    > [!NOTE]
    > 모델 클래스를 사용할 계획이 아니라면 `Field` 개체를 직접.만들어 여전히 인덱스를 정의할 수 있습니다. 필드 이름을 데이터 형식(또는 문자열 필드의 분석기)과 함께 생성자에 제공할 수 있습니다. 또한 `IsSearchable`, `IsFilterable`과 같은 다른 속성은 몇 가지 이름으로 설정할 수도 있습니다.
    >

1. F5 키를 눌러서 앱을 빌드하고 색인을 만듭니다. 

    프로젝트 빌드가 성공하면 콘솔 창이 열리고 인덱스 삭제 및 생성에 대한 상태 메시지가 화면에 출력됩니다. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

Azure Cognitive Search에서 문서는 인덱싱에 대한 입력인 동시에 쿼리의 출력인 데이터 구조입니다. 외부 데이터 소스에서 가져온, 문서 입력은 데이터베이스의 행, Blob Storage의 Blob 또는 디스크의 JSON 문서일 수 있습니다. 이 예에서는 손쉬운 방법을 사용하여 4개 호텔에 대한 JSON 문서를 코드 자체에 포함합니다. 

문서를 업로드할 때는 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 개체를 사용해야 합니다. `IndexBatch`는 [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 개체의 컬렉션을 포함하며, 이들 각각은 Azure Cognitive Search가 수행할 작업([업로드, 병합, 삭제 및 mergeOrUpload](search-what-is-data-import.md#indexing-actions))을 알려주는 문서와 속성을 포함합니다.

1. Program.cs에서 문서 및 인덱스 작업의 배열을 만든 다음, 배열을 `IndexBatch`에 전달합니다. 아래 문서는 호텔 및 주소 클래스에 정의된 호텔-빠른 시작 인덱스를 준수합니다.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    `IndexBatch` 개체를 초기화하면 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 개체에서 [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet)를 호출하여 인덱스로 보낼 수 있습니다. `Documents`는 인덱스의 문서를 추가, 수정, 삭제 또는 쿼리하는 메서드를 제공하는 `SearchIndexClient`의 속성입니다.

    `Index` 메서드에 대한 호출을 둘러싼 `try`/`catch`는 인덱싱 실패를 catch합니다. 실패는 서비스 부하가 높을 때 발생할 수 있습니다. 프로덕션 코드에서는 실패한 문서의 인덱싱을 지연시켰다가 다시 시도하거나, 샘플처럼 기록하고 계속하거나, 애플리케이션의 데이터 일관성 요구 사항을 충족시키는 다른 방식으로 처리할 수 있습니다.

    2초 지연은 비동기식 인덱싱을 보정합니다. 따라서 쿼리가 실행되기 전에 모든 문서를 인덱싱할 수 있습니다. 지연 시 코딩은 일반적으로 데모, 테스트, 샘플 애플리케이션에서만 필요합니다.

1. Program.cs의 main에서 "2 - Load documents" 줄의 주석 처리를 제거합니다. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. F5를 눌러서 앱을 다시 빌드합니다. 

    프로젝트 빌드가 성공하면 콘솔 창이 열리고 이번에는 문서 업로드에 대한 메시지가 포함된 상태 메시지가 출력됩니다. Azure Portal 검색 서비스 **개요** 페이지의 호텔-빠른 인덱스에 이제 4개의 문서가 있습니다.

문서 처리에 대한 자세한 내용은 [".NET SDK가 문서를 처리하는 방법"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)을 참조하세요.

## <a name="3---search-an-index"></a>3 - 인덱스 검색

첫 번째 문서의 인덱싱이 완료되는 즉시 쿼리 결과를 얻을 수 있지만 인덱스에 대한 실제 테스트는 모든 문서의 인덱싱이 완료될 때까지 기다려야 합니다. 

이 섹션에서는 쿼리 논리 및 결과라는 두 가지 기능을 추가합니다. 쿼리에는 [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) 메서드를 사용합니다. 이 메서드는 검색 텍스트뿐만 아니라 다른 [매개 변수](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)를 사용합니다. 

[`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) 클래스는 결과를 나타냅니다.


1. Program.cs에서 검색 결과를 콘솔에 출력하는 WriteDocuments 메서드를 만듭니다.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. 쿼리를 실행하고 결과를 반환하는 RunQueries 메서드를 만듭니다. 결과는 Hotel 개체입니다. select 매개 변수를 사용하여 개별 필드를 나타낼 수 있습니다. select 매개 변수에 필드가 포함되어 있지 않으면 해당 Hotel 속성은 null입니다.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    [쿼리에서 용어를 매칭하는 방법](search-query-overview.md#types-of-queries)은 2가지(전체 텍스트 검색 및 필터)입니다. 전체 텍스트 검색 쿼리는 인덱스의 `IsSearchable` 필드에서 하나 이상의 용어를 검색합니다. 필터는 인덱스의 `IsFilterable` 필드를 통해 평가되는 부울 식입니다. 전체 텍스트 검색과 필터를 함께 사용하거나 별도로 사용할 수 있습니다.

    검색 및 필터는 모두 `Documents.Search` 메서드를 사용하여 수행됩니다. 검색 쿼리는 `searchText` 매개 변수에 전달될 수 있는 반면 필터 식은 `SearchParameters` 클래스의 `Filter` 속성에 전달될 수 있습니다. 검색하지 않고 필터링하려면 `searchText` 매개 변수에 `"*"`를 전달합니다. 필터링하지 않고 검색하려면 `Filter` 속성을 설정하지 않고 그대로 두거나 `SearchParameters` 인스턴스에 전달하지 않아야 합니다.

1. Program.cs의 main에서 "3 - Search" 줄의 주석 처리를 제거합니다. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. 이제 솔루션 완료되었습니다. F5 키를 눌러서 애플리케이션을 다시 빌드하고 프로그램 전체를 실행합니다. 

    출력에는 전과 동일한 메시지가 포함되며 쿼리 정보와 결과가 추가됩니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

C# 빠른 시작에서는 인덱스를 만들어서 문서와 함께 로드하고 쿼리를 실행하는 일련의 작업을 수행했습니다. 여러 단계에서, 가독성과 이해를 돕기 위해 손쉬운 방법을 사용하여 코드를 간소화했습니다. 기본 개념에 익숙한 경우, 지식의 깊이를 더할 수 있도록 대안적인 접근법과 개념을 알아볼 수 있는 다음 문서를 살펴보는 것이 좋습니다. 

샘플 코드와 인덱스는 확장된 버전입니다. 다음 샘플에서는 방 컬렉션을 추가하고 다양한 클래스와 작업을 사용하며 프로세싱이 작동하는 원리를 자세히 살펴봅니다.

> [!div class="nextstepaction"]
> [.NET으로 개발하는 방법](search-howto-dotnet-sdk.md)