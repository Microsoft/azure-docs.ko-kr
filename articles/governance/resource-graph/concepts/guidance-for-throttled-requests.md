---
title: 제한된 요청에 대한 지침
description: Azure 리소스 그래프에 의해 제한되는 요청을 방지하기 위해 병렬로 그룹화하고, 시차를 두고, 페이지를 매기고, 쿼리하는 방법을 알아봅니다.
ms.date: 08/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: c8576fe38433026a28a3fb09a03332b5dd756bab
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006009"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Resource Graph의 제한된 요청에 대한 지침

Azure Resource Graph 데이터를 프로그래밍 방식으로 자주 사용하는 경우 제한이 쿼리 결과에 어떠한 영향을 미치는지 고려해야 합니다. 데이터를 요청하는 방식을 변경하면 사용자와 조직이 제한되는 것을 방지하고 Azure 리소스에 관한 시기적절한 데이터 흐름을 유지 관리할 수 있습니다.

이 문서에서는 Azure Resource Graph에서 쿼리를 만드는 것과 관련된 네 가지 영역 및 패턴에 대해 설명합니다.

- 제한 헤더 이해
- 쿼리 그룹화
- 쿼리 시차
- 페이지 매김의 영향

## <a name="understand-throttling-headers"></a>제한 헤더 이해

Azure Resource Graph는 시간 범위를 기준으로 각 사용자에 대한 할당량 번호를 할당합니다. 예를 들어 사용자는 제한 없이 5초마다 최대 15개의 쿼리를 보낼 수 있습니다. 많은 요인에 따라 할당량 값이 결정되며, 변경될 수 있습니다.

모든 쿼리 응답에서 Azure Resource Graph는 다음과 같은 두 개의 제한 헤더를 추가합니다.

- `x-ms-user-quota-remaining`(int): 사용자의 나머지 리소스 할당량입니다. 이 값은 쿼리 수에 매핑됩니다.
- `x-ms-user-quota-resets-after`(hh:mm:ss): 사용자의 할당량 소비가 재설정될 때까지 남은 기간

보안 주체가 테 넌 트 또는 관리 그룹 [쿼리 범위](./query-language.md#query-scope)내에서 5000 개 이상의 구독에 액세스할 수 있는 경우 응답은 첫 번째 5000 구독으로 제한 되 고 `x-ms-tenant-subscription-limit-hit` 헤더는로 반환 됩니다 `true` .

헤더의 작동 방식을 설명하기 위해 `x-ms-user-quota-remaining: 10` 및 `x-ms-user-quota-resets-after: 00:00:03`의 헤더와 값이 있는 쿼리 응답을 살펴보겠습니다.

- 다음 3초 이내에 최대 10개의 쿼리를 제한 없이 제출할 수 있습니다.
- 3초 후에 `x-ms-user-quota-remaining` 및 `x-ms-user-quota-resets-after`의 값이 각각 `15` 및 `00:00:05`로 다시 설정됩니다.

헤더를 사용하여 쿼리 요청에서 _백오프_하는 예제를 보려면 [병렬 쿼리](#query-in-parallel)의 샘플을 참조하세요.

## <a name="grouping-queries"></a>쿼리 그룹화

구독, 리소스 그룹 또는 개별 리소스 별로 쿼리를 그룹화하는 것은 쿼리를 병렬화하는 것보다 효율적입니다. 큰 쿼리의 할당량 비용은 종종 많은 작은 쿼리 및 대상 쿼리의 할당량 비용보다 낮습니다. 그룹 크기는 _300_보다 작게 하는 것이 좋습니다.

- 잘못 최적화된 방법의 예

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 최적화된 그룹화 방법의 예 #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 한 쿼리에서 여러 리소스를 가져오기 위한 최적화된 그룹화 방법의 예 #2

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>쿼리 시차

제한이 적용되는 방식으로 인해 쿼리는 시차를 두고 적용하는 것이 좋습니다. 즉, 동시에 60개의 쿼리를 전송하는 대신 쿼리를 4개의 5초 창으로 시차를 적용합니다.

- 시차가 없는 쿼리 일정

  | 쿼리 개수         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 시간 간격(초) | 0-5 | 5-10 | 10-15 | 15-20 |

- 시차가 적용된 쿼리 일정

  | 쿼리 개수         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 시간 간격(초) | 0-5 | 5-10 | 10-15 | 15-20 |

다음은 Azure Resource Graph를 쿼리할 때 제한 헤더를 준수하는 예입니다.

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>병렬 쿼리

병렬 처리를 통해 그룹화가 권장되더라도 쿼리를 쉽게 그룹화할 수 없는 경우가 있습니다. 이러한 경우 여러 쿼리를 병렬 방식으로 전송하여 Azure Resource Graph를 쿼리하는 것이 좋습니다. 다음은 이러한 시나리오에서 제한 헤더에 따라 _백오프_하는 방법에 대한 예입니다.

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>페이지 매김

Azure Resource Graph가 단일 쿼리 응답에서 최대 1000개 항목을 반환하므로 찾고 있는 완전한 데이터 세트를 가져오려면 [쿼리](./work-with-data.md#paging-results)에 페이지를 매겨야 할 것입니다. 그러나 일부 Azure Resouce Graph 클라이언트는 페이지 매김을 다른 방식으로 처리합니다.

- C# SDK

  ResourceGraph SDK를 사용하는 경우 이전 쿼리 응답에서 다음으로 페이지가 매겨진 쿼리로 반환되는 건너뛰기 토큰을 전달하여 페이지 매김을 처리해야 합니다. 이 디자인은 페이지가 매겨진 모든 호출에서 결과를 수집하고 끝에 함께 결합해야 함을 의미합니다. 이 경우 전송되는 각 페이지가 매겨진 쿼리는 하나의 쿼리 할당량을 사용합니다.

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  Azure CLI 또는 Azure PowerShell을 사용하는 경우 Azure Resource Graph에 대한 쿼리는 최대 5000개 항목에서 자동으로 페이지가 매겨집니다. 쿼리 결과는 페이지가 매겨진 모든 호출에서 항목의 결합된 목록을 반환합니다. 이 경우 쿼리 결과의 항목 수에 따라 페이지가 매겨진 단일 쿼리에서 둘 이상의 쿼리 할당량을 사용할 수 있습니다. 예를 들어 아래 예제에서는 쿼리를 한 번 실행할 때 쿼리 할당량을 5개까지 사용할 수 있습니다.

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>여전히 제한된 상태인가요?

위의 권장 사항을 적용한 후 제한되는 경우 [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)로 팀에 문의하세요.

이러한 세부 정보를 제공해 주세요.

- 특정 사용 사례 및 비즈니스 드라이버는 더 높은 조정 제한을 요구합니다.
- 액세스 권한이 있는 리소스는 몇 개입니까? 단일 쿼리에서 반환되는 개수는 몇 개입니까?
- 관심 있는 리소스 유형은 무엇인가요?
- 쿼리 패턴은 무엇인가요? Y 초 당 X 쿼리 등

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어를 확인합니다.
- [고급 쿼리](../samples/advanced.md)의 고급 사용법을 확인합니다.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기