---
title: .NET Standard SDK를 사용하는 Azure Cosmos DB Table API
description: Azure Cosmos DB Table API 계정에서 구조화된 데이터를 저장하고 쿼리하는 방법 알아보기
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 80918c63c1fef82c13b23569bcda10545628e8e8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998019"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>.NET SDK를 사용하여 Azure Cosmos DB Table API 및 Azure Table Storage 시작

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Cosmos DB Table API 또는 Azure Table Storage를 사용하여 클라우드에 정형 NoSQL 데이터를 저장하고, 스키마 없이 디자인된 키/특성 저장소를 제공할 수 있습니다. Azure Cosmos DB Table API 및 Table Storage는 모두 스키마가 없기 때문에 애플리케이션의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. Azure Cosmos DB Table API 또는 Table Storage를 사용하여 웹 애플리케이션의 사용자 데이터, 주소록, 디바이스 정보 또는 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 세트를 저장할 수 있습니다. 

이 자습서에서는 Azure Cosmos DB Table API 및 Azure Table Storage 시나리오를 사용하여 [.NET용 Microsoft Azure Cosmos DB Table Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)를 사용하는 방법을 보여주는 샘플을 설명합니다. Azure 서비스에 특정된 연결을 사용해야 합니다. 테이블을 만들고, 데이터를 삽입/업데이트하고, 데이터를 쿼리하고, 테이블을 삭제하는 방법을 설명하는 C# 예제를 사용하여 이러한 시나리오를 살펴보겠습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 샘플을 성공적으로 완료하려면 다음이 필요합니다.

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure Cosmos DB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - 이 라이브러리는 현재 .NET Standard 및 .NET Framework에 제공됩니다. 

* [Azure Cosmos DB Table API 계정](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET 콘솔 프로젝트 만들기

Visual Studio에서 새 .NET 콘솔 애플리케이션을 만듭니다. 다음 단계에서는 Visual Studio 2019에서 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. Azure 클라우드 서비스, 웹앱, 데스크톱 및 모바일 애플리케이션을 비롯한 모든 .NET 애플리케이션 형식에서 Azure Cosmos DB Table Library를 사용할 수 있습니다. 이 가이드에서는 편의상 콘솔 애플리케이션을 사용합니다.

1. **File** > **New** > **Project**를 선택합니다.

1. **콘솔 앱(.NET Core)** 을 선택한 후 **다음**을 선택합니다.

1. **프로젝트 이름** 필드에 애플리케이션의 이름(예: **CosmosTableSamples**)을 입력합니다. (필요에 따라 다른 이름을 제공할 수 있습니다.)

1. **만들기**를 선택합니다.

이 샘플에 예시로 나온 모든 코드는 콘솔 애플리케이션의 **Program.cs** 파일의 Main() 메서드에 추가할 수 있습니다.

## <a name="install-the-required-nuget-package"></a>필요한 NuGet 패키지 설치

NuGet 패키지를 가져오려면 다음 단계를 수행합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

1. 온라인에서 [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json), [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)를 검색하고 **설치**를 선택하여 Microsoft Azure Cosmos DB Table Library를 설치합니다.

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

1. [Azure Portal](https://portal.azure.com/)에서 Azure Cosmos 계정 또는 Table Storage 계정으로 이동합니다. 

1. **연결 문자열** 또는 **액세스 키** 창을 엽니다. 창의 오른쪽에서 복사 단추를 사용하여 **기본 연결 문자열**을 복사합니다.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="연결 문자열 창에서 기본 연결 문자열 보기 및 복사":::
   
1. 연결 문자열을 구성하려면 Visual Studio에서 마우스 오른쪽 단추로 **CosmosTableSamples** 프로젝트를 클릭합니다.

1. **추가**를 선택한 다음, **새 항목**을 선택합니다. 파일 유형이 **TypeScript JSON 구성** 파일인 **Settings.json**이라는 새 파일을 만듭니다. 

1. Settings.json 파일의 코드를 다음 코드로 바꾸고 기본 연결 문자열을 할당합니다.

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. **CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가**, **새 항목**을 선택하고 **AppSettings.cs**라는 클래스를 추가합니다.

1. 다음 코드를 AppSettings.cs 파일에 추가합니다. 이 파일은 Settings.json 파일에서 연결 문자열을 읽고 이를 구성 매개 변수에 할당합니다.

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>연결 세부 정보의 구문 분석 및 확인 

1. **CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가**, **새 항목**을 선택하고 **Common.cs**라는 클래스를 추가합니다. 연결 세부 정보를 확인하는 코드를 작성하고 이 클래스 내에 테이블을 만듭니다.

1. `CreateStorageAccountFromConnectionString` 메서드를 아래와 같이 정의합니다. 이 메서드는 연결 문자열 세부 정보를 구문 분석하고, “Settings.json” 파일에 제공된 계정 이름과 계정 키 세부 정보가 올바른지 확인합니다. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>테이블 만들기 

[CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) 클래스를 사용하면 Table Storage에 저장된 테이블 및 엔터티를 검색할 수 있습니다. Cosmos DB Table API 계정에 테이블이 없으므로 `CreateTableAsync` 메서드를 **Common.cs** 클래스에 추가하여 테이블을 만들어보겠습니다.

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

"503 서비스를 사용할 수 없는 예외" 오류가 발생하면 연결 모드에 필요한 포트가 방화벽에 의해 차단되었을 수 있습니다. 이 문제를 해결하려면 다음 코드와 같이 필요한 포트를 열거나 게이트웨이 모드 연결을 사용합니다.

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>엔터티 정의 

엔터티는 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)에서 파생된 사용자 지정 클래스를 사용하여 C# 개체에 매핑됩니다. 테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다.

**CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가**, **새 폴더**를 선택하고 이름을 **Model**로 지정합니다. Model 폴더 내에 **CustomerEntity.cs**라는 클래스를 추가하고, 여기에 다음 코드를 추가합니다.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

이 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 엔터티 클래스를 정의합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다. 테이블에 저장되는 엔터티는 예를 들어 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) 클래스에서 파생되는 지원되는 형식이어야 합니다. 테이블에 저장하려는 엔터티 속성은 해당 형식의 공용 속성이어야 하며 값의 가져오기 및 설정하기를 모두 지원해야 합니다. 또한 엔터티 형식은 매개 변수가 없는 생성자를 표시해야 합니다.

## <a name="insert-or-merge-an-entity"></a>엔터티 삽입 또는 병합

다음 코드 예제에서는 엔터티 개체를 만들고 테이블에 추가합니다. [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) 클래스 내의 InsertOrMerge 메서드는 엔터티를 삽입 또는 병합하는 데 사용됩니다. [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) 메서드가 호출되어 작업이 실행됩니다. 

**CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가**, **새 항목**을 선택하고 **SamplesUtils.cs**라는 클래스를 추가합니다. 이 클래스는 엔터티에 대한 CRUD 작업을 수행하는 데 필요한 모든 코드를 저장합니다. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

## <a name="get-an-entity-from-a-partition"></a>파티션에서 엔터티 가져오기

[TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) 클래스에서 검색 메서드를 사용하여 파티션에서 엔터티를 가져올 수 있습니다. 다음 코드 예제에서는 고객 엔터티의 파티션 키 행 키, 이메일 및 전화 번호를 가져옵니다. 또한 이 예제에서는 엔터티에 대한 쿼리에 사용되는 요청 단위를 출력합니다. 엔터티를 쿼리하려면 다음 코드를 **SamplesUtils.cs** 파일에 추가합니다. 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>엔터티 삭제

엔터티를 검색한 후 엔터티 업데이트를 위해 표시된 것과 동일한 패턴을 사용하여 엔터티를 쉽게 삭제할 수 있습니다. 다음 코드는 고객 엔터티를 검색하고 삭제합니다. 엔터티를 삭제하려면 다음 코드를 **SamplesUtils.cs** 파일에 추가합니다. 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>샘플 데이터에서 CRUD 작업 실행

테이블 만들기, 엔터티 삽입 또는 병합 메서드를 정의한 후, 샘플 데이터에서 이러한 메서드를 실행합니다. 수행하려면 **CosmosTableSamples** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가**, **새 항목**을 선택하고 **BasicSamples.cs**라는 클래스를 추가하고 여기에 다음 코드를 추가합니다. 이 코드는 테이블을 만들고 여기에 엔터티를 추가합니다. 프로젝트의 마지막에 엔터티 및 테이블을 삭제하려는 경우 다음 코드에서 `table.DeleteIfExistsAsync()` 및 `SamplesUtils.DeleteEntityAsync(table, customer)`의 주석을 제거합니다.

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

이전 코드는 “demo”로 시작하는 테이블을 만들고 생성된 GUID는 테이블 이름에 추가됩니다. 그런 다음, 이름 및 성이 “Harp Walter”인 고객 엔터티를 추가하고 나중에 이 사용자의 전화 번호를 업데이트합니다. 

이 자습서에서는 Table API 계정에 저장된 데이터에 대해 기본 CRUD 작업을 수행하는 코드를 빌드했습니다. 또한 데이터 배치 삽입, 파티션 내 모든 데이터 쿼리, 파티션 내 다양한 데이터 쿼리, 이름이 지정된 접두사로 시작하는 계정의 테이블 나열과 같은 고급 작업을 수행할 수 있습니다. 전체 샘플 양식 [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub 리포지토리를 다운로드할 수 있습니다. 데이터에 수행할 수 있는 더 많은 작업이 [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) 클래스에 있습니다.  

## <a name="run-the-project"></a>프로젝트 실행

**CosmosTableSamples** 프로젝트에서 **Program.cs**라는 클래스를 열고 프로젝트가 실행될 때 BasicSamples를 호출하기 위해 다음 코드를 추가합니다.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

이제 솔루션을 빌드하고 F5 키를 눌러 프로젝트를 실행합니다. 프로젝트를 실행하는 경우 명령 프롬프트에 다음 출력이 표시됩니다.

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="명령 프롬프트의 출력":::

프로젝트 실행 시 Settings.json 파일을 찾을 수 없다는 오류가 표시되는 경우 다음 XML 항목을 프로젝트 설정에 추가하여 오류를 해결할 수 있습니다. CosmosTableSamples를 마우스 오른쪽 단추로 클릭하고 CosmosTableSamples.csproj 편집을 선택한 후 다음 itemGroup을 추가합니다. 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
이제 Azure Portal에 로그인하고 데이터가 테이블에 있는지 확인할 수 있습니다. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="포털의 결과":::

## <a name="next-steps"></a>다음 단계

이제 다음 자습서로 진행하여 Azure Cosmos DB Table API 계정으로 데이터를 마이그레이션하는 방법을 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
>[데이터를 쿼리하는 방법](../cosmos-db/table-import.md)
