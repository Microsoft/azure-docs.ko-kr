---
title: Azure Key Vault 관리 스토리지 계정 - PowerShell 버전
description: 관리 되는 storage 계정 기능은 Azure Key Vault와 Azure storage 계정 간에 원활한 통합을 제공 합니다.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 584a37ffb9727a48e2adb5e339697314cffe93f7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980859"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Key Vault 및 Azure PowerShell를 사용 하 여 저장소 계정 키 관리

Azure storage 계정은 계정 이름과 키를 구성 하는 자격 증명을 사용 합니다. 키가 자동으로 생성 되 고 암호화 키가 아닌 암호로 사용 됩니다. Key Vault 저장소 계정 키를 [Key Vault 비밀](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)으로 저장 하 여 관리 합니다. 

Key Vault 관리 저장소 계정 키 기능을 사용 하 여 Azure storage 계정으로 키를 나열 (동기화) 하 고 키를 주기적으로 다시 생성 (회전) 할 수 있습니다. 저장소 계정 및 클래식 저장소 계정에 대 한 키를 관리할 수 있습니다.

관리 되는 저장소 계정 키 기능을 사용 하는 경우 다음 사항을 고려 하세요.

- 호출자에 대 한 응답으로 키 값이 반환 되지 않습니다.
- Key Vault만 저장소 계정 키를 관리 해야 합니다. 키를 직접 관리 하지 않고 Key Vault 프로세스를 방해 하지 않습니다.
- 단일 Key Vault 개체만 저장소 계정 키를 관리 해야 합니다. 여러 개체의 키 관리를 허용 하지 않습니다.
- 사용자 계정을 사용 하 여 저장소 계정을 관리 하는 Key Vault를 요청 하 고 서비스 사용자는 사용할 수 없습니다.
- Key Vault만 사용 하 여 키를 다시 생성 합니다. 저장소 계정 키를 수동으로 다시 생성 하지 마세요.

Microsoft의 클라우드 기반 id 및 액세스 관리 서비스인 Azure Active Directory (Azure AD)와 Azure Storage 통합 하는 것이 좋습니다. Azure AD 통합은 [azure blob 및 큐](../storage/common/storage-auth-aad.md)에서 사용할 수 있으며, Azure Storage에 대 한 OAuth2 토큰 기반 액세스를 제공 합니다 (Azure Key Vault와 마찬가지로).

Azure AD를 사용 하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 id를 사용 하 여 클라이언트 응용 프로그램을 인증할 수 있습니다. Azure에서 실행할 때 [AZURE AD 관리 id](/azure/active-directory/managed-identities-azure-resources/) 를 사용할 수 있습니다. 관리 되는 id는 클라이언트 인증에 대 한 필요성을 제거 하 고 또는 응용 프로그램에 자격 증명을 저장 합니다.

Azure AD는 RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 부여를 관리 합니다 .이는 Key Vault 에서도 지원 됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>서비스 사용자 응용 프로그램 ID

Azure AD 테넌트는 등록 된 각 응용 프로그램을 [서비스 사용자](/azure/active-directory/develop/developer-glossary#service-principal-object)에 게 제공 합니다. 서비스 주체는 RBAC를 통해 다른 Azure 리소스에 액세스 하기 위해 권한 부여 설정 중에 사용 되는 응용 프로그램 ID로 사용 됩니다.

Key Vault은 모든 Azure AD 테넌트에서 미리 등록 된 Microsoft 응용 프로그램입니다. Key Vault은 각 Azure 클라우드에서 동일한 응용 프로그램 ID로 등록 됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 퍼블릭 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>필수 조건

이 가이드를 완료 하려면 먼저 다음을 수행 해야 합니다.

- [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps?view=azps-2.6.0)합니다.
- [키 자격 증명 모음 만들기](quick-create-powershell.md)
- [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-powershell) 저장소 계정 이름에는 소문자와 숫자만 사용 해야 합니다. 이름의 길이는 3 자에서 24 자 사이 여야 합니다.
      

## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용 하 여 PowerShell 세션을 인증 합니다. 

```azurepowershell-interactive
Connect-AzAccount
```
여러 Azure 구독이 있는 경우 [AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet을 사용 하 여 구독을 나열 하 고 [AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet에 사용할 구독을 지정할 수 있습니다. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에 사용할 변수를 설정 합니다. <YourResourceGroupName>, <YourStorageAccountName>및 <YourKeyVaultName> 자리 표시자를 업데이트 하 고 위의 [서비스 사용자 응용 프로그램 ID](#service-principal-application-id)에 지정 된 대로 $keyVaultSpAppId을 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`로 설정 해야 합니다.

또한 Azure PowerShell [AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) 및 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet을 사용 하 여 사용자 ID와 Azure storage 계정의 컨텍스트를 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>저장소 계정에 대 한 Key Vault 액세스 권한 부여

Key Vault가 스토리지 계정 키에 액세스하여 관리하려면 사용자가 스토리지 계정에 액세스 권한을 부여해야 합니다. Key Vault 애플리케이션은 스토리지 계정의 키를 *나열*하고 *다시 생성*할 수 있는 권한이 필요합니다. 이러한 권한은 기본 제공된 RBAC 역할 [스토리지 계정 키 운영자 서비스 역할](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)을 통해 사용 설정되어 있습니다. 

Azure PowerShell [AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet을 사용 하 여 범위를 저장소 계정으로 제한 하 여 Key Vault 서비스 사용자에 게이 역할을 할당 합니다.

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
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

Key Vault가 이미 스토리지 계정의 역할에 추가되었다면 *"역할 할당이 이미 있습니다."* 오류가 표시됩니다. 또한 Azure Portal에서 스토리지 계정 “액세스 제어(IAM)” 페이지를 사용하여 역할 할당을 확인할 수도 있습니다.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>관리 스토리지 계정에 사용자 계정 권한 부여

Azure PowerShell [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet을 사용 하 여 Key Vault 액세스 정책을 업데이트 하 고 사용자 계정에 저장소 계정 권한을 부여 합니다.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

스토리지 계정에 대한 권한은 Azure Portal의 스토리지 계정 “액세스 정책” 페이지에서 제공되지 않습니다.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault 인스턴스에 관리 스토리지 계정 추가

Azure PowerShell [AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet을 사용 하 여 Key Vault 인스턴스에서 관리 되는 저장소 계정을 만듭니다. `-DisableAutoRegenerateKey` 스위치는 스토리지 계정 키를 다시 생성하지 않도록 지정합니다.

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

저장소 계정 키를 정기적으로 다시 생성 [Key Vault Azure PowerShell cmdlet](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) 을 사용 하 여 다시 생성 기간을 설정할 수 있습니다. 이 예에서는 3 일의 다시 생성 기간을 설정 합니다. 3 Key Vault 일 후에는 ' key2 '를 다시 생성 하 고 활성 키를 ' key2 '에서 ' key1 '로 바꿉니다.

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

Key Vault를 요청 하 여 공유 액세스 서명 토큰을 생성할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유 하지 않고 저장소 계정의 리소스에 대 한 액세스 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상 시 키 지 않고 저장소 리소스를 공유 하는 안전한 방법을 제공 합니다.

이 섹션의 명령은 다음 작업을 완료 합니다.

- 계정 공유 액세스 서명 정의를 설정 합니다. 
- Blob, 파일, 테이블 및 큐 서비스에 대 한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 유형 서비스, 컨테이너 및 개체에 대해 생성 됩니다. 토큰은 지정 된 시작 및 종료 날짜를 사용 하 여 https를 통해 모든 사용 권한으로 만들어집니다.
- 자격 증명 모음에서 관리 되는 저장소 공유 액세스 서명 정의를 Key Vault 설정 합니다. 정의에는 만들어진 공유 액세스 서명 토큰의 템플릿 URI가 있습니다. 이 정의는 `account` 공유 액세스 서명 유형 이며 N 일 동안 유효 합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 암호로 저장 되었는지 확인 합니다.
- 
### <a name="set-variables"></a>변수 설정

먼저 다음 단계에서 PowerShell cmdlet에 사용할 변수를 설정 합니다. <YourStorageAccountName> 및 <YourKeyVaultName> 자리 표시자를 업데이트 해야 합니다.

또한 Azure PowerShell [AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet을 사용 하 여 Azure storage 계정의 컨텍스트를 가져옵니다.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure PowerShell [AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet을 사용 하 여 공유 액세스 서명 정의를 만듭니다.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$SasToken 값은 다음과 같습니다.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

Azure PowerShell [AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet을 사용 하 여 공유 액세스 서명 정의를 만듭니다.  사용자가 선택한 이름을 `-Name` 매개 변수에 제공할 수 있습니다.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure PowerShell [AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet을 사용 하 여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장 되었는지 확인할 수 있습니다.

먼저 주요 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

SAS 정의에 해당 하는 암호에는 다음 속성이 포함 됩니다.

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

이제 [AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 및 secret `Name` 속성을 사용 하 여 해당 암호의 콘텐츠를 볼 수 있습니다.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

이 명령의 출력은 SAS 정의 문자열을 표시 합니다.


## <a name="next-steps"></a>다음 단계

- [관리 스토리지 계정 키 예제](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 참조](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
