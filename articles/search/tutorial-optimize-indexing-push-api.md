---
title: C# 자습서 - 푸시 API를 사용하여 인덱싱 최적화
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 푸시 API를 사용하여 데이터를 효율적으로 인덱싱하는 방법을 알아봅니다. 이 자습서 및 샘플 코드는 C#에 있습니다.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bfb2598fb3a207bbdfaade9086efd07827b077dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998427"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>자습서: 푸시 API를 사용하여 인덱싱 최적화

Azure Cognitive Search는 데이터를 검색 인덱스로 가져오기 위한 [두 가지 기본 방법](search-what-is-data-import.md)을 지원합니다. 즉, 프로그래밍 방식으로 데이터를 인덱스로 *푸시*하거나 지원되는 데이터 원본에서 [Azure Cognitive Search 인덱서](search-indexer-overview.md)를 가리켜서 데이터를 *풀*합니다.

이 자습서에서는 요청을 일괄 처리하고 지수 백오프 다시 시도 전략을 사용하여 [푸시 모델](search-what-is-data-import.md#pushing-data-to-an-index)을 통해 데이터를 효율적으로 인덱싱하는 방법에 대해 설명합니다. [애플리케이션을 다운로드하여 실행](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing)할 수 있습니다. 이 문서에서는 애플리케이션의 주요 측면과 데이터를 인덱싱할 때 고려해야 하는 요소에 대해 설명합니다.

이 자습서에서는 C# 및 [.NET SDK](/dotnet/api/overview/azure/search)를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 인덱스 만들기
> * 다양한 일괄 처리 크기를 테스트하여 가장 효율적인 크기 결정
> * 비동기 데이터 인덱싱
> * 여러 스레드를 사용하여 인덱싱 속도 향상
> * 지수 백오프 다시 시도 전략을 사용하여 실패한 항목 다시 시도

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 필요한 서비스와 도구는 다음과 같습니다.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

<a name="get-service-info"></a>

## <a name="download-files"></a>파일 다운로드

이 자습서의 소스 코드는 [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub 리포지토리의 [optimzize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) 폴더에 있습니다.

## <a name="key-considerations"></a>주요 고려 사항

데이터를 인덱스로 푸시할 때 인덱싱 속도에 영향을 주는 몇 가지 주요 고려 사항이 있습니다. 이러한 요소는 [큰 데이터 세트 인덱싱 문서](search-howto-large-index.md)에서 자세히 알아볼 수 있습니다.

고려해야 할 6가지 주요 요소는 다음과 같습니다.

+ **서비스 계층 및 파티션/복제본 수** - 파티션을 추가하고 계층을 늘리면 인덱싱 속도가 모두 빨라집니다.
+ **인덱스 스키마** - 필드를 추가하고 추가 속성(예: *searchable*, *facetable* 또는 *filterable*)을 필드에 추가하면 인덱싱 속도가 모두 느려집니다.
+ **일괄 처리 크기** - 최적의 일괄 처리 크기는 인덱스 스키마 및 데이터 세트에 따라 달라집니다.
+ **스레드/작업자 수** - 단일 스레드는 인덱싱 속도를 최대한 활용하지 못합니다.
+ **다시 시도 전략** - 지수 백오프 다시 시도 전략을 사용하여 인덱싱을 최적화해야 합니다.
+ **네트워크 데이터 전송 속도** - 데이터 전송 속도는 제한 요소가 될 수 있습니다. 데이터 전송 속도를 높이려면 Azure 환경 내에서 데이터를 인덱싱합니다.


## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search 서비스 만들기

이 자습서를 완료하려면 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search 서비스가 필요합니다. 인덱싱 속도를 정확하게 테스트하고 최적화할 수 있도록 프로덕션에서 사용하려는 것과 동일한 계층을 사용하는 것이 좋습니다.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

API 호출에는 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

## <a name="2---set-up-your-environment"></a>2 - 환경 설정

1. Visual Studio를 시작하고 **OptimizeDataIndexing.sln**을 엽니다.
1. 솔루션 탐색기에서 **appsettings.json**을 열고 연결 정보를 제공합니다.
1. `searchServiceName`의 경우 전체 URL이 "https://my-demo-service.search.windows.net"이면 제공할 서비스 이름은 "my-demo-service"입니다.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - 코드 살펴보기

*appsettings.json*이 업데이트되면 **OptimizeDataIndexing.sln**의 샘플 프로그램을 빌드하고 실행할 준비가 됩니다.

이 코드는 [C# 빠른 시작](search-get-started-dotnet.md)에서 파생되었습니다. .NET SDK 작업의 기본 사항에 대한 자세한 정보는 해당 문서에서 확인할 수 있습니다.

이 간단한 C#/.NET 콘솔 앱에서 수행하는 작업은 다음과 같습니다.

+ C# Hotel 클래스(Address 클래스도 참조)의 데이터 구조를 기반으로 하여 새 인덱스를 만듭니다.
+ 다양한 일괄 처리 크기를 테스트하여 가장 효율적인 크기를 결정합니다.
+ 데이터를 비동기적으로 인덱싱합니다.
    + 여러 스레드를 사용하여 인덱싱 속도 향상
    + 지수 백오프 다시 시도 전략을 사용하여 실패한 항목 다시 시도

 프로그램을 실행하기 전에 잠시 시간을 내어 이 샘플에 대한 코드와 인덱스 정의를 살펴보세요. 관련 코드는 다음과 같은 여러 파일에 있습니다.

  + **Hotel.cs** 및 **Address.cs** - 인덱스를 정의하는 스키마가 포함되어 있습니다.
  + **DataGenerator.cs** - 대량의 호텔 데이터를 쉽게 만들 수 있는 단순 클래스가 포함되어 있습니다.
  + **ExponentialBackoff.cs** - 아래에서 설명한 대로 인덱싱 프로세스를 최적화하는 코드가 포함되어 있습니다.
  + **Program.cs** - Azure Cognitive Search 인덱스를 만들고 삭제하고, 데이터 일괄 처리를 인덱싱하며, 다양한 일괄 처리 크기를 테스트하는 함수가 포함되어 있습니다.

### <a name="creating-the-index"></a>인덱스 만들기

이 샘플 프로그램에서는 .NET SDK를 사용하여 Azure Cognitive Search 인덱스를 정의하고 만듭니다. [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) 클래스를 활용하여 C# 데이터 모델 클래스에서 인덱스 구조를 생성합니다.

데이터 모델은 Hotel 클래스에서 정의되며, Address 클래스에 대한 참조도 포함하고 있습니다. FieldBuilder는 여러 클래스 정의를 드릴다운하여 인덱스에 대한 복합 데이터 구조를 생성합니다. 메타데이터 태그는 검색 가능인지 아니면 정렬 가능인지와 같은 각 필드의 특성을 정의하는 데 사용됩니다.

**Hotel.cs** 파일의 다음 코드 조각에서는 단일 필드 및 다른 데이터 모델 클래스에 대한 참조를 지정하는 방법을 보여 줍니다.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

**Program.cs** 파일에서 인덱스는 `FieldBuilder.BuildForType<Hotel>()` 메서드에서 생성된 이름과 필드 컬렉션으로 정의되어 다음과 같이 만들어집니다.

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>데이터 생성

테스트할 데이터를 생성하기 위해 단순 클래스가 **DataGenerator.cs** 파일에서 구현됩니다. 이 클래스의 유일한 목적은 인덱싱에 대한 고유 ID를 사용하여 많은 수의 문서를 쉽게 생성할 수 있도록 하는 것입니다.

고유 ID를 사용하는 100,000개의 호텔 목록을 얻으려면 다음 두 줄의 코드를 실행합니다.

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

이 샘플에는 테스트에 사용할 수 있는 두 가지 크기(**small** 및 **large**)의 호텔이 있습니다.

인덱스의 스키마는 인덱싱 속도에 큰 영향을 줄 수 있습니다. 이러한 영향으로 인해 이 자습서를 실행한 후에는 원하는 인덱스 스키마와 일치하는 데이터를 생성하기 위해 이 클래스를 변환하는 것이 좋습니다.

## <a name="4---test-batch-sizes"></a>4 - 일괄 처리 크기 테스트

Azure Cognitive Search는 단일 또는 여러 문서를 인덱스에 로드하기 위해 다음 API를 지원합니다.

+ [문서 추가, 업데이트 또는 삭제(REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction 클래스](/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 또는 [indexBatch 클래스](/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

문서를 일괄 처리 방식으로 인덱싱하면 인덱싱 성능이 크게 향상됩니다. 이러한 일괄 처리는 최대 1,000개의 문서 또는 일괄 처리당 최대 16MB까지 가능합니다.

가장 적합한 데이터 일괄 처리 크기를 결정하는 것은 인덱싱 속도를 최적화하는 핵심 구성 요소입니다. 최적의 일괄 처리 크기에 영향을 주는 두 가지 주요 요소는 다음과 같습니다.

+ 인덱스의 스키마
+ 데이터의 크기

최적의 일괄 처리 크기는 인덱스와 데이터에 따라 달라지므로 다양한 일괄 처리 크기를 테스트하여 시나리오에서 가장 빠른 인덱싱 속도를 달성할 수 있는 결과를 확인하는 것이 가장 좋습니다.

다음 함수에서는 일괄 처리 크기를 테스트하는 간단한 방법을 보여 줍니다.

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

모든 문서의 크기가 동일하지 않으므로(이 샘플에 있지만), 검색 서비스에 보내는 데이터의 크기를 추정합니다. 이를 위해 아래 함수를 사용하여 먼저 개체를 json으로 변환한 다음, 크기를 바이트 단위로 결정합니다. 이 기법을 사용하면 MB/초 인덱싱 속도 측면에서 가장 효율적인 일괄 처리 크기를 결정할 수 있습니다.

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

이 함수에는 `ISearchIndexClient` 및 각 일괄 처리 크기에 대해 테스트하려는 시도 횟수가 필요합니다. 각 일괄 처리의 인덱싱 시간이 약간 변동될 수 있으므로 결과의 통계적 유의미성을 높이기 위해 기본적으로 각 일괄 처리를 세 번 시도합니다.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

이 함수를 실행하면 콘솔에 아래와 같은 출력이 표시됩니다.

   ![일괄 처리 크기 테스트 함수의 출력](media/tutorial-optimize-data-indexing/test-batch-sizes.png "일괄 처리 크기 테스트 함수의 출력")

가장 효율적인 일괄 처리 크기를 확인한 다음, 자습서의 다음 단계에서 해당 일괄 처리 크기를 사용합니다. MB/초에서 정체 상태인 여러 일괄 처리 크기가 표시될 수 있습니다.

## <a name="5---index-data"></a>5 - 데이터 인덱싱

이제 사용하려는 일괄 처리 크기를 확인했으므로 다음 단계에서는 데이터 인덱싱을 시작합니다. 데이터를 효율적으로 인덱싱하기 위해 이 샘플에서 다음을 수행합니다.

* 여러 스레드/작업자를 사용합니다.
* 지수 백오프 다시 시도 전략을 구현합니다.

### <a name="use-multiple-threadsworkers"></a>여러 스레드/작업자 사용

Azure Cognitive Search의 인덱싱 속도를 최대한 활용하려면 여러 스레드를 사용하여 일괄 처리 인덱싱 요청을 서비스에 동시에 보내야 할 수 있습니다.  

위에서 언급한 몇 가지 주요 고려 사항은 최적의 스레드 수에 영향을 줍니다. 이 샘플을 수정하고 다양한 스레드 수로 테스트하여 시나리오에 가장 적합한 스레드 수를 결정할 수 있습니다. 그러나 여러 스레드가 동시에 실행되는 동안에는 대부분의 효율성 이점을 활용할 수 있어야 합니다.

검색 서비스에 대한 요청을 늘리면 요청이 완전히 성공하지 못했음을 나타내는 [HTTP 상태 코드](/rest/api/searchservice/http-status-codes)가 발생할 수 있습니다. 인덱싱 중에 발생하는 두 가지 일반적인 HTTP 상태 코드는 다음과 같습니다.

+ **503 서비스를 사용할 수 없음** - 이 오류는 시스템의 부하가 높고 요청을 현재 처리할 수 없음을 의미합니다.
+ **207 여러 상태** - 이 오류는 일부 문서가 성공했지만 하나 이상의 문서가 실패했음을 의미합니다.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>지수 백오프 다시 시도 전략 구현

오류가 발생하면 [지수 백오프 다시 시도 전략](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)을 사용하여 요청을 다시 시도해야 합니다.

Azure Cognitive Search의 .NET SDK에서 503 및 기타 실패한 요청을 자동으로 다시 시도하지만 207을 다시 시도하는 사용자 고유의 논리를 구현해야 합니다. [Polly](https://github.com/App-vNext/Polly)와 같은 오픈 소스 도구를 사용하여 다시 시도 전략을 구현할 수도 있습니다. 

이 샘플에서는 자체의 지수 백오프 다시 시도 전략을 구현합니다. 이 전략을 구현하기 위해 먼저 실패한 요청에 대한 `maxRetryAttempts` 및 초기 `delay`를 포함하여 일부 변수를 정의합니다.

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

[IndexBatchException](/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet)을 catch하는 것이 중요합니다. 이러한 예외는 인덱싱 작업이 부분적으로만 성공했음(207)을 나타내기 때문입니다. 실패한 항목만 포함하는 새 일괄 처리를 쉽게 만들 수 있도록 하는 `FindFailedActionsToRetry` 메서드를 사용하여 실패한 항목을 다시 시도해야 합니다.

`IndexBatchException` 이외의 예외도 catch하여 요청이 완전히 실패했음을 나타내야 합니다. 이러한 예외는 특히 .NET SDK에서 503을 자동으로 다시 시도하므로 일반적이지 않습니다.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

여기서는 쉽게 호출할 수 있도록 지수 백오프 코드를 함수로 래핑합니다.

그런 다음, 활성 스레드를 관리하는 다른 함수를 만듭니다. 간단히 하기 위해 이 함수는 여기에 포함되지 않았지만 [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs)에서 확인할 수 있습니다. 이 함수는 다음 명령을 사용하여 호출할 수 있습니다. 여기서 `hotels`는 업로드하려는 데이터이고, `1000`은 일괄 처리 크기이며, `8`은 동시 스레드 수입니다.

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

함수를 실행하면 다음과 같은 출력이 표시됩니다.

![데이터 인덱싱 함수의 출력](media/tutorial-optimize-data-indexing/index-data-start.png "데이터 인덱싱 함수의 출력")

문서 일괄 처리가 실패하면 실패 및 일괄 처리가 다시 시도되고 있음을 나타내는 오류가 출력됩니다.

![데이터 인덱싱 함수의 오류](media/tutorial-optimize-data-indexing/index-data-error.png "일괄 처리 크기 테스트 함수의 출력")

함수 실행이 완료되면 모든 문서가 인덱스에 추가되었는지 확인할 수 있습니다.

## <a name="6---explore-index"></a>6- 인덱스 검색

프로그램이 프로그래밍 방식으로 실행되거나 포털에서 [**검색 탐색기**](search-explorer.md)를 사용하여 실행되면 채워진 검색 인덱스를 검색할 수 있습니다.

### <a name="programatically"></a>프로그래밍 방식으로

인덱스의 문서 수를 확인하는 두 가지 주요 옵션으로 [문서 수 계산 API](/rest/api/searchservice/count-documents) 및 [인덱스 통계 가져오기 API](/rest/api/searchservice/get-index-statistics)가 있습니다. 두 경로에는 모두 업데이트하는 데 약간의 추가 시간이 필요할 수 있으므로 반환되는 문서 수가 처음에 예상한 것보다 적으면 경고를 표시하지 않습니다.

#### <a name="count-documents"></a>문서 수 계산

문서 수 계산 작업은 검색 인덱스의 문서 수를 검색합니다.

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>인덱스 통계 가져오기

인덱스 통계 가져오기 작업은 현재 인덱스의 문서 수와 스토리지 사용량을 반환합니다. 인덱스 통계는 업데이트하는 데 문서 수보다 더 오래 걸립니다.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 검색 서비스 **개요** 페이지를 열고, **인덱스** 목록에서 **optimize-indexing** 인덱스를 찾습니다.

  ![Azure Cognitive Search 인덱스 목록](media/tutorial-optimize-data-indexing/portal-output.png "Azure Cognitive Search 인덱스 목록")

*문서 수* 및 *스토리지 크기*는 [인덱스 통계 가져오기 API](/rest/api/searchservice/get-index-statistics)를 기반으로 하며 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

이 자습서의 샘플 코드는 기존 인덱스를 확인하고 삭제하여 코드를 다시 실행할 수 있도록 합니다.

포털을 사용하여 인덱스를 삭제할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 데이터를 효율적으로 수집한다는 개념에 익숙해졌으므로 Lucene 쿼리 아키텍처 및 Azure Cognitive Search에서 전체 텍스트 검색이 작동하는 방식에 대해 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)