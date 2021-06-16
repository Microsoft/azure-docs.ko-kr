---
title: 여러 리소스 인스턴스 만들기
description: ARM 템플릿(Azure Resource Manager 템플릿)을 만들어 여러 Azure 리소스 인스턴스를 만드는 방법을 알아봅니다.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b65e23b167e805f7c442b8867c8c335797af0664
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111893033"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스 만들기

ARM 템플릿(Azure Resource Manager 템플릿)을 반복하여 Azure 리소스의 여러 인스턴스를 만드는 방법을 알아봅니다. 이 자습서에서는 3개의 스토리지 계정 인스턴스를 만들도록 템플릿을 수정합니다.

![Azure Resource Manager가 여러 인스턴스를 생성하는 다이어그램](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

리소스 복사를 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager 도구 확장이 포함된 Visual Studio Code. [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 ARM 템플릿용 리포지토리입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 빠른 시작에서 사용되는 템플릿은 [표준 스토리지 계정 만들기](https://azure.microsoft.com/resources/templates/storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일** > **파일 열기** 를 차례로 선택합니다.
1. **파일 이름** 에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json
    ```

1. **열기** 를 선택하여 파일을 엽니다.
1. 템플릿에 정의된 `Microsoft.Storage/storageAccounts` 리소스가 있습니다. 이 템플릿을 [템플릿 참조](/azure/templates/Microsoft.Storage/storageAccounts)와 비교합니다. 템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
1. **파일** > **이름으로 저장** 을 차례로 선택하여 파일을 _azuredeploy.json_ 으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

기존 템플릿은 스토리지 계정 1개를 만듭니다. 스토리지 계정 3개를 만들도록 템플릿을 사용자 지정합니다.

Visual Studio Code에서 다음 4개의 변경 내용을 만듭니다.

![Azure Resource Manager가 여러 인스턴스 생성](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 스토리지 계정 리소스 정의에 `copy` 요소를 추가합니다. `copy` 요소에서 이 루프의 반복 횟수와 변수를 지정합니다. count 값은 양의 정수여야 하며 800을 초과할 수 없습니다.
2. `copyIndex()` 함수는 루프에서 현재 반복을 반환합니다. 인덱스를 이름 접두사로 사용합니다. `copyIndex()`는 0부터 시작합니다. 인덱스 값을 오프셋하려면 `copyIndex()` 함수에 값을 전달하면 됩니다. 예들 들어 `copyIndex(1)`입니다.
3. 더 이상 사용되지 않으므로 `variables` 요소를 삭제합니다.
4. `outputs` 요소를 삭제합니다. 더 이상 필요하지 않습니다.

완성된 템플릿은 다음과 같습니다.

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

여러 인스턴스를 만드는 방법에 대한 자세한 내용은 [ARM 템플릿의 리소스 반복](./copy-resources.md)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. [Azure Cloud Shell](https://shell.azure.com)에 로그인

1. 왼쪽 위 모서리에서 **PowerShell** 또는 **Bash**(CLI용)를 선택하여 기본 환경을 선택합니다. 전환하는 경우 셸을 다시 시작해야 합니다.

    ![Azure Portal Cloud Shell 업로드 파일](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **파일 업로드/다운로드** 를 선택한 다음, **업로드** 를 선택합니다. 이전 스크린샷을 참조하세요. 이전 섹션에서 저장한 파일을 선택합니다. 파일을 업로드한 후 `ls` 명령 및 `cat` 명령을 사용하여 파일이 성공적으로 업로드되었는지 확인할 수 있습니다.

1. Cloud Shell에서 다음 명령을 실행합니다. 탭을 선택하여 PowerShell 코드 또는 CLI 코드를 표시합니다.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

템플릿을 성공적으로 배포한 후에는 지정된 리소스 그룹에서 만든 세 개의 스토리지 계정을 표시할 수 있습니다. 스토리지 계정 이름을 템플릿의 이름 정의와 비교합니다.

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 세 개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 스토리지 계정 인스턴스를 만드는 방법을 배웠습니다. 다음 자습서에서는 여러 리소스 및 여러 리소스 종류가 있는 템플릿을 개발합니다. 일부 리소스에는 종속 리소스가 있습니다.

> [!div class="nextstepaction"]
> [종속 리소스 만들기](./template-tutorial-create-templates-with-dependent-resources.md)
