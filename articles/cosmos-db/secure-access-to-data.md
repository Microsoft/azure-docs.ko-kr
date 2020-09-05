---
title: Azure Cosmos DB에서 데이터에 대한 액세스를 보호하는 방법
description: 마스터 키, 읽기 전용 키, 사용자 및 권한을 포함해서 Azure Cosmos DB의 액세스 제어 개념에 대해 알아봅니다.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4714ec9773b98887de483b7353eea9f4416eec19
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017756"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 액세스 보호

이 문서에서는 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 저장 된 데이터에 대 한 액세스를 보호 하는 방법을 간략하게 설명 합니다.

Azure Cosmos DB는 두 가지 유형의 키를 사용하여 사용자를 인증하고 해당 데이터 및 리소스에 대한 액세스를 제공합니다. 

|키 유형|리소스|
|---|---|
|[마스터 키](#master-keys) |데이터베이스 계정, 데이터베이스, 사용자, 사용 권한을 비롯한 관리 리소스에 사용됩니다.|
|[리소스 토큰](#resource-tokens)|컨테이너, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF를 비롯한 애플리케이션 리소스에 사용됩니다.|

<a id="master-keys"></a>

## <a name="master-keys"></a>마스터 키

마스터 키는 데이터베이스 계정에 대 한 모든 관리 리소스에 대 한 액세스를 제공 합니다. 마스터 키:

- 계정, 데이터베이스, 사용자 및 사용 권한에 대한 액세스를 제공합니다. 
- 컨테이너 및 문서에 대한 세분화된 액세스를 제공하는 데 사용할 수 없습니다.
- 계정 생성 중에 만들어집니다.
- 언제든지 다시 생성할 수 있습니다.

각 계정은 두 개의 마스터 키(주 키와 보조 키)로 구성됩니다. 이중 키를 사용하는 목적은 키를 다시 생성하거나 롤링하여 계정 및 데이터에 지속적인 액세스를 제공하는 것입니다.

Cosmos DB 계정에 대한 두 개의 마스터 키 외에도 두 개의 읽기 전용 키가 있습니다. 이러한 읽기 전용 키는 계정에 대한 읽기 작업만 허용합니다. 읽기 전용 키는 읽기 권한 리소스에 대한 액세스를 제공하지 않습니다.

Azure Portal을 사용하여 주, 보조, 읽기 전용 및 읽기-쓰기 마스터 키를 검색 및 다시 생성할 수 있습니다. 자세한 내용은 [액세스 키 보기, 복사 및 다시 생성](manage-with-cli.md#regenerate-account-key)을 참조하세요.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure Portal에서 액세스 제어(IAM) - NoSQL 데이터베이스 보안 설명":::

### <a name="key-rotation"></a>키 회전<a id="key-rotation"></a>

마스터 키를 회전하는 프로세스는 간단합니다. 

1. Azure Portal로 이동 하 여 보조 키를 검색 합니다.
2. 응용 프로그램에서 기본 키를 보조 키로 바꿉니다. 모든 배포에 대 한 모든 Cosmos DB 클라이언트가 즉시 다시 시작 되 고 업데이트 된 키를 사용 하 여 시작 되는지 확인 합니다.
3. Azure Portal에서 기본 키를 회전 합니다.
4. 새 기본 키가 모든 리소스에 대해 작동 하는지 확인 합니다. 키 회전 프로세스는 Cosmos DB 계정의 크기에 따라 1 분에서 몇 시간까지 걸릴 수 있습니다.
5. 보조 키를 새 기본 키로 바꿉니다.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure Portal에서 마스터 키 회전 - NoSQL 데이터베이스 보안 설명" border="false":::

### <a name="code-sample-to-use-a-master-key"></a>마스터 키를 사용할 코드 샘플

다음 코드 샘플에서는 Cosmos DB 계정 끝점 및 마스터 키를 사용 하 여 DocumentClient를 인스턴스화하고 데이터베이스를 만드는 방법을 보여 줍니다.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

다음 코드 샘플에서는 Azure Cosmos DB 계정 끝점 및 마스터 키를 사용 하 여 개체를 인스턴스화하는 방법을 보여 줍니다 `CosmosClient` .

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>리소스 토큰 <a id="resource-tokens"></a>

리소스 토큰은 데이터베이스 내에서 애플리케이션 리소스에 대한 액세스를 제공합니다. 리소스 토큰:

- 특정 컨테이너, 파티션 키, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF에 대한 액세스를 제공합니다.
- [사용자](#users)에게 특정 리소스에 대한 [사용 권한](#permissions)이 부여된 경우 생성됩니다.
- POST, GET 또는 PUT 호출로 권한 리소스가 작동할 때 다시 생성됩니다.
- 사용자, 리소스 및 사용 권한을 위해 특별히 구성된 해시 리소스 토큰을 사용합니다.
- 시간은 사용자 지정 가능한 유효 기간으로 제한됩니다. 기본 유효한 시간 범위는 1 시간입니다. 하지만 토큰 수명은 최대 5시간까지 명시적으로 지정할 수 있습니다.
- 마스터 키를 전달하기 위한 안전한 대안을 제공합니다.
- Cosmos DB 계정에서 클라이언트가 부여된 권한에 따라 리소스를 읽고 쓰고 삭제할 수 있도록 합니다.

Cosmos DB 계정에 있는 리소스에 대한 액세스 권한을 마스터 키로 신뢰할 수 없는 클라이언트에 제공하려는 경우 Cosmos DB 사용자 및 권한을 만들어서 리소스 토큰을 사용할 수 있습니다.  

Cosmos DB 리소스 토큰은 사용자가 부여한 권한에 따라 마스터 또는 읽기 전용 키에 대한 요구 없이 Cosmos DB 계정에서 클라이언트가 리소스를 읽고, 쓰고, 삭제할 수 있게 해주는 안전한 대안을 제공합니다.

다음은 리소스 토큰을 요청, 생성 및 클라이언트에 제공할 수 있는 일반적인 디자인 패턴이 있습니다.

1. 중간 계층 서비스는 모바일 애플리케이션이 사용자 사진을 공유하도록 설정됩니다.
2. 중간 계층 서비스는 Cosmos DB 계정의 마스터 키를 소유합니다.
3. 사진 앱은 최종 사용자 모바일 디바이스에 설치됩니다.
4. 로그인하면 사진 앱이 중간 계층 서비스를 사용해서 사용자의 ID를 설정합니다. ID 설정 방식은 순전히 애플리케이션에 달려 있습니다.
5. ID가 설정된 다음에는 중간 계층 서비스 요청 권한이 ID를 기반으로 합니다.
6. 중간 계층 서비스는 리소스 토큰을 다시 전화 앱으로 전송합니다.
7. 전화 앱은 계속 리소스 토큰을 사용해서 리소스의 토큰으로 정의된 권한을 사용하여 리소스 토큰에서 허용하는 간격으로 Cosmos DB 리소스에 직접 액세스합니다.
8. 리소스 토큰이 만료되면 후속 요청은 401 허가되지 않은 예외를 수신합니다.  이 지점에서는 전화 앱이 ID 를 다시 설정하고 새로운 리소스 토큰을 요청합니다.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB 리소스 토큰 워크플로" border="false":::

리소스 토큰 생성 및 관리는 네이티브 Cosmos DB 클라이언트 라이브러리에서 처리하지만 REST를 사용하는 경우 요청/인증 헤더를 구성해야 합니다. REST에 대 한 인증 헤더를 만드는 방법에 대 한 자세한 내용은 [Cosmos DB 리소스에](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) 대 한 Access Control 또는 [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) 또는 [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)의 소스 코드를 참조 하세요.

리소스 토큰을 생성하거나 broker하는 데 사용되는 중간 계층 서비스의 예는 [ResourceTokenBroker 앱](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)을 참조하세요.

## <a name="users"></a>사용자<a id="users"></a>

사용자 Azure Cosmos DB Cosmos 데이터베이스와 연결 됩니다.  각 데이터베이스는 0개 이상의 Cosmos DB 사용자를 포함할 수 있습니다. 다음 코드 샘플에서는 [.NET SDK v3 Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement)를 사용 하 여 Cosmos DB 사용자를 만드는 방법을 보여 줍니다.

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 각 Cosmos DB 사용자에 게는 사용자와 연결 된 [사용 권한](#permissions) 목록을 검색 하는 데 사용할 수 있는 ReadAsync () 메서드가 있습니다.

## <a name="permissions"></a>사용 권한<a id="permissions"></a>

권한 리소스는 사용자와 연결 되며 컨테이너 및 파티션 키 수준에서 할당 됩니다. 각 사용자는 0 개 이상의 권한을 포함할 수 있습니다. 권한 리소스는 특정 파티션 키의 특정 컨테이너 또는 데이터에 액세스 하려고 할 때 사용자가 필요로 하는 보안 토큰에 대 한 액세스를 제공 합니다. 권한 리소스에서 제공될 수 있는 사용 가능한 액세스 수준은 다음 두 가지입니다.

- 전체: 사용자가 리소스에 대한 모든 권한을 갖습니다.
- 읽기: 사용자가 리소스 내용을 읽을 수만 있고 리소스에 대해 쓰기, 업데이트 또는 삭제 작업을 수행할 수 없습니다.

> [!NOTE]
> 저장 프로시저를 실행 하려면 사용자에 게 저장 프로시저가 실행 될 컨테이너에 대 한 All 권한이 있어야 합니다.

### <a name="code-sample-to-create-permission"></a>사용 권한을 만드는 코드 샘플

다음 코드 샘플은 권한 리소스를 만들고, 권한 리소스의 리소스 토큰을 읽고, 위에서 생성된 [사용자](#users)와 사용 권한을 연결시키는 방법을 보여 줍니다.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>사용자에 대 한 읽기 권한을 부여 하는 코드 샘플

다음 코드 조각은 위에서 만든 사용자와 연결 된 사용 권한을 검색 하 고 단일 파티션 키로 범위가 지정 된 사용자를 대신 하 여 새 CosmosClient를 인스턴스화하는 방법을 보여 줍니다.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>사용자 추가 및 역할 할당

사용자 계정에 Azure Cosmos DB 계정 독자 액세스를 추가하려면 구독 소유자가 Azure Portal에서 다음 단계를 수행하도록 합니다.

1. Azure Portal을 열고, Azure Cosmos DB 계정을 선택합니다.
2. **Access Control(IAM)** 탭을 클릭한 다음, **+ 역할 할당 추가**를 클릭합니다.
3. **역할 할당 추가** 창의 **역할** 상자에서 **Cosmos DB 계정 독자 역할**을 선택합니다.
4. 다음에 대 한 **액세스 할당 상자**에서 **Azure AD 사용자, 그룹 또는 응용 프로그램**을 선택 합니다.
5. 액세스 권한을 부여하려는 디렉터리에서 사용자, 그룹 또는 애플리케이션을 선택합니다.  표시 이름, 이메일 주소 또는 개체 식별자로 디렉터리를 검색할 수 있습니다.
    선택한 사용자, 그룹 또는 애플리케이션이 선택한 멤버 목록에 나타납니다.
6. **저장**을 클릭합니다.

이제 엔터티는 Azure Cosmos DB 리소스를 읽을 수 있습니다.

## <a name="delete-or-export-user-data"></a>사용자 데이터 삭제 또는 내보내기

Azure Cosmos DB를 사용하면 데이터베이스 또는 컬렉션에 있는 개인 데이터를 검색, 선택, 수정 및 삭제할 수 있습니다. Azure Cosmos DB는 개인 데이터를 찾고 삭제하는 API를 제공하지만, API를 사용하고 개인 데이터를 지우는 데 필요한 논리를 정의하는 것은 사용자의 책임입니다. 각 다중 모델 API(SQL, MongoDB, Gremlin, Cassandra, Table)는 개인 데이터를 검색하고 삭제하는 방법이 포함된 여러 가지 언어 SDK를 제공합니다. 또한 [TTL(Time to Live)](time-to-live.md) 기능을 사용하여 추가 비용을 발생시키지 않고 지정한 기간 후에 데이터를 자동으로 삭제할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

- Cosmos 데이터베이스 보안에 대 한 자세한 내용은 [데이터베이스 보안 Cosmos DB](database-security.md)를 참조 하세요.
- Azure Cosmos DB 권한 부여 토큰을 생성하는 방법에 대한 자세한 내용은 [Azure Cosmos DB 리소스에 대한 Access Control](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)을 참조하세요.
- 사용자 및 권한이 있는 사용자 관리 샘플, [.NET SDK v3 사용자 관리 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
