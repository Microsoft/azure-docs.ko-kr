---
title: Azure Cosmos DB Sdk에서 저장 프로시저, 트리거 및 사용자 정의 함수 등록 및 사용
description: Azure Cosmos DB SDK를 사용하여 저장 프로시저, 트리거 및 사용자 정의 함수를 등록하고 호출하는 방법 알아보기
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: e21d74ca250061ec67d0d27477591da953a37cfa
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997118"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 등록하고 사용하는 방법

Azure Cosmos DB의 SQL API는 저장 프로시저, 트리거 및 JavaScript로 작성된 UDF(사용자 정의 함수)를 등록하고 호출하도록 지원합니다. SQL API [.net](sql-api-sdk-dotnet.md), [.Net Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)또는 [Python](sql-api-sdk-python.md) sdk를 사용 하 여 저장 프로시저를 등록 하 고 호출할 수 있습니다. 하나 이상의 저장 프로시저, 트리거 및 사용자 정의 함수를 정의한 후에는 데이터 탐색기를 사용 하 여 [Azure Portal](https://portal.azure.com/) 에서 로드 하 고 볼 수 있습니다.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>저장 프로시저를 실행하는 방법

저장 프로시저는 JavaScript를 사용하여 작성됩니다. Azure Cosmos 컨테이너 내에서 항목을 만들고, 업데이트하고, 읽고, 쿼리하고, 삭제할 수 있습니다. Azure Cosmos DB에서 저장 프로시저를 작성하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 저장 프로시저를 작성하는 방법](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) 문서를 참조하세요.

다음 예제에서는 Azure Cosmos DB SDK를 사용하여 저장 프로시저를 등록하고 호출하는 방법을 보여줍니다. 저장 프로시저에 대한 원본이 `spCreateToDoItem.js`로 저장되면 [문서 만들기](how-to-write-stored-procedures-triggers-udfs.md#create-an-item)를 참조하세요.

> [!NOTE]
> 분할된 컨테이너의 경우 저장 프로시저를 실행할 때 파티션 키 값은 요청 옵션에서 제공되어야 합니다. 저장 프로시저의 범위는 항상 파티션 키로 지정됩니다. 다른 파티션 키 값을 가진 항목은 저장 프로시저에 표시되지 않습니다. 이 트리거에도 적용되었습니다.

### <a name="stored-procedures---net-sdk-v2"></a>저장 프로시저-.NET SDK V2

다음 예에서는 .NET SDK V2를 사용 하 여 저장 프로시저를 등록 하는 방법을 보여 줍니다.

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

다음 코드에서는 .NET SDK V2를 사용 하 여 저장 프로시저를 호출 하는 방법을 보여 줍니다.

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>저장 프로시저-.NET SDK V3

다음 예에서는 .NET SDK V3를 사용 하 여 저장 프로시저를 등록 하는 방법을 보여 줍니다.

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

다음 코드에서는 .NET SDK V3을 사용 하 여 저장 프로시저를 호출 하는 방법을 보여 줍니다.

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>저장 프로시저 - Java SDK

다음 예제는 Java SDK를 사용하여 저장 프로시저를 등록하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

다음 코드는 Java SDK를 사용하여 저장 프로시저를 호출하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>저장 프로시저 - JavaScript SDK

다음 예제는 JavaScript SDK를 사용하여 저장 프로시저를 등록하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

다음 코드는 JavaScript SDK를 사용하여 저장 프로시저를 호출하는 방법을 보여줍니다.

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>저장 프로시저 - Python SDK

다음 예에서는 Python SDK를 사용 하 여 저장 프로시저를 등록 하는 방법을 보여 줍니다.

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()

sproc = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
created_sproc = container.scripts.create_stored_procedure(body=sproc) 
```

다음 코드에서는 Python SDK를 사용 하 여 저장 프로시저를 호출 하는 방법을 보여 줍니다.

```python
import uuid

new_id= str(uuid.uuid4())

# Creating a document for a container with "id" as a partition key.

new_item =   {
      "id": new_id, 
      "category":"Personal",
      "name":"Groceries",
      "description":"Pick up strawberries",
      "isComplete":False
   }
result = container.scripts.execute_stored_procedure(sproc=created_sproc,params=[[new_item]], partition_key=new_id) 
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>사전 트리거를 실행하는 방법

다음 예제에서는 Azure Cosmos DB SDK를 사용하여 사전 트리거를 등록하고 호출하는 방법을 보여줍니다. 이 사전 트리거의 원본이 `trgPreValidateToDoItemTimestamp.js`로 저장되면 [사전 트리거 예제](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers)를 참조하세요.

실행하는 경우 사전 트리거가 `PreTriggerInclude`를 지정한 다음, 트리거의 이름을 목록 개체에 전달하여 RequestOptions 개체에 전달됩니다.

> [!NOTE]
> 트리거의 이름이 목록으로 전달되는 경우에도 작업당 하나의 트리거만 실행할 수 있습니다.

### <a name="pre-triggers---net-sdk-v2"></a>사전 트리거-.NET SDK V2

다음 코드는 .NET SDK V2를 사용 하 여 사전 트리거를 등록 하는 방법을 보여 줍니다.

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

다음 코드는 .NET SDK V2를 사용 하 여 사전 트리거를 호출 하는 방법을 보여 줍니다.

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>사전 트리거-.NET SDK V3

다음 코드는 .NET SDK V3을 사용 하 여 사전 트리거를 등록 하는 방법을 보여 줍니다.

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

다음 코드는 .NET SDK V3을 사용 하 여 사전 트리거를 호출 하는 방법을 보여 줍니다.

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>사전 트리거 - Java SDK

다음 코드는 Java SDK를 사용하여 사전 트리거를 등록하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

다음 코드는 Java SDK를 사용하여 사전 트리거를 호출하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>사전 트리거 - JavaScript SDK

다음 코드는 JavaScript SDK를 사용하여 사전 트리거를 등록하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

다음 코드는 JavaScript SDK를 사용하여 사전 트리거를 호출하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>사전 트리거 - Python SDK

다음 코드는 Python SDK를 사용하여 사전 트리거를 등록하는 방법을 보여줍니다.

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

다음 코드는 Python SDK를 사용하여 사전 트리거를 호출하는 방법을 보여줍니다.

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>사후 트리거를 실행하는 방법

다음 예제에서는 Azure Cosmos DB SDK를 사용하여 사후 트리거를 등록하는 방법을 보여줍니다. 이 사후 트리거의 원본이 `trgPostUpdateMetadata.js`로 저장되면 [사후 트리거 예제](how-to-write-stored-procedures-triggers-udfs.md#post-triggers)를 참조하세요.

### <a name="post-triggers---net-sdk-v2"></a>사후 트리거-.NET SDK V2

다음 코드에서는 .NET SDK V2를 사용 하 여 사후 트리거를 등록 하는 방법을 보여 줍니다.

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

다음 코드는 .NET SDK V2를 사용 하 여 사후 트리거를 호출 하는 방법을 보여 줍니다.

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>사후 트리거-.NET SDK V3

다음 코드에서는 .NET SDK V3을 사용 하 여 사후 트리거를 등록 하는 방법을 보여 줍니다.

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

다음 코드는 .NET SDK V3을 사용 하 여 사후 트리거를 호출 하는 방법을 보여 줍니다.

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>사후 트리거 - Java SDK

다음 코드는 Java SDK를 사용하여 사후 트리거를 등록하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

다음 코드는 Java SDK를 사용하여 사후 트리거를 호출하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>사후 트리거 - JavaScript SDK

다음 코드는 JavaScript SDK를 사용하여 사후 트리거를 등록하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

다음 코드는 JavaScript SDK를 사용하여 사후 트리거를 호출하는 방법을 보여줍니다.

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>사후 트리거 - Python SDK

다음 코드는 Python SDK를 사용하여 사후 트리거를 등록하는 방법을 보여줍니다.

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPostValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPostValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

다음 코드는 Python SDK를 사용하여 사후 트리거를 호출하는 방법을 보여줍니다.

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'post_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>사용자 정의 함수로 작업하는 방법

다음 예제에서는 Azure Cosmos DB SDK를 사용하여 사용자 정의 함수를 등록하는 방법을 보여줍니다. 이 사후 트리거의 원본이 `udfTax.js`로 저장되면 [사용자 정의 함수 예제](how-to-write-stored-procedures-triggers-udfs.md#udfs)를 참조하세요.

### <a name="user-defined-functions---net-sdk-v2"></a>사용자 정의 함수-.NET SDK V2

다음 코드에서는 .NET SDK V2를 사용 하 여 사용자 정의 함수를 등록 하는 방법을 보여 줍니다.

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

다음 코드에서는 .NET SDK V2를 사용 하 여 사용자 정의 함수를 호출 하는 방법을 보여 줍니다.

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>사용자 정의 함수-.NET SDK V3

다음 코드에서는 .NET SDK V3을 사용 하 여 사용자 정의 함수를 등록 하는 방법을 보여 줍니다.

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

다음 코드에서는 .NET SDK V3을 사용 하 여 사용자 정의 함수를 호출 하는 방법을 보여 줍니다.

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>사용자 정의 함수 - Java SDK

다음 코드는 Java SDK를 사용하여 사용자 정의 함수를 등록하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

다음 코드는 Java SDK를 사용하여 사용자 정의 함수를 호출하는 방법을 보여줍니다.

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>사용자 정의 함수 - JavaScript SDK

다음 코드는 JavaScript SDK를 사용하여 사용자 정의 함수를 등록하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

다음 코드는 JavaScript SDK를 사용하여 사용자 정의 함수를 호출하는 방법을 보여줍니다.

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>사용자 정의 함수 - Python SDK

다음 코드는 Python SDK를 사용하여 사용자 정의 함수를 등록하는 방법을 보여줍니다.

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/udfTax.js') as file:
    file_contents = file.read()
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.scripts.create_user_defined_function(udf_definition)
```

다음 코드는 Python SDK를 사용하여 사용자 정의 함수를 호출하는 방법을 보여줍니다.

```python
results = list(container.query_items(
    'query': 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 작성하고 사용하는 개념 및 방법을 알아봅니다.

- [Azure Cosmos DB에서 Azure Cosmos DB 저장 프로시저, 트리거 및 사용자 정의 함수 작업](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB에서 JavaScript LINQ(Language-Integrated Query) API 작업](javascript-query-api.md)
- [Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 작성하는 방법](how-to-write-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB에서 JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거를 작성하는 방법](how-to-write-javascript-query-api.md)
