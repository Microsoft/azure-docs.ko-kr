---
title: Azure Key Vault 관리 스토리지 계정 - PowerShell 버전
description: 관리형 스토리지 계정 기능은 Azure Key Vault와 Azure Storage 계정 간에 완벽한 통합을 제공합니다.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 8e8479179aa74f2fb2ead41dec28d247de9657c3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585103"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Key Vault 및 Azure PowerShell을 사용하여 스토리지 계정 키 관리

Azure 스토리지 계정은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동 생성되며 암호화 키가 아닌 암호 역할을 합니다. Key Vault는 스토리지 계정에서 스토리지 계정 키를 주기적으로 다시 생성하여 스토리지 계정 키를 관리하며, 스토리지 계정의 리소스에 대한 위임된 액세스가 가능하도록 공유 액세스 서명 토큰을 제공합니다.

Key Vault 관리형 스토리키 계정 키 기능을 사용하여 키를 나열(Azure 스토리지 계정과 동기화)하고, 주기적으로 키를 다시 생성(순환)할 수 있습니다. 스토리지 계정과 클래식 스토리지 계정의 키를 모두 관리할 수 있습니다.

관리형 스토리지 계정 키 기능을 사용할 때 다음 사항을 고려해야 합니다.

- 키 값은 호출자에게 응답으로 반환되지 않습니다.
- Key Vault에서만 스토리지 계정 키를 관리해야 합니다. 키를 직접 관리하거나 Key Vault 프로세스를 방해하지 마세요.
- 단일 Key Vault 개체에서만 스토리지 계정 키를 관리해야 합니다. 여러 개체에서 키를 관리하도록 허용하지 마세요.
- 사용자 계정을 사용하여 스토리지 계정을 관리하도록 Key Vault에 요청할 수 있지만, 서비스 주체를 사용하여 관리하도록 요청할 수는 없습니다.
- Key Vault만을 사용하여 키를 다시 생성해야 합니다. 스토리지 계정 키를 수동으로 다시 생성하지 않습니다.

Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스인 Azure AD(Azure Active Directory)와 Azure Storage를 통합하여 사용하는 것이 좋습니다. Azure AD 통합은 [Azure BLOB 및 큐](../../storage/common/storage-auth-aad.md)에 사용할 수 있으며, Azure Key Vault와 마찬가지로 Azure Storage에 대한 OAuth2 토큰 기반 액세스를 제공합니다.

Azure AD를 사용하면 스토리지 계정 자격 증명 대신 애플리케이션 또는 사용자 ID를 사용하여 클라이언트 애플리케이션을 인증할 수 있습니다. Azure에서 실행할 때 [Azure AD 관리 ID](/azure/active-directory/managed-identities-azure-resources/)를 사용할 수 있습니다. 관리 ID를 사용하면 클라이언트 인증이 필요 없는 것은 물론이고, 애플리케이션에 또는 애플리케이션을 통해 자격 증명을 저장할 필요가 없습니다.

Azure AD는 Key Vault에서도 지원되는 RBAC(역할 기반 액세스 제어)를 사용하여 권한 부여를 관리합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>서비스 주체 애플리케이션 ID

Azure AD 테넌트는 등록된 각 애플리케이션에 [서비스 주체](/azure/active-directory/develop/developer-glossary#service-principal-object)를 제공합니다. 서비스 주체는 애플리케이션 ID 역할을 하며, RBAC를 통해 다른 Azure 리소스에 액세스하기 위해 권한 부여를 설정하는 동안 사용됩니다.

Key Vault는 모든 Azure AD 테넌트에서 미리 등록되는 Microsoft 애플리케이션입니다. Key Vault는 각 Azure 클라우드에서 동일한 애플리케이션 ID로 등록됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 공용 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 완료하려면 먼저 다음을 수행해야 합니다.

- [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps?view=azps-2.6.0)
- [키 자격 증명 모음 만들기](quick-create-powershell.md)
- [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-powershell) 스토리지 계정 이름에는 소문자와 숫자만 사용해야 합니다. 이름은 3~24자 사이여야 합니다.
      

## <a name="manage-storage-account-keys"></a>스토리지 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용하여 PowerShell 세션을 인증합니다. 

```azurepowershell-interactive
Connect-AzAccount
```
Azure 구독이 여러 개 있는 경우 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet을 사용하여 구독을 나열하고, [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet을 통해 사용하려는 구독을 지정할 수 있습니다. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에 사용할 변수를 설정합니다. <YourResourceGroupName>, <YourStorageAccountName> 및 <YourKeyVaultName> 자리 표시자를 업데이트하고, 위의 [서비스 주체 애플리케이션 ID](#service-principal-application-id)에 지정된 대로 $keyVaultSpAppId를 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`으로 설정합니다.

또한 여기서는 Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) 및 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 사용자 ID와 Azure 스토리지 계정의 컨텍스트를 가져올 것입니다.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> 클래식 스토리지 계정의 경우 $storageAccountKey에 "primary" 및 "secondary"를 사용합니다. <br>
> 클래식 스토리지 계정의 경우 'Get-AzStorageAccount' 대신 'Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName'을 사용합니다.

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault에 스토리지 계정에 대한 액세스 권한 부여

Key Vault가 스토리지 계정 키에 액세스하여 관리하려면 사용자가 스토리지 계정에 액세스 권한을 부여해야 합니다. Key Vault 애플리케이션은 스토리지 계정의 키를 *나열*하고 *다시 생성*할 수 있는 권한이 필요합니다. 이러한 권한은 Azure의 기본 제공 역할인 [스토리지 계정 키 운영자 서비스 역할](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)을 통해 설정됩니다. 

Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet을 사용하여 Key Vault 서비스 주체에 이 역할을 할당하고 스토리지 계정으로 범위를 제한합니다.

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

역할 할당에 성공하면 다음 예제와 비슷한 결과가 나타납니다.

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Key Vault가 이미 스토리지 계정의 역할에 추가되었다면 *"역할 할당이 이미 있습니다."* 오류로 인해 path\filename 파일을 삭제하지 못했습니다. 또한 Azure Portal에서 스토리지 계정 “액세스 제어(IAM)” 페이지를 사용하여 역할 할당을 확인할 수도 있습니다.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>관리 스토리지 계정에 사용자 계정 권한 부여

Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet을 사용하여 Key Vault 액세스 정책을 업데이트하고 사용자 계정에 스토리지 계정 권한을 부여합니다.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

스토리지 계정에 대한 권한은 Azure Portal의 스토리지 계정 “액세스 정책” 페이지에서 제공되지 않습니다.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault 인스턴스에 관리 스토리지 계정 추가

Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 Key Vault 인스턴스에 관리형 스토리지 계정을 만듭니다. `-DisableAutoRegenerateKey` 스위치는 스토리지 계정 키를 다시 생성하지 않도록 지정합니다.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

키가 다시 생성되지 않고 스토리지 계정 추가에 성공하면 다음 예제와 유사한 출력이 표시됩니다.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>키 다시 생성 사용

Key Vault에서 스토리지 계정 키를 주기적으로 다시 생성하게 하려면 Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet을 사용하여 다시 생성 기간을 설정하면 됩니다. 이 예제에서는 다시 생성 기간을 3일로 설정합니다. 순환할 시간이 되면 Key Vault는 키를 다시 생성한 다음(활성 상태가 아님), 활성 상태로 설정합니다. 한 번에 하나의 키만 SAS 토큰을 발급하는 데 사용됩니다. 이 키가 활성 키입니다.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

키가 다시 생성되고 스토리지 계정 추가에 성공하면 다음 예제와 유사한 출력이 표시됩니다.

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="shared-access-signature-tokens"></a>공유 액세스 서명 토큰

Key Vault에 공유 액세스 서명 토큰을 생성하고 요청할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유하지 않고도 스토리지 계정의 리소스에 대한 액세스 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상시키지 않고 스토리지 리소스를 공유할 수 있는 안전한 방법을 제공합니다.

이 섹션의 명령은 다음 작업을 완료합니다.

- 계정 공유 액세스 서명 정의를 설정합니다. 
- Blob, 파일, 테이블 및 큐 서비스에 대한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 종류 서비스, 컨테이너 및 개체에 대해 생성됩니다. 토큰은 모든 권한을 사용하여, https를 통해, 지정된 시작 및 종료 날짜를 사용하여 생성됩니다.
- 자격 증명 모음에서 Key Vault 관리형 스토리지 공유 액세스 서명 정의를 설정합니다. 정의에는 생성된 공유 액세스 서명 토큰의 템플릿 URI가 포함됩니다. 정의의 공유 액세스 서명 유형은 `account`이며 N일 동안 유효합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 비밀로 저장되었는지 확인합니다.
- 
### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에 사용할 변수를 설정합니다. <YourStorageAccountName> 및 <YourKeyVaultName> 자리 표시자를 업데이트해야 합니다.

또한 여기서는 Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet을 사용하여 Azure 스토리지 계정의 컨텍스트를 가져올 것입니다.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의를 만듭니다.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$sasToken 값은 다음과 같습니다.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의를 만듭니다.  `-Name` 매개 변수 이름을 원하는 대로 지정할 수 있습니다.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet을 사용하여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장되었는지 확인할 수 있습니다.

먼저 키 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

SAS 정의에 해당하는 비밀의 속성은 다음과 같습니다.

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

이제 [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 및 비밀 `Name` 속성을 사용하여 해당 비밀의 내용을 볼 수 있습니다.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

이 명령의 출력은 SAS 정의 문자열을 보여줍니다.


## <a name="next-steps"></a>다음 단계

- [관리 스토리지 계정 키 예제](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
