---
title: Azure Cosmos DB에서 라이브 시간 구성 및 관리
description: 컨테이너와 항목에 대해 ttl (time to live)을 구성 하 고 관리 하는 방법에 대해 알아봅니다 Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/27/2020
ms.author: anfeldma
ms.custom: devx-track-javascript, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 75299ab83543b0f28f4cf8f02e41b692c32d19ed
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997271"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Azure Cosmos DB에서 TTL(Time to Live) 구성

Azure Cosmos DB에서는 컨테이너 수준에서 TTL(Time to Live)을 구성하도록 선택하거나 컨테이너를 설정한 후 항목 수준에서 재정의할 수 있습니다. Azure Portal 또는 언어별 SDK를 사용하여 컨테이너에 대해 TTL을 구성할 수 있습니다. SDK를 사용하여 항목 수준 TTL 재정의를 구성할 수 있습니다.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Azure Portal을 사용하여 컨테이너에서 TTL(Time to Live)을 사용하도록 설정

컨테이너에서 만료 없이 TTL(Time to Live)을 사용하도록 설정하려면 다음 단계를 따르세요. 항목 수준에서 TTL이 재정의되도록 허용하려면 이 기능을 사용하세요. 초의 값으로 0이 아닌 값을 입력하여 TTL을 설정할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 새 Azure Cosmos 계정을 만들거나 기존 계정을 선택합니다.

3. **데이터 탐색기** 창을 엽니다.

4. 기존 컨테이너를 선택하여 확장하고, 다음 값을 수정합니다.

   * **배율 및 설정** 창을 엽니다.
   * **설정** 아래에서 **TTL(Time to Live)** 을 찾습니다.
   * **설정(기본값 없음)** 를 선택하거나 **켜기**를 선택하고 TTL 값을 설정합니다.
   * **저장**을 클릭하여 변경 내용을 저장합니다.

   :::image type="content" source="./media/how-to-time-to-live/how-to-time-to-live-portal.png" alt-text="Azure Portal에서 TTL(Time to Live) 구성":::

* DefaultTimeToLive가 Null인 경우 TTL(Time to Live)이 꺼짐
* DefaultTimeToLive가 -1인 경우 TTL(Time to Live) 설정이 켜짐(기본값 없음)
* DefaultTimeToLive가 다른 Int 값(0 제외)을 가진 경우 TTL(Time to Live) 설정이 켜짐

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용 하 여 컨테이너에 대해 ttl (time to live) 사용

컨테이너에서 TTL을 만들거나 사용 하도록 설정 하려면을 참조 하세요.

* [Azure CLI를 사용 하 여 TTL이 있는 컨테이너 만들기](manage-with-cli.md#create-a-container-with-ttl)
* [PowerShell을 사용 하 여 TTL이 있는 컨테이너 만들기](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>SDK를 사용하여 컨테이너에서 TTL(Time to Live)을 사용하도록 설정

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven:: azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>SDK를 사용하여 컨테이너에 대한 TTL(Time to Live) 설정

컨테이너에 대한 TTL(Time to Live)을 설정하려면 기간을 초 단위로 나타내는 0이 아닌 양수 값을 입력해야 합니다. 구성된 TTL 값에 따라, 컨테이너에 있는 모든 항목은 항목 `_ts`의 마지막으로 수정된 타임스탬프 후에 삭제됩니다.

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven:: azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>항목에 대한 TTL(Time to live) 설정

컨테이너에 대한 기본 TTL(Time to Live)을 설정하는 것 외에도 항목에 대한 TTL(Time to Live)을 설정할 수 있습니다. 항목 수준에서 TTL(Time to Live)을 설정하면 해당 컨테이너에 있는 항목의 기본 TTL이 재정의됩니다.

* 항목에 대한 TTL을 설정하려면 항목 `_ts`의 타임스탬프를 마지막으로 수정한 이후 항목을 만료할 기간(초)을 나타내는 0이 아닌 양수 값을 제공해야 합니다.

* 항목에 TTL 필드에 없는 경우 기본적으로 컨테이너에 설정된 TTL이 항목에 적용됩니다.

* 컬렉션 수준에서 TTL이 설정되지 않으면 항목의 TTL 필드는 컨테이너에 대한 TTL이 다시 설정될 때까지 무시됩니다.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure Portal

항목에 대해 TTL(Time to Live)을 사용하도록 설정하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 새 Azure Cosmos 계정을 만들거나 기존 계정을 선택합니다.

3. **데이터 탐색기** 창을 엽니다.

4. 기존 컨테이너를 선택하여 확장하고, 다음 값을 수정합니다.

   * **배율 및 설정** 창을 엽니다.
   * **설정** 아래에서 **TTL(Time to Live)** 을 찾습니다.
   * **켜기 (기본값 없음)** 를 선택 하거나 TTL 값을 **선택 하 고** 설정 합니다. 
   * **저장**을 클릭하여 변경 내용을 저장합니다.

5. 다음으로, TTL(Time to Live)을 설정하려는 항목으로 이동한 후 `ttl` 속성을 추가하고 **업데이트**를 선택합니다. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (any)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven:: azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```
---

## <a name="reset-time-to-live"></a>TTL 다시 설정

항목에 대한 쓰기 또는 업데이트 작업을 수행하여 항목에 대한 TTL을 다시 설정할 수 있습니다. 쓰기 또는 업데이트 작업은 `_ts`를 현재 시간으로 설정하고, 항목이 만료되는 TTL이 다시 시작 됩니다. 항목의 TTL을 변경하려면 다른 필드를 업데이트하는 것처럼 필드를 업데이트하면 됩니다.

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

SDK V3 (Maven:: azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="turn-off-time-to-live"></a>TTL(Time to live) 끄기

항목에 TTL(Time to Live)이 설정되어 있는데 더 이상 해당 항목이 만료되는 것을 원하지 않는 경우 항목을 가져오고, TTL 필드를 제거하고, 서버에서 해당 항목을 대체할 수 있습니다. 항목에서 TTL 필드가 제거되면 컨테이너에 할당된 기본 TTL 값이 항목에 적용됩니다. 항목이 만료되지 않고 컨테이너에서 TTL 값을 상속하지 않도록 하려면 TTL 값을 -1로 설정합니다.

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven:: azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="disable-time-to-live"></a>TTL(Time to Live) 해제

컨테이너에 대한 TTL(Time to Live)을 해제하고 백그라운드 프로세스에서 만료된 항목 검사를 중지하려면 컨테이너의 `DefaultTimeToLive` 속성을 삭제해야 합니다. 이 속성을 삭제하는 것은 -1로 설정하는 것과 다릅니다. -1로 설정하면 컨테이너에 추가되는 새 항목이 영구적으로 라이브 상태로 유지되지만 컨테이너의 특정 항목에서 이 값을 재정의할 수 있습니다. 컨테이너에서 TTL 속성을 제거하면 항목이 기존의 기본 TTL 값을 명시적으로 재정의하더라도 항목이 절대 만료되지 않습니다.

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4(Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven:: azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>다음 단계

다음 문서에서 TTL(Time to Live)에 대해 자세히 알아보세요.

* [TTL(Time to live)](time-to-live.md)
