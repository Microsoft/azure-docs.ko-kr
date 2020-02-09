---
title: 암호화 된 VM에 대한 Key Vault 키 & 암호 복원
description: PowerShell을 사용하여 Azure Backup에서 Key Vault 키 및 암호를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450060"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Azure Backup을 사용하여 암호화된 VM의 Key Vault 키 및 암호 복원

이 문서에서는 Key Vault에 키와 암호가 존재하지 않는 경우 Azure VM Backup을 사용하여 암호화된 Azure VM의 복원을 수행하는 방법을 설명합니다. 복원된 VM에 대한 키(키 암호화 키)와 암호(BitLocker 암호화 키)의 별도의 복사본을 유지하려는 경우 이러한 단계도 사용할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

* **암호화된 VM 백업** - 암호화된 Azure VM은 Azure Backup을 사용하여 백업됩니다. 암호화 된 Azure Vm을 백업 하는 방법에 대한 자세한 내용은 [PowerShell을 사용 하 여 Azure vm의 백업 및 복원 관리](backup-azure-vms-automation.md) 문서를 참조 하세요.
* **Azure Key Vault 구성** – 키 및 암호를 복원해야 하는 Key Vault가 이미 있는지 확인합니다. Key Vault 관리에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md) 문서를 참조하세요.
* **디스크 복원** - [PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)를 사용하여 암호화된 VM에 대한 디스크를 복원하는 복원 작업을 트리거했는지 확인합니다. 이 작업은 복원할 암호화된 VM의 키 및 암호가 포함된 스토리지 계정에 JSON 파일을 생성하기 때문입니다.

## <a name="get-key-and-secret-from-azure-backup"></a>Azure Backup에서 키 및 비밀 가져오기

> [!NOTE]
> 암호화된 VM의 디스크가 복원되면 다음 사항을 확인하세요.
>
> * [디스크 복원 섹션의 PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)에 설명된 것처럼 $details가 디스크 복원 작업 세부 정보로 채워집니다.
> * **키 및 비밀이 키 자격 증명 모음으로 복원된 후**에만 복원된 디스크에서 VM이 만들어져야 합니다.

복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Azure Storage 컨텍스트를 설정하고 암호화된 VM에 대한 키 및 비밀 세부 정보가 포함된 JSON 구성 파일을 복원합니다.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>키 복원

위에 언급된 대상 경로에 JSON 파일이 생성되면 JSON에서 키 BLOB 파일을 생성하고 restore key cmdlet에 제공하여 해당 키(KEK)를 다시 키 자격 증명 모음에 지정합니다.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>암호 복원

위에서 생성된 JSON 파일을 사용하여 비밀 이름 및 값을 가져오고 set secret cmdlet에 제공하여 해당 비밀(BEK)을 다시 키 자격 증명 모음에 지정합니다. **VM이 BEK 및 KEK를 사용 하 여 암호화 된**경우 이러한 cmdlet을 사용 합니다.

**Windows VM이 BEK 및 KEK를 사용하여 암호화된 경우 이러한 cmdlet을 사용합니다.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Linux VM이 BEK 및 KEK를 사용하여 암호화된 경우 이러한 cmdlet을 사용합니다.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

위에서 생성된 JSON 파일을 사용하여 비밀 이름 및 값을 가져오고 set secret cmdlet에 제공하여 해당 비밀(BEK)을 다시 키 자격 증명 모음에 지정합니다. **VM이 BEK만 사용 하 여 암호화 된** 경우 이러한 cmdlet을 사용 합니다.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * $Secretname의 값은 $encryptionObject. SecretUrl의 출력을 참조 하 고 암호/예: 출력 비밀 URL은 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 이며 암호 이름은 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA입니다입니다.
> * DiskEncryptionKeyFileName 태그의 값은 암호 이름과 동일 합니다.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>복원된 디스크에서 가상 머신 만들기

Azure VM Backup을 사용하여 암호화된 VM을 백업한 경우 위에 언급된 PowerShell cmdlet은 키 및 비밀을 Key Vault로 다시 복원하는 데 도움이 됩니다. 복원한 후에는 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 문서를 참조하여 복원된 디스크, 키 및 비밀에서 암호화된 VM을 만듭니다.

## <a name="legacy-approach"></a>기존의 접근 방식

위에 설명된 접근 방식이 모든 복구 지점에 유효합니다. 그렇지만 BEK 및 KEK를 사용하여 암호화된 VM의 경우는 복구 지점에서 키 및 비밀 정보를 가져오는 이전의 접근 방식도 2017년 7월 11일 이전의 복구 지점에 계속 유효합니다. [PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)를 사용하여 암호화된 VM에 대한 디스크 복원 작업을 완료하면 $rp가 유효한 값으로 채워졌는지 확인합니다.

### <a name="restore-key"></a>키 복원

다음 cmdlet을 사용하여 복구 지점에서 키(KEK) 정보를 가져오고 restore key cmdlet에 제공하여 키 자격 증명 모음에 다시 지정합니다.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>암호 복원

다음 cmdlet을 사용하여 복구 지점에서 비밀(BEK) 정보를 가져오고 set secret cmdlet에 제공하여 키 자격 증명 모음에 다시 지정합니다.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * $secretname 값은 $secretname 값은 $rp1.KeyAndSecretDetails.SecretUrl 출력을 참조하고 secrets/ 뒤의 텍스트를 사용하여 얻을 수 있습니다. 예를 들어 출력 비밀 URL은 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 이고 비밀 이름은 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA입니다.
> * DiskEncryptionKeyFileName 태그 값은 비밀 이름과 동일합니다.
> * 키를 다시 복원한 후 [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) cmdlet을 사용하여 DiskEncryptionKeyEncryptionKeyURL에 대한 값을 Key Vault에서 얻을 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

키와 암호를 key vault로 다시 복원한 후에는 [PowerShell을 사용 하 여 Azure vm의 백업 및 복원 관리](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 문서를 참조 하 여 복원 된 디스크, 키 및 비밀에서 암호화 된 vm을 만듭니다.
