---
title: Java에서 Azure Table 스토리지 또는 Azure Cosmos DB Table API 사용
description: Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-java
ms.openlocfilehash: e28770bae9f845ae8f5edd3b67bc55175392052a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056672"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Java에서 Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하는 방법

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

이 문서에서는 테이블을 만들고, 데이터를 저장하고, 데이터에 대한 CRUD 작업을 수행하는 방법을 보여줍니다. Azure Table service 또는 Azure Cosmos DB Table API 중 하나를 선택합니다. 샘플은 Java로 작성되었으며 [Java용 Azure Storage SDK][Azure Storage SDK for Java](영문)를 사용합니다. 여기에서 다루는 시나리오에는 **creating**, **listing**, **deleting** 테이블과 테이블의 **inserting**, **querying**, **modifying**, **deleting** 엔터티가 포함됩니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

> [!NOTE]
> SDK는 Android 디바이스에서 Azure Storage를 사용하는 개발자에게 제공됩니다. 자세한 내용은 [Android용 Azure Storage SDK][Azure Storage SDK for Android]를 참조하세요.
>

## <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure 스토리지 계정 만들기**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB 계정 만들기**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

이 가이드에서는 Java 애플리케이션에서 로컬로 실행할 수 있거나 Azure에서 웹 역할 또는 작업자 역할로 실행되는 코드에서 실행할 수 있는 스토리지 기능을 사용합니다.

이 문서의 샘플을 사용하려면 JDK(Java Development Kit)를 설치하고 Azure 구독에서 Azure Storage 계정 또는 Azure Cosmos DB 계정을 만듭니다. 그런 다음, 개발 시스템에서 GitHub의 [Java용 Azure Storage SDK][Azure Storage SDK for Java] 리포지토리에 나열된 최소 요구 사항과 종속성을 충족하는지 확인합니다. 시스템에서 해당 요구 사항을 충족하는 경우에는 리포지토리에서 시스템의 Java용 Azure Storage Library를 다운로드 및 설치하기 위한 지침을 따를 수 있습니다. 작업을 완료하고 나면 이 문서의 예제를 사용하는 Java 애플리케이션을 만들 수 있습니다.

## <a name="configure-your-application-to-access-table-storage"></a>테이블 스토리지에 액세스하도록 애플리케이션 구성

Azure Storage API 또는 Azure Cosmos DB Table API를 사용하여 테이블에 액세스하려는 경우 Java 파일의 맨 위에 다음 import 문을 추가합니다.

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-your-connection-string"></a>연결 문자열 추가

Azure 스토리지 계정 또는 Azure Cosmos DB Table API 계정에 연결할 수 있습니다. 사용 중인 계정 유형에 따라 연결 문자열을 가져옵니다.

### <a name="add-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 추가

Azure Storage 클라이언트는 스토리지 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 저장합니다. 클라이언트 애플리케이션에서 실행할 경우 **AccountName**과 **AccountKey** 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 스토리지 계정의 이름과 기본 선택키를 사용하여 다음 형식의 스토리지 연결 문자열을 제공해야 합니다. 

이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Azure Cosmos DB Table API 연결 문자열 추가

Azure Cosmos DB 계정은 연결 문자열을 사용하여 테이블 엔드포인트와 자격 증명을 저장합니다. 클라이언트 애플리케이션에서 실행할 경우, **AccountName**과 **AccountKey** 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 Azure Cosmos DB 계정의 이름과 기본 선택키를 사용하여 다음 형식의 Azure Cosmos DB 연결 문자열을 제공해야 합니다. 

이 예제에서는 고정 필드가 Azure Cosmos DB 연결 문자열을 보유하도록 선언하는 방법을 보여줍니다.

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Azure의 역할 내에서 실행되는 애플리케이션에서는 이 문자열을 서비스 구성 파일 *ServiceConfiguration.cscfg*에 저장할 수 있고, **RoleEnvironment.getConfigurationSettings** 메서드 호출을 통해 이 문자열에 액세스할 수 있습니다. 다음은 서비스 구성 파일에서 이름이 **StorageConnectionString** 인 *설정* 요소에서 연결 문자열을 가져오는 예제입니다.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

프로젝트의 config.properties 파일에 연결 문자열을 저장할 수도 있습니다.

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

다음 샘플에서는 스토리지 연결 문자열을 가져오기 위해 이러한 메서드 중 하나를 사용한 것으로 가정합니다.

## <a name="create-a-table"></a>테이블 만들기

`CloudTableClient` 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 `CloudTableClient` 개체를 만들고 이 개체를 사용하여 새 `CloudTable` 개체를 만듭니다. 이 개체는 “people”이라는 테이블을 나타냅니다. 

> [!NOTE]
> `CloudStorageAccount` 개체를 만드는 다른 방법이 있습니다. 자세한 내용은 [Azure Storage 클라이언트 SDK 참조]에서 `CloudStorageAccount`를 참조하세요.
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>테이블 나열

테이블의 목록을 가져오려면 **CloudTableClient.listTables()** 메서드를 호출하여 반복 가능한 테이블 이름 목록을 검색합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

엔터티는 `TableEntity`를 구현하는 사용자 지정 클래스를 사용하여 Java 개체에 매핑됩니다. 사용 편의를 위해 `TableServiceEntity` 클래스는 `TableEntity`를 구현하고, 리플렉션을 사용하여 속성에 맞춰 명명된 getter 및 setter 메서드에 속성을 매핑합니다. 테이블에 엔터티를 추가하려면 먼저 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있습니다.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

엔터티 관련 테이블 작업에는 `TableOperation` 개체가 필요합니다. 이 개체는 엔터티에서 수행될 작업을 정의하고, 이 작업은 `CloudTable` 개체를 사용하여 실행될 수 있습니다. 다음 코드는 저장할 고객 데이터를 사용하여 `CustomerEntity` 클래스의 새 인스턴스를 만듭니다. 그런 다음, `TableOperation`.insertOrReplace**를 호출하여 `TableOperation` 개체를 만들어서 엔터티를 테이블에 삽입하고, 새 `CustomerEntity`를 테이블과 연결합니다. 끝으로 이 코드는 `CloudTable` 개체에서 `execute` 메서드를 호출하여, 'people' 테이블 및 새로운 `TableOperation`을 지정한 후, 새 고객 엔터티를 'people' 테이블에 삽입하거나, 엔터티가 이미 있는 경우 바꾸라는 요청을 스토리지 서비스로 보냅니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입

하나의 쓰기 작업으로 테이블 서비스에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 `TableBatchOperation` 개체를 만든 다음, 이 개체에 3개의 삽입 작업을 추가합니다. 각 삽입 작업을 추가하기 위해 새 엔터티 개체를 만들고 값을 설정한 다음, `TableBatchOperation` 개체에서 `insert` 메서드를 호출하여 엔터티를 새로운 삽입 작업과 연결합니다. 그런 다음, 이 코드는 `CloudTable` 개체에 대해 `execute`를 호출하여 'people' 테이블 및 `TableBatchOperation` 개체를 지정한 후, 테이블 일괄 작업을 단일 요청으로 스토리지 서비스로 보냅니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

일괄 작업에 대해 유의할 사항은 다음과 같습니다.

* 최대 100개의 삽입, 삭제, 병합, 바꾸기, 삽입 또는 병합 및 삽입 또는 바꾸기 작업을 임의로 조합하여 단일 일괄 작업으로 수행할 수 있습니다.
* 검색 작업이 일괄 작업의 유일한 작업이면 일괄 작업에 검색 작업이 포함될 수 있습니다.
* 단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
* 일괄 작업은 4MB 데이터 페이로드로 제한됩니다.

## <a name="retrieve-all-entities-in-a-partition"></a>파티션의 모든 엔터티 검색

테이블에서 파티션의 엔터티를 쿼리하려는 경우 `TableQuery`를 사용할 수 있습니다. 지정된 결과 유형을 반환하는 쿼리를 특정 테이블에 대해 만들려면 `TableQuery.from`을 호출합니다. 다음 코드는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. `TableQuery.generateFilterCondition`은 쿼리에 대한 필터를 만드는 도우미 메서드입니다. 쿼리에 필터를 적용하려면 `TableQuery.from` 메서드가 반환한 참조에서 `where`를 호출합니다. `CloudTable` 개체에서 `execute`를 호출하여 쿼리를 실행하면 `CustomerEntity` 결과 유형이 지정된 `Iterator`가 반환됩니다. 그러면 "ForEach" 루프에서 반환된 `Iterator`를 사용하여 결과를 사용할 수 있습니다. 이 코드는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>파티션의 엔터티 범위 검색

파티션의 모든 엔터티를 쿼리하지 않으려면 비교 연산자를 필터에서 사용하여 범위를 지정할 수 있습니다. 다음 코드는 두 개의 필터를 결합하여 행 키(이름)가 알파벳에서 'E'까지의 문자로 시작하는 'Smith' 파티션의 모든 엔터티를 가져옵니다. 그런 다음 쿼리 결과를 출력합니다. 이 가이드의 일괄 삽입 섹션에 있는 테이블에 추가된 엔터티를 사용할 경우 두 개의 엔터티(Ben 및 Denise Smith)만 반환되고 Jeff Smith는 반환되지 않습니다.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>단일 엔터티 검색

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드는 `Table Query`를 만들고 필터를 사용하여 작업을 수행하지 않고, 파티션 키 및 행 키 매개 변수를 사용하여 `TableOperation.retrieve`를 호출하여 고객 'Jeff Smith'를 지정합니다. 코드가 실행되면 검색 작업은 컬렉션 대신 엔터티 1개만 반환합니다. `getResultAsType` 메서드는 할당 대상 형식인 `CustomerEntity` 개체로 결과를 캐스트합니다. 이 형식이 쿼리에 지정된 형식과 호환되지 않으면 예외가 발생합니다. 파티션과 행 키가 정확하게 일치하는 엔터티가 없는 경우 null 값이 반환됩니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 Table service에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>엔티티 수정

엔터티를 수정하려면 테이블 서비스에서 엔터티를 검색하고 엔터티 개체를 변경한 후, 바꾸기 또는 병합 작업으로 변경 사항을 테이블 서비스에 다시 저장하세요. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 삽입할 때처럼 **TableOperation.insert**를 호출하는 대신, 이 코드는 **TableOperation.replace**를 호출합니다. 이 애플리케이션에서 엔터티를 검색한 이후에 다른 애플리케이션에서 엔터티를 변경하지 않은 경우 **CloudTable.execute** 메서드가 테이블 서비스를 호출하고 엔터티는 교체됩니다. 다른 응용 프로그램에서 엔터티를 변경한 경우에는 예외가 발생하고 엔터티를 다시 검색하고 수정한 다음 저장해야 합니다. 이 낙관적 동시성 다시 시도 패턴은 분산된 스토리지 시스템에서 일반적으로 발생합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 다음 코드의 쿼리는 `select` 메서드를 사용하여 테이블에 있는 엔터티의 이메일 주소만 반환합니다. 이 결과는 서버에서 반환된 엔터티에 대해 형식 변환을 수행하는 `Entity Resolver`의 도움으로 `String` 컬렉션에 프로젝션됩니다. [Azure 테이블: Upsert 및 쿼리 프로젝션 소개][Azure 테이블: Upsert 및 쿼리 프로젝션 소개]에서 삽입 또는 바꾸기 및 삽입 또는 병합에 대해 자세히 알아볼 수 있습니다. 로컬 스토리지 에뮬레이터에서는 프로젝션이 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>엔터티 삽입 또는 바꾸기

엔터티가 테이블에 이미 있는지 모르는 상태에서 테이블에 엔터티를 추가할 수 있습니다. 이 경우 삽입 또는 바꾸기 작업을 사용하여 엔터티가 없는 경우 엔터티를 삽입하고 엔터티가 있는 경우 기존 엔터티를 바꾸도록 하는 단일 요청을 생성할 수 있습니다. 이전의 예를 기반으로 하는 다음 코드에서는 'Walter Harp'에 대한 엔터티를 삽입하거나 바꿉니다. 이 코드에서는 새 엔터티를 만든 후 **TableOperation.insertOrReplace** 메서드를 호출합니다. 그런 다음, 이 코드는 테이블 및 테이블 삽입 또는 바꾸기 작업을 매개 변수로 하여 **Cloud Table** 개체에 대해 **execute**를 호출합니다. 엔터티의 일부만 업데이트하려면 **TableOperation.insertOrMerge** 메서드를 대신 사용할 수 있습니다. 로컬 스토리지 에뮬레이터에서는 삽입 또는 바꾸기가 지원되지 않으므로 이 코드는 테이블 서비스의 계정을 사용하는 경우에만 실행됩니다. 이 [Azure 테이블: Upsert 및 쿼리 프로젝션 소개][Azure 테이블: Upsert 및 쿼리 프로젝션 소개]에서 삽입 또는 바꾸기 및 삽입 또는 병합에 대해 자세히 알아볼 수 있습니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>엔터티 삭제

엔터티를 검색한 다음 쉽게 삭제할 수 있습니다. 엔터티를 검색한 후, 삭제할 엔터티로 `TableOperation.delete`를 호출합니다. 그런 다음, `CloudTable` 개체에서 `execute`를 호출합니다. 다음 코드는 고객 엔터티를 검색하고 삭제합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>테이블 삭제

마지막으로, 다음 코드는 스토리지 계정에서 테이블을 삭제합니다. 테이블을 삭제한 후 약 40초 동안은 다시 만들 수 없습니다. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>다음 단계

* [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* [Java용 Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage 클라이언트 SDK 참조][Azure Storage Client SDK Reference]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage 팀 블로그][Azure Storage Team Blog]

자세한 내용은 [Java 개발자용 Azure](/java/azure)를 방문하세요.

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage 클라이언트 SDK 참조]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/
