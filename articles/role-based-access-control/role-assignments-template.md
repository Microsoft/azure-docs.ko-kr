---
title: Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 - Azure RBAC
description: Azure Resource Manager 템플릿 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID에 Azure 리소스 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f0368c3d2ee0132816852bfdf170700939bee46
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111949243"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Azure PowerShell 또는 Azure CLI를 사용하는 것 외에도 RBAC 및 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/syntax.md)을 사용하여 역할을 할당할 수 있습니다. 템플릿은 리소스를 일관되고 반복적으로 배포해야 하는 경우 유용할 수 있습니다. 이 문서에서는 템플릿을 사용하여 역할을 할당하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>개체 ID 가져오기

역할을 할당하려면 역할을 할당하려는 사용자, 그룹 또는 애플리케이션의 ID를 지정해야 합니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 ID를 가져올 수 있습니다.

### <a name="user"></a>사용자

사용자의 ID를 가져오려면 [AzADUser](/powershell/module/az.resources/get-azaduser) 또는 [az ad user show](/cli/azure/ad/user#az_ad_user_show) 명령을 사용할 수 있습니다.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>그룹

그룹의 ID를 가져오려면 [AzADGroup](/powershell/module/az.resources/get-azadgroup) 또는 [az ad group show](/cli/azure/ad/group#az_ad_group_show) 명령을 사용할 수 있습니다.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>관리 ID

관리 ID의 ID를 가져오려면 [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) 또는 [az ad sp](/cli/azure/ad/sp) 명령을 사용하면 됩니다.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>애플리케이션

서비스 주체의 ID(애플리케이션에서 사용하는 ID)를 가져오려면 [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) 또는 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 명령을 사용할 수 있습니다. 서비스 주체의 경우 애플리케이션 ID가 **아니라** 개체 ID를 사용합니다.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Azure 역할 할당

Azure RBAC에서 액세스 권한을 부여하기 위해 역할을 할당합니다.

### <a name="resource-group-scope-without-parameters"></a>리소스 그룹 범위(매개 변수 없음)

다음 템플릿에서는 역할을 할당하는 기본 방법을 보여줍니다. 일부 값은 템플릿 내에서 지정됩니다. 다음 템플릿은 다음을 보여줍니다.

-  리소스 그룹 범위에서 사용자, 그룹 또는 애플리케이션에 [읽기 권한자](built-in-roles.md#reader) 역할을 할당하는 방법

템플릿을 사용하려면 다음을 수행해야 합니다.

- 새 JSON 파일을 만들고 템플릿 복사
- `<your-principal-id>`를 역할을 할당할 사용자, 그룹, 관리 ID 또는 애플리케이션의 ID로 바꾸기

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

ExampleGroup이라는 리소스 그룹에서 배포를 시작하는 방법에 대한 예제 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

다음에서는 템플릿을 배포한 후 리소스 그룹에 대한 읽기 권한자 역할을 사용자에게 할당하는 예를 보여 줍니다.

![리소스 그룹 범위의 역할 할당](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>리소스 그룹 또는 구독 범위

이전 템플릿은 그리 유연하지 않습니다. 다음 템플릿에서는 매개 변수를 사용하고 다양한 범위에서 사용할 수 있습니다. 다음 템플릿은 다음을 보여줍니다.

- 리소스 그룹 또는 구독 범위에서 사용자, 그룹 또는 애플리케이션에 역할을 할당하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 역할을 할당할 사용자, 그룹, 관리 ID 또는 애플리케이션의 ID
- 역할 할당에 사용되는 고유 ID. 기본 ID를 사용할 수도 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> 동일한 `roleNameGuid` 값이 템플릿의 각 배포에 대한 매개 변수로 제공되지 않는 한, 이 템플릿은 idempotent가 아닙니다. `roleNameGuid`를 제공하지 않으면 기본적으로 각 배포에 새 GUID가 생성되고 후속 배포는 `Conflict: RoleAssignmentExists` 오류로 인해 실패합니다.

역할 할당의 범위는 배포 수준에서 결정됩니다. 리소스 그룹 범위에서 배포를 시작하는 방법에 대한 예제 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

구독 범위에서 배포를 시작하고 위치를 지정하는 방법에 대한 예제 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 및 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>리소스 범위

리소스 수준에서 역할을 할당해야 하는 경우 역할 할당의 `scope` 속성을 리소스 이름으로 설정합니다.

다음 템플릿은 다음을 보여줍니다.

- 새 스토리지 계정을 만드는 방법
- 스토리지 계정 범위에서 사용자, 그룹 또는 애플리케이션에 역할을 할당하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 역할을 할당할 사용자, 그룹, 관리 ID 또는 애플리케이션의 ID

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

이전 템플릿을 배포하려면 리소스 그룹 명령을 사용합니다. 리소스 범위에서 배포를 시작하는 방법에 대한 예제 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

다음에서는 템플릿을 배포한 후 스토리지 계정에 대한 기여자 역할을 사용자에게 할당하는 예를 보여 줍니다.

![리소스 범위의 역할 할당](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>새 서비스 주체

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 관리 ID를 새로 만든 다음, 동일한 Azure Resource Manager 템플릿에서 해당 서비스 주체에 역할을 할당하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생하는 이유는 복제 지연 때문일 수 있습니다. 서비스 주체는 한 지역에 생성됩니다. 그러나 서비스 주체를 아직 복제하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다.

이 시나리오를 해결하려면 역할 할당을 만들 때 `principalType` 속성을 `ServicePrincipal`로 설정해야 합니다. 또한 역할 할당의 `apiVersion`을 `2018-09-01-preview` 이상으로 설정해야 합니다.

다음 템플릿은 다음을 보여줍니다.

- 새 관리 ID 서비스 주체를 만드는 방법
- `principalType`을 지정하는 방법
- 리소스 그룹 범위에서 해당 서비스 주체에 기여자 역할을 할당하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 관리 ID의 기본 이름. 기본 문자열을 사용할 수도 있습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

리소스 그룹 범위에서 배포를 시작하는 방법에 대한 예제 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

다음에서는 템플릿을 배포한 후 새 관리 ID 서비스 주체에 기여자 역할을 할당하는 예를 보여 줍니다.

![새 관리 ID 서비스 주체에 대한 역할 할당](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Portal을 사용하여 ARM 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [ARM 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/syntax.md)
- [구독 수준에서 리소스 그룹 및 리소스 만들기](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=rbac)