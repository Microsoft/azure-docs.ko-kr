---
title: 구독에 리소스 배포
description: Azure Resource Manager 템플릿에서 리소스 그룹을 만드는 방법을 설명합니다. 또한 Azure 구독 범위에서 리소스를 배포하는 방법도 보여 줍니다.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 50db0b4d46ff4e367411829aa75fa017a168372f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207658"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>구독 수준에서 리소스 그룹 및 리소스 만들기

일반적으로 Azure 리소스는 Azure 구독의 리소스 그룹에 배포합니다. 그러나 구독 수준에서 리소스를 만들 수도 있습니다. 구독 수준 배포를 사용 하 여 리소스 그룹을 만들고 [역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 할당 하는 등 해당 수준에서 적합 한 작업을 수행할 수 있습니다.

구독 수준에서 템플릿을 배포 하려면 Azure CLI, PowerShell 또는 REST API를 사용 합니다. Azure Portal은 구독 수준의 배포를 지원하지 않습니다.

## <a name="supported-resources"></a>지원되는 리소스

구독 수준에서 다음과 같은 리소스 유형을 배포할 수 있습니다.

* [배포](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>스키마

구독 수준 배포에 사용 하는 스키마는 리소스 그룹 배포에 대 한 스키마와 다릅니다.

템플릿의 경우 다음을 사용 합니다.

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

매개 변수 파일의 경우 다음을 사용 합니다.

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>배포 명령

구독 수준 배포에 대 한 명령은 리소스 그룹 배포에 대 한 명령과 다릅니다.

Azure CLI의 경우 [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)를 사용 합니다. 다음 예제에서는 템플릿을 배포 하 여 리소스 그룹을 만듭니다.

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


PowerShell 배포 명령의 경우 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)를 사용합니다. 다음 예제에서는 템플릿을 배포 하 여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API의 경우 [배포-구독 범위에서 만들기](/rest/api/resources/deployments/createorupdateatsubscriptionscope)를 사용 합니다.

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

구독 수준 배포의 경우 배포 위치를 제공 해야 합니다. 배포 위치는 배포 하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정 합니다.

배포 이름을 제공 하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

각 배포 이름에는 위치를 변경할 수 없습니다. 다른 위치에 동일한 이름의 기존 배포가 있는 경우 한 위치에 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="use-template-functions"></a>템플릿 함수 사용

구독 수준 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [reference()](template-functions-resource.md#reference) 및 [list()](template-functions-resource.md#list) 함수는 지원됩니다.
* [resourceId()](template-functions-resource.md#resourceid) 함수는 지원됩니다. 이를 사용하여 구독 수준 배포에 사용되는 리소스에 대한 리소스 ID를 가져옵니다. 리소스 그룹 매개 변수에 대 한 값을 제공 하지 마십시오.

  예를 들어 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.
  
  ```json
  resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  반환 된 리소스 ID 형식은 다음과 같습니다.

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

  또는 [Subscriptionresourceid ()](template-functions-resource.md#subscriptionresourceid) 함수를 사용 하 여 구독 수준 리소스의 리소스 ID를 가져옵니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

Azure Resource Manager 템플릿에서 리소스 그룹을 만들려면 리소스 그룹의 이름 및 위치를 사용하여 [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) 리소스를 정의합니다. 리소스 그룹을 만든 후 동일한 템플릿에서 해당 리소스 그룹에 리소스를 배포할 수 있습니다.

다음 템플릿은 빈 리소스 그룹을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

리소스 그룹을 2개 이상 만들려면 리소스 그룹에서 [요소 복사](copy-resources.md)를 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

리소스 반복에 대 한 자세한 내용은 [Azure Resource Manager 템플릿에 리소스의 둘 이상의 인스턴스 배포](./copy-resources.md)및 [자습서: 리소스 관리자 템플릿을 사용 하 여 여러 리소스 인스턴스 만들기](./template-tutorial-create-multiple-instances.md)를 참조 하세요.

## <a name="resource-group-and-resources"></a>리소스 그룹 및 리소스

리소스 그룹을 만들고 거기에 리소스를 배포하려면 중첩 템플릿을 사용합니다. 중첩 템플릿은 리소스 그룹에 배포할 리소스를 정의합니다. 리소스를 배포하기 전에 리소스 그룹이 존재하도록 중첩 템플릿을 리소스 그룹에 종속된 것으로 설정합니다.

다음 예제는 리소스 그룹을 만들고 스토리지 계정을 배포합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>정책 만들기

### <a name="assign-policy"></a>정책 할당

다음 예제는 기존 정책 정의를 구독에 할당합니다. 정책이 매개 변수를 사용하는 경우 개체로 제공합니다. 정책이 매개 변수를 사용하지 않으면 기본 빈 개체를 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Azure CLI를 사용하여 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>정책 정의 및 할당

정책은 같은 서식 파일에 [정의](../../governance/policy/concepts/definition-structure.md)하고 할당할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

구독에서 정책 정의를 만들어 구독에 적용하려면 다음 CLI 명령을 사용합니다.

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>템플릿 샘플

* 리소스 그룹을 만들고 잠그고 사용 권한을 부여 합니다. [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)를 참조하세요.
* 리소스 그룹, 정책 및 정책 할당을 만듭니다.  [여기](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 역할 할당에 대 한 자세한 내용은 [RBAC를 사용 하 여 Azure 리소스에 대 한 액세스 관리 및 템플릿 Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)을 참조 하세요.
* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* 샘플 템플릿은 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)에서 찾을 수 있습니다.
* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](template-syntax.md)을 참조하세요.
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](template-functions.md)를 참조하세요.
