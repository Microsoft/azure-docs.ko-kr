---
title: Azure Cosmos DB에서 SQL 쿼리 실행
description: Azure Cosmos DB에서 SQL 쿼리 실행에 알아봅니다
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342574"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 쿼리 실행

HTTP/HTTPS 요청을 수행할 수 있는 임의의 언어로 Cosmos DB REST API를 호출할 수 있습니다. Cosmos DB는 또한.NET, Node.js, JavaScript 및 Python 프로그래밍 언어를 위한 프로그래밍 라이브러리를 제공합니다. REST API 및 라이브러리를 모든 SQL를 통해 쿼리를 지원 하 고.NET SDK도 지원 [LINQ 쿼리에](sql-query-linq-to-sql.md)합니다.

다음 예제에서는 쿼리를 만들고 Cosmos DB 데이터베이스 계정에 대해 제출하는 방법을 보여 줍니다.

## <a id="REST-API"></a>REST API

Cosmos DB는 HTTP를 통해 개방형 RESTful 프로그래밍 모델을 제공합니다. 리소스 모델의 단일 데이터베이스 계정 아래에 있는 리소스 집합으로 구성 하는 Azure 구독 프로 비전 합니다. 데이터베이스 계정은 구성 집합이 *데이터베이스*, 여러 개 포함할 수 있으며 각 *컨테이너*에 *항목*, Udf 및 기타 리소스 유형이 합니다. 각 Cosmos DB 리소스는 논리적이 고 안정적인 URI를 사용 하 여 주소를 지정할 수 있습니다. 리소스 집합을 호출 되는 *피드*합니다. 

이러한 리소스를 사용한 기본 조작 모델은 HTTP 동사 `GET`, `PUT`를 `POST`, 및 `DELETE`, 해당 표준 해석을 사용 하 여 합니다. 사용 하 여 `POST` 새 리소스를 만들려면 저장된 프로시저를 실행 하거나 Cosmos DB 쿼리를 실행 합니다. 쿼리는 항상 파생 작업이 없는 읽기 전용 작업입니다.

다음 예제에 나온은 `POST` 샘플 항목에 대 한 SQL API 쿼리에 대 한 합니다. 쿼리는 JSON에 대 한 간단한 필터 `name` 속성입니다. 합니다 `x-ms-documentdb-isquery` 및 Content-type: `application/query+json` 헤더 작업이 쿼리 임을 나타냅니다. 대체 `mysqlapicosmosdb.documents.azure.com:443` Cosmos DB 계정에 대 한 URI를 사용 합니다.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

결과는 다음과 같습니다.

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

다음으로 더 복잡 한 쿼리는 조인에서 여러 결과 반환합니다.

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

결과는 다음과 같습니다. 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

REST API를 통해 연속 토큰도 반환 쿼리 결과 단일 페이지에 맞출 수 없습니다, 하는 경우는 `x-ms-continuation-token` 응답 헤더입니다. 클라이언트는 후속 결과에 헤더를 포함 하 여 결과 페이지를 매길 수 있습니다. 통해 페이지당 결과 수를 제어할 수도 있습니다는 `x-ms-max-item-count` 번호 머리글입니다.

쿼리 수와 같은 집계 함수에 있는 경우 쿼리 페이지 결과 하나의 페이지에 대해 부분적으로 집계 된 값을 반환할 수 있습니다. 클라이언트는 최종 결과 생성 하기 위해 이러한 결과 대해 두 번째 수준 집계를 수행 해야 합니다. 예를 들어, 총 개수를 반환 하는 개별 페이지에서 반환 된 개수 합계입니다.

쿼리에 대 한 데이터 일관성 정책을 관리 하려면 사용 된 `x-ms-consistency-level` 모든 REST API 요청 처럼 헤더입니다. 세션 일관성 해야 최신 에코 `x-ms-session-token` 쿼리 요청의 쿠키 헤더입니다. 쿼리된 컨테이너의 인덱싱 정책이 쿼리 결과의 일관성에 영향을 줄 수도 있습니다. 기본 컨테이너에 대 한 정책 인덱싱, 인덱스는 항목의 내용을 사용 하 여 현재 항상와 쿼리 결과 데이터에 대해 선택한 일관성과 일치 합니다. 자세한 내용은 [Azure Cosmos DB 일관성 수준]을 참조 하세요. [일관성 수준].

컨테이너에 구성 된 인덱싱 정책이 지정된 된 쿼리를 지원할 수 없습니다, 하는 경우 Azure Cosmos DB 서버 400 "잘못 된 요청"을 반환 합니다. 쿼리에 대 한 인덱싱에서 명시적으로 제외 된 경로 사용 하 여이 오류 메시지를 반환 합니다. 지정할 수는 `x-ms-documentdb-query-enable-scan` 인덱스를 사용할 수 없는 경우 검사를 수행 하려면 쿼리를 허용 하는 헤더입니다.

설정 하 여 쿼리 실행에 대 한 자세한 메트릭을 가져올 수 있습니다 합니다 `x-ms-documentdb-populatequerymetrics` 헤더를 `true`입니다. 자세한 내용은 [SQL query metrics for Azure Cosmos DB](sql-api-query-metrics.md)(Azure Cosmos DB에 대한 SQL 쿼리 메트릭)를 참조하세요.

## <a name="c-net-sdk"></a>C#(.NET SDK)

.NET SDK는 LINQ 및 SQL 쿼리를 둘 다 지원합니다. 다음 예제에서는.NET을 사용 하 여 이전 필터 쿼리를 수행 하는 방법을 보여 줍니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 각 항목 내의 같음에 대 한 두 속성을 비교 하 고 익명 프로젝션을 사용 합니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 LINQ를 통해 표현 된 조인을 `SelectMany`합니다.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 클라이언트에서 쿼리 결과의 모든 페이지를 자동으로 반복 된 `foreach` 앞의 예제와 같이 차단 합니다. 에 도입 된 쿼리 옵션을 [REST API](#REST-API) 섹션도.NET SDK에서 사용할 수 있습니다 사용 하 여는 `FeedOptions` 및 `FeedResponse` 의 클래스는 `CreateDocumentQuery` 메서드. 사용 하 여 페이지의 수를 제어할 수는 `MaxItemCount` 설정 합니다.

만들어 페이징을 명시적으로 제어할 수 있습니다 `IDocumentQueryable` 를 사용 하 여는 `IQueryable` 읽으면 다음 개체를 `ResponseContinuationToken` 값 전달 하는 것으로 다시 `RequestContinuationToken` 에서 `FeedOptions`합니다. 설정할 수 있습니다 `EnableScanInQuery` 쿼리가 구성 된 인덱싱 정책이에서 지원 되지 않는 경우 검색을 사용 하도록 설정 합니다. 분할 된 컨테이너에 대해 사용할 수 있습니다 `PartitionKey` 를 Azure Cosmos DB 자동으로 추출할 수이 쿼리 텍스트에서 있지만 단일 파티션에 대해 쿼리를 실행 합니다. 사용할 수 있습니다 `EnableCrossPartitionQuery` 여러 파티션에 대해 쿼리를 실행할 수 있습니다.

쿼리를 사용 하 여 자세한.NET 샘플에 대 한 참조를 [Azure Cosmos DB.NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet) GitHub에서.

## <a id="JavaScript-server-side-API"></a>JavaScript 서버 쪽 API

Azure Cosmos DB에 대 한 프로그래밍 모델을 제공 [기반 응용 프로그램을 실행 하는 JavaScript](stored-procedures-triggers-udfs.md) 논리를 사용 하 여 컨테이너에 직접 저장 프로시저 및 트리거. 그런 다음 컨테이너 수준에서 등록 된 JavaScript 논리가 앰비언트 ACID 트랜잭션에 래핑됩니다 지정된 된 컨테이너의 항목에 대 한 데이터베이스 작업을 발행할 수 있습니다.

다음 예제에서는 사용 하는 방법을 보여 줍니다 `queryDocuments` JavaScript 서버에서 쿼리를 수행 하는 API에서 저장 프로시저와 트리거 내부:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 일관성 수준](consistency-levels.md)
