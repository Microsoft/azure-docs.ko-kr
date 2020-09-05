---
title: Azure Functions의 Azure Table Storage 바인딩
description: Azure Functions에서 Azure Table Storage 바인딩을 사용하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 50706e1b525a3e3a39701ef2135d44c02c35077e
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181139"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions의 Azure Table Storage 바인딩

이 문서에서는 Azure Functions에서 Azure Table Storage 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Azure Table Storage에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Table Storage 바인딩은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

Table Storage 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>입력

Azure Table Storage 입력 바인딩을 사용하여 Azure Storage 계정에서 테이블을 읽을 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>하나의 엔터티

다음 예제에서는 단일 테이블 행을 읽을 수 있는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 큐에 전송 되는 모든 메시지에 대해 함수가 트리거됩니다.

행 키 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

다음 예제에서는 클래스가에서 파생 되는 여러 테이블 행을 읽는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다 `MyPoco` `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` 는 [v1 Runtime 함수](functions-versions.md)에서만 지원 됩니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 `CloudTable` 메서드 매개 변수를 사용합니다. Azure Functions 로그 테이블을 쿼리 하는 함수의 예는 다음과 같습니다.

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

CloudTable을 사용하는 방법에 대한 자세한 내용은 [Azure Table Storage 시작](../cosmos-db/tutorial-develop-table-dotnet.md)을 참조하세요.

`CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>하나의 엔터티

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 트리거를 사용하여 단일 테이블 행을 읽습니다. 

*function.json* 파일은 `partitionKey` 및 `rowKey`를 지정합니다. `rowKey` 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 메시지에 지정된 파티션 키의 엔터티를 읽습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 엔터티 형식이 `TableEntity`에서 파생할 수 있도록 Azure Storage SDK에 대한 참조를 추가합니다.

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable`[버전 2.x 이상의](functions-versions.md)함수 런타임에서는 지원 되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 `CloudTable` 메서드 매개 변수를 사용합니다. Azure Functions 로그 테이블을 쿼리 하는 함수의 예는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

CloudTable을 사용하는 방법에 대한 자세한 내용은 [Azure Table Storage 시작](../cosmos-db/tutorial-develop-table-dotnet.md)을 참조하세요.

`CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 코드](functions-reference-node.md)에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 트리거를 사용하여 단일 테이블 행을 읽습니다. 

*function.json* 파일은 `partitionKey` 및 `rowKey`를 지정합니다. `rowKey` 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

단일 테이블 행 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

다음 예에서는 테이블 저장소의 지정 된 파티션에 있는 person 개체의 목록을 반환 하는 HTTP 트리거 함수를 보여 줍니다. 이 예에서는 http 경로에서 파티션 키를 추출 하 고 tableName과 연결을 함수 설정에서 추출 합니다. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput 주석은 다음 예제와 같이 요청의 json 본문에서 바인딩을 추출할 수도 있습니다.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

다음 예에서는 필터를 사용 하 여 Azure 테이블의 특정 이름을 가진 사람을 쿼리하고 가능한 일치 항목 수를 10 개 결과로 제한 합니다.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>입력-특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

 [C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 테이블 입력 바인딩을 구성합니다.

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  특성의 생성자는 테이블 이름, 파티션 키 및 행 키를 사용합니다. 특성은 `out` 다음 예제에 표시 된 것 처럼 매개 변수 또는 함수의 반환 값에 사용할 수 있습니다.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  전체 예제는 입력 - C# 예제를 참조하세요.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  사용할 스토리지 계정을 지정하는 다른 방법을 제공합니다. 생성자는 스토리지 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

사용할 스토리지 계정은 다음과 같은 순서로 결정됩니다.

* `Table` 특성의 `Connection` 속성
* `Table` 특성과 동일한 매개 변수에 적용된 `StorageAccount` 특성
* 함수에 적용된 `StorageAccount` 특성
* 클래스에 적용된 `StorageAccount` 특성
* 함수 앱의 기본 스토리지 계정("AzureWebJobsStorage" 앱 설정)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Table Storage에서 제공되는 매개 변수에 대한 `@TableInput` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="input---configuration"></a>입력 - 구성

다음 표에서는 *function.json* 파일 및 `Table` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `table`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 테이블 또는 엔터티를 나타내는 변수의 이름입니다. | 
|**tableName** | **TableName** | 테이블의 이름입니다.| 
|**partitionKey** | **PartitionKey** |선택 사항입니다. 읽을 테이블 엔터티의 파티션 키입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#input---usage) 섹션을 참조하세요.| 
|**rowKey** |**RowKey** | 선택 사항입니다. 읽을 테이블 엔터티의 행 키입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#input---usage) 섹션을 참조하세요.| 
|**take** |**Take** | 선택 사항입니다. JavaScript에서 읽을 수 있는 엔터티의 최대 수입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#input---usage) 섹션을 참조하세요.| 
|**filter** |**Filter** | 선택 사항입니다. JavaScript에서 테이블 입력에 대한 OData 필터 식입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#input---usage) 섹션을 참조하세요.| 
|**connection** |**연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 설정은 "AzureWebJobs" 접두사가 붙은 앱 설정 또는 연결 문자열 이름의 이름일 수 있습니다. 예를 들어 설정 이름이 "AzureWebJobsMyStorage" 인 경우 여기에서 "MyStorage"를 지정할 수 있습니다. 함수 런타임은 "AzureWebJobsMyStorage" 라는 앱 설정을 자동으로 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>입력 - 사용

# <a name="c"></a>[C#](#tab/csharp)

* **에서 한 행을 읽습니다.**

  `partitionKey` 및 `rowKey`를 설정합니다. `T <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `T`는 일반적으로 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* **하나 이상의 행 읽기**

  `IQueryable<T> <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `T`는 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `IQueryable` 메서드를 사용하여 필요한 필터링을 수행할 수 있습니다 `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.  

  > [!NOTE]
  > `IQueryable`은 [Functions v2 런타임](functions-versions.md)에서 지원되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 [CloudTable paramName 메서드 매개 변수를 사용](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)합니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **에서 한 행을 읽습니다.**

  `partitionKey` 및 `rowKey`를 설정합니다. `T <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `T`는 일반적으로 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* **하나 이상의 행 읽기**

  `IQueryable<T> <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `T`는 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `IQueryable` 메서드를 사용하여 필요한 필터링을 수행할 수 있습니다 `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.  

  > [!NOTE]
  > `IQueryable`은 [Functions v2 런타임](functions-versions.md)에서 지원되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 [CloudTable paramName 메서드 매개 변수를 사용](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)합니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`filter` 및 `take` 속성을 설정합니다. `partitionKey` 또는 `rowKey`를 설정하지 않습니다. `context.bindings.<BINDING_NAME>`을 사용하여 입력 테이블 엔터티(또는 여러 엔터티)에 액세스합니다. 역직렬화된 개체는 `RowKey` 및 `PartitionKey` 속성을 가집니다.

# <a name="python"></a>[Python](#tab/python)

테이블 데이터는 JSON 문자열로 함수에 전달 됩니다. 입력 예제와 같이를 호출 하 여 메시지를 역직렬화 `json.loads` 합니다 [example](#input).

# <a name="java"></a>[Java](#tab/java)

[Tableinput](/java/api/com.microsoft.azure.functions.annotation.tableinput) 특성은 함수를 트리거한 테이블 행에 대 한 액세스를 제공 합니다.

---

## <a name="output"></a>출력

Azure Table Storage 출력 바인딩을 사용하여 Azure Storage 계정에서 테이블에 엔터티를 쓸 수 있습니다.

> [!NOTE]
> 이 출력 바인딩은 기존 엔터티 업데이트를 지원하지 않습니다. [Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity)에서 `TableOperation.Replace` 작업을 사용하여 기존 엔터티를 업데이트합니다.

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 단일 테이블 행을 쓰기 위해 HTTP 트리거를 사용하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 출력 바인딩을 보여줍니다. 함수는 여러 테이블 엔터티를 씁니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 함수](functions-reference-node.md)에서 테이블 출력 바인딩을 보여줍니다. 함수는 여러 테이블 엔터티를 씁니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 Table storage 출력 바인딩을 사용 하는 방법을 보여 줍니다. `table`바인딩은,, 및에 값을 할당 하 여 *function.js* 에서 구성 됩니다 `name` `tableName` `partitionKey` `connection` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

다음 함수는 값에 대 한 고유 UUI를 생성 `rowKey` 하 고 테이블 저장소에 메시지를 유지 합니다.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 단일 테이블 행을 쓰기 위해 HTTP 트리거를 사용 하는 Java 함수를 보여 줍니다.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

다음 예제에서는 여러 테이블 행을 쓰기 위해 HTTP 트리거를 사용 하는 Java 함수를 보여 줍니다.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>출력-특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)를 사용합니다.

특성의 생성자는 테이블 이름을 사용합니다. 특성은 `out` 다음 예제에 표시 된 것 처럼 매개 변수 또는 함수의 반환 값에 사용할 수 있습니다.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#output)를 참조하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [입력 - 특성](#input---attributes-and-annotations)을 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 매개 변수에 대 한 [tableoutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 주석을 사용 하 여 테이블 저장소에 값을 씁니다.

자세한 내용은 [예제](#output)를 참조 하세요.

---

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `Table` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `table`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 테이블 또는 엔터티를 나타내는 함수 코드에서 사용되는 변수 이름입니다. `$return`으로 설정하여 함수 반환 값을 참조합니다.| 
|**tableName** |**TableName** | 테이블의 이름입니다.| 
|**partitionKey** |**PartitionKey** | 쓸 테이블 엔터티의 파티션 키입니다. 이 속성을 사용 하는 방법에 대 한 지침은 [사용 섹션](#output---usage) 을 참조 하세요.| 
|**rowKey** |**RowKey** | 쓸 테이블 엔터티의 행 키입니다. 이 속성을 사용 하는 방법에 대 한 지침은 [사용 섹션](#output---usage) 을 참조 하세요.| 
|**connection** |**연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어를 `connection` "MyStorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

# <a name="c"></a>[C#](#tab/csharp)

메서드 매개 변수를 사용 하거나에 및 속성을 포함 하 여 출력 테이블 엔터티에 액세스 `ICollector<T> paramName` `IAsyncCollector<T> paramName` `T` `PartitionKey` `RowKey` 합니다. 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` .

또는 `CloudTable` AZURE STORAGE SDK를 사용 하 여 메서드 매개 변수를 사용 하 여 테이블에 쓸 수 있습니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

메서드 매개 변수를 사용 하거나에 및 속성을 포함 하 여 출력 테이블 엔터티에 액세스 `ICollector<T> paramName` `IAsyncCollector<T> paramName` `T` `PartitionKey` `RowKey` 합니다. 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` . `paramName`값은 `name` *function.js*의 속성에 지정 됩니다.

또는 `CloudTable` AZURE STORAGE SDK를 사용 하 여 메서드 매개 변수를 사용 하 여 테이블에 쓸 수 있습니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`을 사용하여 출력 이벤트에 액세스합니다. 여기서 `<name>`은 *function.json*의 `name` 속성에 지정된 값입니다.

# <a name="python"></a>[Python](#tab/python)

함수에서 테이블 저장소 행 메시지를 출력 하는 두 가지 옵션은 다음과 같습니다.

- **반환 값**:function.js의 `name` 속성을 *function.json* 로 설정 `$return` 합니다. 이 구성을 사용 하면 함수의 반환 값이 테이블 저장소 행으로 유지 됩니다.

- **명령적**: [Out](/python/api/azure-functions/azure.functions.out?view=azure-python) 형식으로 선언 된 매개 변수의 [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) 메서드에 값을 전달 합니다. 에 전달 된 값은 `set` 이벤트 허브 메시지로 유지 됩니다.

# <a name="java"></a>[Java](#tab/java)

[Tablestorageoutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) 주석을 사용 하 여 함수에서 테이블 저장소 행을 출력 하는 두 가지 옵션이 있습니다.

- **반환 값**: 함수 자체에 주석을 적용 하면 함수의 반환 값이 테이블 저장소 행으로 유지 됩니다.

- **명령적**: 메시지 값을 명시적으로 설정 하려면 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 및 속성을 포함 하는 형식의 특정 매개 변수에 주석을 적용 합니다 `T` `PartitionKey` `RowKey` . 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| 테이블 | [테이블 오류 코드](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, 테이블, 큐 | [스토리지 오류 코드](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 | [문제 해결](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
