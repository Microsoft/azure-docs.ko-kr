---
title: RBAC 및 Azure Resource Manager 템플릿을 사용 하 여 역할 할당 추가
description: Azure RBAC (역할 기반 액세스 제어) 및 Azure Resource Manager 템플릿을 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138295"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Azure RBAC 및 Azure Resource Manager 템플릿을 사용 하 여 역할 할당 추가

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Azure PowerShell 또는 Azure CLI를 사용 하는 것 외에도 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/template-syntax.md)사용 하 여 역할을 할당할 수 있습니다. 템플릿은 리소스를 일관되고 반복적으로 배포해야 하는 경우 유용할 수 있습니다. 이 문서에서는 템플릿을 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

## <a name="get-object-ids"></a>개체 Id 가져오기

역할을 할당 하려면 역할을 할당 하려는 사용자, 그룹 또는 응용 프로그램의 ID를 지정 해야 합니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 ID를 가져올 수 있습니다.

### <a name="user"></a>사용자

사용자의 ID를 가져오려면 [AzADUser](/powershell/module/az.resources/get-azaduser) 또는 [az ad user show](/cli/azure/ad/user#az-ad-user-show) 명령을 사용할 수 있습니다.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>그룹

그룹의 ID를 가져오려면 [AzADGroup](/powershell/module/az.resources/get-azadgroup) 또는 [az ad group show](/cli/azure/ad/group#az-ad-group-show) 명령을 사용할 수 있습니다.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>응용 프로그램

서비스 사용자 (응용 프로그램에서 사용 하는 id)의 ID를 가져오려면 [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) 또는 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 명령을 사용할 수 있습니다. 서비스 사용자의 경우 응용 프로그램 ID가 **아니라** 개체 id를 사용 합니다.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여 하려면 역할 할당을 추가 합니다.

### <a name="resource-group-without-parameters"></a>리소스 그룹 (매개 변수 없음)

다음 템플릿에서는 역할 할당을 추가 하는 기본적인 방법을 보여 줍니다. 일부 값은 템플릿 내에서 지정 됩니다. 다음 템플릿은 다음을 보여줍니다.

-  리소스 그룹 범위에서 사용자, 그룹 또는 응용 프로그램에 [읽기 권한자](built-in-roles.md#reader) 역할을 할당 하는 방법

템플릿을 사용 하려면 다음을 수행 해야 합니다.

- 새 JSON 파일을 만들고 템플릿을 복사 합니다.
- `<your-principal-id>`를 역할을 할당할 사용자, 그룹 또는 응용 프로그램의 ID로 바꿉니다.

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

ExampleGroup 이라는 리소스 그룹에서 배포를 시작 하는 방법에 대 한 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령의 예는 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

다음은 템플릿을 배포한 후 리소스 그룹의 사용자에 대 한 읽기 권한자 역할 할당의 예를 보여 줍니다.

![리소스 그룹 범위에서 역할 할당](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>리소스 그룹 또는 구독

이전 템플릿은 매우 유연 하지 않습니다. 다음 템플릿에서는 매개 변수를 사용 하 고 다양 한 범위에서 사용할 수 있습니다. 다음 템플릿은 다음을 보여줍니다.

- 리소스 그룹 또는 구독 범위에서 사용자, 그룹 또는 응용 프로그램에 역할을 할당 하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 역할을 할당할 사용자, 그룹 또는 응용 프로그램의 ID입니다.
- 역할 할당에 사용 되는 고유 ID입니다. 또는 기본 ID를 사용할 수 있습니다.

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
> 동일한 `roleNameGuid` 값이 템플릿의 각 배포에 대 한 매개 변수로 제공 되지 않는 한이 템플릿은 idempotent 되지 않습니다. `roleNameGuid`를 제공 하지 않으면 기본적으로 각 배포에 새 GUID가 생성 되 고 후속 배포는 `Conflict: RoleAssignmentExists` 오류로 인해 실패 합니다.

역할 할당의 범위는 배포 수준에서 결정 됩니다. 다음은 리소스 그룹 범위에서 배포를 시작 하는 방법에 대 한 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령의 예입니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

다음은 구독 범위에서 배포를 시작 하 고 위치를 지정 하는 방법에 대 한 [AzDeployment](/powershell/module/az.resources/new-azdeployment) 및 [az deployment create](/cli/azure/deployment#az-deployment-create) 명령의 예입니다.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>리소스

리소스 수준에서 역할 할당을 추가 해야 하는 경우 역할 할당의 형식이 다릅니다. 역할을 할당할 리소스의 리소스 공급자 네임 스페이스 및 리소스 형식을 제공 합니다. 또한 역할 할당 이름에 리소스 이름을 포함 합니다.

역할 할당의 유형 및 이름에는 다음 형식을 사용 합니다.

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

다음 템플릿은 다음을 보여줍니다.

- 새 스토리지 계정을 만드는 방법
- 저장소 계정 범위에서 사용자, 그룹 또는 응용 프로그램에 역할을 할당 하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 역할을 할당할 사용자, 그룹 또는 응용 프로그램의 ID입니다.

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
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
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

이전 템플릿을 배포 하려면 리소스 그룹 명령을 사용 합니다. 다음은 리소스 범위에서 배포를 시작 하는 방법에 대 한 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령의 예입니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

다음은 템플릿을 배포한 후 사용자에 게 저장소 계정에 대 한 참가자 역할 할당의 예를 보여 줍니다.

![리소스 범위에서 역할 할당](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>새 서비스 사용자

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당 하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 관리 되는 id를 새로 만든 다음 동일한 Azure Resource Manager 템플릿에서 해당 서비스 주체에 역할을 할당 하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생 하는 이유는 복제 지연 일 수 있습니다. 서비스 사용자는 한 지역에 생성 됩니다. 그러나 서비스 사용자를 아직 복제 하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다. 이 시나리오를 해결 하려면 역할 할당을 만들 때 `principalType` 속성을 `ServicePrincipal`으로 설정 해야 합니다.

다음 템플릿은 다음을 보여줍니다.

- 새 관리 id 서비스 주체를 만드는 방법
- `principalType` 지정 하는 방법
- 리소스 그룹 범위에서 해당 서비스 사용자에 게 참가자 역할을 할당 하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 관리 id의 기본 이름 이거나, 기본 문자열을 사용할 수 있습니다.

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
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

다음은 리소스 그룹 범위에서 배포를 시작 하는 방법에 대 한 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령의 예입니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

다음은 템플릿을 배포한 후 새 관리 id 서비스 주체에 대 한 참가자 역할 할당의 예를 보여 줍니다.

![새 관리 되는 id 서비스 사용자에 대 한 역할 할당](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/template-syntax.md)
- [구독 수준에서 리소스 그룹 및 리소스 만들기](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=rbac)
