---
title: SQL 쿼리 성능 & 실행 메트릭 가져오기
description: SQL 쿼리 실행 메트릭을 검색 하 고 Azure Cosmos DB 요청에 대 한 SQL 쿼리 성능을 프로 파일링 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 71ebc90834083def5b82e16dc387a6e61943206d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021751"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>.NET SDK를 사용 하 여 SQL 쿼리 실행 메트릭 가져오기 및 쿼리 성능 분석

이 문서에서는 Azure Cosmos DB에서 SQL 쿼리 성능을 프로 파일링 하는 방법을 보여 줍니다. 이 프로 파일링은 .NET SDK에서 검색 된를 사용 하 여 수행할 수 `QueryMetrics` 있으며 여기에 자세히 설명 되어 있습니다. [Querymetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) 백 엔드 쿼리 실행에 대 한 정보를 포함 하는 강력한 형식의 개체입니다. 이러한 메트릭은 [쿼리 성능 조정](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서에 자세히 설명 되어 있습니다.

## <a name="set-the-feedoptions-parameter"></a>FeedOptions 매개 변수 설정

[Documentclient. CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) 의 모든 오버 로드는 선택적 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 매개 변수를 사용 합니다. 이 옵션을 사용 하면 쿼리 실행을 튜닝 하 고 매개 변수화 할 수 있습니다. 

Sql 쿼리 실행 메트릭을 수집 하려면 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 의 [feedoptions.populatequerymetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) 매개 변수를로 설정 해야 합니다 `true` . `PopulateQueryMetrics`를 true로 설정 하면가 `FeedResponse` 관련를 포함 하 게 됩니다 `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery ()를 사용 하 여 쿼리 메트릭 가져오기
다음 코드 샘플에서는 [Asdocumentquery ()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) 메서드를 사용할 때 메트릭을 검색 하는 방법을 보여 줍니다.

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics 집계

이전 섹션에서 [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) 메서드를 여러 번 호출 했습니다. 각 호출은 쿼리를 `FeedResponse` 연속 하는 `QueryMetrics` 모든 항목에 대 한 사전을 포함 하는 개체를 반환 했습니다. 다음 예제에서는 `QueryMetrics` LINQ를 사용 하 여 집계 하는 방법을 보여 줍니다.

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>파티션 ID로 쿼리 메트릭 그룹화

파티션 ID를 기준으로를 그룹화 할 수 있습니다 `QueryMetrics` . 파티션 ID를 기준으로 그룹화 하면 특정 파티션이 다른 파티션에 비해 성능 문제가 발생 하 고 있는지 확인할 수 있습니다. 다음 예제에서는 LINQ를 사용 하 여 그룹화 하는 방법을 보여 줍니다 `QueryMetrics` .

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ on DocumentQuery

`FeedResponse`메서드를 사용 하 여 LINQ 쿼리에서를 가져올 수도 있습니다 `AsDocumentQuery()` .

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>비용이 높은 쿼리

각 쿼리에서 사용 되는 요청 단위를 캡처하여 높은 처리량을 사용 하는 비용이 많이 드는 쿼리나 쿼리를 조사할 수 있습니다. 에서 [requestcharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) 속성을 사용 하 여 요청 요금을 얻을 수 있습니다 `FeedResponse` . Azure Portal 및 다른 Sdk를 사용 하 여 요청 요금을 얻는 방법에 대 한 자세한 내용은 [요청 단위 요금 청구](find-request-unit-charge.md) 문서를 참조 하세요.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>쿼리 실행 시간 가져오기

클라이언트 쪽 쿼리를 실행 하는 데 필요한 시간을 계산할 때 메서드를 호출 하는 시간만 포함 하 `ExecuteNextAsync` 고 코드 베이스의 다른 부분은 포함 하지 않아야 합니다. 이러한 호출은 다음 예제와 같이 쿼리 실행이 걸린 시간을 계산 하는 데 도움이 됩니다.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>검색 쿼리 (일반적으로 느리고 비용이 많이 듭니다.)

검색 쿼리는 인덱스에서 제공 하지 않은 쿼리를 참조 하며,이는 결과 집합을 반환 하기 전에 많은 문서가 로드 되기 때문입니다.

검색 쿼리의 예는 다음과 같습니다.

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이 쿼리 필터는 인덱스에서 제공 되지 않는 시스템 함수 UPPER를 사용 합니다. 규모가 많은 컬렉션에 대해이 쿼리를 실행 하면 첫 번째 연속 작업에 대해 다음과 같은 쿼리 메트릭이 생성 됩니다.

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

쿼리 메트릭 출력에서 다음 값을 확인 합니다.

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

이 쿼리는 399998938 바이트를 합산 하는 60951 문서를 로드 했습니다. 이 많은 바이트를 로드 하면 비용이 많이 듭니다. 또한 쿼리를 실행 하는 데 시간이 오래 걸리고이는 총 시간 속성을 사용 하 여 명확 합니다.

```
Total Query Execution Time               :        4,500.34 milliseconds
```

즉, 쿼리가 실행 되는 데 4.5 초 정도 걸렸습니다.

이 예제 쿼리를 최적화 하려면 필터에서 상한을 사용 하지 마십시오. 대신 문서를 만들거나 업데이트할 때 `c.description` 값을 모두 대문자로 삽입 해야 합니다. 그러면 쿼리가 다음과 같이 됩니다. 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

이제 인덱스에서이 쿼리를 제공할 수 있습니다.

쿼리 성능 튜닝에 대 한 자세한 내용은 [쿼리 성능 조정](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) 문서를 참조 하세요.

## <a name="references"></a><a id="References"></a>참고자료

- [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>다음 단계

- [쿼리 성능 조정](sql-api-query-metrics.md)
- [인덱싱 개요](index-overview.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
