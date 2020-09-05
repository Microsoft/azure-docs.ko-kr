---
title: Azure Resource Manager 템플릿을 사용하여 Azure Recovery Services 자격 증명 모음을 만들기 위한 빠른 시작입니다.
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Recovery Services 자격 증명 모음을 만드는 방법에 대해 알아봅니다.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684119"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Recovery Services 자격 증명 모음 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Recovery Services 자격 증명 모음을 설정하는 방법을 설명합니다. [Azure Site Recovery](site-recovery-overview.md) 서비스는 계획되거나 계획되지 않은 정전 중에도 비즈니스 애플리케이션을 온라인 상태로 유지되도록 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 복제, 장애 조치(failover) 및 복구를 포함하여 온-프레미스 머신 및 Azure VM(가상 머신)의 재해 복구를 관리합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

활성 Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만든 후에 시작할 수 있습니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/vaults): 자격 증명 모음을 만듭니다.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): 자격 증명 모음의 백업 중복 설정을 구성합니다.

템플릿에는 자격 증명 모음의 백업 구성에 대한 선택적 매개 변수가 포함되어 있습니다. 스토리지 중복 설정은 LRS(로컬 중복 스토리지) 또는 GRS(지역 중복 스토리지)입니다. 자세한 내용은 [스토리지 중복 설정](../backup/backup-create-rs-vault.md#set-storage-redundancy)을 참조하세요.

추가 Azure Recovery Services 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하려면 **구독**, **리소스 그룹**및 **자격 증명 모음 이름**이 필요합니다.

1. Azure에 로그인하고 템플릿을 열려면 **Azure에 배포** 이미지를 선택합니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. 다음 값을 선택하거나 입력합니다.

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Recovery Services 자격 증명 모음을 만드는 템플릿":::

   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: 기존 그룹을 선택하거나 **새로 만들기**를 선택하여 그룹을 추가합니다.
   - **위치**: 리소스 그룹의 위치를 기본값으로 설정하고 리소스 그룹을 선택한 후에는 사용할 수 없게 됩니다.
   - **자격 증명 모음 이름**: 자격 증명 모음의 이름을 입력합니다.
   - **스토리지 유형 변경**: 기본값은 **false**입니다. 자격 증명 모음의 스토리지 유형을 변경해야 하는 경우에만 **true**를 선택합니다.
   - **자격 증명 모음 스토리지 유형**: 기본값은 **GloballyRedundant**입니다. 스토리지 유형이 **true**로 설정된 경우 **LocallyRedundant**를 선택합니다.
   - **위치**: `[resourceGroup().location]` 함수는 기본적으로 리소스 그룹의 위치로 설정됩니다. 위치를 변경하려면 **미국 서부**와 같은 값을 입력합니다.
   - **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.

1. 자격 증명 모음의 배포를 시작하려면 **구매** 단추를 선택합니다. 성공적으로 배포된 후 알림이 표시됩니다.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="자격 증명 모음을 배포했습니다.":::

## <a name="validate-the-deployment"></a>배포 유효성 검사

자격 증명 모음이 생성되었는지 확인하려면 Azure CLI 또는 Azure PowerShell을 사용합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

다음 출력은 자격 증명 모음의 정보를 발췌한 것입니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>리소스 정리

새 리소스를 사용하려는 경우에는 필요한 작업이 없습니다. 그렇지 않으면 이 빠른 시작에서 만든 리소스 그룹 및 자격 증명 모음을 제거할 수 있습니다. 리소스 그룹 및 해당 리소스를 삭제하려면 Azure CLI 또는 Azure PowerShell을 사용합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들었습니다. 재해 복구에 대해 자세히 알아보려면 다음 빠른 시작 문서를 참조하세요.

> [!div class="nextstepaction"]
> [재해 복구 설정](azure-to-azure-quickstart.md)
