---
title: Azure 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 Azure Key Vault 및 비밀 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Azure Key Vault를 만들고 볼트에 비밀 추가 방법을 보여주는 빠른 시작입니다.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: cff67e99d40b092341e89c96a9f1e037d8572248
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690632"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Key Vault에서 비밀 설정 및 검색

[Azure Key Vault](../general/overview.md)는 키, 암호, 인증서 및 기타 비밀 등, 비밀에 안전한 자격 증명을 제공하는 클라우드 서비스입니다. 이 빠른 시작에서는 키 자격 증명 및 비밀을 만들기 위해 ARM 플랫폼(Azure Resource Manager 템플릿)을 배포하는 과정을 다루고 있습니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* 권한을 구성하려면 템플릿에 Azure AD 사용자 개체 ID가 필요합니다. 다음 절차는 개체 ID(GUID)를 가져옵니다.

    1. **사용해 보세요**를 선택하여 다음 Azure PowerShell 또는 Azure CLI 명령을 수행한 다음, 스크립트를 셸 창에 붙여넣습니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. 개체 ID를 기록해 둡니다. 이 빠른 시작의 다음 섹션에서 필요합니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-key-vault-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): Azure 키 자격 증명 모음을 만듭니다.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): 키 자격 증명 모음 비밀을 만듭니다.

추가 Azure Key Vault 템플릿 샘플은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 키 자격 증명 모음 및 비밀이 생성됩니다.

    [![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    ![ARM 템플릿, Key Vault 통합, 배포 포털](../media/quick-create-template/create-key-vault-using-template-portal.png)

    지정되지 않은 경우 기본 값을 사용하여 키 자격 증명 모음과 비밀을 만듭니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹에 고유한 이름을 입력한 다음, **확인**을 클릭합니다.
    * **위치**: 위치를 선택합니다. 예: **미국 중부**
    * **Key Vault 이름**: .vault.azure.net 네임스페이스 내에서 전역적으로 고유한 키 자격 증명 모음 이름을 입력합니다. 다음 섹션에서 배포의 유효성을 검사할 때 이 이름이 필요합니다.
    * **테넌트 ID**: 템플릿 함수가 자동으로 테넌트 ID를 검색합니다. 기본값을 변경하지 마세요.
    * **AD 사용자 ID**: [필수 조건](#prerequisites)에서 검색한 Azure AD 사용자 개체 ID를 입력합니다.
    * **비밀 이름**: 키 자격 증명 모음에 저장하는 비밀의 이름을 입력합니다. 예: **adminpassword**.
    * **비밀 값**: 비밀 값을 입력합니다. 암호를 저장할 경우 필수 구성 요소에서 만든 생성된 암호를 사용하는 것이 좋습니다.
    * **위에 명시된 사용 약관에 동의함**: 선택합니다.
3. **구매**를 선택합니다. 키 자격 증명 모음이 성공적으로 배포되면 알림을 받게 됩니다.

    ![ARM 템플릿, Key Vault 통합, 배포 포털 알림](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 키 자격 증명 모음 및 비밀을 확인하거나 다음 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 생성된 비밀을 나열합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

출력은 다음과 비슷합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

![ARM 템플릿, Key Vault 통합, 배포 포털 유효성 검사 출력](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM 템플릿, Key Vault 통합, 배포 포털 유효성 검사 출력](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>리소스 정리

다른 Key Vault 빠른 시작과 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요 없으면 리소스 그룹을 삭제하고 Key Vault 및 관련 리소스를 삭제합니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 키 자격 증명 모음 및 비밀을 만들고 배포의 유효성을 검사했습니다. Key Vault 및 Azure Resource Manager에 대한 자세한 내용은 아래 문서를 참조하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토
