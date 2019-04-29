---
title: Functions 2.x의 Azure Cosmos DB 바인딩
description: Azure Functions에서 Azure Cosmos DB 트리거 및 바인딩을 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5762e934d7735dd9617cefc1f56105823d74312f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342976"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Functions 2.x의 Azure Cosmos DB 바인딩

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [버전 1](functions-bindings-cosmosdb.md)
> * [버전 2](functions-bindings-cosmosdb-v2.md)

이 아티클에서는 Azure Functions 2.x에서 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Azure Cosmos DB에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

> [!NOTE]
> 이 문서는 [Azure Functions 버전 2.x](functions-versions.md)용입니다.  Functions 1.x에서 이러한 바인딩을 사용하는 방법에 대한 내용은 [Azure Functions 1.x에 대한 Azure Cosmos DB 바인딩](functions-bindings-cosmosdb.md)을 참조하세요.
>
> 이 바인딩의 원래 이름은 DocumentDB입니다. Functions 버전 2.x에서는 트리거, 바인딩 및 패키지의 이름이 모두 Cosmos DB로 지정됩니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>지원되는 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

Functions 버전 2.x에 대한 Cosmos DB 바인딩은 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet 패키지인 버전 3.x에서 제공됩니다. 이 바인딩에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>트리거

Azure Cosmos DB 트리거는 [Azure Cosmos DB 변경 피드](../cosmos-db/change-feed.md)를 사용하여 파티션에 대한 삽입 및 업데이트를 수신 대기합니다. 변경 피드는 삽입과 업데이트를 게시하지만 삭제는 게시하지 않습니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

트리거 건너뛰기 예제

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 지정한 데이터베이스 및 컬렉션에서 삽입 또는 업데이트가 있을 때 호출되는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents, 
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

트리거 건너뛰기 예제

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 이 함수는 Cosmos DB 레코드가 수정될 때 로그 메시지를 작성합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

트리거 건너뛰기 예제

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 이 함수는 Cosmos DB 레코드가 수정될 때 로그 메시지를 작성합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

### <a name="trigger---java-example"></a>트리거 - Java 예제

다음 예제에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [Java 함수](functions-reference-java.md)를 보여줍니다. 지정한 데이터베이스 및 컬렉션에서 삽입 또는 업데이트가 있을 때 함수가 호출됩니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Java 코드는 다음과 같습니다.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Cosmos DB에서 제공되는 매개 변수에 대한 `@CosmosDBTrigger` 주석을 사용합니다.  옵션 <T>를 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.


트리거 건너뛰기 예제

### <a name="trigger---python-example"></a>트리거 - Python 예제

다음 예제에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 이 함수는 Cosmos DB 레코드가 수정될 때 로그 메시지를 작성합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

다음은 Python 코드입니다.

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>트리거 - C# 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [트리거 - 구성](#trigger---configuration)을 참조하세요. 다음은 메서드 서명의 `CosmosDBTrigger` 특성 예제입니다.

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.


## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `CosmosDBTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** || `cosmosDBTrigger`로 설정해야 합니다. |
|**direction** || `in`로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** || 변경 사항이 포함된 문서 목록을 나타내는 함수 코드에 사용되는 변수 이름. |
|**connectionStringSetting**|**ConnectionStringSetting** | 모니터링되는 Azure Cosmos DB 계정에 연결하는 데 사용되는 연결 문자열을 포함하고 있는 앱 설정의 이름입니다. |
|**databaseName**|**DatabaseName**  | 컬렉션이 모니터링되는 Azure Cosmos DB 데이터베이스의 이름입니다. |
|**collectionName** |**CollectionName** | 모니터링되는 컬렉션의 이름입니다. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (선택 사항) 임대 컬렉션을 보유하고 있는 서비스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 설정하지 않으면 `connectionStringSetting` 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. 임대 컬렉션에 대한 연결 문자열에 쓰기 권한이 있어야 합니다.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션을 보유하는 데이터베이스의 이름입니다. 설정하지 않으면 `databaseName` 설정 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. |
|**leaseCollectionName** | **LeaseCollectionName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션의 이름입니다. 설정하지 않으면 `leases` 값이 사용됩니다. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (선택 사항) `true`로 설정하면 임대 컬렉션이 없는 경우 자동으로 임대 컬렉션이 생성됩니다. 기본값은 `false`입니다. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (선택 사항) 임대 컬렉션이 생성될 때 할당할 요청 단위의 양을 정의합니다. 이 설정은 `createLeaseCollectionIfNotExists`가 `true`로 설정된 경우에만 사용됩니다. 이 매개 변수는 포털을 사용하여 바인딩이 생성될 때 자동으로 설정됩니다.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (선택 사항) 설정하면 이 함수에 대한 임대 컬렉션에 생성된 임대에 접두사를 추가하여, 서로 다른 접두사를 사용하여 두 별도의 Azure Functions가 동일한 임대 컬렉션을 효과적으로 공유할 수 있도록 합니다.
|**feedPollDelay**| **FeedPollDelay**| (선택 사항) 설정하면 현재 변경 내용이 모두 삭제된 후에 피드에 대한 새로운 변경 내용의 파티션을 폴링하는 작업 간의 지연을 밀리초로 정의합니다. 기본값은 5000(5초)입니다.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (선택 사항) 설정하면 파티션이 알려진 호스트 인스턴스 간에 균등하게 배포되는지를 계산하는 태스크를 시작하는 간격을 밀리초로 정의합니다. 기본값은 13000(13초)입니다.
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (선택 사항) 설정하면 파티션을 나타내는 임대에 대한 임대 기간인 간격을 밀리초로 정의합니다. 이 간격 내에서 임대를 갱신하지 않으면 기간이 만료되어 다른 인스턴스로 파티션 소유권이 이동합니다. 기본값은 60000(60초)입니다.
|**leaseRenewInterval**| **LeaseRenewInterval**| (선택 사항) 설정하면 인스턴스가 현재 보유한 파티션의 모든 임대에 대한 갱신 간격을 밀리초로 정의합니다. 기본값은 17000(17초)입니다.
|**checkpointFrequency**| **CheckpointFrequency**| (선택 사항) 설정하면 임대 검사점 간격을 밀리초로 정의합니다. 기본값은 항상 각 함수 호출 이후입니다.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (선택 사항) 설정하면 함수 호출당 받은 최대 항목 수를 사용자 지정합니다.
|**startFromBeginning**| **StartFromBeginning**| (선택 사항) 설정하면 현재 시간이 아니라 컬렉션 기록 시작부터 변경 내용을 읽기 시작하도록 트리거에 알립니다. 후속 실행에서는 검사점이 이미 저장되므로 트리거가 처음 시작될 때만 작동합니다. 임대가 이미 만들어진 후에 이 값을 `true`로 설정하면 아무 효과가 없습니다.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>트리거 - 사용

트리거에는 파티션에 _임대_를 저장하는 데 사용할 보조 컬렉션이 필요합니다. 트리거가 작동할 수 있도록 모니터링되는 컬렉션과 임대를 포함하고 있는 컬렉션을 모두 사용할 수 있어야 합니다.

>[!IMPORTANT]
> 동일한 컬렉션에 Cosmos DB 트리거를 사용하도록 여러 함수가 구성된 경우 각 함수는 전용 임대 컬렉션을 사용하거나 각 함수에 다른 `LeaseCollectionPrefix`를 지정해야 합니다. 그러지 않으면 함수 중 하나만 트리거됩니다. 접두사에 대한 정보는 [구성 섹션](#trigger---configuration)을 참조하세요.

트리거는 문서가 업데이트 또는 삽입되었는지 여부를 나타내지 않고 문서 자체만 제공합니다. 업데이트 및 삽입을 다르게 처리해야 하는 경우 삽입 또는 업데이트에 대한 타임스탬프 필드를 구현하면 됩니다.

## <a name="input"></a>입력

Azure Cosmos DB 입력 바인딩은 SQL API를 사용하여 하나 이상의 Azure Cosmos DB 문서를 검색하고, 함수의 입력 매개 변수에 전달합니다. 문서 ID 또는 쿼리 매개 변수는 함수를 호출하는 트리거를 기반으로 결정할 수 있습니다.

## <a name="input---examples"></a>입력 - 예제

ID 값을 지정하여 단일 문서를 읽는 언어 관련 예제를 참조하세요.

* [C#](#input---c-examples)
* [C# 스크립트(.csx)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[입력 건너뛰기 예제](#input---attributes)

### <a name="input---c-examples"></a>입력 - C# 예제

이 섹션에는 다음 예제가 포함되어 있습니다.

* [큐 트리거, JSON에서 ID 조회](#queue-trigger-look-up-id-from-json-c)
* [HTTP 트리거, 쿼리 문자열에서 ID 조회](#http-trigger-look-up-id-from-query-string-c)
* [HTTP 트리거, 경로 데이터에서 ID 조회](#http-trigger-look-up-id-from-route-data-c)
* [HTTP 트리거, 경로 데이터에서 ID 조회, SqlQuery 사용](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP 트리거, 여러 문서 가져오기, SqlQuery 사용](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP 트리거, 여러 문서 가져오기, DocumentClient 사용](#http-trigger-get-multiple-docs-using-documentclient-c)

예제에서는 간단한 `ToDoItem` 형식을 참조하세요.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>큐 트리거, JSON에서 ID 조회(C#)

다음 예제에서는 단일 문서를 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 JSON 개체가 있는 큐 메시지에 의해 트리거됩니다. 큐 트리거는 `ToDoItemLookup`이라는 개체로 JSON을 구문 분석합니다. 여기에는 조회할 ID가 포함됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회(C#)

다음 예제에서는 단일 문서를 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

>[!NOTE]
>HTTP 쿼리 문자열 매개 변수는 대/소문자를 구분합니다.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP 트리거, 경로 데이터에서 ID 조회(C#)

다음 예제에서는 단일 문서를 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 경로 데이터를 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP 트리거, 경로 데이터에서 ID 조회, SqlQuery 사용(C#)

다음 예제에서는 단일 문서를 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 경로 데이터를 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

이 예제에서는 `SqlQuery` 매개 변수에서 바인딩 식을 사용하는 방법을 보여줍니다. 표시된 대로 경로 데이터를 `SqlQuery` 매개 변수에 전달할 수는 있지만 현재 [쿼리 문자열 값을 전달할 수 없습니다](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP 트리거, 여러 문서 가져오기, SqlQuery 사용(C#)

다음 예제에서는 문서 목록을 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 HTTP 요청에 의해 트리거됩니다. 쿼리는 `SqlQuery` 특성 속성에 지정됩니다.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP 트리거, 여러 문서 가져오기, DocumentClient 사용(C#)

다음 예제에서는 문서 목록을 검색하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 함수는 HTTP 요청에 의해 트리거됩니다. 코드를 Azure Cosmos DB 바인딩에 의해 제공된 `DocumentClient` 인스턴스를 사용하여 문서 목록을 읽습니다. `DocumentClient` 인스턴스는 쓰기 작업에 사용될 수도 있습니다.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

### <a name="input---c-script-examples"></a>입력 - C# 스크립트 예제

이 섹션에는 다음 예제가 포함되어 있습니다.

* [큐 트리거, 문자열에서 ID 조회](#queue-trigger-look-up-id-from-string-c-script)
* [큐 트리거, 여러 문서 가져오기, SqlQuery 사용](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 트리거, 쿼리 문자열에서 ID 조회](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP 트리거, 경로 데이터에서 ID 조회](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP 트리거, 여러 문서 가져오기, SqlQuery 사용](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP 트리거, 여러 문서 가져오기, DocumentClient 사용](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP 트리거 예제에서는 간단한 `ToDoItem` 형식을 참조합니다.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>큐 트리거, 문자열에서 ID 조회(C# 스크립트)

다음 예에서는 *function.json* 파일의 Cosmos DB 입력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 이 함수는 단일 문서를 읽고 문서의 텍스트 값을 업데이트합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>큐 트리거, 여러 문서 가져오기, SqlQuery 사용(C# 스크립트)

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 입력 바인딩 및 해당 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 이 함수는 큐 트리거를 사용하여 쿼리 매개 변수를 사용자 지정하여 SQL 쿼리로 지정된 여러 문서를 검색합니다.

큐 트리거는 매개 변수 `departmentId`를 제공합니다. `{ "departmentId" : "Finance" }`의 큐 메시지는 재무 부서에 대한 모든 레코드를 반환합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회(C# 스크립트)

다음 예제에서는 단일 문서를 검색하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP 트리거, 경로 데이터에서 ID 조회(C# 스크립트)

다음 예제에서는 단일 문서를 검색하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 경로 데이터를 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP 트리거, 여러 문서 가져오기, SqlQuery 사용(C# 스크립트)

다음 예제에서는 문서 목록을 검색하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 HTTP 요청에 의해 트리거됩니다. 쿼리는 `SqlQuery` 특성 속성에 지정됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP 트리거, 여러 문서 가져오기, DocumentClient 사용(C# 스크립트)

다음 예제에서는 문서 목록을 검색하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 HTTP 요청에 의해 트리거됩니다. 코드를 Azure Cosmos DB 바인딩에 의해 제공된 `DocumentClient` 인스턴스를 사용하여 문서 목록을 읽습니다. `DocumentClient` 인스턴스는 쓰기 작업에 사용될 수도 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[입력 건너뛰기 예제](#input---attributes)

### <a name="input---javascript-examples"></a>입력 - JavaScript 예제

이 섹션에는 다양한 원본의 ID 값을 지정하여 단일 문서를 읽는 다음과 같은 예제가 포함됩니다.

* [큐 트리거, JSON에서 ID 조회](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP 트리거, 쿼리 문자열에서 ID 조회](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP 트리거, 경로 데이터에서 ID 조회](#http-trigger-look-up-id-from-route-data-javascript)
* [큐 트리거, 여러 문서 가져오기, SqlQuery 사용](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>큐 트리거, JSON에서 ID 조회(JavaScript)

다음 예에서는 *function.json* 파일의 Cosmos DB 입력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 이 함수는 단일 문서를 읽고 문서의 텍스트 값을 업데이트합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회(JavaScript)

다음 예제에서는 단일 문서를 검색하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP 트리거, 경로 데이터에서 ID 조회(JavaScript)

다음 예제에서는 단일 문서를 검색하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>큐 트리거, 여러 문서 가져오기, SqlQuery 사용(JavaScript)

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 입력 바인딩 및 해당 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 이 함수는 큐 트리거를 사용하여 쿼리 매개 변수를 사용자 지정하여 SQL 쿼리로 지정된 여러 문서를 검색합니다.

큐 트리거는 매개 변수 `departmentId`를 제공합니다. `{ "departmentId" : "Finance" }`의 큐 메시지는 재무 부서에 대한 모든 레코드를 반환합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

[입력 건너뛰기 예제](#input---attributes)

### <a name="input---python-examples"></a>입력 - Python 예제

이 섹션에는 다양한 원본의 ID 값을 지정하여 단일 문서를 읽는 다음과 같은 예제가 포함됩니다.

* [큐 트리거, JSON에서 ID 조회](#queue-trigger-look-up-id-from-json-python)
* [HTTP 트리거, 쿼리 문자열에서 ID 조회](#http-trigger-look-up-id-from-query-string-python)
* [HTTP 트리거, 경로 데이터에서 ID 조회](#http-trigger-look-up-id-from-route-data-python)
* [큐 트리거, 여러 문서 가져오기, SqlQuery 사용](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>큐 트리거, JSON에서 ID 조회(Python)

다음 예제에서는 *function.json* 파일의 Cosmos DB 입력 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 이 함수는 단일 문서를 읽고 문서의 텍스트 값을 업데이트합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회(Python)

다음 예제에서는 단일 문서를 검색하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>HTTP 트리거, 경로 데이터에서 ID 조회(Python)

다음 예제에서는 단일 문서를 검색하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 `ToDoItem` 문서를 검색하는 데 사용됩니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

[입력 건너뛰기 예제](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>큐 트리거, 여러 문서 가져오기, SqlQuery 사용(Python)

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 입력 바인딩 및 해당 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 이 함수는 큐 트리거를 사용하여 쿼리 매개 변수를 사용자 지정하여 SQL 쿼리로 지정된 여러 문서를 검색합니다.

큐 트리거는 매개 변수 `departmentId`를 제공합니다. `{ "departmentId" : "Finance" }`의 큐 메시지는 재무 부서에 대한 모든 레코드를 반환합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[입력 건너뛰기 예제](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>입력 - F# 예제

다음 예에서는 *function.json* 파일의 Cosmos DB 입력 바인딩 및 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여줍니다. 이 함수는 단일 문서를 읽고 문서의 텍스트 값을 업데이트합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

F# 코드는 다음과 같습니다.

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

이 예제에는 `FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일이 필요합니다.

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

`project.json` 파일을 추가하려면 [F# 패키지 관리](functions-reference-fsharp.md#package)를 참조하세요.

### <a name="input---java-examples"></a>입력 - Java 예제

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, 쿼리 문자열에서 ID 조회 - 문자열 매개 변수](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP 트리거, 쿼리 문자열에서 ID 조회 - POJO 매개 변수](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP 트리거, 경로 데이터에서 ID 조회](#http-trigger-look-up-id-from-route-data-java)
* [HTTP 트리거, 경로 데이터에서 ID 조회, SqlQuery 사용](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP 트리거, SqlQuery를 사용하여 경로 데이터에서 여러 문서 가져오기](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

예제에서는 간단한 `ToDoItem` 형식을 참조하세요.

```java
public class ToDoItem {

  private String id;
  private String description;  

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회 - 문자열 매개 변수(Java)

다음 예제에서는 단일 문서를 검색하는 Java 함수를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 문자열 형식의 문서를 검색하는 데 사용됩니다.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS) 
            HttpRequestMessage<Optional<String>> request,        
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.id}",
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            Optional<String> item,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string 
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Cosmos DB에서 제공되는 함수 매개 변수에 대한 `@CosmosDBInput` 주석을 사용합니다.  옵션 <T>를 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>HTTP 트리거, 쿼리 문자열에서 ID 조회 - POJO 매개 변수(Java)

다음 예제에서는 단일 문서를 검색하는 Java 함수를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 쿼리 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 문서를 검색하는 데 사용됩니다. 그런 후 문서는 이전에 만든 ```ToDoItem``` POJO의 인스턴스로 변환되고 함수에 인수로 전달됩니다.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS) 
            HttpRequestMessage<Optional<String>> request,        
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.id}",
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            ToDoItem item,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>HTTP 트리거, 경로 데이터에서 ID 조회(Java)

다음 예제에서는 단일 문서를 검색하는 Java 함수를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 경로 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정된 데이터베이스 및 컬렉션에서 문서를 검색한 후 ```Optional<String>```으로 반환하는 데 사용됩니다.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{id}")
            HttpRequestMessage<Optional<String>> request,        
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{id}",
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            Optional<String> item,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string 
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>HTTP 트리거, 경로 데이터에서 ID 조회, SqlQuery 사용(Java)

다음 예제에서는 단일 문서를 검색하는 Java 함수를 보여줍니다. 함수는 조회할 ID를 지정하기 위해 경로 문자열을 사용하는 HTTP 요청에 의해 트리거됩니다. ID는 지정한 데이터베이스 및 컬렉션에서 문서를 검색한 후, 쿼리 조건에 따라 많은 문서가 반환될 수 있으므로 결과 세트를 ```ToDoItem[]```으로 변환하는 데 사용됩니다.

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}") 
            HttpRequestMessage<Optional<String>> request,        
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            ToDoItem[] item,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP 트리거, SqlQuery를 사용하여 경로 데이터에서 여러 문서 가져오기(Java)

다음 예제에서는 여러 문서를 검색하는 Java 함수를 보여줍니다. 함수는 ```description``` 필드에서 검색할 문자열을 지정하기 위해 경로 문자열 ```desc```를 사용하는 HTTP 요청에 의해 트리거됩니다. 검색 용어는 지정한 데이터베이스 및 컬렉션에서 문서 컬렉션을 검색한 후 결과 세트를 ```ToDoItem[]```으로 변환하고 함수에 인수로 전달하는 데 사용됩니다.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,        
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            ToDoItem[] items,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        } 
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

## <a name="input---attributes"></a>입력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [다음 구성 섹션](#input---configuration)을 참조하세요.

## <a name="input---configuration"></a>입력 - 구성

다음 표에서는 *function.json* 파일 및 `CosmosDB` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type**     || `cosmosDB`로 설정해야 합니다.        |
|**direction**     || `in`로 설정해야 합니다.         |
|**name**     || 함수에서 문서를 나타내는 바인딩 매개 변수의 이름입니다.  |
|**databaseName** |**DatabaseName** |문서를 포함하는 데이터베이스입니다.        |
|**collectionName** |**CollectionName** | 문서를 포함하는 컬렉션의 이름입니다. |
|**id**    | **Id** | 검색할 문서의 ID입니다. 이 속성은 [바인딩 식](./functions-bindings-expressions-patterns.md)을 지원합니다. **id** 및 **sqlQuery** 속성을 둘 다 설정하지 마십시오. 둘 중 하나를 설정하지 않으면 전체 컬렉션이 검색됩니다. |
|**sqlQuery**  |**SqlQuery**  | 여러 문서를 검색하는 데 사용되는 Azure Cosmos DB SQL 쿼리입니다. 이 속성은 런타임 바인딩을 지원합니다(예: `SELECT * FROM c where c.departmentId = {departmentId}`). **id** 및 **sqlQuery** 속성을 둘 다 설정하지 마십시오. 둘 중 하나를 설정하지 않으면 전체 컬렉션이 검색됩니다.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Azure Cosmos DB 연결 문자열을 포함하는 앱 설정의 이름입니다.        |
|**partitionKey**|**PartitionKey**|조회를 위한 파티션 키 값을 지정합니다. 바인딩 매개 변수가 포함될 수 있습니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>입력 - 사용

C# 및 F# 함수에서 함수가 성공적으로 종료되면 명명된 입력 매개 변수를 통해 입력 문서에 변경한 내용이 자동으로 유지됩니다.

JavaScript 함수에서는 함수 종료 시 자동으로 업데이트되지 않습니다. 대신 `context.bindings.<documentName>In` 및 `context.bindings.<documentName>Out`을 사용하여 업데이트합니다. JavaScript 예제를 참조하세요.

## <a name="output"></a>출력

Azure Cosmos DB 출력 바인딩을 사용하면 Azure Cosmos DB 데이터베이스에 SQL API를 사용하여 새 문서를 작성할 수 있습니다.

## <a name="output---examples"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [C#](#output---c-examples)
* [C# 스크립트(.csx)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)

`DocumentClient`을 사용하는 [입력 예제](#input---c-examples)도 참조하세요.

[출력 건너뛰기 예제](#output---attributes)

### <a name="output---c-examples"></a>출력 - C# 예제

이 섹션에는 다음 예제가 포함되어 있습니다.

* 큐 트리거, 하나의 문서 쓰기
* 큐 트리거, IAsyncCollector를 사용하여 문서 쓰기

예제에서는 간단한 `ToDoItem` 형식을 참조하세요.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[출력 건너뛰기 예제](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>큐 트리거, 하나의 문서 쓰기(C#)

다음 예제에서는 Queue storage의 메시지에 제공된 데이터를 사용하여 문서를 데이터베이스에 추가하는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

[출력 건너뛰기 예제](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>큐 트리거, IAsyncCollector를 사용하여 문서 쓰기(C#)

다음 예제에서는 큐 메시지 JSON에서 제공된 데이터를 사용하여 문서의 컬렉션을 데이터베이스에 추가하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[출력 건너뛰기 예제](#output---attributes)

### <a name="output---c-script-examples"></a>출력 - C# 스크립트 예제

이 섹션에는 다음 예제가 포함되어 있습니다.

* 큐 트리거, 하나의 문서 쓰기
* 큐 트리거, IAsyncCollector를 사용하여 문서 쓰기

[출력 건너뛰기 예제](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>큐 트리거, 하나의 문서 쓰기(C# 스크립트)

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 출력 바인딩 및 해당 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 이 함수는 다음 형식으로 JSON을 수신하는 큐에 대한 큐 입력 바인딩을 사용합니다.

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

이 함수는 각 레코드에 대해 다음과 같은 형식의 Azure Cosmos DB 문서를 만듭니다.

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>큐 트리거, IAsyncCollector를 사용하여 문서 쓰기

여러 문서를 만들려면 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.

이 예제에서는 간단한 `ToDoItem` 형식을 참조합니다.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

function.json 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[출력 건너뛰기 예제](#output---attributes)

### <a name="output---javascript-examples"></a>출력 - JavaScript 예제

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 출력 바인딩 및 해당 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 이 함수는 다음 형식으로 JSON을 수신하는 큐에 대한 큐 입력 바인딩을 사용합니다.

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

이 함수는 각 레코드에 대해 다음과 같은 형식의 Azure Cosmos DB 문서를 만듭니다.

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

[출력 건너뛰기 예제](#output---attributes)

### <a name="output---f-examples"></a>출력 - F# 예제

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 출력 바인딩 및 해당 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여 줍니다. 이 함수는 다음 형식으로 JSON을 수신하는 큐에 대한 큐 입력 바인딩을 사용합니다.

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

이 함수는 각 레코드에 대해 다음과 같은 형식의 Azure Cosmos DB 문서를 만듭니다.

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

F# 코드는 다음과 같습니다.

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

이 예제에는 `FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일이 필요합니다.

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

`project.json` 파일을 추가하려면 [F# 패키지 관리](functions-reference-fsharp.md#package)를 참조하세요.

### <a name="output---java-examples"></a>출력 - Java 예제

* [큐 트리거, 반환 값을 통해 데이터베이스에 메시지 저장](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP 트리거, 반환 값을 통해 데이터베이스에 단일 문서 저장](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP 트리거, OutputBinding을 통해 데이터베이스에 단일 문서 저장](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP 트리거, OutputBinding을 통해 데이터베이스에 여러 문서 저장](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>큐 트리거, 반환 값을 통해 데이터베이스에 메시지 저장(Java)

다음 예제에서는 Queue Storage의 메시지에서 데이터를 사용하여 문서를 데이터베이스에 추가하는 Java 함수를 보여줍니다.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database", 
  databaseName = "ToDoList", 
  collectionName = "Items", 
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg", 
      queueName = "myqueue-items", 
      connection = "AzureWebJobsStorage") 
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>HTTP 트리거, 반환 값을 통해 데이터베이스에 단일 문서 저장(Java)

다음 예제에서는 시그니처에 ```@CosmosDBOutput```이 주석으로 지정되고 ```String``` 형식의 반환 값을 갖는 Java 함수를 보여줍니다. 함수에서 반환된 JSON 문서는 해당 CosmosDB 컬렉션에 자동으로 기록됩니다.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database", 
      databaseName = "ToDoList",
      collectionName = "Items", 
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS) 
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter        
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>HTTP 트리거, OutputBinding을 통해 데이터베이스에 단일 문서 저장(Java)

다음 예제에서는 ```OutputBinding<T>``` 출력 매개 변수를 통해 CosmosDB에 문서를 쓰는 Java 함수를 보여줍니다. 이 설정에서는 함수 시그니처가 아니라 ```@CosmosDBOutput```을 주석으로 지정해야 하는 ```outputItem``` 매개 변수입니다. ```OutputBinding<T>```을 사용하면 JSON 또는 XML 문서와 같은 함수 호출자에 다른 값을 반환하도록 허용하면서 함수가 바인딩을 활용하여 CosmosDB에 문서를 쓸 수 있습니다.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS) 
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database", 
              databaseName = "ToDoList", 
              collectionName = "Items", 
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            OutputBinding<String> outputItem,
            final ExecutionContext context) {
  
        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
      
        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>HTTP 트리거, OutputBinding을 통해 데이터베이스에 여러 문서 저장(Java)

다음 예제에서는 ```OutputBinding<T>``` 출력 매개 변수를 통해 CosmosDB에 여러 문서를 쓰는 Java 함수를 보여줍니다. 이 설정에서는 함수 시그니처가 아니라 ```@CosmosDBOutput```을 주석으로 지정해야 하는 ```outputItem``` 매개 변수입니다. 출력 매개 변수 ```outputItem```에는 ```ToDoItem``` 개체 목록이 템플릿 매개 변수 형식으로 포함되어 있습니다. ```OutputBinding<T>```을 사용하면 JSON 또는 XML 문서와 같은 함수 호출자에 다른 값을 반환하도록 허용하면서 함수가 바인딩을 활용하여 CosmosDB에 문서를 쓸 수 있습니다.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET, HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS) 
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database", 
              databaseName = "ToDoList", 
              collectionName = "Items", 
              connectionStringSetting = "Cosmos_DB_Connection_String") 
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {
  
        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
      
        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);
          
          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 Cosmos DB에 작성될 매개 변수에 대한 `@CosmosDBOutput` 주석을 사용합니다.  주석 매개 변수 형식은 ```OutputBinding<T>```이어야 합니다. 여기서 T는 원시 Java 형식 또는 POJO입니다.


## <a name="output---attributes"></a>출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [출력 - 구성](#output---configuration)을 참조하세요. 다음은 메서드 서명의 `CosmosDB` 특성 예제입니다.

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

전체 예제는 출력 - C# 예제를 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `CosmosDB` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type**     || `cosmosDB`로 설정해야 합니다.        |
|**direction**     || `out`로 설정해야 합니다.         |
|**name**     || 함수에서 문서를 나타내는 바인딩 매개 변수의 이름입니다.  |
|**databaseName** | **DatabaseName**|문서가 만들어진 컬렉션을 포함하는 데이터베이스입니다.     |
|**collectionName** |**CollectionName**  | 문서가 만들어진 컬렉션의 이름입니다. |
|**createIfNotExists**  |**CreateIfNotExists**    | 컬렉션이 존재하지 않는 경우 만들 수 있는지 여부를 나타내는 부울 값입니다. 새 컬렉션이 예약된 처리량으로 만들어져 비용이 부과되기 기본값은 *false*입니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.  |
|**partitionKey**|**PartitionKey** |`CreateIfNotExists`가 true이면 생성된 컬렉션의 파티션 키 경로를 정의합니다.|
|**collectionThroughput**|**CollectionThroughput**| `CreateIfNotExists`가 true이면 생성된 컬렉션의 [처리량](../cosmos-db/set-throughput.md)을 정의합니다.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Azure Cosmos DB 연결 문자열을 포함하는 앱 설정의 이름입니다.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

기본적으로 함수에서 출력 매개 변수에 쓸 경우 데이터베이스에서 문서가 생성됩니다. 이 문서에는 자동으로 생성된 GUID가 문서 ID로 지정되어 있습니다. 출력 매개 변수에 전달되는 JSON 개체에 `id` 속성을 지정하여 출력 문서의 문서 ID를 지정할 수 있습니다.

> [!Note]  
> 기존 문서의 ID를 지정하면 새 출력 문서에 의해 덮어쓰여집니다.

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| CosmosDB | [CosmosDB 오류 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```  

|자산  |Default | 설명 |
|---------|---------|---------| 
|GatewayMode|게이트웨이|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 모드입니다. 옵션은 `Direct` 및 `Gateway`입니다.|
|Protocol|Https|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 프로토콜입니다.  [두 모드에 대한 설명은 여기](../cosmos-db/performance-tips.md#networking)를 참조하세요.| 
|leasePrefix|해당 없음|앱의 모든 함수에서 사용할 접두사를 임대합니다.| 

## <a name="next-steps"></a>다음 단계

* [Cosmos DB로 서버를 사용하지 않는 데이터베이스 컴퓨팅에 대해 자세히 알아보기](../cosmos-db/serverless-computing-database.md)
* [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
