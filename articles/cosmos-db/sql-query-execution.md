---
title: Azure Cosmos DB에서 SQL 쿼리 실행
description: SQL 쿼리를 만들고 Azure Cosmos DB에서 실행 하는 방법에 대해 알아봅니다. 이 문서에서는 REST API, .Net SDK, JavaScript SDK 및 기타 다양 한 Sdk를 사용 하 여 SQL 쿼리를 만들고 실행 하는 방법을 설명 합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871264"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 쿼리 실행

HTTP/HTTPS 요청을 수행할 수 있는 모든 언어는 Cosmos DB REST API를 호출할 수 있습니다. 또한 Cosmos DB는 .NET, node.js, JavaScript 및 Python 프로그래밍 언어를 위한 프로그래밍 라이브러리를 제공 합니다. REST API 및 라이브러리는 모두 SQL을 통한 쿼리를 지원 하며 .NET SDK는 [LINQ 쿼리](sql-query-linq-to-sql.md)도 지원 합니다.

다음 예에서는 쿼리를 만들고 Cosmos 데이터베이스 계정에 대해 제출 하는 방법을 보여 줍니다.

## <a id="REST-API"></a>REST API

Cosmos DB는 HTTP를 통해 개방형 RESTful 프로그래밍 모델을 제공합니다. 리소스 모델은 Azure 구독에서 프로 비전 하는 데이터베이스 계정에 있는 리소스 집합으로 구성 됩니다. 데이터베이스 계정은 각각 *항목*, udf 및 기타 리소스 유형을 포함 하는 여러 *컨테이너*를 포함할 수 있는 일련의 *데이터베이스*로 구성 됩니다. 각 Cosmos DB 리소스는 논리적이 고 안정적인 URI를 사용 하 여 주소를 지정할 수 있습니다. 리소스 집합을 *피드*라고 합니다. 

이러한 리소스를 사용 하는 기본 상호 작용 모델은 HTTP 동사 `GET`, `PUT`, `POST`및 `DELETE`를 통해 표준 해석을 사용 합니다. `POST`를 사용 하 여 새 리소스를 만들거나 저장 프로시저를 실행 하거나 Cosmos DB 쿼리를 실행 합니다. 쿼리는 항상 파생 작업이 없는 읽기 전용 작업입니다.

다음 예에서는 샘플 항목에 대한 SQL API 쿼리의 `POST`를 보여 줍니다. 이 쿼리는 JSON `name` 속성에 대한 간단한 필터를 가집니다. `x-ms-documentdb-isquery` 및 Content-type: `application/query+json` 헤더는 작업이 쿼리 임을 나타냅니다. `mysqlapicosmosdb.documents.azure.com:443`을 Cosmos DB 계정의 URI로 바꿉니다.

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

다음으로 좀 더 복잡 한 쿼리는 조인에서 여러 결과를 반환 합니다.

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

쿼리 결과가 단일 페이지에 맞지 않는 경우 REST API은 `x-ms-continuation-token` 응답 헤더를 통해 연속 토큰을 반환 합니다. 클라이언트는 후속 결과에 헤더를 포함 하 여 결과를 매길 수 있습니다. `x-ms-max-item-count` number 헤더를 통해 페이지 당 결과 수를 제어할 수도 있습니다.

쿼리에 COUNT와 같은 집계 함수가 있는 경우 쿼리 페이지는 결과의 한 페이지에 대해 부분적으로 집계 된 값을 반환할 수 있습니다. 클라이언트는 이러한 결과에 대해 두 번째 수준 집계를 수행 하 여 최종 결과를 생성 해야 합니다. 예를 들어 개별 페이지에서 반환 된 개수에 대한 합계를 계산 하 여 총 개수를 반환 합니다.

쿼리에 대한 데이터 일관성 정책을 관리 하려면 모든 REST API 요청에서와 같이 `x-ms-consistency-level` 헤더를 사용 합니다. 또한 세션 일관성을 위해 쿼리 요청에서 최신 `x-ms-session-token` 쿠키 헤더를 사용 해야 합니다. 쿼리된 컨테이너의 인덱싱 정책이 쿼리 결과의 일관성에 영향을 줄 수도 있습니다. 컨테이너에 대한 기본 인덱싱 정책 설정을 사용 하 여 인덱스는 항상 항목 내용으로 최신 상태 이며 쿼리 결과는 데이터에 대해 선택한 일관성과 일치 합니다. 자세한 내용은 [Azure Cosmos DB 일관성 수준] [일관성 수준]을 참조 하세요.

컨테이너의 구성 된 인덱싱 정책이 지정 된 쿼리를 지원할 수 없는 경우 Azure Cosmos DB 서버는 400 "잘못 된 요청"을 반환 합니다. 이 오류 메시지는 인덱싱에서 명시적으로 제외 된 경로를 포함 하는 쿼리에 대해 반환 됩니다. 인덱스를 사용할 수 없는 경우 쿼리에서 검색을 수행할 수 있도록 `x-ms-documentdb-query-enable-scan` 헤더를 지정할 수 있습니다.

`true``x-ms-documentdb-populatequerymetrics` 헤더를 설정 하 여 쿼리 실행에 대한 자세한 메트릭을 가져올 수 있습니다. 자세한 내용은 [SQL query metrics for Azure Cosmos DB](sql-api-query-metrics.md)(Azure Cosmos DB에 대한 SQL 쿼리 메트릭)를 참조하세요.

## <a name="c-net-sdk"></a>C#(.NET SDK)

.NET SDK는 LINQ 및 SQL 쿼리를 둘 다 지원합니다. 다음 예제에서는 .NET을 사용 하 여 위의 필터 쿼리를 수행 하는 방법을 보여 줍니다.

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

다음 예제에서는 각 항목 내에서 두 개의 속성이 같은지 비교 하 고 익명 프로젝션을 사용 합니다.

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

다음 예제에서는 LINQ `SelectMany`를 통해 표현 되는 조인을 보여 줍니다.

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

.NET 클라이언트는 앞의 예제와 같이 `foreach` 블록에서 쿼리 결과의 모든 페이지를 자동으로 반복 합니다. [REST API](#REST-API) 섹션에 도입 된 쿼리 옵션은 `CreateDocumentQuery` 메서드의 `FeedOptions` 및 `FeedResponse` 클래스를 사용 하 여 .net SDK 에서도 사용할 수 있습니다. `MaxItemCount` 설정을 사용 하 여 페이지 수를 제어할 수 있습니다.

`IQueryable` 개체를 사용 하 여 `IDocumentQueryable`를 만든 다음 `ResponseContinuationToken` 값을 읽고 `FeedOptions`에서 `RequestContinuationToken`로 다시 전달 하 여 페이징을 명시적으로 제어할 수도 있습니다. 쿼리가 구성 된 인덱싱 정책에 의해 지원 되지 않는 경우 검색을 사용 하도록 `EnableScanInQuery`를 설정할 수 있습니다. 분할 된 컨테이너의 경우 `PartitionKey`를 사용 하 여 단일 파티션에 대해 쿼리를 실행할 수 있지만 Azure Cosmos DB는 쿼리 텍스트에서이를 자동으로 추출할 수 있습니다. `EnableCrossPartitionQuery`를 사용 하 여 여러 파티션에 대해 쿼리를 실행할 수 있습니다.

쿼리를 사용 하는 .NET 샘플에 대한 자세한 내용은 GitHub의 [Azure Cosmos DB .net 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3) 을 참조 하세요.

## <a id="JavaScript-server-side-API"></a>JavaScript 서버 쪽 API

Azure Cosmos DB는 저장 프로시저 및 트리거를 사용 하 여 컨테이너에서 직접 [JavaScript 기반 응용 프로그램](stored-procedures-triggers-udfs.md) 논리를 실행 하기 위한 프로그래밍 모델을 제공 합니다. 그러면 컨테이너 수준에서 등록 된 JavaScript 논리가 앰비언트 ACID 트랜잭션으로 래핑된 지정 된 컨테이너의 항목에 대해 데이터베이스 작업을 실행할 수 있습니다.

다음 예제에서는 JavaScript 서버 API에서 `queryDocuments`를 사용 하 여 저장 프로시저와 트리거 내부에서 쿼리를 수행 하는 방법을 보여 줍니다.

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
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure Cosmos DB 일관성 수준](consistency-levels.md)
