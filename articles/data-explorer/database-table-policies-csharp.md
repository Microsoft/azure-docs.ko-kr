---
title: Azure 데이터 탐색기 C# SDK를 사용 하 여 정책 만들기
description: 이 문서에서는를 사용 C#하 여 정책을 만드는 방법을 설명 합니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667302"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>을 사용 하 여 Azure 데이터 탐색기에 대한 데이터베이스 및 테이블 정책 만들기C#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는를 사용 C#하 여 Azure 데이터 탐색기에 대한 데이터베이스 및 테이블 정책을 만듭니다.

## <a name="prerequisites"></a>전제 조건

* Visual Studio 2019. Visual Studio 2019을 사용 하지 않는 경우 *무료* [visual studio Community 2019](https://www.visualstudio.com/downloads/)를 다운로드 하 여 사용할 수 있습니다. Visual Studio를 설치 하는 동안 **Azure 개발** 을 선택 해야 합니다.
* Azure 구독. 필요한 경우 시작 하기 전에 [무료 Azure 계정을](https://azure.microsoft.com/free/) 만들 수 있습니다.
* [테스트 클러스터 및 데이터베이스](create-cluster-database-csharp.md).
* [테스트 테이블](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)입니다.

## <a name="install-c-nuget"></a>NuGet C# 설치

* [Azure 데이터 탐색기 (Kusto) NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치 합니다.
* [Microsoft.](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/) n e t. n e t. n e t. n e t.
 (선택 사항, 테이블 정책을 변경 하는 경우)
* 인증을 위해 [system.identitymodel. ActiveDirectory NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)를 설치 합니다.

## <a name="authentication"></a>Authentication
이 문서의 예제를 실행 하려면 리소스에 액세스할 수 있는 Azure Active Directory (Azure AD) 응용 프로그램 및 서비스 주체가 필요 합니다. [테스트 클러스터와 데이터베이스](create-cluster-database-csharp.md#authentication)의 인증에 동일한 Azure AD 응용 프로그램을 사용할 수 있습니다. 다른 Azure AD 응용 프로그램을 사용 하려는 경우 [AZURE ad 응용 프로그램 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 를 참조 하 여 무료 azure ad 응용 프로그램을 만들고 구독 범위에서 역할 할당을 추가 합니다. 또한이 문서에서는 `Directory (tenant) ID`, `Application ID`및 `Client secret`를 가져오는 방법을 보여 줍니다. 새 Azure AD 응용 프로그램을 데이터베이스의 보안 주체로 추가 해야 할 수도 있습니다. 자세한 내용은 [Azure 데이터 탐색기 데이터베이스 사용 권한 관리](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)를 참조 하세요.

## <a name="alter-database-retention-policy"></a>Alter database 보존 정책
10 일 일시 삭제 기간을 사용 하 여 보존 정책을 설정 합니다.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Alter database cache 정책
데이터베이스에 대한 캐시 정책을 설정 합니다. 이전 5 일간의 데이터는 클러스터 SSD에 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Alter table cache 정책
테이블에 대한 캐시 정책을 설정 합니다. 이전 5 일간의 데이터는 클러스터 SSD에 있습니다.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>데이터베이스에 대한 새 보안 주체 추가
새 Azure AD 응용 프로그램을 데이터베이스의 관리 주체로 추가 합니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>다음 단계

* [데이터베이스 및 테이블 정책에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/kusto/management/policies)
