---
title: Azure 가상 컴퓨터에 대 한 선택적 디스크 백업 및 복원
description: 이 문서에서는 Azure 가상 컴퓨터 백업 솔루션을 사용 하 여 선택적 디스크 백업 및 복원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 12b5b4cd35d70d8ebbd6b269e82c46984652bd07
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961995"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Azure 가상 컴퓨터에 대 한 선택적 디스크 백업 및 복원

Azure Backup는 가상 컴퓨터 백업 솔루션을 사용 하 여 VM의 모든 디스크 (운영 체제 및 데이터)를 백업 하는 작업을 지원 합니다. 이제 선택적 디스크 백업 및 복원 기능을 사용 하 여 VM에서 데이터 디스크의 하위 집합을 백업할 수 있습니다. 이는 사용자의 백업 및 복원 요구 사항을 충족하는 능률적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함 된 디스크만 포함 됩니다. 이렇게 하면 복원 작업을 수행 하는 동안 지정 된 복구 지점에서 복원 된 디스크의 하위 집합을 사용할 수 있습니다. 이는 스냅숏과 자격 증명 모음에서의 복원 모두에 적용 됩니다.

>[!NOTE]
>Azure 가상 컴퓨터에 대 한 선택적 디스크 백업 및 복원은 모든 지역에서 공개 미리 보기로 제공 됩니다.

## <a name="scenarios"></a>시나리오

이 솔루션은 특히 다음과 같은 시나리오에서 유용 합니다.

1. 백업 저장소 비용을 최소화 하기 위해 VM에 연결 된 디스크의 나머지 부분을 백업 하지 않으려는 경우, 하나의 디스크 또는 디스크의 하위 집합에만 백업할 중요 한 데이터가 있는 경우
2. VM 또는 데이터의 일부에 대 한 다른 백업 솔루션이 있는 경우 예를 들어 다른 워크 로드 백업 솔루션을 사용 하 여 데이터베이스나 데이터를 백업 하 고 나머지 데이터 또는 디스크에 대해 Azure VM 수준 백업을 사용 하 여 사용 가능한 최상의 기능을 통해 효율적이 고 강력한 시스템을 구축 하려는 경우

PowerShell 또는 Azure CLI를 사용 하 여 Azure VM의 선택적 디스크 백업을 구성할 수 있습니다.  스크립트를 사용 하 여 LUN 번호를 사용 하 여 데이터 디스크를 포함 하거나 제외할 수 있습니다.  현재 Azure Portal를 통해 선택적 디스크 백업을 구성 하는 기능은 **OS 디스크만 백업** 옵션으로 제한 됩니다. 따라서 OS 디스크를 사용 하 여 Azure VM의 백업을 구성 하 고 연결 된 모든 데이터 디스크를 제외할 수 있습니다.

>[!NOTE]
> OS 디스크는 기본적으로 VM 백업에 추가 되며 제외할 수 없습니다.

## <a name="using-azure-cli"></a>Azure CLI 사용

Az CLI version 2.0.80 이상을 사용 하 고 있는지 확인 합니다. 다음 명령을 사용 하 여 CLI 버전을 가져올 수 있습니다.

```azurecli
az --version
```

Recovery Services 자격 증명 모음 및 VM이 있는 구독 ID에 로그인 합니다.

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>아래 각 명령에는 자격 증명 모음에 해당 하는 **resourcegroup** 이름 (개체가 아님)만 필요 합니다.

### <a name="configure-backup-with-azure-cli"></a>Azure CLI를 사용 하 여 백업 구성

보호 구성 작업 중에 포함 제외 매개 변수 **를 사용**하 여 디스크 목록 설정을 지정 하 여  /  **exclusion** 백업에 포함 하거나 제외할 디스크의 LUN 번호를 제공 해야 합니다.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

VM이 자격 증명 모음과 동일한 리소스 그룹에 없는 경우 **ResourceGroup** 은 자격 증명 모음이 만들어진 리소스 그룹을 참조 합니다. VM 이름 대신 아래에 표시 된 대로 VM ID를 제공 합니다.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Azure CLI를 사용 하 여 이미 백업 된 Vm에 대 한 보호 수정

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Azure CLI를 사용 하 여 백업을 구성 하는 동안 OS 디스크만 백업

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Azure CLI를 사용 하 여 보호를 수정 하는 동안 OS 디스크만 백업

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Azure CLI를 사용 하 여 디스크 복원

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Azure CLI를 사용 하 여 OS 디스크만 복원

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Azure CLI를 사용 하 여 디스크 제외 정보를 가져오기 위해 보호 된 항목을 가져옵니다.

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

아래와 같이 보호 된 항목에 추가 **diskExclusionProperties** 매개 변수가 추가 되었습니다.

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Azure CLI를 사용 하 여 백업 작업 가져오기

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

이 명령은 아래와 같이 백업 된 디스크 및 제외 된 디스크의 세부 정보를 가져오는 데 도움이 됩니다.

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Azure CLI 복구 지점의 목록 표시

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

이렇게 하면 VM에 연결 되 고 백업 된 디스크 수에 대 한 정보가 제공 됩니다.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Azure CLI를 사용 하 여 복구 지점 가져오기

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

각 복구 지점에 포함 된 디스크와 제외 된 디스크의 정보는 다음과 같습니다.

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Azure CLI를 사용 하 여 디스크 제외 설정 제거 및 보호 된 항목 가져오기

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

이러한 명령을 실행 하면이 표시 됩니다 `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>PowerShell 사용

Azure PowerShell 버전 3.7.0 이상을 사용 하 고 있는지 확인 합니다.

### <a name="enable-backup-with-powershell"></a>PowerShell을 사용 하 여 백업 사용

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>PowerShell을 사용 하 여 백업을 구성 하는 동안 OS 디스크만 백업

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>PowerShell을 사용 하 여 수정 방지에서 전달할 백업 항목 개체 가져오기

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

위의 가져온 **$item** 개체를 다음 cmdlet의 **– item** 매개 변수에 전달 해야 합니다.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>PowerShell을 사용 하 여 이미 백업 된 Vm에 대 한 보호 수정

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>PowerShell을 사용 하 여 보호를 수정 하는 동안 OS 디스크만 백업

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>PowerShell을 사용 하 여 디스크 제외 설정 다시 설정

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>PowerShell을 사용 하 여 선택적 디스크 복원

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>PowerShell을 사용 하 여 OS 디스크만 복원

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Portal를 사용 하 여 VM 백업 세부 정보 창 및 백업 작업 세부 정보 창에서 포함 및 제외 된 디스크를 볼 수 있습니다.  복원 하는 동안 복원할 복구 지점을 선택 하면 해당 복구 지점에서 백업 된 디스크를 볼 수 있습니다.

여기서 VM 백업 세부 정보 창에서 포털의 가상 컴퓨터에 대 한 포함 및 제외 된 디스크를 볼 수 있습니다.

![백업 세부 정보 창에서 포함 및 제외 된 디스크 보기](./media/selective-disk-backup-restore/backup-details.png)

여기에서 작업 세부 정보 창의 백업에서 포함 및 제외 된 디스크를 볼 수 있습니다.

![작업 세부 정보 창에서 포함 및 제외 된 디스크 보기](./media/selective-disk-backup-restore/job-details.png)

여기서 복원할 복구 지점을 선택할 때 복원 중에 백업 된 디스크를 볼 수 있습니다.

![복원 중 백업 디스크 보기](./media/selective-disk-backup-restore/during-restore.png)

Azure Portal를 통해 VM에 대 한 선택적 디스크 백업 환경을 구성 하는 것은 **OS 디스크만 백업** 옵션으로 제한 됩니다. 이미 백업 된 VM에 대 한 선택적 디스크 백업을 사용 하거나 VM의 특정 데이터 디스크에 대 한 고급 포함 또는 제외를 사용 하려면 PowerShell 또는 Azure CLI을 사용 합니다.

>[!NOTE]
>데이터가 여러 디스크에 걸쳐 있는 경우 모든 종속 디스크가 백업에 포함 되어 있는지 확인 합니다. 볼륨의 모든 종속 디스크를 백업 하지 않는 경우 복원 중에 일부 백업 되지 않은 디스크로 구성 된 볼륨은 생성 되지 않습니다.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Azure Portal에서 OS 디스크만 백업

Azure Portal를 사용 하 여 백업을 사용 하도록 설정 하는 경우 **OS 디스크만 백업** 옵션을 선택할 수 있습니다. 따라서 OS 디스크를 사용 하 여 Azure VM의 백업을 구성 하 고 연결 된 모든 데이터 디스크를 제외할 수 있습니다.

![OS 디스크만 백업 구성](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="selective-disk-restore"></a>선택적 디스크 복원

선택적 디스크 복원은 선택적 디스크 백업 기능을 사용 하도록 설정할 때 제공 되는 추가 기능입니다. 이 기능을 사용 하면 복구 지점에 백업 된 모든 디스크에서 선택적 디스크를 복원할 수 있습니다. 더 효율적 이며 복원 해야 하는 디스크를 아는 시나리오에서 시간을 절약할 수 있습니다.

- OS 디스크는 VM 백업 및 복원에 기본적으로 포함 되어 있으며 제외할 수 없습니다.
- 선택적 디스크 복원은 디스크 제외 기능을 사용 하도록 설정한 후 생성 된 복구 지점만 지원 됩니다.
- 디스크 **제외 설정을 사용** 하는 백업은 **디스크 복원** 옵션만 지원 합니다. 이 경우 **VM 복원** 또는 기존 복원 옵션 **바꾸기** 는 지원 되지 않습니다.

![복원 작업을 수행 하는 동안 VM을 복원 하는 옵션과 기존을 바꿀 수 없음](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>제한 사항

선택적 디스크 백업 기능은 클래식 가상 머신 및 암호화 된 가상 머신에 대해 지원 되지 않습니다. 따라서 Windows VM 암호화에 BitLocker를 사용 하 여 Azure Disk Encryption (ADE)로 암호화 된 Azure Vm 및 Linux Vm의 dm 기능을 지원 하지 않습니다.

선택적 디스크 백업 기능이 사용 하도록 설정 된 VM에 대해서는 **새 vm을 만들고** **기존 기존을 바꾸는** 복원 옵션이 지원 되지 않습니다.

## <a name="billing"></a>결제

Azure 가상 머신 백업은 [여기](https://azure.microsoft.com/pricing/details/backup/)에 자세히 설명 된 기존 가격 책정 모델을 따릅니다.

**Os** 디스크만 사용 옵션을 백업 하도록 선택한 경우에만 os 디스크에 대해 **PI (Protected Instance) 비용이** 계산 됩니다.  백업을 구성 하 고 하나 이상의 데이터 디스크를 선택 하는 경우에는 VM에 연결 된 모든 디스크에 대해 PI 비용이 계산 됩니다. **백업 저장소 비용은** 포함 된 디스크만을 기준으로 계산 되므로 저장소 비용을 절약 하는 데 도움이 됩니다. **스냅숏 비용은** VM의 모든 디스크 (포함 된 디스크와 제외 된 디스크 모두)에 대해 항상 계산 됩니다.  

## <a name="next-steps"></a>다음 단계

- [Azure VM 백업의 지원 매트릭스](backup-support-matrix-iaas.md)
- [질문과 대답-Azure Vm 백업](backup-azure-vm-backup-faq.md)
