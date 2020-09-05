---
title: 함수 2.x 이상에 대 한 Azure Cosmos DB 트리거입니다.
description: Azure Functions에서 Azure Cosmos DB 트리거를 사용 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 838d6244127bc1b3609ab5e925e54dbab7fe3a2d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212695"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 이상에 대 한 Azure Cosmos DB 트리거

Azure Cosmos DB 트리거는 [Azure Cosmos DB 변경 피드](../cosmos-db/change-feed.md)를 사용하여 파티션에 대한 삽입 및 업데이트를 수신 대기합니다. 변경 피드는 삽입과 업데이트를 게시하지만 삭제는 게시하지 않습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-cosmosdb-v2.md)를 참조하세요.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 Cosmos DB 레코드가 추가 되거나 수정 될 때 로그 메시지를 기록 합니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 Cosmos DB 레코드가 추가 되거나 수정 될 때 로그 메시지를 기록 합니다.

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

이 함수는 지정 된 데이터베이스 및 컬렉션에 삽입 또는 업데이트가 있을 때 호출 됩니다.

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


[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Cosmos DB에서 제공되는 매개 변수에 대한 `@CosmosDBTrigger` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [트리거 - 구성](#configuration)을 참조하세요. 다음은 메서드 서명의 `CosmosDBTrigger` 특성 예제입니다.

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

전체 예제는 [트리거](#example)를 참조 하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 `@CosmosDBInput` Cosmos DB 데이터를 읽는 매개 변수에 대 한 주석을 사용 합니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `CosmosDBTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `cosmosDBTrigger`로 설정해야 합니다. |
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 변경 사항이 포함된 문서 목록을 나타내는 함수 코드에 사용되는 변수 이름. |
|**connectionStringSetting**|**ConnectionStringSetting** | 모니터링되는 Azure Cosmos DB 계정에 연결하는 데 사용되는 연결 문자열을 포함하고 있는 앱 설정의 이름입니다. |
|**databaseName**|**DatabaseName**  | 컬렉션이 모니터링되는 Azure Cosmos DB 데이터베이스의 이름입니다. |
|**collectionName** |**CollectionName** | 모니터링되는 컬렉션의 이름입니다. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | 필드 임대 컬렉션을 보유 하는 Azure Cosmos DB 계정에 대 한 연결 문자열을 포함 하는 앱 설정의 이름입니다. 설정하지 않으면 `connectionStringSetting` 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. 임대 컬렉션에 대한 연결 문자열에 쓰기 권한이 있어야 합니다.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션을 보유하는 데이터베이스의 이름입니다. 설정하지 않으면 `databaseName` 설정 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. |
|**leaseCollectionName** | **LeaseCollectionName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션의 이름입니다. 설정하지 않으면 `leases` 값이 사용됩니다. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (선택 사항) `true`로 설정하면 임대 컬렉션이 없는 경우 자동으로 임대 컬렉션이 생성됩니다. 기본값은 `false`입니다. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| 필드 임대 컬렉션을 만들 때 할당할 요청 단위 수를 정의 합니다. 이 설정은가로 설정 된 경우에만 사용 됩니다 `createLeaseCollectionIfNotExists` `true` . 이 매개 변수는 포털을 사용하여 바인딩이 생성될 때 자동으로 설정됩니다.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| 필드 설정 하면이 함수에 대 한 임대 컬렉션에서 만든 임대에 값이 접두사로 추가 됩니다. 접두사를 사용 하면 별도의 두 Azure Functions 서로 다른 접두사를 사용 하 여 동일한 임대 컬렉션을 공유할 수 있습니다.
|**feedPollDelay**| **FeedPollDelay**| 필드 모든 현재 변경 내용이 끝난 후에 피드의 새 변경 내용에 대 한 파티션 폴링 간의 지연 시간 (밀리초)입니다. 기본값은 5000 밀리초 또는 5 초입니다.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (선택 사항) 설정하면 파티션이 알려진 호스트 인스턴스 간에 균등하게 배포되는지를 계산하는 태스크를 시작하는 간격을 밀리초로 정의합니다. 기본값은 13000(13초)입니다.
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (선택 사항) 설정하면 파티션을 나타내는 임대에 대한 임대 기간인 간격을 밀리초로 정의합니다. 이 간격 내에서 임대를 갱신하지 않으면 기간이 만료되어 다른 인스턴스로 파티션 소유권이 이동합니다. 기본값은 60000(60초)입니다.
|**leaseRenewInterval**| **LeaseRenewInterval**| (선택 사항) 설정하면 인스턴스가 현재 보유한 파티션의 모든 임대에 대한 갱신 간격을 밀리초로 정의합니다. 기본값은 17000(17초)입니다.
|**checkpointFrequency**| **CheckpointFrequency**| (선택 사항) 설정하면 임대 검사점 간격을 밀리초로 정의합니다. 기본값은 항상 각 함수 호출 이후입니다.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| 필드 설정 되 면이 속성은 함수 호출 당 수신 되는 최대 항목 수를 설정 합니다. 모니터링 되는 컬렉션의 작업이 저장 프로시저를 통해 수행 되는 경우 변경 피드에서 항목을 읽을 때 [트랜잭션 범위가](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) 유지 됩니다. 결과적으로, 받은 항목 수가 지정 된 값 보다 높을 수 있으므로 동일한 트랜잭션으로 변경 된 항목이 하나의 원자성 일괄 처리의 일부로 반환 됩니다.
|**시작 Fromstart**| **StartFromBeginning**| 필드 이 옵션은 현재 시간에서 시작 하는 대신 컬렉션의 변경 기록 시작 부분에서 변경 내용을 읽도록 트리거에 지시 합니다. 처음부터 시작 하 여 처음부터 다시 시작 하는 경우에만 트리거가 시작 됩니다. 후속 실행과 마찬가지로 검사점은 이미 저장 되어 있습니다. 이미 생성 된 임대가 있는 경우이 옵션을로 설정 `true` 해도 아무런 효과가 없습니다. |
|**preferredLocations**| **PreferredLocations**| 필드 Azure Cosmos DB 서비스에서 지역에서 복제 된 데이터베이스 계정에 대 한 기본 위치 (지역)를 정의 합니다. 값은 쉼표로 구분 해야 합니다. 예를 들면 "미국 동부, 미국 중 북부, 유럽 유럽"입니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용량

트리거에는 파티션에 _임대_를 저장하는 데 사용할 보조 컬렉션이 필요합니다. 트리거가 작동할 수 있도록 모니터링되는 컬렉션과 임대를 포함하고 있는 컬렉션을 모두 사용할 수 있어야 합니다.

>[!IMPORTANT]
> 동일한 컬렉션에 Cosmos DB 트리거를 사용하도록 여러 함수가 구성된 경우 각 함수는 전용 임대 컬렉션을 사용하거나 각 함수에 다른 `LeaseCollectionPrefix`를 지정해야 합니다. 그러지 않으면 함수 중 하나만 트리거됩니다. 접두사에 대한 정보는 [구성 섹션](#configuration)을 참조하세요.

트리거는 문서가 업데이트 또는 삽입되었는지 여부를 나타내지 않고 문서 자체만 제공합니다. 업데이트 및 삽입을 다르게 처리해야 하는 경우 삽입 또는 업데이트에 대한 타임스탬프 필드를 구현하면 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 문서 읽기 (입력 바인딩)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB 문서에 변경 내용 저장 (출력 바인딩)](./functions-bindings-cosmosdb-v2-output.md)
