---
title: '자습서: .NET 콘솔 앱을 빌드하여 Azure Cosmos DB SQL API 계정에서 데이터 관리'
description: '자습서: C# 콘솔 애플리케이션을 사용하여 Azure Cosmos DB SQL API 리소스를 만드는 방법을 알아봅니다.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 514fe30da9c0e232c168992c2aabbb484644aa99
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015308"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>자습서: .NET 콘솔 앱을 빌드하여 Azure Cosmos DB SQL API 계정에서 데이터 관리

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
>

Azure Cosmos DB SQL API 시작 자습서를 시작합니다. 이 자습서를 따라 하면 Azure Cosmos DB 리소스를 만들고 쿼리하는 콘솔 애플리케이션이 생깁니다.

이 자습서에는 [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) 버전 3.0 이상을 사용합니다. [.NET Framework 또는 .NET Core](https://dotnet.microsoft.com/download)로 작업할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
>
> * Azure Cosmos 계정 만들기 및 연결
> * Visual Studio에서 프로젝트 구성
> * 데이터베이스 및 컨테이너 만들기
> * 컨테이너에 항목 추가
> * 컨테이너 쿼리
> * 항목에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업 수행
> * 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침을 보려면 [전체 자습서 솔루션 가져오기 섹션](#GetSolution)으로 이동하세요.

이제 시작하겠습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 보겠습니다. 사용할 계정이 이미 있으면 이 섹션을 건너뜁니다. Azure Cosmos DB 에뮬레이터를 사용하려면 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설정합니다. 그런 다음, [2단계: Visual Studio 프로젝트 설정](#SetupVS)으로 건너뜁니다.

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>2단계: Visual Studio 프로젝트 설정

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 C#에 대해 **콘솔 앱(.NET Framework)** 을 선택한 후 **다음**을 선택합니다.
1. 프로젝트 이름을 *CosmosGettingStartedTutorial*로 지정한 다음, **만들기**를 선택 합니다.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="프로젝트 구성":::

1. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 애플리케이션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
1. **NuGet 패키지 관리자**에서 **찾아보기**를 선택하고 *Microsoft.Azure.Cosmos*를 검색합니다. **Microsoft.Azure.Cosmos**를 선택하고 **설치**를 선택합니다.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Azure Cosmos DB 클라이언트 SDK용 NuGet 설치":::

   Azure Cosmos DB SQL API 클라이언트 라이브러리의 패키지 ID는 [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)입니다.

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다. 이 자습서의 완료된 프로젝트는 [Azure Cosmos DB를 사용하여 .NET 콘솔 앱 개발](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)을 참조하세요.

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>3단계: Azure Cosmos DB 계정에 연결

1. *Program.cs* 파일에서 C# 애플리케이션의 시작 부분에 있는 참조를 다음 참조로 바꿉니다.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. 다음 상수와 변수를 `Program` 클래스에 추가합니다.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > 이전 버전의 .NET SDK에 익숙한 경우 *컬렉션*과 *문서*라는 용어가 친숙할 수 있습니다. Azure Cosmos DB는 여러 API 모델을 지원하기 때문에 .NET SDK 버전 3.0에서는 *컨테이너*와 *항목*이라는 일반적인 용어가 사용됩니다. *컨테이너*는 컬렉션, 그래프 또는 테이블이 될 수 있습니다. *항목*은 문서, 가장자리/꼭짓점 또는 행이 될 수 있고, 컨테이너 내부의 콘텐츠입니다. 자세한 내용은 [Azure Cosmos DB에서 데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)을 엽니다. Azure Cosmos DB 계정을 찾은 다음, **키**를 선택합니다.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Azure Portal에서 Azure Cosmos DB 키 가져오기":::

1. *Program.cs*에서 `<your endpoint URL>`을 **URI** 값으로 바꿉니다. `<your primary key>`를 **기본 키** 값으로 대체합니다.

1. **Main** 메서드 아래에 새 `CosmosClient`를 인스턴스화하는 **GetStartedDemoAsync**라는 새 비동기 작업을 추가합니다.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    **GetStartedDemoAsync**를 Azure Cosmos DB 리소스에서 작동하는 메서드를 호출하는 진입점으로 사용합니다.

1. 다음 코드를 추가하여 **Main** 메서드에서 **GetStartedDemoAsync** 비동기 작업을 실행합니다. **Main** 메서드가 예외를 catch하고 콘솔에 씁니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. F5 키를 선택하여 애플리케이션을 실행합니다.

    콘솔에 **End of demo, press any key to exit.** 메시지가 표시됩니다. 이 메시지는 애플리케이션이 Azure Cosmos DB에 연결된 것을 확인합니다. 그런 다음 콘솔 창을 닫을 수 있습니다.

축하합니다! Azure Cosmos DB 계정에 성공적으로 연결되었습니다.

## <a name="step-4-create-a-database"></a>4단계: 데이터베이스 만들기

데이터베이스는 여러 컨테이너에 분할된 항목의 논리적 컨테이너입니다. [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 클래스의 `CreateDatabaseIfNotExistsAsync` 또는 `CreateDatabaseAsync` 메서드는 데이터베이스를 만들 수 있습니다.

1. `CreateDatabaseAsync` 메서드를 복사하여 `GetStartedDemoAsync` 메서드 아래에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`는 `databaseId` 필드에서 지정된 ID가 있는 데이터베이스가 없으면, ID가 `FamilyDatabase`인 새 데이터베이스를 만듭니다.

1. 코드를 복사하여 CosmosClient를 인스턴스화한 위치 아래에 붙여넣고 방금 추가한 **CreateDatabaseAsync** 메서드를 호출합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    *Program.cs*가 이제 다음과 같이 보이고, 엔드포인트와 기본 키가 채워져 있습니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

   > [!NOTE]
   > "503 서비스를 사용할 수 없는 예외" 오류가 발생하면 직접 연결 모드에 필요한 [포트](performance-tips.md#networking)가 방화벽에 의해 차단되었을 수 있습니다. 이 문제를 해결하려면 다음 코드와 같이 필요한 포트를 열거나 게이트웨이 모드 연결을 사용합니다.
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

축하합니다! Azure Cosmos 데이터베이스가 성공적으로 만들어졌습니다.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>5단계: 컨테이너 만들기

> [!WARNING]
> `CreateContainerIfNotExistsAsync` 메서드는 가격 책정에 영향을 주는 새 컨테이너를 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
>
>

컨테이너는 `CosmosDatabase` 클래스의 [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 또는 [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 메서드를 사용하여 만들 수 있습니다. 컨테이너는 항목(SQL API의 경우 JSON 문서) 및 JavaScript의 관련 서버 쪽 애플리케이션 논리(예: 저장 프로시저, 사용자 정의 함수 및 트리거)로 구성됩니다.

1. `CreateContainerAsync` 메서드를 복사하여 `CreateDatabaseAsync` 메서드 아래에 붙여넣습니다. `CreateContainerAsync`는 ID가 `FamilyContainer`인 컨테이너가 없으면 `LastName` 속성으로 분할된 `containerId` 필드에서 지정된 ID를 사용하여 해당 컨테이너를 새로 만듭니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. CosmosClient를 인스턴스화한 아래 코드를 복사하여 붙여넣어 방금 추가한 **CreateContainer** 메서드를 호출합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos 컨테이너를 성공적으로 만들었습니다.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>6단계: 컨테이너에 항목 추가

`CosmosContainer` 클래스의 [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) 메서드는 항목을 만들 수 있습니다. SQL API를 사용하는 경우 항목은 사용자 정의 임의 JSON 콘텐츠인 문서로 프로젝션됩니다. 이제 Azure Cosmos 컨테이너에 항목을 삽입할 수 있습니다.

먼저 이 샘플에서는 Azure Cosmos DB 내에 저장된 개체를 나타내는 `Family` 클래스를 만들어 보겠습니다. `Family` 내에서 사용되는 `Parent`, `Child`, `Pet`, `Address` 하위 클래스도 만듭니다. 항목에는 `id`로 직렬화된 JSON 형식의 `Id` 속성이 있어야 합니다.

1. Ctrl+Shift+A를 선택하여 **새 항목 추가**를 엽니다. 프로젝트에 새 클래스인 `Family.cs`를 추가합니다.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="프로젝트에 새 Family.cs 클래스를 추가하는 스크린샷":::

1. `Family`, `Parent`, `Child`, `Pet` 및 `Address` 클래스를 복사하여 `Family.cs`에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. *Program.cs*로 돌아가서 `CreateContainerAsync` 메서드 뒤에 `AddItemsToContainerAsync` 메서드를 추가합니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    이 코드는 ID가 같은 항목이 존재하는지 확인합니다. *Andersen Family*와 *Wakefield Family*에 대해 하나씩 두 개의 항목을 삽입하겠습니다.

1. 호출을 `GetStartedDemoAsync` 메서드의 `AddItemsToContainerAsync`에 추가합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! 두 개의 Azure Cosmos 항목을 성공적으로 만들었습니다.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>7단계: Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 각 컨테이너에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다. 자세한 내용은 [SQL 쿼리 시작](sql-api-sql-query.md)을 참조하세요. 다음 샘플 코드에서는 이전 단계에서 삽입한 항목에 대해 쿼리를 실행하는 방법을 보여줍니다.

1. `QueryItemsAsync` 메서드를 복사하여 `AddItemsToContainerAsync` 메서드 뒤에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. 호출을 ``GetStartedDemoAsync`` 메서드의 ``QueryItemsAsync``에 추가합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos 컨테이너를 성공적으로 쿼리했습니다.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>8단계: JSON 항목 바꾸기

이제 Azure Cosmos DB에서 항목을 업데이트하겠습니다. `Family`의 `IsRegistered` 속성과 자식 요소 중 하나의 `Grade`를 변경합니다.

1. `ReplaceFamilyItemAsync` 메서드를 복사하여 `QueryItemsAsync` 메서드 뒤에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. 호출을 `GetStartedDemoAsync` 메서드의 `ReplaceFamilyItemAsync`에 추가합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos 항목을 성공적으로 대체했습니다.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>9단계: 항목 삭제

이제 Azure Cosmos DB에서 항목을 삭제하겠습니다.

1. `DeleteFamilyItemAsync` 메서드를 복사하여 `ReplaceFamilyItemAsync` 메서드 뒤에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. 호출을 `GetStartedDemoAsync` 메서드의 `DeleteFamilyItemAsync`에 추가합니다.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos 항목을 성공적으로 삭제했습니다.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>10단계: 데이터베이스 삭제

이제 데이터베이스를 삭제하겠습니다. 만든 데이터베이스를 삭제하면 데이터베이스와 모든 하위 리소스가 제거됩니다. 리소스에는 컨테이너, 항목, 저장 프로 시저, 사용자 정의 함수 및 트리거가 포함됩니다. `CosmosClient` 인스턴스도 삭제합니다.

1. `DeleteDatabaseAndCleanupAsync` 메서드를 복사하여 `DeleteFamilyItemAsync` 메서드 뒤에 붙여넣습니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. 호출을 ``GetStartedDemoAsync`` 메서드의 ``DeleteDatabaseAndCleanupAsync``에 추가합니다.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. F5 키를 선택하여 애플리케이션을 실행합니다.

축하합니다! Azure Cosmos 데이터베이스를 성공적으로 삭제했습니다.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>11단계: C# 콘솔 애플리케이션 모두 함께 실행

디버그 모드에서 애플리케이션을 빌드하고 실행하려면 Visual Studio에서 F5를 선택합니다.

전체 앱의 출력이 콘솔 창에 표시됩니다. 출력에는 추가한 쿼리의 결과가 표시됩니다. 아래 예제 텍스트와 일치해야 합니다.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

축하합니다! 이 자습서를 완료했으며 실행되는 C# 콘솔 애플리케이션이 생겼습니다.

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> 전체 자습서 솔루션 가져오기

이 자습서의 단계를 완료할 시간이 없거나 코드 샘플만 다운로드하려는 경우에는 다운로드할 수 있습니다.

`GetStarted` 솔루션을 빌드하려면 다음 필수 구성 요소가 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* [Azure Cosmos DB 계정][cosmos-db-create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 솔루션

Visual Studio에서 Azure Cosmos DB .NET SDK에 대한 참조를 복원하려면 **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 복원**을 선택합니다. 그런 다음, *App.config* 파일에서 `EndPointUri`와 `PrimaryKey` 값을 [3단계: Azure Cosmos DB 계정에 연결](#Connect)에 따라 업데이트합니다.

정말 간단하죠? 빌드하고 원하는 대로 진행하세요!

## <a name="next-steps"></a>다음 단계

* 보다 복잡한 ASP.NET MVC 자습서가 필요하신가요? [자습서: .NET SDK를 사용하여 Azure Cosmos DB를 통해 ASP.NET Core MVC 웹 애플리케이션 개발](sql-api-dotnet-application.md)을 참조하세요.
* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하시겠습니까? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* Azure Cosmos DB 요청, 사용량 및 스토리지를 모니터링하는 방법을 알아보려면 [Azure Cosmos DB에서 성능 및 스토리지 메트릭 모니터링](monitor-accounts.md)을 참조하세요.
* 샘플 데이터 세트에 대해 쿼리를 실행하려면 [Query Playground](https://www.documentdb.com/sql/demo)를 참조하세요.
* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 시작](https://docs.microsoft.com/azure/cosmos-db/introduction)을 참조하세요.

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
