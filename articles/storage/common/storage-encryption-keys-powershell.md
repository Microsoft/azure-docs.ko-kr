---
title: PowerShell을 사용 하 여 고객 관리 키 구성
titleSuffix: Azure Storage
description: PowerShell을 사용 하 여 Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2beaae3fd6aaae719ac84ef86d7dd7877c94f757
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076126"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 PowerShell을 사용 하 여 고객 관리 키를 사용 하 여 Azure Key Vault를 구성 하는 방법을 보여 줍니다. Azure CLI를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: PowerShell을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/secrets/quick-create-powershell.md)을 참조 하세요.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

저장소 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 먼저 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 이 관리 되는 id를 사용 하 여 저장소 계정에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다.

PowerShell을 사용 하 여 관리 되는 id를 할당 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

PowerShell을 사용 하 여 시스템 할당 관리 id를 구성 하는 방법에 대 한 자세한 내용은 [powershell을 사용 하 여 AZURE VM에서 azure 리소스에 대 한 관리 Id 구성](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)을 참조 하세요.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

PowerShell을 사용 하 여 새 키 자격 증명 모음을 만들려면 [Az. KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 모듈의 버전 2.0.0 이상을 설치 합니다. 그런 다음 [AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 를 호출 하 여 새 키 자격 증명 모음을 만듭니다.

Azure Storage 암호화를 위해 고객이 관리 하는 키를 저장 하는 데 사용 하는 key vault에는 두 가지 키 보호 설정, **일시 삭제** 및 **제거 안 함**이 있어야 합니다. 2.0.0 버전 이상에서 새 키 자격 증명 모음을 만들 때 일시 삭제는 기본적으로 사용 하도록 설정 되어 있습니다.

다음 예에서는 **일시 삭제** 및 **제거 안 함** 속성을 사용 하 여 새 key vault를 만듭니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell을 사용 하 여 기존 주요 자격 증명 모음에서 **일시** 삭제를 사용 하도록 설정 하 고 **제거 하지 않도록** 설정 하는 방법에 대 한 자세한 내용은 powershell에서 일시 [삭제를 사용 하는 방법](../../key-vault/general/soft-delete-powershell.md)에서 **일시 삭제** 및 **제거 방지** 사용 설정 섹션을 참조 하세요.

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음, 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 합니다. 이 단계에서는 이전에 저장소 계정에 할당 한 관리 되는 id를 사용 합니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 새 키를 만듭니다. 새 키를 만들려면 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Azure storage 암호화는 2048, 3072 및 4096 크기의 RSA 및 RSA HSM 키를 지원 합니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)정보를 참조 하세요.

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 구성

기본적으로 Azure Storage 암호화는 Microsoft 관리 키를 사용 합니다. 이 단계에서는 Azure Key Vault에서 고객 관리 키를 사용 하도록 Azure Storage 계정을 구성 하 고 저장소 계정과 연결할 키를 지정 합니다.

고객 관리 키를 사용 하 여 암호화를 구성 하는 경우 연결 된 키 자격 증명 모음에서 키 버전이 변경 되 면 암호화에 사용 되는 키를 자동으로 업데이트 하도록 선택할 수 있습니다. 또는 키 버전이 수동으로 업데이트 될 때까지 암호화에 사용할 키 버전을 명시적으로 지정할 수 있습니다.

> [!NOTE]
> 키를 회전 하려면 Azure Key Vault에서 키의 새 버전을 만듭니다. Azure Storage는 Azure Key Vault 키 회전을 처리 하지 않으므로 키를 수동으로 회전 하거나 일정에 따라 회전 하는 함수를 만들어야 합니다.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>키 버전을 자동으로 업데이트 하도록 암호화 구성

고객 관리 키를 사용 하 여 암호화를 구성 하 여 키 버전을 자동으로 업데이트 하려면 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈, version 2.0.0 이상을 설치 합니다.

고객 관리 키에 대 한 키 버전을 자동으로 업데이트 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성할 때 키 버전을 생략 합니다. 다음 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 하 고 **-KeyvaultEncryption** 옵션을 포함 하 여 저장소 계정에 대 한 고객 관리 키를 사용 하도록 설정 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>키 버전 수동 업데이트에 대 한 암호화 구성

암호화에 사용할 키 버전을 명시적으로 지정 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성할 때 키 버전을 제공 합니다. 다음 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 하 고 **-KeyvaultEncryption** 옵션을 포함 하 여 저장소 계정에 대 한 고객 관리 키를 사용 하도록 설정 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

키 버전을 수동으로 업데이트 하는 경우 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 해야 합니다. 먼저 [AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 를 호출 하 여 최신 버전의 키를 가져옵니다. 그런 다음 이전 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="use-a-different-key"></a>다른 키 사용

Azure Storage 암호화에 사용 되는 키를 변경 하려면 [고객이 관리 하는 키를 사용 하 여 암호화 구성](#configure-encryption-with-customer-managed-keys) 에 표시 된 대로 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 고 새 키 이름 및 버전을 제공 합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트 합니다.

## <a name="revoke-customer-managed-keys"></a>고객 관리 키 철회

키 자격 증명 모음 액세스 정책을 제거 하 여 고객 관리 키를 해지할 수 있습니다. 고객이 관리 하는 키를 해지 하려면 다음 예제와 같이 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 명령을 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>고객 관리 키 사용 안 함

고객 관리 키를 사용 하지 않도록 설정 하면 저장소 계정이 Microsoft 관리 키로 다시 암호화 됩니다. 고객이 관리 하는 키를 사용 하지 않도록 설정 하려면 다음 예제와 같이 옵션을 사용 하 여 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 `-StorageEncryption` 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
