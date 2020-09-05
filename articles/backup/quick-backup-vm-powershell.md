---
title: 빠른 시작 - PowerShell을 사용하여 VM 백업
description: 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure Virtual Machines를 백업하는 방법을 알아봅니다.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4a97a084b11b34f97e0c020f367ec730d8dfb079
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075735"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>PowerShell을 사용하여 Azure에서 가상 머신 백업

[Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) 모듈은 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 사용됩니다.

[Azure Backup](backup-overview.md)은 온-프레미스 머신과 앱, Azure VM을 백업합니다. 이 문서에서는 AZ 모듈을 사용하여 Azure VM을 백업하는 방법을 보여 줍니다. 또는 [Azure CLI](quick-backup-vm-cli.md)를 사용하거나 [Azure Portal](quick-backup-vm-portal.md)에서 VM을 백업할 수 있습니다.

이 빠른 시작을 사용하면 기존 Azure VM에서 백업할 수 있습니다. VM을 생성해야 하는 경우 [Azure PowerShell을 사용하여 VM을 만들](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) 수 있습니다.

이 빠른 시작에는 Azure PowerShell AZ 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>로그인 및 등록

1. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```powershell
    Connect-AzAccount
    ```

2. Azure Backup을 처음 사용하는 경우 다음과 같이 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)를 사용하여 구독에서 Azure Recovery Service 공급자를 등록해야 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

[Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 Azure VM과 같은 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 백업 작업이 실행되면 Recovery Services 자격 증명 모음 내에 복구 지점이 만들어집니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

자격 증명 모음을 만드는 경우 다음을 수행합니다.

- 리소스 그룹 및 위치에 대해 백업하려는 VM의 리소스 그룹과 위치를 지정합니다.
- 이 [샘플 스크립트](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)를 사용하여 VM을 만든 경우 리소스 그룹은 **myResourceGroup**이고, VM은 ***myVM**이며, 리소스는 **WestEurope** 지역에 있습니다.
- Azure Backup은 백업된 데이터에 대한 스토리지를 자동으로 처리합니다. 기본적으로 자격 증명 모음은 [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md)를 사용합니다. 지역 중복은 주 지역에서 수백 마일 떨어져 있는 보조 Azure 지역에 백업된 데이터가 복제되도록 합니다.

이제 자격 증명 모음을 만듭니다.

1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)를 사용하여 자격 증명 모음을 만듭니다.

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. 다음과 같이 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)를 사용하여 자격 증명 모음 컨텍스트를 설정합니다.

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. 다음과 같이 [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)를 사용하여 자격 증명 모음의 스토리지 중복 구성(LRS/GRS)을 변경합니다.

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > 자격 증명 모음에 보호된 백업 항목이 없는 경우에만 스토리지 중복을 수정할 수 있습니다.

## <a name="enable-backup-for-an-azure-vm"></a>Azure VM에 백업 사용

Azure VM에 대해 백업을 사용하도록 설정하고 백업 정책을 지정합니다.

- 정책은 백업 실행 시기와 백업에서 만든 복구 지점에 대한 보존 기간을 정의합니다.
- 기본 보호 정책은 VM에 대한 백업을 하루에 한 번 실행하고, 만들어진 복구 지점을 30일 동안 유지합니다. 이 기본 정책을 사용하여 VM을 빠르게 보호할 수 있습니다.

다음과 같이 백업을 사용하도록 설정합니다.

1. 먼저, [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)를 사용하여 기본 정책을 설정합니다.

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection)을 사용하여 VM 백업을 사용하도록 설정합니다. 정책, 리소스 그룹 및 VM 이름을 지정합니다.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>백업 작업 시작

백업은 백업 정책에 지정된 일정에 따라 실행됩니다. 다음과 같이 주문형 백업을 실행할 수도 있습니다.

- 첫 번째 초기 백업 작업에서는 전체 복구 지점이 만들어집니다.
- 초기 백업 후에는 각 백업 작업에서 증분 복구 지점이 만들어집니다.
- 증분 복구 지점은 마지막 백업 이후 변경된 내용만을 전송하기 때문에 스토리지 및 시간 효율적입니다.

주문형 백업을 실행하려면 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)을 사용합니다.

- [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)를 사용하여 백업 데이터를 보관하는 컨테이너를 자격 증명 모음에 지정합니다.
- 백업할 각 VM은 항목으로 처리됩니다. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)을 사용하여 VM에 대한 정보를 가져옵니다.

주문형 백업 작업은 다음과 같이 실행합니다.

1. 컨테이너를 지정하고, VM 정보를 가져오고, 백업을 실행합니다.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. 첫 번째 백업 작업에서 전체 복구 지점을 만들므로 최대 20분을 기다려야 할 수도 있습니다. 다음 절차에서 설명한 대로 작업을 모니터링합니다.

## <a name="monitor-the-backup-job"></a>백업 작업 모니터링

1. [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob)을 실행하여 작업 상태를 모니터링합니다.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    출력은 다음 예제와 비슷합니다. 여기서는 작업 상태를 **InProgress**(진행 중)로 보여 줍니다.

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. 작업 상태가 **Completed**(완료)이면 VM이 보호되고 전체 복구 지점이 저장됩니다.

## <a name="clean-up-the-deployment"></a>배포 정리

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
