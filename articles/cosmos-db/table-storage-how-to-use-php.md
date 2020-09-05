---
title: PHP에서 Azure Storage Table Service 또는 Azure Cosmos DB Table API 사용
description: Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: c690002141c6c275d90d5bae41318f9a5907fb85
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236438"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>PHP에서 Azure Storage Table 서비스 또는 Azure Cosmos DB Table API를 사용하는 방법

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

이 문서에서는 테이블을 만들고, 데이터를 저장하고, 데이터에 대한 CRUD 작업을 수행하는 방법을 보여줍니다. Azure Table 서비스 또는 Azure Cosmos DB Table API 중 하나를 선택합니다. 샘플은 PHP로 작성되었으며 [Azure Storage Table PHP 클라이언트 라이브러리][download]를 사용합니다. 여기에서 다루는 시나리오에는 **테이블 만들기 및 삭제** 및 **테이블에서 엔터티 삽입, 삭제 및 쿼리**가 포함됩니다. Azure Table service에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure 스토리지 계정 만들기**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Table API 계정 만들기**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>PHP 애플리케이션 만들기

Storage Table service 또는 Azure Cosmos DB Table API에 액세스하는 PHP 애플리케이션을 만들기 위한 유일한 요구 사항은 코드 내에서 PHP에 대한 azure-storage-table SDK의 클래스를 참조하는 것입니다. 애플리케이션을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 애플리케이션 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 Storage Table service 또는 Azure Cosmos DB 기능을 사용합니다.

## <a name="get-the-client-library"></a>클라이언트 라이브러리 가져오기

1. 프로젝트 루트에 composer.json이라는 파일을 만들고 다음 코드를 추가합니다.
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. 루트에 [composer.phar](https://getcomposer.org/composer.phar)을 다운로드합니다. 
3. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   ```
   php composer.phar install
   ```
   또는 GitHub에서 [Azure Storage Table PHP Client Library](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)로 이동하여 소스 코드를 복제합니다.

## <a name="add-required-references"></a>필수 참조 추가

Storage Table service 또는 Azure Cosmos DB API를 사용하려면 다음을 수행해야 합니다.

* [require_once][require_once] 문을 사용하여 자동 로더 파일 참조 및
* 사용하는 모든 클래스를 참조합니다.

다음 예제에서는 자동 로더 파일을 포함하고 **TableRestProxy** 클래스를 참조하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

아래 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## <a name="add-your-connection-string"></a>연결 문자열 추가

Azure 스토리지 계정 또는 Azure Cosmos DB Table API 계정에 연결할 수 있습니다. 사용 중인 계정 유형에 따라 연결 문자열을 가져옵니다.

### <a name="add-a-storage-table-service-connection"></a>Storage Table service 연결 추가

Storage Table service 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. Storage Table service 연결 문자열 형식은 다음과 같습니다.

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Storage Emulator 연결 추가

에뮬레이터 스토리지에 액세스하려면 다음을 수행합니다.

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB 연결 추가

Azure Cosmos DB Table 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. Azure Cosmos DB 연결 문자열의 형식은 다음과 같습니다.

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Azure Table service 클라이언트 또는 Azure Cosmos DB 클라이언트를 만들려면 **TableRestProxy** 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

* 연결 문자열을 직접 전달할 수 있습니다.
* **CCM(CloudConfigurationManager)** 을 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
  * 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
  * `ConnectionStringSource` 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>테이블 만들기

**TableRestProxy** 개체를 통해 **createTable** 메서드를 사용하여 테이블을 만들 수 있습니다. 테이블을 만드는 중에 Table service 제한 시간을 설정할 수 있습니다. (Table service 제한 시간에 대한 자세한 내용은 [Table service 작업의 제한 시간 설정][table-service-timeouts]을 참조하세요.)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

테이블 이름의 제한 사항에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해][table-data-model]를 참조하세요.

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

테이블에 엔터티를 추가하려면 새 **항목** 개체를 만들어 **TableRestProxy->insertEntity**에 전달합니다. 엔터티를 만들 때 `PartitionKey` 및 `RowKey`를 지정해야 한다는 점에 주의하세요. 이 두 키는 엔터티의 고유한 식별자이며, 다른 엔터티 속성보다 훨씬 더 빠르게 쿼리할 수 있는 값입니다. 시스템은 `PartitionKey`를 사용하여 여러 스토리지 노드를 통해 테이블의 엔터티를 자동으로 분산합니다. 동일한 `PartitionKey` 의 엔터티는 동일한 노드에 저장됩니다. 동일한 노드에 저장된 여러 엔터티에 대해 작업을 수행하는 것이 여러 노드에 걸쳐 저장된 엔터티에 대해 작업을 수행하는 것보다 성능 면에서 우수합니다. `RowKey`는 파티션 내에서 엔터티의 고유한 ID입니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

테이블 속성 및 유형에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해][table-data-model]를 참조하세요.

**TableRestProxy** 클래스는 엔터티 삽입을 위해 **insertOrMergeEntity** 및 **insertOrReplaceEntity**라는 2개의 대체 메서드를 제공합니다. 이러한 메서드를 사용하려면 새 **Entity** 를 만들어 두 메서드 중 하나에 매개 변수로 전달합니다. 각 메서드는 엔터티가 없을 경우 해당 엔터티를 삽입합니다. 엔터티가 이미 있을 경우, **insertOrMergeEntity**는 속성이 이미 있으면 속성 값을 업데이트하고 속성이 없으면 새 속성을 추가하지만 **insertOrReplaceEntity**는 기존 엔터티를 완전히 바꿉니다. 다음 예제에서는 **insertOrMergeEntity**를 사용하는 방법을 보여 줍니다. `PartitionKey`"tasksSeattle”과 `RowKey`"1"인 엔터티가 아직 없으면 이 엔터티가 삽입됩니다. 그러나 위의 예제와 같이 엔터티가 이미 삽입되어 있으면 `DueDate` 속성이 업데이트되고 `Status` 속성이 추가됩니다. `Description`및`Location` 속성도 업데이트되지만 실질적으로 변경하지 않은 상태로 유지하는 값으로 업데이트됩니다. 이 2개의 속성이 예제와 같이 추가되지는 않지만 대상 엔터티에 이미 있는 경우, 두 속성의 기존 값은 변경되지 않습니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>단일 엔터티 검색

**TableRestProxy->getEntity** 메서드를 사용하면 `PartitionKey` 및 `RowKey`를 쿼리하여 단일 엔터티를 검색할 수 있습니다. 아래 예제에서는 파티션 키 `tasksSeattle` 및 행 키 `1`이 **getEntity** 메서드에 전달됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>파티션의 모든 엔터티 검색

엔터티 쿼리는 필터를 사용하여 구성됩니다(자세한 내용은 [테이블 및 엔터티 쿼리][filters] 참조). 파티션의 모든 엔터티를 검색하려면 "PartitionKey eq *partition_name*" 필터를 사용합니다. 다음 예제에서는 필터를 **queryEntities** 메서드에 전달하여 `tasksSeattle` 파티션의 모든 엔터티를 검색하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>파티션의 엔터티 하위 집합 검색

이전 예제에서 사용된 동일한 패턴을 파티션의 엔터티 하위 집합 검색에 사용할 수 있습니다. 검색되는 엔터티 하위 집합은 사용하는 필터에 의해 결정됩니다(자세한 내용은 [테이블 및 엔터티 쿼리][filters] 참조). 다음 예제에서는 필터를 사용하여 특정 `Location`과 지정한 날짜보다 작은 `DueDate`를 갖는 모든 엔터티를 검색하는 방법을 설명합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 검색

쿼리를 통해 엔터티 속성 하위 집합을 검색할 수 있습니다. *프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 검색할 속성을 지정하려면 속성 이름을 **Query->addSelectField** 메서드에 전달합니다. 이 메서드를 여러 번 호출하여 더 많은 속성을 추가할 수 있습니다. **TableRestProxy->queryEntities**를 실행한 후에 반환된 엔터티에는 선택한 속성만 있게 됩니다. 테이블 엔터티 하위 집합을 반환하려는 경우에는 위의 쿼리와 같이 필터를 사용합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>엔터티 업데이트

엔터티에 대해 **Entity->setProperty** 및 **Entity->addProperty** 메서드를 사용한 다음, **TableRestProxy->updateEntity**를 호출하면 기존 엔터티를 업데이트할 수 있습니다. 다음 예제에서는 엔터티를 검색한 후, 속성 1개를 수정하고 다른 속성을 제거하고 새 속성을 추가합니다. 참고로, 속성을 제거할 때는 속성 값을 **null**로 설정하면 됩니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>엔터티 삭제

엔터티를 삭제하려면 테이블 이름과 엔터티의 `PartitionKey` 및 `RowKey`를 **TableRestProxy->deleteEntity** 메서드에 전달합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

동시성 검사의 경우 **DeleteEntityOptions->setEtag** 메서드를 사용하고 **DeleteEntityOptions** 개체를 네 번째 매개 변수로 **deleteEntity**에 전달하여 삭제될 엔터티에 대해 Etag를 설정할 수 있습니다.

## <a name="batch-table-operations"></a>테이블 일괄 작업

**TableRestProxy->batch** 메서드를 통해 단일 요청으로 여러 작업을 실행할 수 있습니다. 여기에서 사용되는 패턴은 작업을 **BatchRequest** 개체에 추가한 후 **BatchRequest** 개체를 **TableRestProxy->batch** 메서드에 전달하는 것입니다. 작업을 **BatchRequest** 개체에 추가하려면 다음 메서드 중 하나를 여러 번 호출하면 됩니다.

* **addInsertEntity** (insertEntity 작업 추가)
* **addUpdateEntity** (updateEntity 작업 추가)
* **addMergeEntity** (mergeEntity 작업 추가)
* **addInsertOrReplaceEntity** (insertOrReplaceEntity 작업 추가)
* **addInsertOrMergeEntity** (insertOrMergeEntity 작업 추가)
* **addDeleteEntity** (deleteEntity 작업 추가)

다음 예제에서는 **insertEntity** 및 **deleteEntity** 작업을 단일 요청으로 실행하는 방법을 보여 줍니다. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

테이블 일괄 작업에 대한 자세한 내용은 [엔터티 그룹 트랜잭션 수행][entity-group-transactions]을 참조하세요.

## <a name="delete-a-table"></a>테이블 삭제

끝으로, 테이블을 삭제하려면 테이블 이름을 **TableRestProxy->deleteTable** 메서드에 전달합니다.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>다음 단계

이제 Azure Table service 및 Azure Cosmos DB의 기본 사항을 배웠으므로 다음 링크를 따라 더 자세히 알아보세요.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.

* [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
