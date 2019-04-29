---
title: Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그
description: Azure Cosmos DB에서 메트릭을 사용하여 일반적인 문제를 디버그하고 데이터베이스를 모니터링합니다.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765841"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그

Azure Cosmos DB는 처리량, 저장소, 일관성, 가용성 및 대기 시간에 대한 메트릭을 제공합니다. [Azure Portal](https://portal.azure.com)은 이러한 메트릭의 집계 보기를 제공합니다. 보다 세부적인 메트릭의 경우 클라이언트 SDK 및 [진단 로그](./logging.md) 모두를 사용할 수 있습니다.

이 문서에서는 일반적인 사용 사례와 Azure Cosmos DB 메트릭을 사용하여 이러한 문제를 분석 및 디버그할 수 있는 방법을 안내합니다. 메트릭은 5분마다 수집되고 7일 동안 유지됩니다.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>성공한 요청 수 또는 오류가 발생하는 요청 수 이해

시작하려면 [Azure Portal](https://portal.azure.com)에서 **메트릭** 블레이드로 이동합니다. 블레이드에서 **1분당 용량을 초과한 요청 수** 차트를 찾습니다. 이 차트에서는 상태 코드로 분할된 총 요청 수를 분 단위로 보여 줍니다. HTTP 상태 코드에 대한 자세한 내용은 [Azure Cosmos DB에 대한 HTTP 상태 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조하세요.

가장 일반적인 오류 상태 코드는 429입니다(속도 제한/제한). 이 오류는 Azure Cosmos DB에 대한 요청이 프로비전된 처리량보다 더 많은 것을 의미합니다. 이 문제에 대한 가장 일반적인 솔루션은 지정된 컬렉션에 대한 [RU를 확장](./set-throughput.md)하는 것입니다.

![분당 요청 수](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>파티션의 처리량 배포 확인

파티션 키의 좋은 카디널리티 사용은 확장 가능한 모든 애플리케이션에 필요합니다. 모든 분할된 컨테이너의 처리량 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 **메트릭 블레이드**로 이동합니다. **처리량** 탭에서 저장소 분석은 **각 실제 파티션별 사용된 최대 RU/초** 차트에 표시됩니다. 다음 그림은 맨 왼쪽에 있는 불일치 파티션에서 표시된 것과 같이 잘못된 데이터 배포의 예제를 보여줍니다.

![오후 3시 05분에 높은 사용량을 표시하는 단일 파티션](media/use-metrics/metrics-17.png)

불균등 처리량 배포 시 *핫* 파티션이 발생할 수 있고, 이로 인해 제한된 요청이 초래되고 다시 분할해야 할 수 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="determine-the-storage-distribution-across-partitions"></a>파티션의 스토리지 배포 확인

파티션의 좋은 카디널리티 사용은 확장 가능한 모든 애플리케이션에 필요합니다. 모든 분할된 컨테이너의 스토리지 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 메트릭 블레이드로 이동합니다. 스토리지 탭에서 스토리지 분석은 상위 파티션 키 차트에서 사용된 데이터 + 인덱스 스토리지에 표시됩니다. 다음 그래픽은 맨 왼쪽에 있는 불일치 파티션에서 표시된 대로 잘못된 데이터 스토리지 배포를 보여줍니다.

![불량 데이터 배포의 예](media/use-metrics/metrics-07.png)

차트에서 파티션을 클릭하여 배포 불균등의 근본 원인인 파티션 키를 확인할 수 있습니다.

![파티션 키로 인해 배포 불균등 발생](media/use-metrics/metrics-05.png)

배포 불균등을 초래하는 파티션 키를 식별한 후 더 많이 배포된 파티션 키로 컨테이너를 다시 분할해야 할 수도 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="compare-data-size-against-index-size"></a>데이터 크기 및 인덱스 크기 비교

Azure Cosmos DB에서 사용된 총 저장소는 데이터 크기와 인덱스 크기의 조합입니다. 일반적으로 인덱스 크기는 데이터 크기의 비율입니다. [Azure Portal](https://portal.azure.com)의 메트릭 블레이드에서, 저장소 탭에는 저장소 사용량이 데이터 및 인덱스에 따라 분석되어 표시됩니다.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

인덱스 공간을 절약하려는 경우 [인덱싱 정책](index-policy.md)을 조정할 수 있습니다.

## <a name="debug-why-queries-are-running-slow"></a>쿼리가 느리게 실행되는 이유 디버그

SQL API SDK에서 Azure Cosmos DB는 쿼리 실행 통계를 제공합니다.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics*는 쿼리의 각 구성 요소를 실행하는 데 걸린 기간을 자세히 보여 줍니다. 장기 실행 쿼리에 대한 가장 일반적인 근본 원인은 검색이며 쿼리에서 인덱스를 활용할 수 없음을 의미합니다. 더 나은 필터 조건을 사용하여 이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 제공된 메트릭을 사용하여 문제를 모니터링 및 디버그하는 방법을 알아보았습니다. 다음 문서를 참조하여 데이터베이스 성능 개선에 대해 자세히 알아볼 수 있습니다.

* [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)
* [Azure Cosmos DB에 대한 성능 팁](performance-tips.md)
