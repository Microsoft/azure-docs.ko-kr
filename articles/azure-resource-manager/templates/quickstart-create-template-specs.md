---
title: 템플릿 사양 만들기 및 배포
description: ARM 템플릿에서 템플릿 사양을 만드는 방법에 대해 알아봅니다. 그런 다음 구독에서 리소스 그룹에 템플릿 사양을 배포합니다.
author: tfitzmac
ms.date: 08/06/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 8fe9ec46050ad831430239b960a7f528af7f4dc2
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924328"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>빠른 시작: 템플릿 사양 만들기 및 배포(미리 보기)

이 빠른 시작에서는 [템플릿 사양](template-specs.md)에 Azure Resource Manager 템플릿(ARM 템플릿)을 패키징한 다음 해당 템플릿 사양을 배포하는 방법을 보여줍니다. 템플릿 사양에는 스토리지 계정을 배포하는 ARM 템플릿이 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> 템플릿 사양은 현재 미리 보기 상태입니다. 이를 사용하려면 [대기 목록에 등록](https://aka.ms/templateSpecOnboarding)해야 합니다.
>
> 대기 목록에서 승인되면 미리 보기 PowerShell 모듈을 설치하기 위한 지침을 볼 수 있습니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

템플릿 사양은 **Microsoft.Resources/templateSpecs**라는 새 리소스 형식입니다. 템플릿 사양을 만들면 Azure PowerShell 또는 ARM 템플릿을 사용할 수 있습니다. 모든 옵션에서 템플릿 사양에 패키징된 ARM 템플릿이 필요합니다.

PowerShell을 사용하면 ARM 템플릿이 명령에 매개 변수로 전달됩니다. ARM 템플릿을 사용하면 템플릿 사양에 패키징되는 ARM 템플릿이 템플릿 사양 정의에 포함됩니다.

아래에는 두 옵션이 모두 나와 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. PowerShell을 사용하여 템플릿 사양을 만들면 로컬 템플릿을 전달할 수 있습니다. 다음 템플릿을 복사하고 **azuredeploy.json**이라는 파일에 로컬로 저장합니다. 이 빠른 시작에서는 **c:\Templates\azuredeploy.json** 경로에 저장했지만 모든 경로를 사용할 수 있다고 가정합니다.

   :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 템플릿 사양을 포함할 새 리소스 그룹을 만듭니다.

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. 그런 다음 해당 리소스 그룹에 템플릿 사양을 만듭니다. 새 템플릿 사양에 **storageSpec**라는 이름을 지정합니다.

   ```powershell
   New-AzTemplateSpec `
     -ResourceGroupName templateSpecRG `
     -Name storageSpec `
     -Version "1.0" `
     -Location westus2 `
     -TemplateJsonFile "c:\Templates\azuredeploy.json"
   ```

# <a name="arm-template"></a>[ARM 템플릿](#tab/azure-resource-manager)

1. ARM 템플릿을 사용하여 템플릿 사양을 만들면 템플릿이 리소스 정의에 포함됩니다. 다음 템플릿을 복사하고 **azuredeploy.json**으로 로컬로 저장합니다. 이 빠른 시작에서는 **c:\Templates\azuredeploy.json** 경로에 저장했지만 모든 경로를 사용할 수 있다고 가정합니다.

   > [!NOTE]
   > 포함된 템플릿에서 모든 왼쪽 대괄호를 두 번째 왼쪽 대괄호로 이스케이프해야 합니다. `[` 대신 `[[`을(를) 사용합니다.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/templateSpecs",
               "apiVersion": "2019-06-01-preview",
               "name": "storageSpec",
               "location": "westus2",
               "properties": {
                   "displayName": "Storage template spec"
               },
               "tags": {},
               "resources": [
                   {
                       "type": "versions",
                       "apiVersion": "2019-06-01-preview",
                       "name": "1.0",
                       "location": "westus2",
                       "dependsOn": [ "storageSpec" ],
                       "properties": {
                           "template": {
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
                                       "defaultValue": "[[resourceGroup().location]",
                                       "metadata": {
                                           "description": "Location for all resources."
                                       }
                                   }
                               },
                               "variables": {
                                   "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                               },
                               "resources": [
                                   {
                                       "type": "Microsoft.Storage/storageAccounts",
                                       "apiVersion": "2019-04-01",
                                       "name": "[[variables('storageAccountName')]",
                                       "location": "[[parameters('location')]",
                                       "sku": {
                                           "name": "[[parameters('storageAccountType')]"
                                       },
                                       "kind": "StorageV2",
                                       "properties": {}
                                   }
                               ],
                               "outputs": {
                                   "storageAccountName": {
                                       "type": "string",
                                       "value": "[[variables('storageAccountName')]"
                                   }
                               }
                           }
                       },
                       "tags": {}
                   }
               ]
           }
       ],
       "outputs": {}
   }
   ```

1. Azure CLI 또는 PowerShell을 사용하여 새 리소스 그룹을 만듭니다.

   ```azurecli
   az group create \
     --name templateSpecRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

   ```azurecli
   az deployment group create \
     --name templateSpecRG \
     --template-file "c:\Templates\azuredeploy.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName templateSpecRG `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

이제 템플릿 사양을 배포할 수 있습니다. 템플릿 사양 배포는 템플릿 사양의 리소스 ID를 전달하는 것을 제외하고 포함된 템플릿을 배포하는 것과 같습니다. 동일한 배포 명령을 사용하고 필요한 경우 템플릿 사양에 대한 매개 변수 값을 전달합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 새 스토리지 계정을 포함하는 리소스 그룹을 만듭니다.

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. 템플릿 사양의 리소스 ID를 가져옵니다.

   ```azurepowershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Version.Id
   ```

1. 템플릿 사양을 배포합니다.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG
   ```

1. ARM 템플릿과 정확히 동일한 매개 변수를 제공합니다. 스토리지 계정 유형에 대한 매개 변수를 사용하여 템플릿 사양을 다시 배포합니다.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -StorageAccountType Standard_GRS
   ```

# <a name="arm-template"></a>[ARM 템플릿](#tab/azure-resource-manager)

1. 다음 템플릿을 복사하고 **storage.json**이라는 파일에 로컬로 저장합니다.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/deployments",
               "apiVersion": "2020-06-01",
               "name": "demo",
               "properties": {
                   "templateLink": {
                       "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                   },
                   "parameters": {
                   },
                   "mode": "Incremental"
               }
           }
       ],
       "outputs": {}
   }
   ```

1. Azure CLI 또는 PowerShell을 사용하여 스토리지 계정에 대한 새 리소스 그룹을 만듭니다.

   ```azurecli
   az group create \
     --name storageRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

   ```azurecli
   az deployment group create \
     --name storageRG \
     --template-file "c:\Templates\storage.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName storageRG `
     -TemplateFile "c:\Templates\storage.json"
   ```

---

## <a name="grant-access"></a>액세스 권한 부여

조직의 다른 사용자가 템플릿 사양을 배포하도록 하려면 해당 사용자에게 읽기 액세스 권한을 부여해야 합니다. 공유하려는 템플릿 사양이 포함된 리소스 그룹에 대한 Azure AD 그룹에 읽기 권한자 역할을 할당할 수 있습니다. 자세한 내용은 [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 그룹 액세스 권한 부여](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 배포한 리소스를 정리하려면 만든 리소스 그룹을 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 리소스 그룹을 선택합니다.

1. 이름으로 필터링 필드에 리소스 그룹 이름(templateSpecRG 및 storageRG)을 입력합니다.

1. 해당 리소스 그룹 이름을 선택합니다.

1. 위쪽 메뉴에서 리소스 그룹 삭제를 선택합니다.

## <a name="next-steps"></a>다음 단계

연결된 템플릿을 포함하는 템플릿 사양을 만드는 방법에 대한 자세한 내용은 [연결된 템플릿의 템플릿 사양 만들기](template-specs-create-linked.md)를 참조하세요.
