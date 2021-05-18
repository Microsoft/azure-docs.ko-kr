---
title: 동의어 C# 예
titleSuffix: Azure Cognitive Search
description: 이 C# 예에서는 Azure Cognitive Search의 인덱스에 동의어 기능을 추가하는 방법을 알아봅니다. 동의어 맵은 동일한 용어 목록입니다. 동의어를 지원하는 필드는 사용자가 제공한 용어 및 모든 관련 동의어를 포함하도록 쿼리를 확장합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704659"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>예: C#에서 Azure Cognitive Search에 대한 동의어 추가

동의어는 입력 용어와 의미상 동일하다고 간주되는 용어를 비교하여 쿼리를 확장합니다. 예를 들어 "car"를 "automobile" 또는 "vehicle"이라는 용어를 포함하는 문서와 비교합니다. 

Azure Cognitive Search에서 동의어는 동등한 용어를 연결하는 *매핑 규칙* 을 통해 *동의어 맵* 에 정의됩니다. 이 예에서는 기존 인덱스에 동의어를 추가하고 사용하는 필수 단계를 설명합니다.

이 예에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * [SynonymMap 클래스](/dotnet/api/azure.search.documents.indexes.models.synonymmap)를 사용하여 동의어 맵을 만듭니다. 
> * 동의어를 통해 쿼리 확장을 지원해야 하는 필드에 대해 [SynonymMapsName 속성](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames)을 설정합니다.

평소처럼 동의어 사용 필드를 쿼리할 수 있습니다. 동의어에 액세스하는 데 필요한 추가 쿼리 구문은 없습니다.

여러 동의어 맵을 만들고 모든 인덱스에 사용할 수 있는 서비스 전반 리소스로 게시한 다음 필드 수준에서 사용할 용어를 참조합니다. Azure Cognitive Search에서는 쿼리 시 인덱스를 검색하는 것 외에도 용어가 쿼리에서 사용되는 필드에 지정된 경우 동의어 맵에서 조회를 수행합니다.

> [!NOTE]
> 동의어는 프로그래밍 방식으로 만들 수 있지만 포털에서는 만들 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

자습서 요구 사항은 다음과 같습니다.

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search 서비스](search-create-service-portal.md)
* [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)

.NET 클라이언트 라이브러리에 익숙하지 않은 경우 [.NET에서 Azure Cognitive Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

## <a name="sample-code"></a>샘플 코드

[GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)에서 이 예에 사용된 샘플 애플리케이션의 전체 소스 코드를 찾을 수 있습니다.

## <a name="overview"></a>개요

이전 및 이후 쿼리는 동의어의 값을 보여 주기 위해 사용됩니다. 이 예에서 샘플 애플리케이션은 쿼리를 실행하고 두 개의 문서로 채워진 샘플 "hotels" 인덱스에 대해 결과를 반환합니다. 첫 번째로, 애플리케이션은 인덱스에 나타나지 않는 용어와 구를 사용하여 검색 쿼리를 실행합니다. 두 번째로,이 코드는 동의어 기능을 사용하도록 설정한 다음 동일한 쿼리를 다시 실행합니다. 이번에는 동의어 맵의 일치를 기반으로 결과를 반환합니다. 

아래 코드에서는 전체 흐름을 보여 줍니다.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>"이전" 쿼리

`RunQueriesWithNonExistentTermsInIndex`에서 "five star", "internet", "economy AND hotel"로 검색 쿼리를 실행합니다.

"별표 5개"와 같은 구문 쿼리는 따옴표로 묶어야 하며 클라이언트에 따라 이스케이프 문자가 필요할 수도 있습니다.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

인덱싱된 두 문서 중 어느 것도 용어를 포함하지 않으므로 첫 번째 `RunQueriesWithNonExistentTermsInIndex`에서 **일치하는 문서 없음** 과 같은 출력이 표시됩니다.

## <a name="enable-synonyms"></a>동의어 사용

"이전" 쿼리를 실행한 후에는 샘플 코드에서 동의어를 사용할 수 있습니다. 동의어 사용은 2단계 프로세스입니다. 첫 번째로, 동의어 규칙을 정의하고 업로드합니다. 두 번째로, 필드를 사용하도록 구성합니다. 프로세스는 `UploadSynonyms` 및 `EnableSynonymsInHotelsIndex`에서 설명됩니다.

1. 동의어 맵을 검색 서비스에 추가합니다. `UploadSynonyms`에서 동의어 맵 'desc-synonymmap'의 네 가지 규칙을 정의하고 서비스에 업로드합니다.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. 인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다. `AddSynonymMapsToFields`에서 `SynonymMapNames` 속성을 새로 업로드된 동의어 맵의 이름으로 설정하여 두 개의 필드인 `category` 및 `tags`에서 동의어를 사용합니다.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   동의어 맵을 추가하면 인덱스를 다시 작성하지 않아도 됩니다. 서비스에 동의어 맵을 추가한 다음 모든 인덱스에서 기존 필드 정의를 수정하여 새 동의어 맵을 사용할 수 있습니다. 새 특성을 추가하더라도 인덱스 가용성에 영향을 주지 않습니다. 필드에 동의어를 사용하지 않도록 설정한 경우에도 마찬가지입니다. `SynonymMapNames` 속성을 빈 목록으로 설정하면 됩니다.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>"이전" 쿼리

동의어 맵이 업로드되고 인덱스가 동의어 맵을 사용하도록 업데이트된 후에 두 번째 `RunQueriesWithNonExistentTermsInIndex` 호출은 다음을 출력합니다.

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

첫 번째 쿼리는 `five star=>luxury` 규칙에서 문서를 찾습니다. 두 번째 쿼리는 `internet,wifi`를 사용하여 검색을 확장하고 세 번째 쿼리는 일치하는 문서를 찾는 데 `hotel, motel` 및 `economy,inexpensive=>budget`을 모두 사용합니다.

동의어를 추가하면 검색 환경이 완전히 변경됩니다. 이 예에서는 인덱스의 문서가 관련된 경우에도 원래 쿼리에서 의미 있는 결과를 반환하지 못했습니다. 동의어를 사용하여 인덱스의 기본 데이터를 변경하지 않고 일반적인 용도로 사용되는 용어를 포함하도록 인덱스를 확장할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 예를 마친 후 정리하는 가장 빠른 방법은 Azure Cognitive Search 서비스를 포함하고 있는 리소스 그룹을 삭제하는 것입니다. 리소스 그룹을 삭제하여 이제 리소스 그룹 내의 모든 항목을 영구 삭제할 수 있습니다. 포털에서 리소스 그룹 이름은 Azure Cognitive Search 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

이 예에서는 매핑 규칙을 만들고 게시한 다음, 쿼리에서 동의어 맵을 호출하기 위해 C# 코드에서 동의어 기능을 보여 줬습니다. 추가 정보는 [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) 및 [REST API](/rest/api/searchservice/) 참조 설명서에서 찾을 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에서 동의어를 사용하는 방법](search-synonyms.md)