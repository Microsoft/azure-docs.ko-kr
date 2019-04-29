---
title: 동의어 C# 예제-Azure Search
description: 이 C# 예제에서는 Azure Search에서 인덱스에 동의어 기능을 추가 하는 방법을 알아봅니다. 동의어 맵은 동일한 용어 목록입니다. 동의어를 지원하는 필드는 사용자가 제공한 용어 및 모든 관련 동의어를 포함하도록 쿼리를 확장합니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f9cadfcf0c027f4aec4f9d4928872709ee7d3e99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281780"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>예제: C#에서 Azure Search에 대한 동의어 추가

동의어는 입력 용어와 의미상 동일하다고 간주되는 용어를 비교하여 쿼리를 확장합니다. 예를 들어 "car"를 "automobile" 또는 "vehicle"이라는 용어를 포함하는 문서와 비교합니다. 

Azure Search에서 동의어는 동등한 용어를 연결하는 *매핑 규칙*을 통해 *동의어 맵*에 정의됩니다. 이 예제에서는 추가 및 기존 인덱스와 동의어를 사용 하기 위한 필수 단계를 다룹니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 매핑 규칙을 만들고 게시하여 동의어를 사용하도록 설정 
> * 쿼리 문자열에서 동의어 맵 참조

여러 동의어 맵을 만들고 모든 인덱스에 사용할 수 있는 서비스 전반 리소스로 게시한 다음 필드 수준에서 사용할 용어를 참조합니다. Azure Search에서는 쿼리 시 인덱스를 검색하는 것 외에도 용어가 쿼리에서 사용되는 필드에 지정된 경우 동의어 맵에서 조회를 수행합니다.

> [!NOTE]
> 동의어는 최신 API 및 SDK 버전에서 지원됩니다(api-버전=2017-11-11, SDK 버전 5.0.0). 지금은 Azure Portal 지원이 없습니다. 동의어에 대한 Azure Portal 지원이 사용자에게 유용한 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 피드백을 제공해 주세요.

## <a name="prerequisites"></a>필수 조건

자습서 요구 사항은 다음과 같습니다.

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search 서비스](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET library](https://aka.ms/search-sdk)
* [.NET 애플리케이션에서 Azure Search를 사용하는 방법](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>개요

이전 및 이후 쿼리는 동의어의 가치를 설명합니다. 이 예제에서는 쿼리를 실행 하 고 샘플 인덱스에서 결과 반환 하는 샘플 응용 프로그램을 사용 합니다. 샘플 애플리케이션은 두 개의 문서로 채워진 "hotels"라는 작은 인덱스를 만듭니다. 애플리케이션은 인덱스에 나타나지 않는 조건 및 구를 사용하여 검색 쿼리를 실행하고 동의어 기능을 사용한 다음, 다시 동일한 검색을 실행합니다. 아래 코드에서는 전체 흐름을 보여 줍니다.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
샘플 인덱스를 만들고 채우는 단계는 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)에서 설명됩니다.

## <a name="before-queries"></a>"이전" 쿼리

`RunQueriesWithNonExistentTermsInIndex`에서 "five star", "internet", "economy AND hotel"로 검색 쿼리를 실행합니다.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
다음과 같이 첫 번째 `RunQueriesWithNonExistentTermsInIndex`에서 출력을 얻게 되므로 인덱싱된 두 개의 문서는 용어를 포함하지 않습니다.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>동의어 사용

동의어 사용은 2단계 프로세스입니다. 먼저 동의어 규칙을 정의하고 업로드한 다음 사용할 필드를 구성합니다. 프로세스는 `UploadSynonyms` 및 `EnableSynonymsInHotelsIndex`에서 설명됩니다.

1. 동의어 맵을 검색 서비스에 추가합니다. `UploadSynonyms`에서 동의어 맵 'desc-synonymmap'의 네 가지 규칙을 정의하고 서비스에 업로드합니다.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   동의어 맵은 오픈 소스 표준 `solr` 형식에 따라야 합니다. 형식은 `Apache Solr synonym format` 섹션 아래의 [Azure Search의 동의어](search-synonyms.md)에서 설명됩니다.

2. 인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다. `EnableSynonymsInHotelsIndex`에서 `synonymMaps` 속성을 새로 업로드된 동의어 맵의 이름으로 설정하여 두 개의 필드인 `category` 및 `tags`에서 동의어를 사용합니다.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   동의어 맵을 추가하면 인덱스를 다시 작성하지 않아도 됩니다. 서비스에 동의어 맵을 추가한 다음 모든 인덱스에서 기존 필드 정의를 수정하여 새 동의어 맵을 사용할 수 있습니다. 새 특성을 추가하더라도 인덱스 가용성에 영향을 주지 않습니다. 필드에 동의어를 사용하지 않도록 설정한 경우에도 마찬가지입니다. `synonymMaps` 속성을 빈 목록으로 설정하면 됩니다.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>"이전" 쿼리

동의어 맵이 업로드되고 인덱스가 동의어 맵을 사용하도록 업데이트된 후에 두 번째 `RunQueriesWithNonExistentTermsInIndex` 호출은 다음을 출력합니다.

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
첫 번째 쿼리는 `five star=>luxury` 규칙에서 문서를 찾습니다. 두 번째 쿼리는 `internet,wifi`를 사용하여 검색을 확장하고 세 번째 쿼리는 일치하는 문서를 찾는 데 `hotel, motel` 및 `economy,inexpensive=>budget`을 모두 사용합니다.

동의어를 추가하면 검색 환경이 완전히 변경됩니다. 이 예제에서는 원본 쿼리는 인덱스의 문서와 관련 된 경우에 의미 있는 결과 반환 하지 못했습니다. 동의어를 사용하여 인덱스의 기본 데이터를 변경하지 않고 일반적인 용도로 사용되는 용어를 포함하도록 인덱스를 확장할 수 있습니다.

## <a name="sample-application-source-code"></a>샘플 애플리케이션 소스 코드
[GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)에서 이 연습에 사용된 샘플 애플리케이션의 전체 소스 코드를 찾을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

에 예제는 Azure Search 서비스를 포함 하는 리소스 그룹을 삭제 하 여 정리 하는 가장 빠른 방법입니다. 리소스 그룹을 삭제하여 이제 리소스 그룹 내의 모든 항목을 영구 삭제할 수 있습니다. 포털에서 리소스 그룹 이름은 Azure Search 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

이 예에서는 합니다 [동의어 REST API](https://aka.ms/rgm6rq) 에서 C# 를 만들고 및 매핑 규칙을 게시 한 다음 쿼리에서 동의어 맵을 호출 하는 코드입니다. 추가 정보는 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 및 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 참조 설명서에서 찾을 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Search에서 동의어를 사용하는 방법](search-synonyms.md)
