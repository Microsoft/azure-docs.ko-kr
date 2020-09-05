---
title: Azure Key Vault - PowerShell로 일시 삭제를 사용하는 방법
description: Azure PowerShell을 사용하여 키 자격 증명 모음 및 키 자격 증명 모음 개체를 복구할 수 있는 Azure Key Vault의 일시 삭제 기능을 사용하는 방법을 알아봅니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 251159c4fcf27ceb4d8b0dddeecbe501c7346e9e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073236"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>PowerShell로 Key Vault 일시 삭제를 사용하는 방법

Azure Key Vault의 일시 삭제 기능을 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 특히, 일시 삭제는 다음과 같은 시나리오를 해결합니다.

- Key Vault의 복구 가능한 삭제 지원
- Key Vault 개체(예: 키, 비밀 및 인증서)의 복구 가능한 삭제를 지원

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 이상 - 설치하지 않은 경우 Azure PowerShell을 설치하고 Azure 구독에 연결합니다. [Azure PowerShell을 설치 및 구성하는 방법](https://docs.microsoft.com/powershell/azure/)을 참조하세요. 

>[!NOTE]
> 올바른 버전이 아니며 사용자 환경으로 로드될 **수도** 있는 Key Vault PowerShell 출력 서식 지정 파일의 오래된 버전이 있습니다. PowerShell의 업데이트된 버전에는 출력 서식 지정에 대한 필요한 수정 사항이 포함되고, 이 토픽은 해당 시점에 업데이트될 예정입니다. 이 서식 지정 문제에 대한 현재 해결 방법은 다음과 같습니다.
> - 이 토픽에 설명된 일시 삭제가 활성화된 속성이 표시되지 않을 경우 다음 쿼리를 사용합니다. `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`


PowerShell에 대한 Key Vault 관련 참조 정보는 [Azure Key Vault PowerShell 참조](/powershell/module/az.keyvault)를 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한

Key Vault 작업은 RBAC(역할 기반 액세스 제어) 권한을 통해 다음과 같이 별도로 관리됩니다.

| 작업(Operation) | Description | 사용자 권한 |
|:--|:--|:--|
|목록|삭제된 Key Vault를 나열합니다.|Microsoft.KeyVault/deletedVaults/read|
|복구|삭제된 Key Vault를 복구합니다.|Microsoft.KeyVault/vaults/write|
|제거|삭제된 Key Vault 및 모든 콘텐츠를 영구적으로 제거합니다.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

권한 및 액세스 제어에 대한 자세한 내용은 [키 자격 증명 모음 보안](secure-your-key-vault.md)을 참조하세요).

## <a name="enabling-soft-delete"></a>일시 삭제를 사용하도록 설정

"일시 삭제"를 사용하여 삭제된 키 자격 증명 모음 또는 키 자격 증명 모음에 저장된 개체를 복구할 수 있습니다.

> [!IMPORTANT]
> 키 자격 증명 모음에 대해 '일시 삭제'를 사용하도록 설정하는 것은 복구 불가능한 작업입니다. 일시 삭제된 속성을 "true"로 설정한 후에는 해당 속성을 변경하거나 제거할 수 없습니다.  

### <a name="existing-key-vault"></a>기존 Key Vault 사용

ContosoVault라는 기존 Key Vault의 경우 다음과 같이 일시 삭제를 사용하도록 설정합니다. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>새로운 Key Vault

일시 삭제는 기본적으로 모든 새 키 자격 증명 모음에 대해 자동으로 설정됩니다. 2020년 12월 31일까지 모든 키 자격 증명 모음에서 일시 삭제를 더 이상 비활성화할 수 없습니다. 

### <a name="verify-soft-delete-enablement"></a>일시 삭제 사용 확인

Key Vault에 일시 삭제가 사용할 수 있도록 설정되어 있는지 확인하려면 *show* 명령을 실행하여 ‘일시 삭제 사용?’ 특성:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>일시 삭제 보호 키 자격 증명 모음 삭제

키 자격 증명을 삭제하는 명령의 동작은 일시 삭제가 사용하도록 설정되었는지 여부에 따라 변합니다.

> [!IMPORTANT]
>일시 삭제를 사용하도록 설정하지 않은 키 자격 증명 모음에 대해 다음 명령을 실행하면 이 키 자격 증명 모음과 해당 자격 증명 모음의 모든 내용을 복구하는 옵션 없이 영구적으로 삭제하게 됩니다!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>일시 삭제가 Key Vault를 보호하는 방식

활성화된 일시 삭제 사용:

- 삭제된 키 자격 증명 모음은 해당 리소스 그룹에서 제거되고 원래 생성되었던 위치와 연결된 예약된 네임스페이스에 놓입니다. 
- 키, 비밀 및 인증서와 같은 삭제된 개체는 해당 개체가 포함하고 있는 키 자격 증명 모음이 삭제된 상태에 있는 한 액세스할 수 없습니다. 
- 삭제된 키 자격 증명 모음에 대한 DNS 이름은 동일한 이름의 새로운 키 자격 증명 모음이 만들어지지 않도록 예약됩니다.  

다음 명령을 사용하여 사용자 구독에 연결된 삭제된 상태 Key Vault를 볼 수도 있습니다.

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID*를 사용하여 복구 또는 제거할 때 리소스를 식별할 수 있습니다. 
- *리소스 ID*는 이 자격 증명 모음의 원본 리소스 ID입니다. 이제 이 Key Vault가 삭제된 상태로 있으므로 해당 리소스 ID를 사용하는 리소스가 없습니다. 
- *예약된 제거 날짜*는 어떤 작업도 수행되지 않는 경우 자격 증명 모음이 영구적으로 삭제되는 시기입니다. *예약된 제거 날짜*를 계산하는 데 사용되는 기본 보존 기간은 90일입니다.

## <a name="recovering-a-key-vault"></a>Key Vault 복구

키 자격 증명 모음을 복구하려면 키 자격 증명 모음, 리소스 그룹 및 위치를 지정합니다. 삭제된 키 자격 증명 모음의 위치 및 리소스 그룹은 복구 프로세스에 필요하므로 적어 둡니다.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

키 자격 증명 모음이 복구될 때 해당 키 자격 증명 모음의 원래 리소스 ID를 가진 새로운 리소스가 생성됩니다. 원래 리소스 그룹이 제거된 경우 복구를 시도하기 전에 동일한 이름의 리소스 그룹을 만들어야 합니다.

## <a name="deleting-and-purging-key-vault-objects"></a>키 자격 증명 모음 개체 삭제 및 제거

다음 명령은 일시 삭제를 사용하도록 설정한 ‘ContosoVault’ 키 자격 증명 모음에서 ‘ContosoFirstKey’ 키를 삭제합니다.

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

일시 삭제가 설정되어 있는 키 자격 증명 모음을 사용하면 삭제된 키를 명시적으로 나열하는 경우가 아니면 삭제된 키가 여전히 삭제된 것으로 표시됩니다. 삭제된 키의 나열, 복구 또는 제거 외에, 삭제된 상태의 키에 대한 작업 대부분은 실패합니다. 

예를 들어, 다음 명령은 ‘ContosoVault’ 키 자격 증명 모음에서 삭제된 키를 나열합니다.

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>전환 상태 

일시 삭제가 활성화되어 있는 Key Vault의 키를 삭제하는 경우 전환이 완료되기까지 몇 초 정도 걸릴 수 있습니다. 이 전환 중에 키가 활성 상태 또는 삭제된 상태에 있지 않은 것으로 나타날 수 있습니다. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Key Vault 개체를 통해 일시 삭제 사용

키 자격 증명 모음과 마찬가지로 삭제된 키, 비밀 또는 인증서도 복구하거나 제거하지 않는 한 최대 90일 동안 삭제된 상태로 남아 있습니다. 

#### <a name="keys"></a>구성

일시 삭제된 키를 복구하려면 다음을 수행합니다.

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

일시 삭제된 키를 영구적으로 삭제(일명 제거)하려면 다음을 수행합니다.

> [!IMPORTANT]
> 키를 제거하면 키가 영구적으로 삭제되며, 복구할 수 없습니다! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**복구** 및 **제거** 작업에는 키 자격 증명 모음 액세스 정책과 연결된 고유 권한이 있습니다. 사용자 또는 서비스 주체가 **복구** 또는 **제거** 동작을 실행할 수 있도록 하려면 이들이 해당 키 또는 비밀에 대한 적절한 권한을 가지고 있어야 합니다. 기본적으로 '모든' 바로 가기를 사용하여 모든 사용 권한을 부여할 때 **제거**는 키 자격 증명 모음의 액세스 정책에 추가되지 않습니다. **제거** 사용 권한을 명시적으로 부여해야 합니다. 

#### <a name="set-a-key-vault-access-policy"></a>Key Vault 액세스 정책 설정

다음 명령은 **제거**를 포함하여 *ContosoVault*의 키에 대해 여러 가지 작업을 사용하는 user@contoso.com 사용 권한을 부여합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> 이제 막 일시 삭제 활성화되도록 기존 Key Vault를 설정한 경우 **복구** 및 **제거** 권한이 없을 수도 있습니다.

#### <a name="secrets"></a>비밀

키와 마찬가지로 비밀도 자체의 명령을 사용하여 관리됩니다.

- SQLPassword라는 비밀 삭제: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Key Vault의 모든 삭제된 비밀 나열: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- 삭제된 상태의 비밀 복구: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- 삭제된 상태의 비밀 제거: 

  > [!IMPORTANT]
  > 비밀을 제거하면 비밀이 영구적으로 삭제되며 복구할 수 없습니다!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>인증서

다음 명령을 사용하여 인증서를 관리할 수 있습니다.

- 인증서 삭제: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- 키 자격 증명 모음의 모든 삭제된 인증서를 나열합니다. 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- 삭제된 상태의 인증서를 복구합니다. 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- 삭제된 상태의 인증서를 제거합니다. 

  > [!IMPORTANT]
  > 인증서를 제거하면 인증서가 영구적으로 삭제되며 복구할 수 없습니다!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>일시 삭제 보호 키 자격 증명 모음 제거

> [!IMPORTANT]
> 키 자격 증명 모음 또는 포함된 해당 개체 중 하나를 제거하면 영구적으로 삭제되며, 이는 복구할 수 없습니다!

제거 함수는 이전에 일시 삭제되었던 Key Vault 개체 또는 전체 Key Vault를 영구적으로 삭제하는 데 사용됩니다. 이전 섹션에서 설명한 대로 일시 삭제 기능을 사용하도록 설정된 키 자격 증명 모음에 저장된 개체는 다음과 같이 여러 가지 상태를 거칠 수 있습니다.
- **활성**: 삭제 전
- **일시 삭제**: 삭제 후 목록에 나열되고 활성 상태로 다시 복구할 수 있습니다.
- **영구 삭제**: 제거 후 복구할 수 없습니다.


키 자격 증명 모음에 대해서도 마찬가지입니다. 일시 삭제 키 자격 증명 모음 및 해당 내용을 영구적으로 삭제하려면 키 자격 증명 모음 자체를 제거해야 합니다.

### <a name="purging-a-key-vault"></a>키 자격 증명 모음 제거

키 자격 증명 모음이 제거되면 키, 비밀 및 인증서를 포함하여 해당 전체 콘텐츠가 영구적으로 삭제됩니다. 일시 삭제된 키 자격 증명 모음을 제거하려면 `-InRemovedState` 옵션이 포함된 `Remove-AzKeyVault` 명령을 사용하고, `-Location location` 인수를 사용하여 삭제된 키 자격 증명 모음의 위치를 지정합니다. `Get-AzKeyVault -InRemovedState` 명령을 사용하여 삭제된 자격 증명 모음의 위치를 찾을 수 있습니다.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>제거 권한 필요
- 삭제된 키 자격 증명 모음을 제거하려면 사용자가 *Microsoft.KeyVault/locations/deletedVaults/purge/action* 작업에 대한 RBAC 사용 권한을 가지고 있어야 합니다. 
- 삭제된 키 자격 증명 모음을 나열하려면 사용자가 *Microsoft.KeyVault/deletedVaults/read* 작업에 대한 RBAC 사용 권한을 가지고 있어야 합니다. 
- 기본적으로 구독 관리자만 이러한 권한을 갖습니다. 

### <a name="scheduled-purge"></a>예약된 제거

삭제된 키 자격 증명 모음 개체를 나열하면 키 자격 증명 모음에 의해 제거하기로 예약된 시기도 표시됩니다. *예약된 제거 날짜*는 어떤 작업도 수행되지 않는 경우 키 자격 증명 모음 개체가 영구적으로 삭제될 때를 나타냅니다. 기본적으로 삭제된 Key Vault 개체에 대한 보존 기간은 90일입니다.

>[!IMPORTANT]
>해당 *예약된 제거 날짜* 필드에 의해 트리거되는 제거된 자격 증명 모음 개체가 영구적으로 삭제됩니다. 이는 복구할 수 없습니다!

## <a name="enabling-purge-protection"></a>제거 보호 활성화

제거 보호를 설정하면 보존 기간이 지날 때까지 삭제된 상태의 자격 증명 모음이나 개체를 제거할 수 없습니다. 즉, 이러한 자격 증명 모음 또는 개체는 여전히 복구할 수 있는 상태입니다. 이 기능을 사용하면 보존 기간이 지날 때까지는 자격 증명 모음이나 개체를 삭제할 수 없습니다. 기본 보존 기간은 90일이지만 키 자격 증명 모음을 만드는 동안 보존 정책 간격을 7일에서 90일 사이의 값으로 설정할 수 있습니다. 제거 보호 보존 정책은 동일한 간격을 사용합니다. 설정되면 보존 정책 간격을 변경할 수 없습니다.

일시 삭제가 활성화된 경우에만 제거 보호를 사용하도록 설정할 수 있습니다. 제거 보호를 비활성화는 것은 지원되지 않습니다. 

자격 증명 모음을 만들 때 일시 삭제와 제거 보호를 모두 설정하려면 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) cmdlet을 사용합니다.

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

이미 일시 삭제가 활성화된 기존 자격 증명 모음에 제거 보호를 추가하려면 [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0) 및 [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) cmdlet을 사용합니다.

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>기타 리소스

- Key Vault의 일시 삭제 기능에 대한 자세한 내용은 [Azure Key Vault 일시 삭제 개요](soft-delete-overview.md)를 참조하세요).
- Azure Key Vault 사용에 대한 일반적인 개요는 [Azure Key Vault란?](overview.md)을 참조하세요).
