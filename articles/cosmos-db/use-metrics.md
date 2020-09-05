---
title: Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그
description: Azure Cosmos DB에서 메트릭을 사용하여 일반적인 문제를 디버그하고 데이터베이스를 모니터링합니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 24f321e3c3c0fe8e85633edb505879874e8c772f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019235"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그

Azure Cosmos DB는 처리량, 스토리지, 일관성, 가용성 및 대기 시간에 대한 메트릭을 제공합니다. Azure Portal은 이러한 메트릭의 집계 보기를 제공합니다. Azure Monitor API에서 Azure Cosmos DB 메트릭을 볼 수도 있습니다. 컨테이너 이름과 같은 메트릭에 대 한 차원 값은 대/소문자를 구분 하지 않습니다. 따라서 이러한 차원 값에 대해 문자열 비교를 수행 하는 경우 대/소문자를 구분 하지 않는 비교를 사용 해야 합니다. Azure monitor에서 메트릭을 보는 방법에 대 한 자세한 내용은 [Azure Monitor에서 메트릭 가져오기](cosmos-db-azure-monitor-metrics.md) 문서를 참조 하세요.

이 문서에서는 일반적인 사용 사례와 Azure Cosmos DB 메트릭을 사용하여 이러한 문제를 분석 및 디버그할 수 있는 방법을 안내합니다. 메트릭은 5분마다 수집되고 7일 동안 유지됩니다.

## <a name="view-metrics-from-azure-portal"></a>Azure Portal에서 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **메트릭** 창을 엽니다. 기본적으로 메트릭 창에는 Azure Cosmos 계정의 모든 데이터베이스에 대 한 저장소, 인덱스, 요청 단위 메트릭이 표시 됩니다. 이러한 메트릭은 데이터베이스, 컨테이너 또는 지역 별로 필터링 할 수 있습니다. 특정 시간 세분성에서 메트릭을 필터링 할 수도 있습니다. 처리량, 저장소, 가용성, 대기 시간 및 일관성 메트릭에 대 한 자세한 내용은 별도 탭에 제공 됩니다. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure Portal에서 Cosmos DB 성능 메트릭":::

**메트릭** 창에서 다음 메트릭을 사용할 수 있습니다. 

* **처리량 메트릭** -컨테이너에 대해 프로 비전 된 처리량 또는 저장소 용량을 초과 했기 때문에이 메트릭은 사용 되거나 실패 한 요청 수 (429 응답 코드)를 표시 합니다.

* **저장소 메트릭** -이 메트릭은 데이터 및 인덱스 사용의 크기를 표시 합니다.

* **가용성 메트릭** -이 메트릭은 시간당 전체 요청에 대해 성공한 요청의 백분율을 보여 줍니다. 성공률은 Azure Cosmos DB Sla에 의해 정의 됩니다.

* **대기 시간 메트릭** -이 메트릭은 계정이 작동 하는 지역에서 Azure Cosmos DB에 의해 관찰 되는 읽기 및 쓰기 대기 시간을 보여 줍니다. 지역에서 복제 된 계정에 대 한 지역 간 대기 시간을 시각화할 수 있습니다. 이 메트릭은 종단 간 요청 대기 시간을 나타내지 않습니다.

* **일관성 메트릭** -이 메트릭은 선택한 일관성 모델에 대 한 최종 일관성을 보여 줍니다. 다중 지역 계정의 경우이 메트릭은 선택한 지역 간의 복제 대기 시간도 표시 합니다.

* **시스템 메트릭** -이 메트릭은 마스터 파티션에서 제공 되는 메타 데이터 요청 수를 표시 합니다. 또한 제한 된 요청을 식별 하는 데 도움이 됩니다.

다음 섹션에서는 Azure Cosmos DB 메트릭을 사용할 수 있는 일반적인 시나리오에 대해 설명 합니다. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>성공한 요청 수 또는 오류가 발생하는 요청 수 이해

시작하려면 [Azure Portal](https://portal.azure.com)에서 **메트릭** 블레이드로 이동합니다. 블레이드에서 1 분당 용량을 초과한 * * 요청 수를 찾습니다. 이 차트에서는 상태 코드로 분할된 총 요청 수를 분 단위로 보여 줍니다. HTTP 상태 코드에 대 한 자세한 내용은 [Azure Cosmos DB에 대 한 http 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조 하세요.

가장 일반적인 오류 상태 코드는 429입니다(속도 제한/제한). 이 오류는 Azure Cosmos DB에 대한 요청이 프로비전된 처리량보다 더 많은 것을 의미합니다. 이 문제에 대한 가장 일반적인 솔루션은 지정된 컬렉션에 대한 [RU를 확장](./set-throughput.md)하는 것입니다.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="분당 요청 수":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>파티션의 처리량 배포 확인

파티션 키의 좋은 카디널리티 사용은 확장 가능한 모든 애플리케이션에 필요합니다. 모든 분할된 컨테이너의 처리량 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 **메트릭 블레이드**로 이동합니다. **처리량** 탭에서 스토리지 분석은 **각 실제 파티션별 사용된 최대 RU/초** 차트에 표시됩니다. 다음 그림은 맨 왼쪽에 있는 불일치 파티션에서 표시된 것과 같이 잘못된 데이터 배포의 예제를 보여줍니다.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="사용량이 많은 단일 파티션":::

불균등 처리량 배포 시 *핫* 파티션이 발생할 수 있고, 이로 인해 제한된 요청이 초래되고 다시 분할해야 할 수 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="determine-the-storage-distribution-across-partitions"></a>파티션의 스토리지 배포 확인

파티션의 좋은 카디널리티 사용은 확장 가능한 모든 애플리케이션에 필요합니다. 모든 분할된 컨테이너의 스토리지 배포를 파티션으로 분석하여 확인하려면 [Azure Portal](https://portal.azure.com)의 메트릭 블레이드로 이동합니다. 스토리지 탭에서 스토리지 분석은 상위 파티션 키 차트에서 사용된 데이터 + 인덱스 스토리지에 표시됩니다. 다음 그래픽은 맨 왼쪽에 있는 불일치 파티션에서 표시된 대로 잘못된 데이터 스토리지 배포를 보여줍니다.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="불량 데이터 배포의 예":::

차트에서 파티션을 클릭하여 배포 불균등의 근본 원인인 파티션 키를 확인할 수 있습니다.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="파티션 키로 인해 배포 불균등 발생":::

배포 불균등을 초래하는 파티션 키를 식별한 후 더 많이 배포된 파티션 키로 컨테이너를 다시 분할해야 할 수도 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partition-data.md)을 참조하세요.

## <a name="compare-data-size-against-index-size"></a>데이터 크기 및 인덱스 크기 비교

Azure Cosmos DB에서 사용된 총 스토리지는 데이터 크기와 인덱스 크기의 조합입니다. 일반적으로 인덱스 크기는 데이터 크기의 비율입니다. [Azure Portal](https://portal.azure.com)의 메트릭 블레이드에서, 스토리지 탭에는 스토리지 사용량이 데이터 및 인덱스에 따라 분석되어 표시됩니다.

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

* Azure monitor에서 메트릭을 보는 방법에 대 한 자세한 내용은 [Azure Monitor에서 메트릭 가져오기](cosmos-db-azure-monitor-metrics.md) 문서를 참조 하세요. 
* [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)
* [Azure Cosmos DB에 대한 성능 팁](performance-tips.md)
