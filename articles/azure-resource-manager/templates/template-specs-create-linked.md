---
title: 연결 된 템플릿을 사용 하 여 템플릿 사양 만들기
description: 연결 된 템플릿으로 템플릿 사양을 만드는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: f1808be73981c3ab4d53fd2a651822b93b5fb790
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228004"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>자습서: 연결 된 템플릿을 사용 하 여 템플릿 사양 만들기 (미리 보기)

[연결 된 템플릿을](linked-templates.md#linked-template)사용 하 여 [템플릿 사양을](template-specs.md) 만드는 방법에 대해 알아봅니다. 템플릿 사양을 사용 하 여 조직 내 다른 사용자와 ARM 템플릿을 공유 합니다. 이 문서에서는 `relativePath` [배포 리소스](/azure/templates/microsoft.resources/deployments)의 새 속성을 사용 하 여 주 템플릿과 연결 된 템플릿을 패키징하는 템플릿 사양을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> 템플릿 사양은 현재 미리 보기 상태입니다. 이를 사용하려면 [미리 보기에 가입](https://aka.ms/templateSpecOnboarding)해야 합니다.

## <a name="create-linked-templates"></a>연결된 템플릿 만들기

주 템플릿과 연결 된 템플릿을 만듭니다.

템플릿을 연결 하려면 [배포 리소스](/azure/templates/microsoft.resources/deployments) 를 기본 템플릿에 추가 합니다. 속성에서 `templateLink` 부모 템플릿의 경로에 따라 연결 된 템플릿의 상대 경로를 지정 합니다.

연결 된 템플릿은 ** 에서linkedTemplate.js**이라고 하며 주 템플릿이 저장 된 경로에서 **아티팩트** 라는 하위 폴더에 저장 됩니다.  RelativePath에 대해 다음 값 중 하나를 사용할 수 있습니다.

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`속성은가 선언 된 템플릿 파일을 기준으로 합니다. `relativePath` 따라서의 linkedTemplate.js에서 호출 되 고 linkedTemplate2.js의 다른 linkedTemplate2.js이 동일한 아티팩트 하위 폴더에 저장 된 경우에는 linkedTemplate.js에 지정 된 relativePath만이 됩니다 `linkedTemplate2.json` .

1. 다음 JSON을 사용 하 여 기본 템플릿을 만듭니다. 주 템플릿을 로컬 컴퓨터에 **azuredeploy.js** 로 저장 합니다. 이 자습서에서는 **c:\Templates\linkedTS\azuredeploy.js** 경로에 저장 했지만 임의의 경로를 사용할 수 있다고 가정 합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion은 `Microsoft.Resources/deployments` 2020-06-01 이상 이어야 합니다.

1. 주 템플릿이 저장 된 폴더에 **아티팩트** 라는 디렉터리를 만듭니다.
1. 다음 JSON을 사용 하 여 연결 된 템플릿을 만듭니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. **아티팩트** 폴더에 **linkedTemplate.js** 로 템플릿을 저장 합니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

템플릿 사양은 리소스 그룹에 저장 됩니다.  리소스 그룹을 만든 후 다음 스크립트를 사용 하 여 템플릿 사양을 만듭니다. 템플릿 사양 이름은 **Webspec**입니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

작업이 완료 되 면 Azure Portal에서 템플릿 사양을 보거나 다음 cmdlet을 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

이제 템플릿 사양을 배포할 수 있습니다. 템플릿 사양 배포는 템플릿 사양의 리소스 ID를 전달하는 것을 제외하고 포함된 템플릿을 배포하는 것과 같습니다. 동일한 배포 명령을 사용하고 필요한 경우 템플릿 사양에 대한 매개 변수 값을 전달합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> 템플릿 사양 ID를 가져오고 Windows PowerShell에서 변수에 할당 하는 것과 관련 된 알려진 문제가 있습니다.

---

## <a name="next-steps"></a>다음 단계

템플릿 사양을 연결 된 템플릿으로 배포 하는 방법에 대 한 자세한 내용은 [자습서: 템플릿 사양을 연결 된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조 하세요.
