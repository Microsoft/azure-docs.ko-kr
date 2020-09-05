---
title: C++로 Azure Table Storage 및 Azure Cosmos DB Table API 사용
description: Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 3d38fa2afe35976283e5129eab7d7f8ef3a1103b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236491"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>C++로 Azure Table Storage 또는 Azure Cosmos DB Table API를 사용하는 방법

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

이 가이드에서는 Azure Table 스토리지 서비스 또는 Azure Cosmos DB Table API를 사용하여 일반 시나리오를 보여줍니다. 샘플은 C++로 작성되었으며 [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)를 사용합니다. 이 문서에는 다음 시나리오를 다룹니다.

* 테이블 만들기 및 삭제
* 테이블 엔터티 작업

> [!NOTE]
> 이 가이드는 Azure Storage Client Library for C++ 버전 1.0.0 이상을 대상으로 합니다. 권장 버전은 Storage Client Library 2.2.0이며, [NuGet](https://www.nuget.org/packages/wastorage) 또는 [GitHub](https://github.com/Azure/azure-storage-cpp/)를 사용하여 얻을 수 있습니다.
>

## <a name="create-accounts"></a>계정 만들기

### <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API 계정 만들기

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++ 애플리케이션 만들기

이 가이드에서는 C++ 애플리케이션의 스토리지 기능을 사용합니다. 이렇게 하려면 Azure Storage Client Library for C++를 설치합니다.

Azure Storage Client Library for C++를 설치하려면 다음 방법을 사용합니다.

* **Linux:** [Azure Storage Client Library for C++ README: Linux에서 시작](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) 페이지에 제공된 지침을 따릅니다.
* **Windows:** Windows에서는 종속성 관리자로 [vcpkg](https://github.com/microsoft/vcpkg)를 사용합니다. [빠른 시작](https://github.com/microsoft/vcpkg#quick-start)에 따라 vcpkg를 초기화합니다. 그런 후, 다음 명령을 사용하여 라이브러리를 설치합니다.

```powershell
.\vcpkg.exe install azure-storage-cpp
```

소스 코드를 빌드하고 [README](https://github.com/Azure/azure-storage-cpp#download--install) 파일에서 Nuget로 내보내는 방법에 대한 가이드를 찾을 수 있습니다.

### <a name="configure-access-to-the-table-client-library"></a>테이블 클라이언트 라이브러리에 대한 액세스 구성

Azure 스토리지 API를 사용하여 테이블에 액세스하려면 C++ 파일의 맨 위에 다음 `include` 문을 추가합니다.

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage 클라이언트 또는 Cosmos DB 클라이언트는 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 저장합니다. 클라이언트 애플리케이션을 실행할 때 적절한 형식의 스토리지 연결 문자열 또는 Azure Cosmos DB 연결 문자열을 제공해야 합니다.

### <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 설정

이 예제에서는 Azure Storage 연결 문자열을 보관할 고정 필드를 선언하는 방법을 보여줍니다.  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

스토리지 계정 이름을 `<your_storage_account>`에 사용합니다. <your_storage_account_key>에는 [Azure Portal](https://portal.azure.com)에 나열된 스토리지 계정의 액세스 키를 사용합니다. 스토리지 계정 및 액세스 키에 대한 자세한 내용은 [스토리지 계정 만들기](../storage/common/storage-create-storage-account.md)를 참조하세요.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB 연결 문자열 설정

이 예제에서는 Azure Cosmos DB 연결 문자열을 보유할 고정 필드를 선언하는 방법을 보여줍니다.

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Azure Cosmos DB 계정의 이름을 `<your_cosmos_db_account>`에 사용합니다. `<your_cosmos_db_account_key>`에 기본 키를 입력합니다. [Azure Portal](https://portal.azure.com)에 나열된 엔드포인트를 `<your_cosmos_db_endpoint>`로 입력합니다.

로컬 Windows 기반 컴퓨터에서 애플리케이션을 테스트하려면 [Azure SDK](https://azure.microsoft.com/downloads/)와 함께 설치된 Azure Storage Emulator를 사용하면 됩니다. Storage Emulator는 로컬 개발 머신에서 사용할 수 있는 Azure Blob, 큐 및 Table services를 시뮬레이션하는 유틸리티입니다. 다음 예제에서는 로컬 스토리지 에뮬레이터에 대한 연결 문자열을 보관할 정적 필드를 선언하는 방법을 보여줍니다.  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure Storage Emulator를 시작하려면 Windows 데스크톱에서 **시작** 단추 또는 Windows 키를 선택합니다. *Microsoft Azure Storage 에뮬레이터*를 입력하고 실행합니다. 자세한 내용은 [개발 및 테스트에 Azure Storage Emulator 사용](../storage/common/storage-use-emulator.md)을 참조하세요.

### <a name="retrieve-your-connection-string"></a>연결 문자열 검색

`cloud_storage_account` 클래스를 사용하여 스토리지 계정 정보를 나타낼 수 있습니다. 스토리지 연결 문자열에서 스토리지 계정 정보를 검색하려면 `parse` 메서드를 사용합니다.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

다음으로, `cloud_table_client` 클래스 참조를 가져옵니다. 이 클래스를 사용하여 Table 스토리지 서비스에 저장된 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 위에서 검색한 스토리지 계정 개체를 사용하여 `cloud_table_client` 개체를 만듭니다.  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>엔터티를 만들고 테이블에 추가

### <a name="create-a-table"></a>테이블 만들기

`cloud_table_client` 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 `cloud_table_client` 개체를 만든 후, 이 개체를 사용하여 새 테이블을 만듭니다.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

테이블에 엔터티를 추가하려면 새 `table_entity` 개체를 만들어서 `table_operation::insert_entity`에 전달합니다. 다음 코드는 고객의 이름을 행키로 사용하고 고객의 성을 파티션 키로 사용합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있습니다. 다양한 파티션 키를 사용하면 병렬 작업 확장성을 높일 수 있습니다. 자세한 내용은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](../storage/common/storage-performance-checklist.md)을 참조하세요.

다음 코드에서는 저장할 고객 데이터가 포함된 새 인스턴스 `table_entity`를 만듭니다. 그런 다음, `table_operation::insert_entity`를 호출하여 `table_operation` 개체를 만들어서 엔터티를 테이블에 삽입하고, 새 테이블 엔터티와 연결합니다. 마지막으로, 이 코드는 `cloud_table` 개체에서 `execute` 메서드를 호출합니다. 새 `table_operation`은 `people` 테이블에 새 고객 엔터티를 삽입하는 Table service에 요청을 보냅니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입

하나의 쓰기 작업으로 Table service에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 `table_batch_operation` 개체를 만든 다음, 이 개체에 3개의 삽입 작업을 추가합니다. 각 삽입 작업을 추가하기 위해 새 엔터티 개체를 만들고 값을 설정한 다음, `table_batch_operation` 개체에서 `insert` 메서드를 호출하여 엔터티를 새로운 삽입 작업과 연결합니다. 그런 다음, 이 코드는 `cloud_table.execute`를 호출하여 작업을 실행합니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

일괄 작업에 대해 유의할 사항은 다음과 같습니다.

* 단일 일괄 처리의 모든 조합에서 `insert`, `delete`, `merge`, `replace`, `insert-or-merge` 및 `insert-or-replace` 작업을 100개까지 수행할 수 있습니다.  
* 검색 작업이 일괄 작업의 유일한 작업이면 일괄 작업에 검색 작업이 포함될 수 있습니다.  
* 단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.  
* 일괄 작업은 4MB 데이터 페이로드로 제한됩니다.  

## <a name="query-and-modify-entities"></a>엔터티 쿼리 및 수정

### <a name="retrieve-all-entities-in-a-partition"></a>파티션의 모든 엔터티 검색

테이블에서 파티션의 모든 엔터티를 쿼리하려면 `table_query` 개체를 사용합니다. 다음 코드 예제에서는 `Smith`가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.  

> [!NOTE]
> 이러한 메서드는 현재 Azure Cosmos DB의 C++에 지원되지 않습니다.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

이 예제의 쿼리는 필터 조건과 일치하는 모든 엔터티를 반환합니다. 큰 테이블 및 테이블 엔터티를 자주 다운로드할 필요가 있다면 데이터를 Azure Storage Blob에 대신 저장하는 것이 좋습니다.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>파티션의 엔터티 범위 검색

파티션의 일부 엔터티를 쿼리하려면 범위를 지정하면 됩니다. 파티션 키 필터를 행 키 필터와 결합합니다. 다음 코드 예제에서는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 `E`보다 앞에 오는 문자로 시작하는 `Smith` 파티션의 모든 엔터티를 가져온 다음, 쿼리 결과를 출력합니다.  

> [!NOTE]
> 이러한 메서드는 현재 Azure Cosmos DB의 C++에 지원되지 않습니다.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>단일 엔터티 검색

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 `table_operation::retrieve_entity`를 사용하여 고객 `Jeff Smith`를 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, 반환된 값은 `table_result`입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 Table service에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>엔터티 바꾸기

엔터티를 바꾸려면 Table service에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 Table service에 다시 저장합니다. 다음 코드에서는 기존 고객의 전화 번호와 메일 주소를 변경합니다. 이 코드는 `table_operation::insert_entity`를 호출하는 대신 `table_operation::replace_entity`를 사용합니다. 이렇게 하면 서버의 엔터티가 검색된 후 변경되지 않는 한, 서버의 엔터티가 완전히 바뀝니다. 변경된 경우 작업이 실패합니다. 이러한 실패는 검색 및 업데이트하는 동안 다른 구성 요소에 의해 변경된 내용을 애플리케이션이 덮어쓰지 않도록 방지하기 위한 조치입니다. 이 실패를 올바르게 처리하는 방법은 엔터티를 다시 검색하고, 변경하고, 여전히 유효하면 다른 `table_operation::replace_entity` 작업을 수행하는 것입니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>엔터티 삽입 또는 바꾸기

서버에서 검색한 엔터티를 변경하면 `table_operation::replace_entity` 작업이 실패합니다. 또한 `table_operation::replace_entity` 작업이 성공하려면 먼저 서버에서 엔터티를 검색해야 합니다. 서버에 엔터티가 있는지 알 수 없는 경우가 있습니다. 현재 서버에 저장된 값은 업데이트에 의해 모두 덮어쓰기 되므로 관련이 없습니다. 이 결과를 얻으려면 `table_operation::insert_or_replace_entity` 작업을 사용합니다. 이 작업은 엔터티가 없으면 엔터티를 삽입합니다. 이 작업은 엔터티가 있는 경우 엔터티를 대체합니다. 다음 코드 예제에서 `Jeff Smith`의 고객 엔터티는 여전히 검색되지만, `table_operation::insert_or_replace_entity`를 사용하여 다시 서버에 저장됩니다. 검색 및 업데이트 작업 사이의 모든 엔터티 업데이트를 덮어씁니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 다음 코드의 쿼리는 `table_query::set_select_columns` 메서드를 사용하여 테이블에 있는 엔터티의 이메일 주소만 반환합니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> 모든 속성을 검색하는 것보다 엔터티의 몇 가지 속성을 쿼리하는 것이 더 효율적인 작업입니다.
>

## <a name="delete-content"></a>콘텐츠 삭제

### <a name="delete-an-entity"></a>엔터티 삭제

엔터티를 검색한 후 삭제할 수 있습니다. 엔터티를 검색한 후, 삭제할 엔터티를 사용하여 `table_operation::delete_entity`를 호출합니다. 그런 다음, `cloud_table.execute` 메서드를 호출합니다. 다음 코드는 파티션 키 `Smith` 및 행 키 `Jeff`가 있는 엔터티를 검색하여 삭제합니다.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>테이블 삭제

마지막으로, 다음 코드 예제에서는 스토리지 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 시간 동안 다시 만들 수 없습니다.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>문제 해결

Visual Studio Community Edition의 경우 포함 파일 *storage_account.h* 및 *table.h*로 인해 프로젝트에서 빌드 오류가 발생하면 **/permissive-** 컴파일러 스위치를 제거합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **속성 페이지** 대화 상자에서 **구성 속성**을 확장하고, **C/C++** 를 확장하고, **언어**를 선택합니다.
1. **준수 모드**를 **아니요**로 설정합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.

Azure Storage 및 Azure Cosmos DB의 Table API에 대한 자세한 내용을 보려면 다음 링크를 사용합니다.

* [Table API 소개](table-introduction.md)
* [C++에서 Azure Storage 리소스 나열](../storage/common/storage-c-plus-plus-enumeration.md)
* [C++용 Storage Client Library 참조(영문)](https://azure.github.io/azure-storage-cpp)
* [Azure Storage 설명서](https://azure.microsoft.com/documentation/services/storage/)
