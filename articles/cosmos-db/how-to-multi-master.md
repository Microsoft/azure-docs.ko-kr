---
title: Azure Cosmos DB에서 다중 마스터를 구성하는 방법
description: Azure Cosmos DB에서 다른 Sdk를 사용 하 여 응용 프로그램에 대 한 다중 마스터를 구성 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 94465e44a6cc2c4fa94ec8ea4504cbed80742f0f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019150"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB를 사용하는 애플리케이션에서 다중 마스터 구성

사용할 수 있는 여러 쓰기 지역으로 계정이 만들어지면 DocumentClient의 ConnectionPolicy에 대한 애플리케이션에서 두 가지를 변경하여 Azure Cosmos DB에서 다중 마스터 및 다중 호밍 기능을 사용하도록 설정해야 합니다. ConnectionPolicy 내에서 UseMultipleWriteLocations를 true로 설정하고 애플리케이션이 배포되는 지역 이름을 SetCurrentLocation으로 전달합니다. 이를 통해 전달된 위치에서 지역 근접도에 따라 PreferredLocations 속성이 채워집니다. 나중에 계정에 새 지역이 추가되는 경우 애플리케이션은 업데이트되거나 다시 배포될 필요 없이 가까운 지역을 자동으로 탐지하고 자동 호밍되어 지역 이벤트가 발생합니다.

> [!Note]
> 처음에 단일 쓰기 지역으로 구성된 Cosmos 계정을 중단 시간이 0인 다중 쓰기 지역(즉, 다중 마스터)에 구성할 수 있습니다. 자세한 내용은 [다중 쓰기 지역 구성](how-to-manage-database-account.md#configure-multiple-write-regions)을 참조하세요.

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

애플리케이션에서 다중 마스터를 사용하도록 설정하려면 `UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `SetCurrentLocation`을 애플리케이션이 배포되고 Azure Cosmos DB가 복제되는 지역으로 설정합니다.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

애플리케이션에서 다중 마스터를 사용하도록 설정하려면 `ApplicationRegion`을 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

필요에 따라 `CosmosClientBuilder` 및 `WithApplicationRegion`을 사용하여 동일한 결과를 얻을 수 있습니다.

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a> Java V4 SDK

응용 프로그램에서 다중 마스터를 사용 하도록 설정 하려면 `.multipleWriteRegionsEnabled(true)` `.preferredRegions(preferredRegions)` 클라이언트 작성기에서 및를 호출 합니다 `preferredRegions` . 여기서는 `List` 응용 프로그램이 배포 되는 영역인 Cosmos DB 복제 되는 영역인 하나의 요소를 포함 하는입니다.

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a> Async Java V2 SDK

Java V2 SDK는 Maven [:: azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)를 사용 했습니다. 애플리케이션에서 다중 마스터를 사용하도록 설정하려면 `policy.setUsingMultipleWriteLocations(true)`를 설정하고 `policy.setPreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript 및 TypeScript SDK

애플리케이션에서 다중 마스터를 사용하도록 설정하려면 `connectionPolicy.UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `connectionPolicy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

애플리케이션에서 다중 마스터를 사용하도록 설정하려면 `connection_policy.UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `connection_policy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 세션 토큰을 사용하여 일관성 관리](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB의 충돌 유형 및 해결 정책](conflict-resolution-policies.md)
* [Azure Cosmos DB의 고가용성](high-availability.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB에서 올바른 일관성 수준 선택](consistency-levels-choosing.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [전역적으로 프로비전된 처리량 크기 조정](scaling-throughput.md)
* [전역 배포: 내부적으로](global-dist-under-the-hood.md)
