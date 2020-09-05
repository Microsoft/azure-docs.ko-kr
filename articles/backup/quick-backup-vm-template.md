---
title: 빠른 시작 - Resource Manager 템플릿 VM Backup
description: Azure Resource Manager 템플릿을 사용하여 가상 머신을 백업하는 방법을 알아봅니다.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826534"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure에서 가상 머신 백업

[Azure Backup](backup-overview.md)은 온-프레미스 머신과 앱, Azure VM을 백업합니다. 이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)과 Azure PowerShell을 사용하여 Azure VM을 백업하는 방법을 보여줍니다. 이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들기 위해 ARM 템플릿을 배포하는 과정에 중점을 둡니다. ARM 탬플릿 개발에 대한 자세한 내용은 [Azure Resource Manager 설명서](../azure-resource-manager/index.yml) 및 [템플릿 참조](/azure/templates/microsoft.recoveryservices/allversions)를 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 Azure VM과 같은 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 백업 작업이 실행되면 Recovery Services 자격 증명 모음 내에 복구 지점이 만들어집니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다. 또는 [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)를 사용하거나 [Azure Portal](quick-backup-vm-portal.md)에서 VM을 백업할 수 있습니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)에서 나온 것입니다. 이 템플릿을 사용하면 보호를 위해 DefaultPolicy로 구성된 간단한 Windows VM 및 Recovery Services 자격 증명 모음을 배포할 수 있습니다.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하려면 **사용해 보세요**를 선택하여 Azure Cloud Shell을 열고, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다. 코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

이 빠른 시작에서는 Azure PowerShell을 사용하여 ARM 템플릿을 배포합니다. [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Rest API](../azure-resource-manager/templates/deploy-rest.md)도 템플릿을 배포하는 데 사용할 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="start-a-backup-job"></a>백업 작업 시작

템플릿은 VM을 만들고 VM에서 다시 사용하도록 설정합니다. 템플릿을 배포한 후에는 백업 작업을 시작해야 합니다. 자세한 내용은 [백업 작업 시작](./quick-backup-vm-powershell.md#start-a-backup-job)을 참조하세요.

### <a name="monitor-the-backup-job"></a>백업 작업 모니터링

백업 작업을 모니터링 하려면 [백업 작업 모니터링](./quick-backup-vm-powershell.md#monitor-the-backup-job)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

VM을 더 이상 백업할 필요가 없으면 이를 정리할 수 있습니다.

- VM 복원을 시도하려면 정리 단계를 건너뜁니다.
- 기존 VM을 사용한 경우 마지막 단계인 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 건너뛰어 리소스 그룹과 VM을 그대로 유지할 수 있습니다.

보호 기능을 해제하고 복원 지점 및 자격 증명 모음을 제거합니다. 그런 다음, 리소스 그룹 및 연결된 VM 리소스를 다음과 같이 삭제합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만들었습니다.

- Azure Portal에서 VM을 백업하는 방법을 [알아봅니다](tutorial-backup-vm-at-scale.md).
- VM을 빠르게 복원하는 방법을 [알아봅니다](tutorial-restore-disk.md).
- ARM 템플릿을 만드는 [방법에 대해 알아봅니다](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
