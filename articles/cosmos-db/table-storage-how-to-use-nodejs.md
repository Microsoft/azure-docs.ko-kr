---
title: Node.js에서 Azure Table 스토리지 또는 Azure Cosmos DB Table API 사용
description: Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-javascript
ms.openlocfilehash: cfcb5645a6284214e233758705537486f32967c6
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079300"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Node.js에서 Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하는 방법

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

이 문서에서는 테이블을 만들고, 데이터를 저장하고, 데이터에 대한 CRUD 작업을 수행하는 방법을 보여 줍니다. Azure Table 서비스 또는 Azure Cosmos DB Table API 중 하나를 선택합니다. 샘플은 Node.js로 작성되었습니다.

## <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure 스토리지 계정 만들기**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Table API 계정 만들기**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Azure Storage 또는 Azure Cosmos DB Table API에 액세스하도록 애플리케이션 구성

Azure Storage 또는 Azure Cosmos DB를 사용하려면 스토리지 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js용 Azure Storage SDK가 필요합니다.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치

1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 애플리케이션을 만든 폴더로 이동합니다.
2. 명령 창에 **npm install azure-storage** 를 입력합니다. 명령 출력은 다음 예제와 비슷합니다.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. **ls** 명령을 수동으로 실행하여 **node_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 스토리지에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure-storage** 패키지를 찾습니다.

### <a name="import-the-package"></a>패키지 가져오기

애플리케이션에서 아래 코드를 **server.js** 파일의 맨 위에 추가합니다.

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>연결 문자열 추가

Azure 스토리지 계정 또는 Azure Cosmos DB Table API 계정에 연결할 수 있습니다. 사용 중인 계정 유형에 따라 연결 문자열을 가져옵니다.

### <a name="add-an-azure-storage-connection"></a>Azure Storage 연결 추가

Azure 모듈은 AZURE_STORAGE_ACCOUNT 및 AZURE_STORAGE_ACCESS_KEY 또는 AZURE_STORAGE_CONNECTION_STRING 환경 변수를 읽고 Azure Storage 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되어 있지 않은 경우 `TableService`를 호출할 때 계정 정보를 지정해야 합니다. 예를 들어, 다음 코드는 `TableService` 개체를 만듭니다.

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB 연결 추가

Azure Cosmos DB 연결을 추가하려면 `TableService` 개체를 만들고 계정 이름, 기본 키 및 엔드포인트를 지정합니다. Cosmos DB 계정에 대한 Azure Portal의 **설정** > **연결 문자열**에서 이러한 값을 복사할 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>테이블 만들기

다음 코드는 `TableService` 개체를 만든 후, 이 개체를 사용하여 새 테이블을 만듭니다.

```javascript
var tableSvc = azure.createTableService();
```

`createTableIfNotExists`를 호출하면 지정된 이름을 사용하는 테이블이 없는 경우 새 테이블을 만듭니다. 다음 예제에서는 'mytable'이라는 새 테이블(없는 경우)을 만듭니다.

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

새 테이블이 만들어지면 `result.created`는 `true`가 되고, 테이블이 이미 있으면 `false`가 됩니다. `response`에는 요청에 대한 정보가 포함됩니다.

### <a name="apply-filters"></a>필터 적용

`TableService`를 사용하여 수행되는 작업에 선택적 필터링을 적용할 수 있습니다. 필터링 작업은 로깅, 자동 재시도 등을 포함할 수 있습니다. 필터는 시그니쳐가 있는 메서드를 구현하는 개체입니다.

```javascript
function handle (requestOptions, next)
```

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 **next**를 호출해야 합니다.

```javascript
function (returnObject, finalCallback, next)
```

이 콜백에서 `returnObject`(서버에 요청 응답 반환)를 처리한 후 콜백은 `next`(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 그렇지 않은 경우 `finalCallback`을 호출하여 서비스 호출을 종료해야 합니다.

재시도 논리를 구현하는 두 개의 필터는 Node.js용 Azure SDK, `ExponentialRetryPolicyFilter` 및 `LinearRetryPolicyFilter`를 포함합니다. 다음은 `ExponentialRetryPolicyFilter`를 사용하는 `TableService` 개체를 만듭니다.

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

엔터티를 추가하려면 먼저 엔터티 속성을 정의하는 개체를 만듭니다. 모든 엔터티에는 엔터티의 고유 식별자인 **PartitionKey**와 **RowKey**를 포함해야 합니다.

* **PartitionKey** - 엔터티가 저장된 파티션을 확인합니다.
* **RowKey** - 파티션에 있는 엔터티를 고유하게 식별합니다.

**PartitionKey**와 **RowKey**는 모두 문자열 값이어야 합니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.

다음은 엔터티를 정의하는 경우의 예입니다. **dueDate**는 `Edm.DateTime` 형식으로 정의됩니다. 유형 지정은 선택적이며 지정하지 않을 경우 유형이 유추됩니다.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> 각 레코드에는 엔터티가 삽입되거나 업데이트될 경우 Azure에서 설정되는 `Timestamp` 필드도 있습니다.

`entityGenerator`를 사용하여 엔터티를 만들 수도 있습니다. 다음 예에서는 `entityGenerator`를 사용하여 같은 작업 엔터티를 만듭니다.

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

테이블에 엔터티를 추가하려면 엔터티 개체를 `insertEntity` 메서드에 전달합니다.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

작업에 성공할 경우 `result` 값에는 삽입한 레코드의 [ETag](https://en.wikipedia.org/wiki/HTTP_ETag)가 포함되고 `response` 값에는 작업에 대한 정보가 포함됩니다.

예제 응답:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> 기본적으로 `insertEntity`는 삽입된 엔터티를 `response` 정보의 일부로 반환하지 않습니다. 이 엔터티에서 다른 작업을 수행할 계획이거나 정보를 캐시하고 싶은 경우 `result`의 일부로 반환하면 유용합니다. 다음과 같이 `echoContent`를 사용하도록 설정하면 됩니다.
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>엔터티 업데이트

다음과 같은 여러 메서드를 사용하여 기존 엔터티를 업데이트할 수 있습니다.

* `replaceEntity` - 기존 엔터티를 바꾸어서 업데이트합니다.
* `mergeEntity` - 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다.
* `insertOrReplaceEntity` - 기존 엔터티를 바꾸어서 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.
* `insertOrMergeEntity` - 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

다음 예제에서는 `replaceEntity`를 사용하여 엔터티를 업데이트하는 방법을 보여 줍니다.

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> 기본적으로는 엔터티를 업데이트할 때 업데이트할 데이터를 다른 프로세서에서 이전에 수정했는지 확인하지 않습니다. 동시 업데이트를 지원하려면:
>
> 1. 업데이트할 개체의 ETag를 가져옵니다. 모든 엔터티 관련 작업에서의 `response`의 일부로 반환되며 `response['.metadata'].etag`를 통해 검색할 수 있습니다.
> 2. 엔터티에서 업데이트 작업을 수행할 때 이전에 검색한 ETag 정보를 새 엔터티에 추가합니다. 예를 들면 다음과 같습니다.
>
>       entity2['.metadata'].etag = currentEtag;
> 3. 업데이트 작업을 수행합니다. ETag 값을 검색한 후에 애플리케이션의 다른 인스턴스 등에서 엔터티가 수정된 경우에는 요청에 지정된 업데이트 조건이 충족되지 않았다는 내용의 `error`가 반환됩니다.
>
>

`replaceEntity` 및 `mergeEntity`를 사용할 경우 업데이트할 엔터티가 존재하지 않으면 업데이트 작업이 실패하므로 존재 여부에 관계없이 엔터티를 저장하려는 경우 `insertOrReplaceEntity` 또는 `insertOrMergeEntity`를 사용합니다.

업데이트 작업이 성공할 경우 `result` 값에는 업데이트된 엔터티의 **Etag** 가 포함됩니다.

## <a name="work-with-groups-of-entities"></a>엔터티 그룹 작업

서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 이렇게 하려면 `TableBatch` 클래스를 사용하여 배치를 만든 다음, `TableService`의 `executeBatch` 메서드를 사용하여 일괄 처리 작업을 수행합니다.

 다음 예제에서는 두 엔터티를 일괄적으로 제출하는 방법을 보여 줍니다.

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

일괄 처리 작업에 성공할 경우 `result` 값에는 일괄 처리의 각 작업에 대한 정보가 포함됩니다.

### <a name="work-with-batched-operations"></a>일괄 처리 작업

`operations` 속성을 확인하여 일괄 처리에 추가된 작업을 검사할 수 있습니다. 작업에 다음 메서드를 사용할 수도 있습니다.

* **clear** - 일괄 처리에서 모든 작업을 지웁니다.
* **getOperations** - 일괄 처리에서 작업을 가져옵니다.
* **hasOperations** - 일괄 처리에 작업이 포함되어 있으면 true를 반환합니다.
* **removeOperations** - 작업을 제거합니다.
* **size** - 배치에 있는 작업의 수를 반환합니다.

## <a name="retrieve-an-entity-by-key"></a>키를 통해 엔터티 검색

**PartitionKey** 및 **RowKey**를 기준으로 특정 엔터티를 반환하려면 **retrieveEntity** 메서드를 사용합니다.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

이 작업이 완료되면 `result`에 엔터티가 포함됩니다.

## <a name="query-a-set-of-entities"></a>엔터티 집합 쿼리

테이블을 쿼리하려면 **TableQuery** 개체와 다음 절을 사용하여 쿼리 식을 구성합니다.

* **select** - 쿼리에서 반환할 필드입니다.
* **where** - where 절입니다.

  * **and** - `and` where 조건입니다.
  * **or** - `or` where 조건입니다.
* **top** - 가져올 항목의 수입니다.

다음 예에서는 PartitionKey가 'hometasks'인 상위 5개의 항목을 반환하는 쿼리를 구성합니다.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

**select**가 사용되지 않기 때문에 모든 필드가 반환됩니다. 테이블에 대해 쿼리를 수행하려면 **queryEntities**를 사용합니다. 다음 예에서는 이 쿼리를 사용하여 'mytable'에서 엔터티를 반환합니다.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

성공할 경우에는 `result.entries` 값에는 쿼리와 일치하는 엔터티의 배열이 포함됩니다. 쿼리에서 엔터티를 모두 반환할 수 없는 경우 `result.continuationToken`이*null*이 아닌 값이 되므로 **queryEntities**의 세 번째 매개 변수로 사용하여 더 많은 결과를 검색할 수 있습니다. 초기 쿼리의 경우 세 번째 매개 변수에 *null* 을 사용합니다.

### <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리

테이블 쿼리에서는 엔터티에서 일부 필드만 검색할 수 있습니다.
따라서 대역폭이 줄어들며 특히 큰 엔터티의 경우 쿼리 성능이 향상될 수 있습니다. **select** 절을 사용하여 반환할 필드의 이름을 전달합니다. 예를 들어, 다음 쿼리에서는 **description** 및 **dueDate** 필드만 반환합니다.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>엔터티 삭제

파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다. 이 예제에서 **task1** 개체는 삭제할 엔터티의 **RowKey** 및 **PartitionKey** 값을 포함합니다. 그런 다음 개체는 **deleteEntity** 메서드에 전달됩니다.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> 항목을 삭제할 때 항목이 다른 프로세스에서 수정되지 않았는지 확인할 수 있도록 ETag를 사용하는 것이 좋습니다. ETag 사용 방법에 대한 자세한 내용은 [엔터티 업데이트](#update-an-entity) 를 참조하세요.
>
>

## <a name="delete-a-table"></a>테이블 삭제

다음 코드는 스토리지 계정에서 테이블을 삭제합니다.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

테이블이 있는지 확실하지 않은 경우에는 **deleteTableIfExists**를 사용합니다.

## <a name="use-continuation-tokens"></a>연속토큰 사용

많은 양의 결과를 얻기 위해 테이블을 쿼리하는 경우 연속 토큰을 찾습니다. 당신이 인식하지 못한 쿼리에 대한 사용 가능한 많은 양의 데이터가 있을 경우 연속토큰의 유무를 인식하지 못하도록 작성하지마세요.

연속 토큰이 있을 경우 `continuationToken` 속성을 설정하는 엔터티를 쿼리하는 동안 **결과** 개체가 반환됩니다. 그 다음 파티션 및 테이블 엔터티 간에 이동을 계속하기 위해 쿼리를 수행 할 경우 이것을 사용할 수 있습니다.

쿼리할 때, 쿼리개체 인스턴스와 콜백 함수 간에 `continuationToken` 매개 변수를 제공할 수 있습니다.

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

`continuationToken` 개체를 조사하면, 모든 결과를 반복하는 데 사용할 수 있는 `nextPartitionKey`, `nextRowKey` 및 `targetLocation`과 같은 속성을 찾을 수 있습니다.

`top`을 `continuationToken`과 함께 사용하여 페이지 크기를 설정할 수도 있습니다.

## <a name="work-with-shared-access-signatures"></a>공유 액세스 서명 작업

SAS(공유 액세스 서명)는 스토리지 계정 이름이나 키를 제공하지 않으면서 테이블에 세분화된 액세스 권한을 안전하게 제공하는 방법입니다. SAS는 모바일 앱에서 레코드를 쿼리하는 경우와 같이 데이터에 대해 제한된 액세스를 제공하는 경우에 자주 사용합니다.

클라우드 기반 서비스와 같이 신뢰할 수 있는 애플리케이션에서는 **TableService**의 **generateSharedAccessSignature**를 사용하여 SAS를 생성하고, 이를 모바일 앱과 같은 신뢰할 수 없거나 신뢰가 약한 애플리케이션에 제공합니다. SAS는 SAS가 유효한 시작 및 종료 날짜와 SAS 소유자에게 부여되는 액세스 수준을 설명하는 정책을 사용하여 생성됩니다.

다음 예에서는 SAS 소유자가 테이블을 쿼리('r')할 수 있도록 허용하며 만든 후 100분이 지나면 만료되는 새 공유 액세스 정책을 생성합니다.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

SAS 소유자가 테이블에 액세스할 때 필요하므로 호스트 정보도 제공해야 합니다.

그러고 나면 클라이언트 애플리케이션에서 **TableServiceWithSAS** 에 SAS를 사용하여 테이블에 대한 작업을 수행합니다. 다음 예에서는 테이블을 연결하고 쿼리를 수행합니다. tableSAS 형식은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../storage/common/storage-sas-overview.md) 문서를 참조하세요.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

SAS가 쿼리 액세스만으로 생성되었기 때문에 엔터티를 삽입, 업데이트 또는 삭제하려고 하면 오류가 반환됩니다.

### <a name="access-control-lists"></a>Access Control 목록

ACL(Access Control 목록)을 사용하여 SAS에 액세스 정책을 설정할 수도 있습니다. 이 방법은 여러 클라이언트에서 테이블에 액세스하게 하면서 각 클라이언트에 서로 다른 액세스 정책을 제공하려는 경우에 유용합니다.

ACL은 각 정책에 ID가 연결된 액세스 정책 배열을 사용하여 구현됩니다. 다음 예에서는 'user1'와 'user2'에 대해 하나씩, 두 개의 정책을 정의합니다.

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

다음 예제에서는 **hometasks** 테이블의 현재 ACL을 가져온 다음 **setTableAcl**을 사용하여 새 정책을 추가합니다. 이 접근 방식을 통해 다음을 수행할 수 있습니다.

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL이 설정된 후에 정책의 ID를 기반으로 SAS를 만들 수 있습니다. 다음 예에서는 'user2'에 대해 새 SAS를 만듭니다.

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* GitHub의 [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) 리포지토리
* [Node.js 개발자용 Azure](https://docs.microsoft.com/azure/developer/javascript/)
* [Azure에서 Node.js 웹앱 만들기](../app-service/quickstart-nodejs.md)
* [Node.js 애플리케이션 빌드 및 Azure 클라우드 서비스에 배포](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Windows PowerShell 사용)
