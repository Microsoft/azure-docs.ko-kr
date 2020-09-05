---
title: 관리 ID
description: Azure App Service 및 Azure Functions에서 관리 ID가 작동하는 방법, 관리되는 ID를 구성하고 백 엔드 리소스에 대한 토큰을 생성하는 방법에 대해 알아봅니다.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 98cec9fc30b4840a763358c0b3cd76659ff865d7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070363"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법

이 항목에서는 App Service 및 Azure Functions 애플리케이션에 대한 관리 ID를 만드는 방법과 다른 리소스에 액세스하는 데 사용하는 방법을 보여 줍니다. 

> [!Important] 
> 앱이 구독/테넌트 간에 마이그레이션되면 App Service 및 Azure Functions에 대한 관리 ID가 예상대로 작동하지 않습니다. 앱에서 새 ID를 확보해야 하며 해당 기능을 사용 중지했다가 다시 사용하도록 설정하여 수행합니다. 아래 [ID 제거](#remove)를 참조하세요. 다운스트림 리소스에도 새 ID를 사용하려면 액세스 정책을 업데이트해야 합니다.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

시스템 할당 ID를 사용하여 앱을 만들려면 애플리케이션에서 추가 속성을 설정해야 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

포털에서 관리 ID를 설정하려면 먼저 정상적으로 애플리케이션을 만든 다음, 기능을 사용하도록 설정합니다.

1. 평소처럼 포털에서 앱을 만듭니다. 포털에서 해당 앱으로 이동합니다.

2. 함수 앱을 사용하는 경우 **플랫폼 기능**으로 이동합니다. 다른 유형의 앱을 사용하는 경우 왼쪽 탐색 창에서 **설정** 그룹이 나올 때까지 아래로 스크롤합니다.

3. **ID**를 선택합니다.

4. **시스템 할당** 탭에서 **상태**를 **켜기**로 바꿉니다. **저장**을 클릭합니다.

    ![App Service의 관리 ID](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Azure Portal에서 웹 앱 또는 슬롯 앱에 대 한 관리 id를 찾으려면 **엔터프라이즈 응용 프로그램**에서 **사용자 설정** 섹션을 확인 합니다. 일반적으로 슬롯 이름은와 비슷합니다 `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 관리 ID를 설정하려면 기존 애플리케이션에 대해 `az webapp identity assign` 명령을 사용해야 합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 위에 있는 “사용해 보세요” 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려면 [최신 버전의 Azure CLI(2.0.31 이상)를 설치](/cli/azure/install-azure-cli)합니다. 

다음 단계는 웹앱을 만들고 CLI를 사용하여 ID를 할당하는 과정을 안내합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az-login)을 사용하여 먼저 Azure에 로그인합니다. 애플리케이션을 배포하려는 Azure 구독과 연결된 계정을 사용합니다.

    ```azurecli-interactive
    az login
    ```

2. CLI를 사용하여 웹 애플리케이션을 만듭니다. App Service에서 CLI를 사용하는 방법에 대한 예제는 [App Service CLI 샘플](../app-service/samples-cli.md)을 참조하세요.

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. 이 애플리케이션에 대한 ID를 만들려면 `identity assign` 명령을 실행합니다.

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 앱을 만들고 Azure PowerShell를 사용 하 여 id를 할당 하는 과정을 안내 합니다. 웹 앱 및 함수 앱을 만드는 방법에 대 한 지침은 서로 다릅니다.

#### <a name="using-azure-powershell-for-a-web-app"></a>웹 앱에 대 한 Azure PowerShell 사용

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Login-AzAccount`를 실행하여 Azure에 연결합니다.

2. Azure PowerShell을 사용하여 웹앱을 만듭니다. App Service에서 Azure PowerShell을 사용하는 방법에 대한 예제는 [App Service PowerShell 샘플](../app-service/samples-powershell.md)을 참조하세요.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. 이 애플리케이션에 대한 ID를 만들려면 `Set-AzWebApp -AssignIdentity` 명령을 실행합니다.

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>함수 앱에 대 한 Azure PowerShell 사용

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Login-AzAccount`를 실행하여 Azure에 연결합니다.

2. Azure PowerShell를 사용 하 여 함수 앱을 만듭니다. Azure Functions에서 Azure PowerShell를 사용 하는 방법에 대 한 자세한 예제를 보려면 [Az. 함수 참조](/powershell/module/az.functions/?view=azps-4.1.0#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

대신를 사용 하 여 기존 함수 앱을 업데이트할 수도 있습니다 `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. App Service 및 Functions에 배포하는 방법에 대한 자세한 내용은 [App Service에서 리소스 배포 자동화](../app-service/deploy-complex-application-predictably.md) 및 [Azure Functions에서 리소스 배포 자동화](../azure-functions/functions-infrastructure-as-code.md)를 참조하세요.

모든 `Microsoft.Web/sites` 유형의 리소스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다.

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> 애플리케이션에는 시스템 할당 ID와 사용자 할당 ID 둘 다 동시에 있을 수 있습니다. 이 경우에 `type` 속성이 `SystemAssigned,UserAssigned`가 됩니다.

시스템 할당 형식을 추가하면 애플리케이션에 대한 ID를 만들어서 관리하라고 Azure에 알려주는 것입니다.

예를 들어 웹앱이 다음과 같을 수 있습니다.

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

사이트가 생성되면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

tenantId 속성은 ID가 속한 Azure AD 테넌트를 식별합니다. principalId는 애플리케이션 새 ID의 고유 식별자입니다. Azure AD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

템플릿의 이후 단계에서 이러한 속성을 참조 해야 하는 경우 다음 예제와 같이 플래그를 사용 하 여 [ `reference()` 템플릿 함수](../azure-resource-manager/templates/template-functions-resource.md#reference) 를 통해 수행할 수 있습니다 `'Full'` .

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>사용자 할당 ID 추가

사용자 할당 ID로 앱을 만들려면 ID를 만든 다음, 앱 구성에 리소스 ID를 추가해야 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

먼저, 사용자 할당 ID 리소스를 만들어야 합니다.

1. [이러한 지침](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)에 따라 사용자 할당 관리 ID 리소스를 만듭니다.

2. 평소처럼 포털에서 앱을 만듭니다. 포털에서 해당 앱으로 이동합니다.

3. 함수 앱을 사용하는 경우 **플랫폼 기능**으로 이동합니다. 다른 유형의 앱을 사용하는 경우 왼쪽 탐색 창에서 **설정** 그룹이 나올 때까지 아래로 스크롤합니다.

4. **ID**를 선택합니다.

5. **사용자 할당** 탭 내에서 **추가**를 클릭합니다.

6. 이전에 만든 ID를 검색한 후 선택합니다. **추가**를 클릭합니다.

    ![App Service의 관리 ID](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 앱을 만들고 Azure PowerShell를 사용 하 여 id를 할당 하는 과정을 안내 합니다.

> [!NOTE]
> Azure App Service Azure PowerShell commandlet의 현재 버전은 사용자 할당 id를 지원 하지 않습니다. 아래 지침은 Azure Functions에 대 한 것입니다.

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Login-AzAccount`를 실행하여 Azure에 연결합니다.

2. Azure PowerShell를 사용 하 여 함수 앱을 만듭니다. Azure Functions에서 Azure PowerShell를 사용 하는 방법에 대 한 자세한 예제는 [Az. 함수 참조](/powershell/module/az.functions/?view=azps-4.1.0#functions)를 참조 하세요. 또한 아래 스크립트는 `New-AzUserAssignedIdentity` [Azure PowerShell 사용 하 여 사용자 할당 관리 Id를 만들기, 나열 또는 삭제 하는 방법](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)으로 별도로 설치 해야 하는를 사용 합니다.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

대신를 사용 하 여 기존 함수 앱을 업데이트할 수도 있습니다 `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. App Service 및 Functions에 배포하는 방법에 대한 자세한 내용은 [App Service에서 리소스 배포 자동화](../app-service/deploy-complex-application-predictably.md) 및 [Azure Functions에서 리소스 배포 자동화](../azure-functions/functions-infrastructure-as-code.md)를 참조하세요.

모든 `Microsoft.Web/sites` 형식의 리소스는 `<RESOURCEID>`를 원하는 ID의 리소스 ID로 바꿔 리소스 정의에 다음 블록을 포함한 ID로 만들 수 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> 애플리케이션에는 시스템 할당 ID와 사용자 할당 ID 둘 다 동시에 있을 수 있습니다. 이 경우에 `type` 속성이 `SystemAssigned,UserAssigned`가 됩니다.

사용자 할당 형식을 추가하면 Azure에서 애플리케이션에 대해 지정된 사용자 할당 ID를 사용하도록 지시하게 됩니다.

예를 들어 웹앱이 다음과 같을 수 있습니다.

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

사이트가 생성되면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

principalId는 Azure AD 관리에 사용되는 ID의 고유 식별자입니다. clientId는 런타임 호출 중 사용할 ID 지정에 사용하는 애플리케이션의 새 ID에 대한 고유 식별자입니다.

## <a name="obtain-tokens-for-azure-resources"></a>Azure 리소스 토큰 가져오기

앱은 자체 관리 ID를 사용하여 Azure Key Vault 같은 Azure AD로 보호되는 다른 리소스에 액세스하는 토큰을 가져올 수 있습니다. 이러한 토큰은 애플리케이션의 특정 사용자가 아닌 리소스에 액세스하는 애플리케이션을 나타냅니다. 

애플리케이션의 액세스를 허용하도록 대상 리소스를 구성해야 할 수도 있습니다. 예를 들어 Key Vault에 액세스하는 토큰을 요청할 때에는 애플리케이션의 ID를 포함하는 액세스 정책을 추가했는지 확인해야 합니다. 그렇지 않으면 토큰이 포함되어 있더라도 Key Vault 호출이 거부됩니다. Azure Active Directory 토큰을 지원하는 리소스에 대한 자세한 내용은 [Azure AD 인증을 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

> [!IMPORTANT]
> 관리 ID에 대한 백 엔드 서비스는 약 8시간 동안 리소스 URI당 캐시를 유지 관리합니다. 특정 대상 리소스의 액세스 정책을 업데이트하고 해당 리소스에 대한 토큰을 즉시 검색하는 경우 토큰이 만료될 때까지 오래된 권한으로 캐시된 토큰을 계속 가져올 수 있습니다. 현재 토큰 새로 고침을 강제로 적용할 수 있는 방법은 없습니다.

App Service 및 Azure Functions에서 토큰을 가져오는 간단한 REST 프로토콜이 있습니다. 이는 모든 애플리케이션 및 언어에 사용할 수 있습니다. .NET 및 Java의 경우 Azure SDK는 이 프로토콜에 대한 추상화를 제공하여 로컬 개발 환경을 지원합니다.

### <a name="using-the-rest-protocol"></a>REST 프로토콜 사용

> [!NOTE]
> “2017-09-01” API 버전을 사용하는 이 프로토콜의 이전 버전은 `X-IDENTITY-HEADER` 대신 `secret` 헤더를 사용했으며 사용자 할당을 위해 `clientid` 속성만 수락했습니다. 또한 타임스탬프 형식에 `expires_on`을 반환했습니다. MSI_ENDPOINT는 IDENTITY_ENDPOINT에 대한 별칭으로 사용할 수 있으며 MSI_SECRET은 IDENTITY_HEADER에 대한 별칭으로 사용할 수 있습니다. 이 프로토콜 버전은 현재 Linux 소비 호스팅 계획에 필요 합니다.

관리 ID가 있는 앱에는 다음 두 가지 환경 변수가 정의되어 있습니다.

- IDENTITY_ENDPOINT - 로컬 토큰 서비스의 URL입니다.
- IDENTITY_HEADER - SSRF(서버 쪽 요청 위조) 공격을 완화하는 데 사용되는 헤더입니다. 플랫폼에서 값을 회전합니다.

**IDENTITY_ENDPOINT**는 앱이 토큰을 요청할 수 있는 로컬 URL입니다. 리소스 토큰을 가져오려면 이 엔드포인트에 다음 매개 변수를 포함하여 HTTP GET 요청을 보냅니다.

> | 매개 변수 이름    | 그런 다음     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | 쿼리  | 토큰을 가져와야 하는 리소스의 Azure AD 리소스 URI입니다. [Azure AD 인증](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 또는 기타 리소스 URI를 지원하는 Azure 서비스 중 하나일 수 있습니다.    |
> | api-version       | 쿼리  | 사용할 토큰 API의 버전입니다. 현재 "2017-09-01"만 제공 하는 Linux 사용을 사용 하지 않는 경우 "2019-08-01" 이상을 사용 하세요. 위의 note를 참조 하세요.                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | 헤더 | IDENTITY_HEADER 환경 변수의 값입니다. 이 헤더는 SSRF(서버 쪽 요청 위조) 공격을 완화하는 데 사용됩니다.                                                                                                                                                                                                    |
> | client_id         | 쿼리  | (선택 사항) 사용할 사용자 할당 ID의 클라이언트 ID입니다. `principal_id`, `mi_res_id` 또는 `object_id`를 포함하는 요청에 사용할 수 없습니다. 모든 ID 매개 변수(`client_id`, `principal_id`, `object_id` 및 `mi_res_id`)가 생략될 경우 시스템 할당 ID가 사용됩니다.                                             |
> | principal_id      | 쿼리  | (선택 사항) 사용할 사용자 할당 ID의 보안 주체 ID입니다. `object_id`는 대신 사용할 수 있는 별칭입니다. client_id, mi_res_id 또는 object_id를 포함하는 요청에 사용할 수 없습니다. 모든 ID 매개 변수(`client_id`, `principal_id`, `object_id` 및 `mi_res_id`)를 생략하면 시스템 할당 ID가 사용됩니다. |
> | mi_res_id         | 쿼리  | (선택 사항) 사용할 사용자 할당 ID의 Azure 리소스 ID입니다. `principal_id`, `client_id` 또는 `object_id`를 포함하는 요청에 사용할 수 없습니다. 모든 ID 매개 변수(`client_id`, `principal_id`, `object_id` 및 `mi_res_id`)를 생략하면 시스템 할당 ID가 사용됩니다.                                      |

> [!IMPORTANT]
> 사용자 할당 ID에 대한 토큰을 가져오려고 시도하는 경우 선택적 속성 중 하나를 포함해야 합니다. 그러지 않으면 토큰 서비스는 시스템 할당 ID에 대한 토큰을 가져오려고 시도하는데 이는 존재 여부가 불분명합니다.

성공적인 200 OK 응답에는 다음 속성을 가진 JSON 본문이 포함됩니다.

> | 속성 이름 | 설명                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | 요청된 액세스 토큰입니다. 호출 웹 서비스는 이 토큰을 사용하여 수신 웹 서비스에 인증할 수 있습니다.                                                                                                                               |
> | client_id     | 사용된 ID의 클라이언트 ID입니다.                                                                                                                                                                                                       |
> | expires_on    | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함).                                                                                |
> | not_before    | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함).                                                      |
> | resource      | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다.                                                                                                                               |
> | token_type    | 토큰 유형 값을 나타냅니다. Azure AD는 FBearer 유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |

이 응답은 [Azure AD 서비스 간 액세스 토큰 요청에 대한 응답](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)과 동일합니다.

### <a name="rest-protocol-examples"></a>REST 프로토콜 예제

예제 요청은 다음과 유사할 수 있습니다.

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

샘플 응답은 다음과 유사할 수 있습니다.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>코드 예제

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> .NET 언어의 경우 이 요청을 직접 만드는 대신 [Microsoft.Azure.Services.AppAuthentication](#asal)을 사용해도 됩니다.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>.NET용 Microsoft.Azure.Services.AppAuthentication 라이브러리 사용

.NET 애플리케이션 및 함수의 경우 관리 ID를 사용하는 가장 간단한 방법은 Microsoft.Azure.Services.AppAuthentication 패키지를 사용하는 것입니다. 또한 이 라이브러리는 개발 머신에서 Visual Studio, [Azure CLI](/cli/azure) 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 로컬로 코드를 테스트할 수 있습니다. 클라우드에서 호스트 되는 경우 기본적으로 시스템 할당 id를 사용 하지만 사용자 할당 id의 클라이언트 ID를 참조 하는 연결 문자열 환경 변수를 사용 하 여이 동작을 사용자 지정할 수 있습니다. 이 라이브러리를 사용 하는 개발 옵션에 대 한 자세한 내용은 [Microsoft. Azure]. 이 섹션에서는 코드에서 이 라이브러리를 시작하는 방법을 보여 줍니다.

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 기타 필요한 NuGet 패키지에 대한 참조를 애플리케이션에 추가합니다. 아래 예제에서는 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)도 사용합니다.

2. 애플리케이션에 다음 코드를 추가하고 올바른 리소스를 대상으로 하도록 수정합니다. 이 예제에서는 Azure Key Vault를 사용하는 두 가지 방법을 보여 줍니다.

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

사용자 할당 관리 id를 사용 하려는 경우 `AzureServicesAuthConnectionString` 응용 프로그램 설정을로 설정할 수 있습니다 `RunAs=App;AppId=<clientId-guid>` . 을 `<clientId-guid>` 사용 하려는 id의 클라이언트 id로 바꿉니다. 사용자 지정 응용 프로그램 설정을 사용 하 고 해당 값을 AzureServiceTokenProvider 생성자에 전달 하 여 이러한 연결 문자열을 여러 개 정의할 수 있습니다.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

AzureServiceTokenProvider 및 노출 되는 작업을 구성 하는 방법에 대 한 자세한 내용은 [Microsoft Azure. 인증 참조] 및 [MSI .net 샘플을 사용 하는 App Service 및 keyvault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)를 참조 하세요.

### <a name="using-the-azure-sdk-for-java"></a>Java용 Azure SDK 사용

Java 애플리케이션 및 함수의 경우 [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java)를 통해 관리 ID를 사용하는 것이 가장 간단한 방법입니다. 이 섹션에서는 코드에서 이 라이브러리를 시작하는 방법을 보여 줍니다.

1. [Azure SDK 라이브러리](https://mvnrepository.com/artifact/com.microsoft.azure/azure)에 대한 참조를 추가합니다. Maven 프로젝트의 경우 프로젝트 POM 파일의 `dependencies` 섹션에 이 코드 조각을 추가할 수 있습니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. 인증에 `AppServiceMSICredentials` 개체를 사용합니다. 다음 예제에서는 이 메커니즘을 사용하여 Azure Key Vault 작업을 수행할 수 있는 방법을 보여 줍니다.

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>ID 제거

사용자 할당 ID는 포털, PowerShell 또는 CLI를 사용하여 생성할 때와 같은 방식으로 기능을 사용하지 않도록 설정하여 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 모든 id를 제거 하려면 id 유형을 "없음"으로 설정 합니다.

이런 방식으로 시스템 할당 ID를 제거하면 Azure AD에서도 삭제됩니다. 앱 리소스가 삭제될 때 시스템 할당 ID도 Azure AD에서 자동으로 제거됩니다.

[ARM 템플릿에서](#using-an-azure-resource-manager-template)모든 id를 제거 하려면 다음을 수행 합니다.

```json
"identity": {
    "type": "None"
}
```

Azure PowerShell의 모든 id를 제거 하려면 (Azure Functions에만 해당):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> 설정할 수 있는 애플리케이션 설정인 WEBSITE_DISABLE_MSI도 있으며 이것은 로컬 토큰 서비스를 비활성화합니다. 그러나 ID는 그대로 유지되고, 도구에는 여전히 관리 ID가 "on" 또는 "enabled"로 표시됩니다. 따라서 이 설정은 사용하지 않는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [관리 ID를 사용하여 SQL Database에 안전하게 액세스](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication 참조]: https://go.microsoft.com/fwlink/p/?linkid=862452
