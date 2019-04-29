---
title: PowerShell에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.
description: PowerShell을 사용 하 여 Azure Storage 암호화에 대 한 고객 관리 키를 구성 하는 방법에 알아봅니다. 고객 관리 키를 사용 하 여 만들기, 회전, 해제 및 액세스 제어를 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 01d5226a2d6f4c4e2e376803a3f0a25d2d5b0fd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593564"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>PowerShell에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 PowerShell을 사용 하 여 고객 관리 키를 사용 하 여 key vault를 구성 하는 방법을 보여 줍니다.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

저장소 계정에 대 한 고객 관리 키를 사용 하려면 먼저 저장소 계정에 자동으로 지정 된 관리 되는 id를 할당 합니다. Key vault에 액세스 하려면 저장소 계정 권한을 승인 하려면이 관리 되는 id를 사용 합니다.

PowerShell을 사용 하는 관리 되는 id에 할당 하려면 호출 [집합 AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

PowerShell을 사용 하 여 관리 되는 id 시스템 할당을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [PowerShell을 사용 하 여 Azure VM에서 Azure 리소스에 대 한 id를 관리 하는 구성](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)합니다.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

PowerShell을 사용 하 여 새 key vault를 만들려면 호출 [새로 만들기-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)합니다. 사용 하 여 Azure Storage 암호화에는 두 가지 키 보호 설정을 사용 하도록 설정 해야 합니다. 고객 관리 키를 저장 하는 key vault **일시 삭제** 하 고 **안 함**합니다. 

대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

다음으로, 저장소 계정에 액세스할 권한이 있도록 키 자격 증명 모음 액세스 정책은 구성 합니다. 이 단계에서는 저장소 계정에 이전에 할당 된 관리 되는 id를 사용 합니다.

Key vault에 대 한 액세스 정책을 설정 하려면 호출 [집합 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿉니다 하 고 이전 예제에서 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 key vault에 새 키를 만듭니다. 새 키를 만들려면 호출 [추가 AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿉니다 하 고 이전 예제에서 정의 된 변수를 사용 해야 합니다.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화를 구성 합니다.

기본적으로 Azure Storage 암호화는 Microsoft 관리 키를 사용합니다. 이 단계에서는 고객 관리 키를 사용 하 여 저장소 계정에 연결 하는 키를 지정 하 여 Azure Storage 계정을 구성 합니다.

호출 [집합 AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) 저장소 계정의 암호화 설정을 업데이트 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿉니다 하 고 이전 예제에서 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들면 새 버전을 사용 하 여 저장소 계정을 업데이트 해야 합니다. 먼저 호출 [Get AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 키의 최신 버전을 가져올 수 있습니다. 그런 다음 호출 [집합 AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) 이전 섹션에 표시 된 대로 키의 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대 한 azure Storage 암호화](storage-service-encryption.md) 
- [Azure Key Vault 란](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
