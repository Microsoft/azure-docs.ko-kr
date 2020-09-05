---
title: Azure Cosmos DB에서 일관성 관리
description: Azure Portal, .NET SDK, Java SDK 및 기타 다양 한 Sdk를 사용 하 여 Azure Cosmos DB에서 일관성 수준을 구성 하 고 관리 하는 방법을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 85c99892a225de933da337e748ebde3370b6f7a3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019609"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준 관리

이 문서에서는 Azure Cosmos DB에서 일관성 수준을 관리하는 방법에 대해 설명합니다. 기본 일관성 수준을 구성하고, 기본 일관성을 재정의하며, 세션 토큰을 수동으로 관리하고, PBS(확률적 제한된 부실) 메트릭을 이해하는 방법을 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

[기본 일관성 수준](consistency-levels.md)은 클라이언트에서 기본적으로 사용하는 일관성 수준입니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

기본 일관성 수준을 보거나 수정하려면 Azure Portal에 로그인합니다. Azure Cosmos 계정을 찾아서 **기본 일관성** 창을 엽니다. 새 기본값으로 사용하려는 일관성 수준을 선택한 다음, **저장**을 선택합니다. 또한 Azure Portal은 음악 메모와 함께 다양한 일관성 수준의 시각화를 제공합니다. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Azure Portal의 일관성 메뉴":::

# <a name="cli"></a>[CLI](#tab/cli)

세션 일관성을 사용 하 여 Cosmos 계정을 만든 다음 기본 일관성을 업데이트 합니다.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

세션 일관성을 사용 하 여 Cosmos 계정을 만든 다음 기본 일관성을 업데이트 합니다.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>기본 일관성 수준 재정의

클라이언트는 서비스에서 설정한 기본 일관성 수준을 재정의할 수 있습니다. 일관성 수준을 요청별로 설정할 수 있으며, 이렇게 하면 계정 수준에서 설정된 기본 일관성 수준이 재정의됩니다.

> [!TIP]
> 일관성은 요청 수준 에서만 **완화할** 수 있습니다. 더 약한에서 더 강력한 일관성으로 이동 하려면 Cosmos 계정에 대 한 기본 일관성을 업데이트 합니다.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4(Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   Java SDK V4(Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2 Sdk

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven:: azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[동기화](#tab/api-sync)

Java V2 SDK 동기화 (Maven:: azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>세션 토큰 사용

Azure Cosmos DB의 일관성 수준 중 하나는 *세션* 일관성입니다. 기본적으로 Cosmos 계정에 적용되는 기본 수준입니다. *세션* 일관성을 작업할 때 클라이언트는 설정된 일관성 수준을 유지하기 위해 각 읽기/쿼리 요청과 관련하여 내부적으로 세션 토큰을 사용합니다.

세션 토큰을 수동으로 관리하려면 응답에서 세션 토큰을 가져와서 요청별로 설정합니다. 세션 토큰을 수동으로 관리할 필요가 없으면 다음 샘플을 사용할 필요가 없습니다. SDK는 세션 토큰을 자동으로 추적합니다. 세션 토큰을 수동으로 설정하지 않으면 기본적으로 SDK에서 최신 세션 토큰을 사용합니다.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4(Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   Java SDK V4(Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2 Sdk

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven:: azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[동기화](#tab/api-sync)

Java V2 SDK 동기화 (Maven:: azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>PBS(확률적 제한된 부실) 메트릭 모니터링

최종 일관성은 어떻게 최종인가요? 평균적인 사례의 경우 버전 기록 및 시간과 관련하여 부실 범위를 제공할 수 있습니다. [**PBS(확률적 제한된 부실)**](https://pbs.cs.berkeley.edu/) 메트릭은 부실의 확률을 수량화하여 메트릭으로 표시하려고 시도합니다. PBS 메트릭을 보려면 Azure Portal에서 Cosmos 계정으로 이동합니다. **메트릭** 창을 열고 **일관성** 탭을 선택 합니다. **작업을 기반으로 하는 강력한 일관성 읽기의 확률**이라는 그래프를 확인 합니다 (PBS 참조).

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Azure Portal의 PBS 그래프":::

## <a name="next-steps"></a>다음 단계

데이터 충돌을 관리하는 방법을 자세히 알아보거나 Azure Cosmos DB의 다음 핵심 개념으로 이동합니다. 다음 문서를 참조하세요.

* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure 지역 간 충돌 관리](how-to-manage-conflicts.md)
* [분할 및 데이터 배포](partition-data.md)
* [최신 분산 데이터베이스 시스템 디자인의 일관성 절충](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [고가용성](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
