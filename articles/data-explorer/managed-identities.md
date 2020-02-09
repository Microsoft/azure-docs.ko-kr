---
title: Azure 데이터 탐색기 클러스터에 대한 관리 되는 id를 구성 하는 방법
description: Azure 데이터 탐색기 클러스터에 대한 관리 id를 구성 하는 방법을 알아봅니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3b0bb62de4a96a45d607e05b32a87feec692e4d4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725976"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대한 관리 id 구성

[Azure Active Directory에서 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하면 클러스터에서 AZURE KEY VAULT 같은 AAD로 보호 되는 다른 리소스에 쉽게 액세스할 수 있습니다. Id는 Azure 플랫폼에서 관리 하며, 암호를 프로 비전 하거나 회전할 필요가 없습니다. 이 문서에서는 Azure 데이터 탐색기 클러스터에 대한 관리 id를 만드는 방법을 보여 줍니다. 

> [!Note]
> 앱이 구독 또는 테넌트 간에 마이그레이션되는 경우 Azure 데이터 탐색기에 대한 관리 id가 예상 대로 작동 하지 않습니다. 앱은 [id 제거](#remove-an-identity)를 사용 하 여 기능을 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 하 여 수행할 수 있는 새 id를 얻어야 합니다. 또한 새 id를 사용 하도록 다운스트림 리소스의 액세스 정책을 업데이트 해야 합니다.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 id 추가

클러스터에는 클러스터에 연결 된 **시스템 할당 id** 가 할당 될 수 있으며, 클러스터를 삭제 하면 삭제 됩니다. 클러스터에는 시스템 할당 id가 하나만 있을 수 있습니다. 시스템에 할당 된 id를 사용 하 여 클러스터를 만들려면 클러스터에서 추가 속성을 설정 해야 합니다.

### <a name="add-a-system-assigned-identity-using-c"></a>을 사용 하 여 시스템 할당 id 추가C#

Azure 데이터 탐색기 C# 클라이언트를 사용 하 여 관리 id를 설정 하려면 다음을 수행 합니다.

* [Azure 데이터 탐색기 (Kusto) NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치 합니다.
* 인증을 위해 [system.identitymodel. ActiveDirectory NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 를 설치 합니다.
* 다음 예제를 실행 하려면 리소스에 액세스할 수 있는 [AZURE AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에서 역할 할당을 추가 하 고 필요한 `Directory (tenant) ID`, `Application ID`및 `Client Secret`를 가져올 수 있습니다.

#### <a name="create-or-update-your-cluster"></a>클러스터 만들기 또는 업데이트

1. `Identity` 속성을 사용 하 여 클러스터를 만들거나 업데이트 합니다.

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 다음 명령을 실행 하 여 클러스터가 성공적으로 만들어지거나 id로 업데이트 되었는지 확인 합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과에 `Succeeded` 값이 `ProvisioningState` 포함 되어 있으면 클러스터가 만들어지거나 업데이트 되며 다음과 같은 속성이 있어야 합니다.
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` 및 `TenantId`는 Guid로 바뀝니다. `TenantId` 속성은 id가 속하는 AAD 테넌트를 식별 합니다. `PrincipalId`은 클러스터의 새 id에 대한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 시스템 할당 id 추가

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. Azure 데이터 탐색기에 배포 하는 방법에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿을 사용 하 여 azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-resource-manager.md)를 참조 하세요.

시스템 할당 유형을 추가 하면 Azure에서 클러스터에 대한 id를 만들고 관리 하 게 됩니다. 모든 `Microsoft.Kusto/clusters` 유형의 리소스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다. 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

예:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

클러스터를 만들면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` 및 `<PRINCIPALID>`는 Guid로 바뀝니다. `TenantId` 속성은 id가 속하는 AAD 테넌트를 식별 합니다. `PrincipalId`은 클러스터의 새 id에 대한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

## <a name="remove-an-identity"></a>Id 제거

시스템 할당 id를 제거 하면 AAD 에서도 삭제 됩니다. 클러스터 리소스를 삭제 하면 시스템 할당 id도 AAD에서 자동으로 제거 됩니다. 이 기능을 사용 하지 않도록 설정 하 여 시스템 할당 id를 제거할 수 있습니다.

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보호](security.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure 데이터 탐색기-Azure Portal에서 클러스터를 보호](manage-cluster-security.md) 합니다.
 * [을 사용 하 여 고객이 관리 하는 키 구성C#](customer-managed-keys-csharp.md)
 * [Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
