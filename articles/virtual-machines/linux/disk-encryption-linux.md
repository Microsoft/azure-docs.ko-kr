---
title: Linux VM에 대한 Azure Disk Encryption 시나리오
description: 이 문서에서는 다양한 시나리오를 위한 Linux VM용 Microsoft Azure Disk Encryption을 사용하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b7d19d782e9cb29cfc917293f084b78830db89bc
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797618"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM에 대한 Azure Disk Encryption 시나리오


Linux 가상 머신(VM)용 Azure Disk Encryption은 Linux의 DM-Crypt 기능을 사용하여 OS 디스크 및 데이터 디스크의 전체 디스크 암호화를 제공합니다. 또한 EncryptFormatAll 기능을 사용하는 경우, 임시 디스크의 암호화를 제공합니다.

Azure Disk Encryption은 [Azure Key Vault와 통합](disk-encryption-key-vault.md)되어 디스크 암호화 키와 비밀을 제어하고 관리하는 작업을 지원합니다. 서비스에 대한 개요는 [Linux VM용 Azure Disk Encryption](disk-encryption-overview.md)을 참조하세요.

디스크 암호화는 지원되는 VM [크기와 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)의 가상 머신에만 적용할 수 있습니다. 또한 다음과 같은 필수 조건도 충족해야 합니다.

- [VM에 대한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

모든 경우에서 [스냅샷을 만들거나](snapshot-copy-managed-disk.md) 또는 디스크를 암호화하기 전에 백업을 만들어야 합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 완료되면 [Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)을 사용하여 -skipVmBackup 매개 변수를 지정함으로써 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [Azure Backup](../../backup/backup-azure-vms-encryption.md) 문서를 참조하세요. 

>[!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 포함한 Azure Disk Encryption](disk-encryption-overview-aad.md)을 참조하세요. 
>
> - Linux OS 볼륨을 암호화하는 경우, VM은 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 할 모든 열린 파일을 차단하는 문제를 방지하려면 암호화가 진행 중일 때 SSH 로그인을 하지 말 것을 적극 권장합니다. 진행률을 확인하려면 [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet을 사용하거나 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 명령을 사용합니다. 이 프로세스는 30GB OS 볼륨을 처리하는 데 몇 시간이 걸리고 데이터 볼륨을 암호화하는 데 추가 시간이 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 encrypt format all 옵션이 사용되지 않는 한 데이터 볼륨의 크기 및 수량에 비례합니다. 
> - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다.  

## <a name="install-tools-and-connect-to-azure"></a>도구 설치 및 Azure에 연결

Azure Disk Encryption은 [Azure CLI](/cli/azure) 및 [Azure PowerShell](/powershell/azure/new-azureps-module-az)을 통해 사용하고 관리할 수 있습니다. 이렇게 하려면 도구를 로컬로 설치 하 고 Azure 구독에 연결 해야 합니다.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure)은 Azure 리소스를 관리하기 위한 명령줄 도구입니다. CLI는 데이터를 유연하게 쿼리하고, 장기 실행 작업을 비차단 프로세스로 지원하고, 쉽게 스크립팅할 수 있도록 설계되었습니다. [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)의 단계에 따라 로컬에 설치할 수 있습니다.

 

[Azure CLI에서 Azure 계정에 로그인](/cli/azure/authenticate-azure-cli)하려면 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용합니다.

```azurecli
az login
```

로그인할 테넌트를 선택하려면 다음을 사용합니다.
    
```azurecli
az login --tenant <tenant>
```

구독이 여러 개이고 특정 구독을 지정하려는 경우 [az account list](/cli/azure/account#az-account-list)를 사용하여 구독 목록을 가져오고 [az account set](/cli/azure/account#az-account-set)으로 지정합니다.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az)은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 모델을 사용하는 cmdlet 집합을 제공합니다. 이 모듈은 브라우저에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 통해 사용하거나 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)의 지침에 따라 로컬 머신에 설치할 수 있습니다. 

이미 로컬에 설치되어있는 경우 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성해야 합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases)를 다운로드합니다.

[Azure PowerShell을 사용하여 Azure 계정에 로그인](/powershell/azure/authenticate-azureps?view=azps-2.5.0)하려면 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용합니다.

```powershell
Connect-AzAccount
```

여러 구독이 있으며 그 중에서 하나의 구독을 지정하려면 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet을 사용하여 해당 구독을 나열한 다음, [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet을 사용합니다.

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet을 실행하면 올바른 구독이 선택되었는지 확인됩니다.

Azure Disk Encryption cmdlet이 설치되어 있는지 확인하려면 [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet을 사용합니다.
     
```powershell
Get-command *diskencryption*
```
자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>기존 또는 실행 중인 Linux VM에서 암호화를 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 가상 머신 확장에 대한 스키마 정보가 필요한 경우 [Linux용 Azure Disk Encryption 확장](../extensions/azure-disk-enc-linux.md) 문서를 참조하세요.

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅샷을 만들고 백업해야 합니다. 관리 디스크에 대한 스냅샷은 포털에서 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 통해 만들 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업을 만들면 Set-AzVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다. 백업이 만들어지고 이 매개 변수가 지정되지 않으면 관리 디스크 기반 VM에 대한 Set-AzVMDiskEncryptionExtension 명령은 실패하게 됩니다. 
>
>암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Azure CLI를 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용 

[Azure CLI](/cli/azure/?view=azure-cli-latest) 명령줄 도구를 설치하고 사용하여 암호화된 VHD에서 디스크 암호화를 사용할 수 있습니다. [Azure Cloud Shell](../../cloud-shell/overview.md)과 함께 브라우저에서 사용하거나 로컬 컴퓨터에 설치하여 PowerShell 세션에서 사용할 수 있습니다. Azure에서 기존 또는 실행 중인 Linux VM에 암호화를 사용하도록 설정하려면 다음 CLI 명령을 사용합니다.

Azure에서 [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) 명령을 사용하여 실행 중인 가상 머신에서 암호화를 사용하도록 설정합니다.

- **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br>
key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되어 있는지 확인:** VM의 암호화 상태를 확인하려면 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>PowerShell을 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용
Azure에서 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용하여 실행 중인 가상 머신에서 암호화를 사용하도록 설정합니다. 디스크를 암호화하기 전에 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 사용하여 [스냅샷](snapshot-copy-managed-disk.md)을 만들거나 VM을 백업하세요. 실행 중인 Linux VM을 암호화하기 위해 PowerShell 스크립트에 -skipVmBackup 매개 변수가 이미 지정되어 있습니다.

-  **실행 중인 VM 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 만들어졌습니다. MyVirtualMachineResourceGroup, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다. 암호화를 진행 중인 디스크를 지정하도록 -VolumeType 매개 변수를 수정합니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **KEK를 사용하여 실행 중인 VM 암호화:** OS 디스크가 아닌 데이터 디스크를 암호화하는 경우 -VolumeType 매개 변수를 추가해야 할 수도 있습니다. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 
    
- **디스크가 암호화되어 있는지 확인:** VM의 암호화 상태를 확인하려면 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용합니다. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>템플릿을 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용

[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)을 사용하여 Azure에서 기존 또는 실행 중인 Linux VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 매개 변수, 약관 및 규약을 선택합니다. **만들기**를 클릭하여 기존 또는 실행 중인 VM에서 암호화를 사용하도록 설정합니다.

다음 표에서는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수를 나열합니다.

| 매개 변수 | Description |
| --- | --- |
| vmName | 암호화 작업을 실행할 VM의 이름. |
| keyVaultName | 암호화 키가 업로드되어야 하는 키 자격 증명 모음의 이름. cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 또는 Azure CLI 명령 `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`을 사용하여 가져올 수 있습니다.|
| keyVaultResourceGroup | 키 자격 증명 모음을 포함하는 리소스 그룹의 이름. |
|  keyEncryptionKeyURL | 암호화 키를 암호화하는 데 사용되는 키 암호화 키의 URL. UseExistingKek 드롭다운 목록에서 **nokek**를 선택하면 이 매개 변수가 선택 사항입니다. UseExistingKek 드롭다운 목록에서 **kek**를 선택하면 _keyEncryptionKeyURL_ 값을 반드시 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. 
| forceUpdateTag | 작업을 강제로 실행해야 할 때마다 GUID 같은 고유한 값으로 전달합니다. |
| 위치 | 모든 리소스에 대한 위치. |

Linux VM 디스크 암호화 템플릿을 구성하는 방법에 대한 자세한 내용은 [Linux용 Azure Disk Encryption](../extensions/azure-disk-enc-linux.md)을 참조하세요.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Linux VM의 데이터 디스크에 EncryptFormatAll 기능 사용

**EncryptFormatAll** 매개 변수는 Linux 데이터 디스크가 암호화되는 시간을 줄여줍니다. 특정 기준을 충족하는 파티션은 현재 파일 시스템과 함께 형식이 지정된 다음, 명령 실행 전에 있던 위치로 되돌아가 다시 탑재됩니다. 조건을 충족하는 데이터 디스크를 제외하려면 명령을 실행하기 전에 분리할 수 있습니다.

 이 명령을 실행한 후에는 이전에 탑재된 모든 드라이브의 형식이 지정되며, 암호화 계층은 현재 비어 있는 드라이브를 기반으로 하여 시작됩니다. 이 옵션을 선택하면 VM에 연결된 임시 디스크도 암호화됩니다. 임시 디스크가 초기화되면 다음 기회에 Azure Disk Encryption 솔루션에서 VM용으로 서식이 다시 지정되고 다시 암호화됩니다. 리소스 디스크가 암호화되면 [Microsoft Azure Linux 에이전트](../extensions/agent-linux.md)에서 리소스 디스크를 관리할 수 없고 스왑 파일을 사용할 수 없으며 다만 스왑 파일을 수동으로 구성할 수는 있습니다.

>[!WARNING]
> VM의 데이터 볼륨에 필요한 데이터가 있는 경우 EncryptFormatAll을 사용하면 안됩니다. 디스크는 분리하여 암호화에서 제외할 수 있습니다. 프로덕션 VM에서 EncryptFormatAll을 시도하기 전에 먼저 테스트 VM에서 시도하여 이 기능 매개 변수와 그 의미를 이해해야 합니다. EncryptFormatAll 옵션은 데이터 디스크를 포맷하여 해당 데이터 디스크의 모든 데이터가 손실됩니다. 계속하기 전에 제외하려는 디스크가 제대로 분리되었는지 확인합니다. </br></br>
 >암호화 설정을 업데이트하는 동안 이 매개 변수를 설정하면 실제로 암호화되기 전에 다시 부팅될 수 있습니다. 이 경우 fstab 파일에서 포맷하지 않으려는 디스크를 제거하는 것도 좋습니다. 마찬가지로 암호화 작업을 시작하기 전에 암호화 형식으로 포맷하려는 파티션을 fstab 파일에 추가해야 합니다. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll 조건
매개 변수에서 아래 조건을 **모두** 충족하는 경우 모든 파티션으로 이동하여 암호화합니다.
- 루트/OS/부팅 파티션이 아닙니다.
- 아직 암호화되지 않았습니다.
- BEK 볼륨이 아닙니다.
- RAID 볼륨이 아닙니다.
- LVM 볼륨이 아닙니다.
- 탑재되어 있습니다.

RAID 볼륨 또는 LVM 볼륨이 아닌, RAID 볼륨 또는 LVM 볼륨을 구성하는 디스크를 암호화합니다.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Azure CLI에서 EncryptFormatAll 매개 변수 사용
Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 명령을 사용하여 실행 중인 가상 머신에서 암호화를 사용하도록 설정합니다.

-  **EncryptFormatAll을 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>PowerShell cmdlet에서 EncryptFormatAll 매개 변수 사용
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 EncryptFormatAll 매개 변수와 함께 사용합니다. 

**EncryptFormatAll을 사용하여 실행 중인 VM 암호화:** 예를 들어, 아래 스크립트는 변수를 초기화하고 EncryptFormatAll 매개 변수를 사용하여 Set-AzVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 만들어졌습니다. MyVirtualMachineResourceGroup, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>LVM(논리 볼륨 관리자)에서 EncryptFormatAll 매개 변수 사용 
LVM-on-crypt 설정을 사용하는 것이 좋습니다. 다음의 모든 예제에서 device-path와 mountpoint를 사용 사례에 맞는 것으로 바꿉니다. 이 설정은 다음과 같이 수행할 수 있습니다.

1.  VM을 구성할 데이터 디스크를 추가합니다.

1. 이러한 디스크를 포맷하고, 탑재하고, fstab 파일에 추가합니다.

1. 파티션 표준을 선택하고 전체 드라이브에 걸쳐 있는 파티션을 만든 다음, 파티션을 포맷합니다. 여기서는 Azure에서 생성한 바로 가기 링크를 사용합니다. 바로 가기 링크를 사용하면 디바이스 이름 변경과 관련된 문제를 방지할 수 있습니다. 자세한 내용은 [디바이스 이름 문제 해결](../troubleshooting/troubleshoot-device-names-problems.md) 문서를 참조하세요.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. 디스크를 탑재:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    fstab 파일에 추가:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-3.8.0) cmdlet을 -EncryptFormatAll과 함께 실행하여 이러한 디스크를 암호화합니다.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    KEK(키 암호화 키)를 사용하려는 경우, KEK의 URI와 키 자격 증명 모음의 ResourceID를 -KeyEncryptionKeyUrl 및 -KeyEncryptionKeyVaultId 매개 변수로 각각 전달합니다.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. 이러한 새 디스크를 기반으로 하여 LVM을 설정합니다. VM 부팅이 완료되면 암호화된 드라이브는 잠금 해제됩니다. 따라서 LVM 탑재도 나중으로 지연되어야 합니다.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>고객 암호화 VHD 및 암호화 키로 만든 새 VM
이 시나리오에서는 PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 

Azure Disk Encryption 동일 스크립트의 지침을 사용하여 Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Linux VHD 준비](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅샷을 만들고 백업해야 합니다. 포털에서 관리 디스크에 대한 스냅샷을 수행하거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업을 만들면 Set-AzVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다. 백업이 만들어지고 이 매개 변수가 지정되지 않으면 관리 디스크 기반 VM에 대한 Set-AzVMDiskEncryptionExtension 명령은 실패하게 됩니다. 
>
> 암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell을 사용하여 미리 암호화된 VHD가 있는 VM을 암호화 
[Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) PowerShell cmdlet을 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 아래 예제에서는 몇 가지 공통 매개 변수를 제공합니다. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용

[az vm disk attach](add-disk.md)를 사용하거나 [Azure Portal을 통해](attach-disk-portal.md) 새 데이터 디스크를 추가할 수 있습니다. 암호화하려면 먼저 새로 연결된 데이터 디스크를 탑재해야 합니다. 암호화가 진행되는 동안 드라이브를 사용할 수 없으므로 데이터 드라이브의 암호화를 요청해야 합니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 사용

 VM이 이전에 "All"로 암호화된 경우에는 --volume-type 매개 변수가 "All"로 유지되어야 합니다. All은 OS 디스크 및 데이터 디스크 모두를 포함합니다. VM이 이전에 "OS" 볼륨 유형으로 암호화된 경우에는 OS와 새 데이터 디스크가 모두 포함되도록 --volume-type 매개 변수를 "All"로 변경해야 합니다. VM이 "Data" 볼륨 유형으로만 암호화된 경우에는 아래 설명처럼 "Data"로 유지하면 됩니다. VM에 새 데이터 디스크를 추가하고 연결하는 것으로는 암호화 준비에 충분하지 않습니다. 암호화를 사용하도록 설정하기 전에 새로 연결된 디스크를 포맷하고 VM 내에서 올바르게 마운트해야 합니다. Linux에서는 디스크를 [영구 블록 디바이스 이름](../troubleshooting/troubleshoot-device-names-problems.md)으로 /etc/fstab에 마운트해야 합니다.  

PowerShell 구문과 달리 CLI에서는 사용자가 암호화를 사용하도록 설정할 때 고유 시퀀스 버전을 제공하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.

-  **실행 중인 VM의 데이터 볼륨 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK를 사용하여 실행 중인 VM의 데이터 볼륨 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 PowerShell을 사용하여 Linux용 새 디스크를 암호화하는 경우 새 순서 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 아래 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 디스크를 암호화하기 전에 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 사용하여 [스냅샷](snapshot-copy-managed-disk.md)을 만들거나 VM을 백업하세요. 새로 추가된 데이터 디스크를 암호화하기 위해 PowerShell 스크립트에 -skipVmBackup 매개 변수가 이미 지정되어 있습니다.
 

-  **실행 중인 VM의 데이터 볼륨 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MyVirtualMachineResourceGroup, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다. -VolumeType 매개 변수에 허용되는 값은 All, OS 및 Data입니다. VM이 이전에 "OS" 또는 "All" 볼륨 유형으로 암호화된 경우에는 OS와 새 데이터 디스크가 모두 포함되도록 -VolumeType 매개 변수를 "All"로 변경해야 합니다.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **KEK를 사용하여 실행 중인 VM의 데이터 볼륨 암호화:** -VolumeType 매개 변수에 허용되는 값은 All, OS 및 Data입니다. VM이 이전에 "OS" 또는 "All" 볼륨 유형으로 암호화된 경우에는 OS와 새 데이터 디스크가 모두 포함되도록 -VolumeType 매개 변수를 All로 변경해야 합니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 


## <a name="disable-encryption-for-linux-vms"></a>Linux VM에 대한 암호화 사용 안 함
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

Azure Disk Encryption는 다음과 같은 Linux 시나리오, 기능 및 기술에 대해 작동하지 않습니다.

- 클래식 VM 만들기 방법을 통해 만든 VM 또는 기본 계층 VM을 암호화
- OS 드라이브가 암호화된 경우 Linux VM의 OS 드라이브 또는 데이터 드라이브에서 암호화를 사용하지 않도록 설정
- Linux 가상 머신 확장 집합에 대 한 OS 드라이브를 암호화 합니다.
- Linux VM에서 사용자 지정 이미지 암호화
- 온-프레미스 키 관리 시스템과의 통합
- Azure 파일(공유 파일 시스템)
- NFS(네트워크 파일 시스템)
- 동적 볼륨
- 사용 후 삭제 OS 디스크
- 다음과 같은 공유/분산 파일 시스템의 암호화(단, 다음 항목에 국한되지 않음), DFS, GFS, DRDB 및 CephFS
- 다른 구독 또는 지역으로 암호화 된 VM 이동
- 암호화 된 VM의 이미지나 스냅숏을 만들어 추가 Vm을 배포 하는 데 사용
- 커널 크래시 덤프(kdump)
- Oracle ACFS(ASM 클러스터 파일 시스템)
- Gen2 VM(참조: [Azure의 2세대 VM 지원](generation-2.md#generation-1-vs-generation-2-capabilities))
- Lsv2 시리즈 Vm의 NVMe 디스크 (참조: [Lsv2 시리즈](../lsv2-series.md)).
- "중첩된 탑재 지점" 즉, 단일 경로 내 여러 탑재 지점(예: "/1stmountpoint/data/2stmountpoint")이 있는 VM
- 데이터 드라이브가 OS 폴더 위에 탑재 된 VM입니다.
- 쓰기 가속기 디스크가 있는 M 시리즈 Vm
- [고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화](disk-encryption.md) 로 암호화 된 디스크를 포함 하는 VM에 ADE 적용 (SSE + cmk). ADE로 암호화 된 VM의 데이터 디스크에 SSE + CMK를 적용 하는 것도 지원 되지 않는 시나리오입니다.
- ADE로 암호화 되었거나 ADE로 **암호화 된 VM** 을 [고객 관리 키를 사용 하는 서버 쪽 암호화](disk-encryption.md)로 마이그레이션합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
