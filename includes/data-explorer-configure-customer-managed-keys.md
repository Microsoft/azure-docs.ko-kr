---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 0d78e48fead7b1f53e67860e6be8fe6d77469e87
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280607"
---
Azure 데이터 탐색기는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대한 추가 제어를 위해 고객 관리 키를 제공 하 여 데이터 암호화에 사용할 수 있습니다. 고객 관리 키는 [Azure Key Vault](/azure/key-vault/key-vault-overview)에 저장 되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault API를 사용 하 여 키를 생성할 수 있습니다. Azure 데이터 탐색기 클러스터와 key vault는 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다. 고객 관리 키에 대한 자세한 설명은 [Azure Key Vault를 사용 하 여 고객 관리 키](/azure/storage/common/storage-service-encryption)를 참조 하세요. 이 문서에서는 고객이 관리 하는 키를 구성 하는 방법을 보여 줍니다.

Azure 데이터 탐색기을 사용 하 여 고객 관리 키를 구성 하려면 [키 자격 증명 모음에 대한 두 가지 속성을 설정](/azure/key-vault/key-vault-ovw-soft-delete)해야 합니다. **일시 삭제** 및 **제거 안 함** 이러한 속성은 기본적으로 사용 되지 않습니다. 이러한 속성을 사용 하도록 설정 하려면 [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) 또는 [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)를 사용 합니다. RSA 키와 키 크기 2048만 지원 됩니다.

> [!NOTE]
> 고객 관리 키를 사용 하는 데이터 암호화는 [리더 및 종동체 클러스터](/azure/data-explorer/follower)에서 지원 되지 않습니다. 

## <a name="assign-an-identity-to-the-cluster"></a>클러스터에 id 할당

클러스터에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 먼저 시스템 할당 관리 되는 id를 클러스터에 할당 합니다. 이 관리 되는 id를 사용 하 여 클러스터에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다. 시스템 할당 관리 id를 구성 하려면 [관리 되는 id](/azure/data-explorer/managed-identities)를 참조 하십시오.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

PowerShell을 사용 하 여 새 키 자격 증명 모음을 만들려면 [AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 호출 합니다. Azure 데이터 탐색기 암호화를 위해 고객 관리 키를 저장 하는 데 사용 하는 key vault에는 두 가지 키 보호 설정, **일시 삭제** 및 **제거 안 함**이 있어야 합니다. 괄호 안의 자리 표시자 값을 아래 예제에서 사용자 고유의 값으로 바꿉니다.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음 키 자격 증명 모음에 대한 액세스 정책을 구성 하 여 클러스터에 액세스할 수 있는 권한을 부여 합니다. 이 단계에서는 이전에 클러스터에 할당 한 시스템 할당 관리 id를 사용 합니다. 키 자격 증명 모음에 대한 액세스 정책을 설정 하려면 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 새 키를 만듭니다. 새 키를 만들려면 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 합니다.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
