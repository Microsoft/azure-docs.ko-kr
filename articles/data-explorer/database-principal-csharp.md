---
title: 을 사용 하 여 Azure 데이터 탐색기에 대한 데이터베이스 보안 주체 추가C#
description: 이 문서에서는를 사용 C#하 여 Azure 데이터 탐색기에 대한 데이터베이스 보안 주체를 추가 하는 방법에 대해 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965035"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>을 사용 하 여 Azure 데이터 탐색기에 대한 데이터베이스 보안 주체 추가C#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 템플릿](database-principal-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는를 사용 C#하 여 Azure 데이터 탐색기에 대한 데이터베이스 보안 주체를 추가 합니다.

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2019가 설치 되어 있지 않으면 **무료** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드 하 여 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스를 만듭니다](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>NuGet C# 설치

* [Microsoft. ..kusto를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치 합니다.
* 인증을 위해 [Microsoft Azure 인증](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) 을 설치 합니다.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>데이터베이스 보안 주체 추가

다음 예에서는 데이터베이스 보안 주체를 프로그래밍 방식으로 추가 하는 방법을 보여 줍니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resourceGroupName | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 데이터베이스의 이름입니다.|
| principalAssignmentName | *databasePrincipalAssignment1* | 데이터베이스 보안 주체 리소스의 이름입니다.|
| principalId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 사용자 전자 메일, 응용 프로그램 ID 또는 보안 그룹 이름일 수 있는 보안 주체 ID입니다.|
| 역할(role) | *관리자* | ' Admin ', ' 수집기 ', ' Monitor ', ' User ', ' UnrestrictedViewers ', ' Viewer ' 일 수 있는 데이터베이스 보안 주체의 역할입니다.|
| tenantIdForPrincipal | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 보안 주체의 테넌트 ID입니다.|
| PrincipalType | *앱* | ' 사용자 ', ' 앱 ' 또는 ' 그룹 ' 일 수 있는 보안 주체의 유형입니다.|

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer Node 라이브러리를 사용하여 데이터 수집](node-ingest-data.md)
