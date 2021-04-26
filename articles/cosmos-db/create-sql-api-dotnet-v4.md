---
title: .NET V4 SDK를 사용하여 Azure Cosmos DB SQL API 리소스 관리
description: .NET V4 SDK를 사용하여 Azure Cosmos DB SQL API 계정 리소스를 관리하는 콘솔 앱을 빌드하는 빠른 시작입니다.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 495191dfcdfd7a4d318bef508878e951d88b3ae6
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483897"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>빠른 시작: .NET V4 SDK(미리 보기)를 사용하여 Azure Cosmos DB SQL API 계정 리소스를 관리하는 콘솔 앱을 빌드합니다.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 커넥터](create-sql-api-spark.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET용 Azure Cosmos DB SQL API 클라이언트 라이브러리를 시작합니다. 이 문서의 단계에 따라 .NET V4(Azure.Cosmos) 패키지를 설치하고, 앱을 빌드하고, Azure Cosmos DB에 저장된 데이터에 대해 기본 CRUD 작업용 예제 코드를 사용해 봅니다.

> [!IMPORTANT]
> Azure Cosmos DB용 .NET V4 SDK는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB는 모든 규모의 개방형 API를 포함하는 Microsoft의 빠른 NoSQL 데이터베이스입니다. Azure Cosmos DB를 사용하여 키/값, 문서 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. .NET용 Azure Cosmos DB SQL API 클라이언트 라이브러리를 사용하여 다음 작업을 수행합니다.

* Azure Cosmos 데이터베이스 및 컨테이너 만들기
* 컨테이너에 샘플 데이터 추가
* 데이터 쿼리 
* 데이터베이스 삭제

[라이브러리 소스 코드](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [무료로 구독을 만들거나](https://azure.microsoft.com/free/) Azure 구독, 요금 및 약정 없이 [Azure Cosmos DB 평가판을 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). `dotnet --version`을 실행하여 작업 환경에서 사용할 수 있는 버전을 확인할 수 있습니다.

## <a name="setting-up"></a>설치

이 섹션에서는 Azure Cosmos 계정을 만들고 .NET용 Azure Cosmos DB SQL API 클라이언트 라이브러리를 사용하여 리소스를 관리하는 프로젝트를 설정하는 과정을 안내합니다. 이 문서에서 설명하는 예제 코드는 `FamilyDatabase` 데이터베이스와 해당 데이터베이스 내에 있는 패밀리 멤버(각 패밀리 멤버가 하나의 항목임)를 만듭니다. 각 패밀리 멤버에는 `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`와 같은 속성이 있습니다. `LastName` 속성은 컨테이너의 파티션 키로 사용됩니다. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos 계정 만들기

[Azure Cosmos DB 체험하기](https://azure.microsoft.com/try/cosmosdb/) 옵션을 사용하여 Azure Cosmos 계정을 만드는 경우, **SQL API** 유형의 Azure Cosmos DB 계정을 만들어야 합니다. Azure Cosmos DB 테스트 계정이 이미 만들어져 있습니다. 계정을 명시적으로 만들 필요가 없으므로, 이 섹션을 건너뛰고 다음 섹션으로 이동해도 됩니다.

고유한 Azure 구독이 있거나 무료로 구독을 만든 경우에는 Azure Cosmos 계정을 명시적으로 만들어야 합니다. 다음 코드에서는 세션 일관성을 사용하여 Azure Cosmos 계정을 만듭니다. 계정은 `South Central US` 및 `North Central US`에서 복제됩니다.  

Azure Cloud Shell을 사용하여 Azure Cosmos 계정을 만들 수 있습니다. Azure Cloud Shell은 Azure 리소스를 관리하기 위해 브라우저에서 액세스할 수 있는 인증된 대화형 셸입니다. Bash 또는 PowerShell 중에서 작업 방식에 가장 적합한 셸 환경을 유연하게 선택할 수 있습니다. 이 빠른 시작에서는 **Bash** 모드를 선택합니다. Azure Cloud Shell은 스토리지 계정도 필요하므로, 메시지가 표시되면 계정을 만들 수 있습니다.

다음 코드 옆에 있는 **Try It**(시도) 단추를 선택하고 **Bash** 모드를 선택한 다음, **스토리지 계정 만들기** 를 선택하고 Cloud Shell에 로그인합니다. 다음 코드를 복사하여 Azure Cloud Shell에 붙여넣고 실행합니다. Azure Cosmos 계정 이름은 전역적으로 고유해야 하므로, 명령을 실행하기 전에 `mysqlapicosmosdb` 값을 업데이트합니다.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Azure Cosmos 계정을 만드는 데 시간이 걸리며, 작업이 성공하면 확인 출력을 볼 수 있습니다. 명령이 성공적으로 완료되면 [Azure Portal](https://portal.azure.com/)에 로그인하여 지정된 이름의 Azure Cosmos 계정이 있는지 확인합니다. 리소스가 생성된 후에는 Azure Cloud Shell 창을 닫아도 됩니다. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>새 .NET 앱 만들기

선호하는 편집기 또는 IDE에서 .NET 애플리케이션을 새로 만듭니다. 로컬 컴퓨터에서 Windows 명령 프롬프트 또는 터미널 창을 엽니다. 명령 프롬프트 또는 터미널에서 다음 섹션의 명령을 모두 실행합니다.  다음 dotnet new 명령을 실행하여 이름이 `todo`인 새 앱을 만듭니다. --langVersion 매개 변수는 생성된 프로젝트 파일의 LangVersion 속성을 설정합니다.

   ```bash
   dotnet new console â€“langVersion:8 -n todo
   ```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

   ```bash
   cd todo
   dotnet build
   ```

이 빌드의 예상 출력은 다음과 같습니다.

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Azure Cosmos DB 패키지 설치

애플리케이션 디렉터리에 있는 동안 dotnet add package 명령을 사용하여 .NET Core용 Azure Cosmos DB 클라이언트 라이브러리를 설치합니다.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure Portal에서 Azure Cosmos 계정 자격 증명 복사

샘플 애플리케이션은 Azure Cosmos 계정을 인증해야 합니다. 인증을 받으려면 Azure Cosmos 계정 자격 증명을 애플리케이션에 전달해야 합니다. 다음 단계를 수행하여 Azure Cosmos 계정 자격 증명을 가져옵니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos 계정으로 이동합니다.

1. **키** 창을 열고 계정의 **URI** 및 **기본 키** 를 복사합니다. 다음 단계에서 URI 및 키 값을 환경 변수에 추가합니다.

## <a name="object-model"></a><a id="object-model"></a>개체 모델

애플리케이션 빌드를 시작하기 전에 Azure Cosmos DB의 리소스 계층 구조와 이러한 리소스를 만들고 액세스하는 데 사용되는 개체 모델을 살펴보겠습니다. Azure Cosmos DB는 다음과 같은 순서로 리소스를 만듭니다.

* Azure Cosmos 계정 
* 데이터베이스 
* 컨테이너 
* Items

다른 엔터티의 계층 구조에 대해 자세히 알아보려면 [Azure Cosmos DB의 데이터베이스, 컨테이너 및 항목 작업](account-databases-containers-items.md) 문서를 참조하세요. 다음 .NET 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* CosmosClient - 이 클래스는 Azure Cosmos DB 서비스에 대한 클라이언트 쪽 논리적 표현을 제공합니다. 이 클라이언트 개체는 서비스에 대한 요청을 구성하고 실행하는 데 사용됩니다.
* CreateDatabaseIfNotExistsAsync - 이 메서드는 데이터베이스 리소스를 비동기 작업으로 만들거나(존재하지 않는 경우) 가져옵니다(존재하는 경우). 
* CreateContainerIfNotExistsAsync - 이 메서드는 컨테이너를 비동기 작업으로 만들거나(존재하지 않는 경우) 가져옵니다(존재하는 경우). 응답의 상태 코드를 확인하여 컨테이너를 새로 만들었는지(201) 또는 기존 컨테이너가 반환되었는지(200) 확인할 수 있습니다. 
* Createitemasync - 이 메서드는 컨테이너 내에 항목을 만듭니다.
* UpsertItemAsync - 이 메서드는 컨테이너 내에 항목이 아직 존재하지 않는 경우 만들거나 항목이 이미 존재하는 경우 대체합니다. 
* GetItemQueryIterator - 이 메서드는 매개 변수화된 값이 있는 SQL 문을 사용하여 Azure Cosmos 데이터베이스의 컨테이너 아래에 있는 항목에 대한 쿼리를 만듭니다. 
* DeleteAsync - Azure Cosmos 계정에서 지정된 데이터베이스를 삭제합니다. `DeleteAsync` 메서드는 데이터베이스만 삭제합니다.

 ## <a name="code-examples"></a><a id="code-examples"></a>코드 예제

이 문서에 설명된 샘플 코드는 Azure Cosmos DB에서 패밀리 데이터베이스를 만듭니다. 패밀리 데이터베이스는 이름, 주소, 위치, 연결된 부모, 자식, 애완 동물 등의 패밀리 세부 정보를 포함합니다. 해당 데이터를 Azure Cosmos 계정에 채우기 전에 패밀리 항목의 속성을 정의합니다. 샘플 애플리케이션의 루트 수준에서 `Family.cs`라는 새 클래스를 만들고 다음 코드를 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>using 지시문 추가 및 클라이언트 개체 정의

프로젝트 디렉터리의 `Program.cs` 파일을 편집기에서 열고 애플리케이션 맨 위에 다음 using 지시문을 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


`Program` 클래스에 다음 전역 변수를 추가합니다. 여기에는 엔드포인트 및 인증 키, 데이터베이스 이름 및 만들 컨테이너가 포함됩니다. 작업 환경에 따라 엔드포인트 및 인증 키 값을 바꾸어야 합니다. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

마지막으로 `Main` 메서드를 다음과 같이 바꿉니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>데이터베이스 만들기 

`program.cs` 클래스 내에서 `CreateDatabaseAsync` 메서드를 정의합니다. 이 메서드는 `FamilyDatabase`가 아직 없는 경우 새로 만듭니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>컨테이너 만들기

`Program` 클래스 내에서 `CreateContainerAsync` 메서드를 정의합니다. 이 메서드는 `FamilyContainer`가 아직 없는 경우 새로 만듭니다. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>항목 만들기

다음 코드를 통해 `AddItemsToContainerAsync` 메서드를 추가하여 패밀리 항목을 만듭니다. `CreateItemAsync` 또는`UpsertItemAsync` 메서드를 사용하여 항목을 만들 수 있습니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>항목 쿼리

항목을 삽입한 후 쿼리를 실행하여 "Andersen" 패밀리의 세부 정보를 가져올 수 있습니다. 다음 코드에서는 SQL 쿼리를 직접 사용하여 쿼리를 실행하는 방법을 보여 줍니다. "Anderson" 패밀리 세부 정보를 가져오는 SQL 쿼리는 다음과 같습니다. `SELECT * FROM c WHERE c.LastName = 'Andersen'` `Program` 클래스 내에서 `QueryItemsAsync` 메서드를 정의하고 다음 코드를 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>항목 바꾸기 

패밀리 항목을 읽은 후 다음 코드를 통해 `ReplaceFamilyItemAsync` 메서드를 추가하여 업데이트합니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>항목 삭제 

다음 코드를 통해 `DeleteFamilyItemAsync` 메서드를 추가하여 패밀리 항목을 삭제합니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>데이터베이스 삭제 

마지막으로 다음 코드를 통해 `DeleteDatabaseAndCleanupAsync` 메서드를 추가하여 데이터베이스를 삭제할 수 있습니다.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

필요한 메서드를 모두 추가한 후 `Program` 파일을 저장합니다. 

## <a name="run-the-code"></a>코드 실행

그런 후, 애플리케이션을 빌드 및 실행하여 Azure Cosmos DB 리소스를 만듭니다.

   ```bash
   dotnet run
   ```

다음 출력은 애플리케이션을 실행할 때 생성됩니다. Azure Portal에 로그인하여 리소스가 생성되었는지 확인할 수도 있습니다.

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Azure Portal에 로그인하여 데이터가 생성되었는지 확인하고 Azure Cosmos 계정에서 필요한 항목을 확인할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 Azure Cosmos 계정 및 해당 리소스 그룹을 제거할 수 있습니다. 다음 명령은 Azure CLI를 사용하여 리소스 그룹을 삭제하는 방법을 보여 줍니다.

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos 계정을 만들고, .NET Core 앱을 사용하여 데이터베이스 및 컨테이너를 만드는 방법을 알아보았습니다. 이제 다음 문서의 지침에 따라 Azure Cosmos 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)
