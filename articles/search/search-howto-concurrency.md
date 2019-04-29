---
title: 리소스에 대한 동시 쓰기를 관리하는 방법 - Azure Search
description: Azure Search 인덱스, 인덱서, 데이터 원본을 업데이트나 삭제하는 중에 충돌이 발생하지 않도록 하려면 낙관적 동시성을 사용합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4599498918b7a01a1207f20135c26924c6758eb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871368"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Azure Search에서 동시성을 관리하는 방법

인덱스 및 데이터 원본과 같은 Azure Search 리소스를 관리할 때는 리소스를 안전하게 업데이트해야 합니다. 특히 애플리케이션의 여러 구성 요소가 리소스에 동시에 액세스할 때는 안전한 업데이트가 더욱 중요합니다. 두 클라이언트가 조정 없이 리소스를 동시에 업데이트하면 경합 상태가 발생할 수 있습니다. Azure Search에서는 이러한 상황을 방지하기 위해 *낙관적 동시성 모델*을 제공합니다. 이 모델에서는 리소스가 잠기지 않습니다. 대신 요청을 작성할 수 있도록 리소스 버전을 식별하는 ETag가 모든 리소스에 포함되어 있으므로 실수로 인한 덮어쓰기를 방지할 수 있습니다.

> [!Tip]
> [샘플 C# 솔루션](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)의 개념 코드를 통해 Azure Search에서 동시성 제어가 작동하는 방식을 파악할 수 있습니다. 이 코드는 동시성 제어를 호출하는 조건을 만듭니다. 대부분의 개발자는 [아래의 코드 조각](#samplecode)만 확인해도 되겠지만, 해당 코드를 실행하려는 경우 appsettings.json을 편집하여 서비스 이름과 관리 API 키를 추가하세요. 서비스 URL이 `http://myservice.search.windows.net`인 경우 서비스 이름은 `myservice`입니다.

## <a name="how-it-works"></a>작동 방법

낙관적 동시성은 인덱스, 인덱서, 데이터 원본 및 synonymMap 리소스에 데이터를 기록하는 API 호출에서 액세스 조건을 확인하는 방식으로 구현됩니다.

모든 리소스에는 개체 버전 정보를 제공하는 [*ETag(엔터티 태그)*](https://en.wikipedia.org/wiki/HTTP_ETag)가 있습니다. ETag를 먼저 확인하면 리소스의 ETag가 로컬 복사본과 일치하는지를 확인하여 일반적인 워크플로(가져오기, 논리적으로 수정, 업데이트)에서 동시 업데이트를 방지할 수 있습니다.

+ REST API는 요청 헤더에서 [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)를 사용합니다.
+ .NET SDK는 accessCondition 개체를 통해 ETag를 설정하여 리소스에 대해 [If-Match | If-Match-None 헤더](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)를 설정합니다. [IResourceWithETag(.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag)에서 상속하는 모든 개체에는 accessCondition 개체가 있습니다.

리소스를 업데이트할 때마다 ETag는 자동으로 변경됩니다. 동시성 관리를 구현할 때는 클라이언트에서 수정한 리소스 복사본과 같은 ETag가 원격 리소스에 포함되어 있어야 하도록 지정하는 사전 조건만 업데이트 요청에 포함하면 됩니다. 동시 프로세스에서 원격 리소스를 이미 변경한 경우 ETag가 사전 조건과 일치하지 않아 요청이 실패하며 HTTP 412가 표시됩니다. .NET SDK를 사용 중이라면 이러한 경우 `CloudException`이 표시됩니다. 여기서 `IsAccessConditionFailed()` 확장 메서드는 true를 반환합니다.

> [!Note]
> 동시성의 메커니즘은 한 가지뿐입니다. 즉, 리소스 업데이트에 사용되는 API에 관계없이 동시성은 항상 사용됩니다.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>사용 사례 및 샘플 코드

다음 코드는 키 업데이트 작업에서 accessCondition을 확인하는 과정을 보여 줍니다.

+ 리소스가 더 이상 없으면 업데이트 실패
+ 리소스 버전이 변경되면 업데이트 실패

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>[DotNetETagsExplainer 프로그램](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)의 샘플 코드

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>디자인 패턴

낙관적 동시성 구현을 위한 디자인 패턴에는 액세스 조건 확인을 다시 시도하는 루프와 액세스 조건에 대한 테스트를 포함해야 하며, 필요에 따라 변경 내용을 다시 적용하기 전에 업데이트된 리소스를 검색하는 과정을 포함할 수 있습니다.

다음 코드 조각은 이미 있는 인덱스에 synonymMap을 추가하는 방법을 보여 줍니다. 이 코드는 합니다 [동의어 (미리 보기) C# Azure Search에 대 한 예제](search-synonyms-tutorial-sdk.md)합니다.

해당 코드 조각은 "hotels" 인덱스를 가져와 업데이트 작업의 개체 버전을 확인한 다음 조건이 실패하면 예외를 throw합니다. 그런 후에 작업을 최대 3회까지 다시 시도하는데, 이때 먼저 서버에서 인덱스를 검색하여 최신 버전을 가져옵니다.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>다음 단계

[synonyms C# 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)을 검토하여 기존 인덱스를 안전하게 업데이트하는 방법과 관련한 추가 컨텍스트를 확인합니다.

다음 샘플 중 하나를 수정하여 ETag 또는 AccessCondition 개체를 포함합니다.

+ [GitHub의 REST API 샘플](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [GitHub의 .NET SDK 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started) 이 솔루션에는 이 문서에 나와 있는 코드를 포함하는 "DotNetEtagsExplainer" 프로젝트가 들어 있습니다.

## <a name="see-also"></a>참고 항목

[일반 HTTP 요청 및 응답 헤더](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP 상태 코드](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[인덱스 작업(REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
