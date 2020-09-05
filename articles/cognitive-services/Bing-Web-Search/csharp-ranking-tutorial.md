---
title: 순위를 사용하여 검색 결과 표시
titleSuffix: Azure Cognitive Services
description: 검색 결과를 순위대로 표시하려면 Bing RankingResponse 답변을 사용하는 방법을 설명합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 8bfd7b6e5c9a2a7e3d9ed750e544036f3874271f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933225"
---
# <a name="build-a-console-app-search-client-in-c"></a>C#에서 콘솔 앱 검색 클라이언트 빌드

이 자습서에서는 사용자가 Bing Web Search API를 쿼리하고 순위 결과를 표시하게 하는 간단한 .NET Core 콘솔 앱을 빌드하는 방법을 설명합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Bing Web Search API에 단순 쿼리 만들기
- 순위대로 쿼리 결과 표시

## <a name="prerequisites"></a>필수 구성 요소

자습서를 따르려면 다음을 수행해야 합니다.

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
* [Visual Studio IDE](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-console-app-project"></a>새 콘솔 앱 프로젝트 만들기

Visual Studio에서 `Ctrl`+`Shift`+`N`를 사용하여 프로젝트를 만듭니다.

**새 프로젝트** 대화 상자에서 **Visual C# > Windows 클래식 바탕 화면 > 콘솔 앱(.NET Framework)** 을 클릭합니다.

애플리케이션 이름을 **MyConsoleSearchApp**으로 지정한 다음, **확인**을 클릭합니다.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>JSON.net NuGet 패키지를 프로젝트에 추가

JSON.net을 사용하면 API에서 반환한 JSON 응답으로 작업할 수 있습니다. NuGet 패키지를 프로젝트에 추가합니다.

- **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리...** 를 선택합니다.
- **찾아보기** 탭에서 `Newtonsoft.Json`를 검색합니다. 최신 버전을 선택한 다음, **설치**를 클릭합니다.
- **변경 내용 검토** 창에서 **확인** 단추를 클릭합니다.
- **NuGet: MyConsoleSearchApp**이라는 Visual Studio 탭을 닫습니다.

## <a name="add-a-reference-to-systemweb"></a>System.Web에 참조 추가

이 자습서는 `System.Web` 어셈블리를 따릅니다. 이 어셈블리에 대한 참조를 프로젝트에 추가합니다.

- **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가...** 를 선택합니다.
- **어셈블리 > 프레임워크**를 선택한 다음, 아래로 스크롤하여 **System.Web** 확인
- **확인**을 선택합니다.

## <a name="add-some-necessary-using-statements"></a>명령문을 사용하여 필요한 몇 가지 추가

이 자습서에서 코드는 명령문을 사용하는 세 개의 추가 항목이 필요합니다. 기존 `using` 문 아래의 이러한 명령문을 **Program.cs**의 상단에 추가합니다.

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>사용자에게 쿼리 요청

**솔루션 탐색기**에서 **Program.cs**를 엽니다. `Main()` 메서드를 업데이트합니다.

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

이 메서드는 다음과 같습니다.

- 사용자에게 쿼리 요청
- 쿼리를 실행하고 결과를 표시하려면 `RunQueryAndDisplayResults(userQuery)`를 호출
- 콘솔 창이 즉시 닫히는 것을 방지하려면 사용자 입력을 대기합니다.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Bing Web Search API를 사용하여 쿼리 결과 검색

다음으로, API를 쿼리하고 결과를 표시하는 메서드를 추가합니다.

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

이 메서드는 다음과 같습니다.

- `HttpClient`를 만들어 Web Search API를 쿼리
- Bing이 요청을 인증하기 위해 사용하는 `Ocp-Apim-Subscription-Key` HTTP 헤더를 설정
- 요청을 실행하고 JSON.net 결과를 사용하여 결과를 역직렬화함
- 순위대로 모든 결과를 표시하려면 `DisplayAllRankedResults(responseObjects)`를 호출

구독 키에 `Ocp-Apim-Subscription-Key`의 값을 설정해야 입니다.

## <a name="display-ranked-results"></a>순위 결과 표시

순위대로 결과를 표시하는 방법을 보여주기 전에 먼저 샘플 웹 검색 응답을 살펴보세요.

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` JSON 개체([설명서](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse))는 검색 결과에 대한 적절한 표시 순서를 설명합니다. 다음과 같이 우선 순위가 지정된 그룹 중 하나 이상을 포함합니다.

- `pole`: 가장 가시적인 처리를 얻을 수 있는 검색 결과입니다(예를 들어 메인라인 및 사이드바 위에 표시됨).
- `mainline`: mainline에 표시할 검색 결과입니다.
- `sidebar`: 사이드바에 표시할 검색 결과입니다. 사이드바가 없는 경우 메인라인 아래에 결과를 표시합니다.

순위 응답 JSON은 그룹 중 하나 이상을 포함할 수 있습니다.

**Program.cs**에서 올바른 순위에 따라 결과를 표시하려면 다음 메서드를 추가합니다.

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

이 메서드는 다음과 같습니다.

- 응답이 포함하는 `rankingResponse` 그룹에 대해 루프
- `DisplaySpecificResults(...)`를 호출하여 각 그룹에 항목 표시

**Program.cs**에서 다음 두 메서드를 추가합니다.

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

이러한 메서드는 콘솔에 검색 결과를 출력하려면 함께 작동합니다.

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행합니다. 출력은 다음과 비슷해야 합니다.

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>다음 단계

[순위를 사용하여 결과 표시](rank-results.md)를 자세히 알아봅니다.
