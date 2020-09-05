---
title: 암호화 범위 만들기 및 관리 (미리 보기)
description: 암호화 범위를 만들어 컨테이너 또는 blob 수준에서 blob 데이터를 격리 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.date: 08/25/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 32b46d21228bcd84fc3da11cc6ed42c740fece39
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870258"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>암호화 범위 만들기 및 관리 (미리 보기)

암호화 범위 (미리 보기)를 사용 하면 개별 blob 또는 컨테이너 수준에서 암호화를 관리할 수 있습니다. 암호화 범위는 스토리지 계정 내의 안전한 인클레이브에 Blob 데이터를 격리합니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다. 암호화 범위에 대 한 자세한 내용은 [Blob storage의 암호화 범위 (미리 보기)](../common/storage-service-encryption.md#encryption-scopes-for-blob-storage-preview)를 참조 하세요.

이 문서에서는 암호화 범위를 만드는 방법을 보여 줍니다. 또한 blob 또는 컨테이너를 만들 때 암호화 범위를 지정 하는 방법을 보여 줍니다.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>암호화 범위 만들기

Microsoft에서 관리 하는 키 또는 Azure Key Vault에 저장 된 고객이 관리 하는 키를 사용 하 여 암호화 범위를 만들 수 있습니다. 고객 관리 키를 사용 하 여 암호화 범위를 만들려면 먼저 Azure key vault를 만들고 범위에 사용 하려는 키를 추가 해야 합니다. 키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 속성이 모두 사용 하도록 설정 되어 있어야 하며, 저장소 계정과 동일한 지역에 있어야 합니다. 자세한 내용은 [Azure Key Vault를 통해 고객 관리 키를 사용하여 Azure Storage 암호화 관리](../common/encryption-customer-managed-keys.md)를 참조하세요.

암호화 범위는 만들 때 자동으로 사용 하도록 설정 됩니다. 암호화 범위를 만든 후에는 blob을 만들 때 지정할 수 있습니다. 컨테이너를 만들 때 기본 암호화 범위를 지정할 수도 있습니다. 컨테이너는 컨테이너의 모든 blob에 자동으로 적용 됩니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 암호화 범위를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **암호화** 설정을 선택 합니다.
1. **암호화 범위** 탭을 선택 합니다.
1. **추가** 단추를 클릭 하 여 새 암호화 범위를 추가 합니다.
1. **암호화 범위** 만들기 창에서 새 범위의 이름을 입력 합니다.
1. **Microsoft에서 관리** 하는 키 또는 **고객이 관리**하는 키 중에서 암호화 유형을 선택 합니다.
    - **Microsoft 관리 키**를 선택한 경우 **만들기** 를 클릭 하 여 암호화 범위를 만듭니다.
    - **고객 관리 키**를 선택한 경우 다음 그림에 표시 된 것 처럼이 암호화 범위에 사용할 주요 자격 증명 모음, 키 및 키 버전을 지정 합니다.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Azure Portal에서 암호화 범위를 만드는 방법을 보여 주는 스크린샷":::

Azure Storage 암호화를 위해 Azure Key Vault를 사용 하 여 고객 관리 키를 구성 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure Key Vault를 통해 고객 관리 키 구성](../common/storage-encryption-keys-portal.md)을 참조 하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 암호화 범위를 만들려면 먼저 Az. Storage preview 모듈 버전을 설치 합니다. 최신 미리 보기 버전을 사용 하는 것이 좋지만 암호화 범위가 버전 1.13.4-preview 이상에서 지원 됩니다. Az. Storage 모듈의 다른 버전을 제거 합니다.

다음 명령은 Az. Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) 모듈을 설치 합니다.

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft에서 관리 하는 키로 보호 되는 암호화 범위 만들기

Microsoft에서 관리 하는 키로 보호 되는 새 암호화 범위를 만들려면 매개 변수를 사용 하 여 **AzStorageEncryptionScope** 명령을 호출 합니다 `-StorageEncryption` .

예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>고객 관리 키로 보호 되는 암호화 범위 만들기

Azure Key Vault를 사용 하 여 고객 관리 키로 보호 되는 새 암호화 범위를 만들려면 먼저 저장소 계정에 대 한 고객 관리 키를 구성 합니다. 저장소 계정에 관리 되는 id를 할당 하 고 관리 되는 id를 사용 하 여 저장소 계정에 액세스할 수 있는 권한을 갖도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 해야 합니다. 자세한 내용은 [PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](../common/storage-encryption-keys-powershell.md)을 참조 하세요.

암호화 범위와 함께 사용 하기 위해 고객 관리 키를 구성 하려면 키 자격 증명 모음에서 **일시 삭제** 및 **제거 보호** 속성을 모두 사용 하도록 설정 해야 합니다. Key vault는 저장소 계정과 동일한 지역에 있어야 합니다. 자세한 내용은 [Azure Key Vault를 통해 고객 관리 키를 사용하여 Azure Storage 암호화 관리](../common/encryption-customer-managed-keys.md)를 참조하세요.

예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

그런 다음 매개 변수를 사용 하 여 **AzStorageEncryptionScope** 명령을 호출 `-KeyvaultEncryption` 하 고 키 URI를 지정 합니다. 키 URI에 키 버전을 포함 해야 합니다. 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용 하 여 암호화 범위를 만들려면 먼저 Azure CLI 버전 2.4.0 이상을 설치 합니다.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft에서 관리 하는 키로 보호 되는 암호화 범위 만들기

Microsoft에서 관리 하는 키로 보호 되는 새 암호화 범위를 만들려면 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 명령을 호출 하 고 `--key-source` 매개 변수를로 지정 합니다 `Microsoft.Storage` . 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>고객 관리 키로 보호 되는 암호화 범위 만들기

Microsoft에서 관리 하는 키로 보호 되는 새 암호화 범위를 만들려면 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 명령을 호출 하 고 `--key-source` 매개 변수를로 지정 합니다 `Microsoft.Storage` . 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

Azure Key Vault를 사용 하 여 고객 관리 키로 보호 되는 새 암호화 범위를 만들려면 먼저 저장소 계정에 대 한 고객 관리 키를 구성 합니다. 저장소 계정에 관리 되는 id를 할당 하 고 관리 되는 id를 사용 하 여 저장소 계정에 액세스할 수 있는 권한을 갖도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 해야 합니다. 자세한 내용은 [Azure CLI를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](../common/storage-encryption-keys-cli.md)을 참조 하세요.

암호화 범위와 함께 사용 하기 위해 고객 관리 키를 구성 하려면 키 자격 증명 모음에서 **일시 삭제** 및 **제거 보호** 속성을 모두 사용 하도록 설정 해야 합니다. Key vault는 저장소 계정과 동일한 지역에 있어야 합니다. 자세한 내용은 [Azure Key Vault를 통해 고객 관리 키를 사용하여 Azure Storage 암호화 관리](../common/encryption-customer-managed-keys.md)를 참조하세요.

예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

그런 다음 매개 변수를 사용 하 여 **az storage 계정 암호화 범위 만들기** 명령을 호출 `--key-uri` 하 고 키 URI를 지정 합니다. 키 URI에 키 버전을 포함 해야 합니다. 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="list-encryption-scopes-for-storage-account"></a>저장소 계정에 대 한 암호화 범위를 나열 합니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 저장소 계정에 대 한 암호화 범위를 보려면 저장소 계정에 대 한 **암호화 범위** 설정으로 이동 합니다. 이 창에서 암호화 범위를 사용 하거나 사용 하지 않도록 설정 하거나 암호화 범위에 대 한 키를 변경할 수 있습니다.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Azure Portal의 암호화 범위 목록을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 저장소 계정에 사용할 수 있는 암호화 범위를 나열 하려면 AzStorageEncryptionScope 명령을 호출 합니다. 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

저장소 계정을 사용 하 여 리소스 그룹의 모든 암호화 범위를 나열 하려면 다음과 같이 파이프라인 구문을 사용 합니다.

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI 있는 저장소 계정에 사용할 수 있는 암호화 범위를 나열 하려면 [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) 명령을 호출 합니다. 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>기본 암호화 범위를 사용 하 여 컨테이너 만들기

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 기본 암호화 범위를 사용 하 여 컨테이너를 만들려면 먼저 [암호화 범위 만들기](#create-an-encryption-scope)에 설명 된 대로 암호화 범위를 만듭니다. 다음 단계를 수행 하 여 컨테이너를 만듭니다.

1. 저장소 계정의 컨테이너 목록으로 이동 하 고 **추가** 단추를 선택 하 여 새 컨테이너를 만듭니다.
1. **새 컨테이너** 창에서 **고급** 설정을 확장 합니다.
1. **암호화 범위** 드롭다운에서 컨테이너의 기본 암호화 범위를 선택 합니다.
1. 컨테이너의 모든 blob이 기본 암호화 범위를 사용 하도록 요구 하려면 **컨테이너의 모든 blob에 대해이 암호화 범위를 사용**하려면 확인란을 선택 합니다. 이 확인란을 선택 하면 컨테이너의 개별 blob에서 기본 암호화 범위를 재정의할 수 없습니다.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="기본 암호화 범위를 사용 하는 컨테이너를 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 기본 암호화 범위를 사용 하 여 컨테이너를 만들려면 [AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) 명령을 호출 하 고 매개 변수의 범위를 지정 합니다 `-DefaultEncryptionScope` . **AzRmStorageContainer** 명령은 Azure Storage 리소스 공급자를 사용 하 여 컨테이너를 만들며,이를 통해 암호화 범위 및 기타 리소스 관리 작업을 구성할 수 있습니다.

컨테이너가 모든 blob의 기본 범위를 사용 하도록 구성 된 경우를 제외 하 고는 자체 암호화 범위를 사용 하 여 개별 blob을 만들 수 있습니다. 컨테이너의 모든 blob이 컨테이너의 기본 범위를 사용 하도록 강제 하려면 `-PreventEncryptionScopeOverride` 매개 변수를로 설정 `true` 합니다.

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI 있는 기본 암호화 범위를 사용 하 여 컨테이너를 만들려면 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 명령을 호출 하 고 매개 변수에 대 한 범위를 지정 합니다 `--default-encryption-scope` . 컨테이너가 모든 blob의 기본 범위를 사용 하도록 구성 된 경우를 제외 하 고는 자체 암호화 범위를 사용 하 여 개별 blob을 만들 수 있습니다. 컨테이너의 모든 blob이 컨테이너의 기본 범위를 사용 하도록 강제 하려면 `--prevent-encryption-scope-override` 매개 변수를로 설정 `true` 합니다.

다음 예제에서는 Azure AD 계정을 사용하여 컨테이너를 만드는 작업에 권한을 부여합니다. 계정 액세스 키를 사용할 수도 있습니다. 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](../common/authorize-data-operations-cli.md)를 참조하세요.

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

기본 암호화 범위가 있는 컨테이너에 blob을 업로드할 때 클라이언트가 범위를 지정 하려고 하 고 blob이 기본 범위를 재정의 하지 않도록 구성 된 경우 해당 작업은 컨테이너 암호화 정책에 의해 요청이 금지 됨을 나타내는 메시지와 함께 실패 합니다.

## <a name="upload-a-blob-with-an-encryption-scope"></a>암호화 범위를 사용 하 여 blob 업로드

Blob을 업로드 하는 경우 해당 blob에 대 한 암호화 범위를 지정 하거나, 컨테이너에 대 한 기본 암호화 범위 (지정 된 경우)를 사용할 수 있습니다. 

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에 지정 된 암호화 범위를 사용 하 여 blob을 업로드 하려면 먼저 [암호화 범위 만들기](#create-an-encryption-scope)에 설명 된 대로 암호화 범위를 만듭니다. 다음 단계를 수행 하 여 blob을 만듭니다.

1. Blob을 업로드 하려는 컨테이너로 이동 합니다.
1. **업로드** 단추를 선택 하 고 업로드할 blob을 찾습니다.
1. **Blob 업로드** 창에서 **고급** 설정을 확장 합니다.
1. **암호화 범위** 드롭다운 섹션을 찾습니다. 기본적으로 blob는 컨테이너에 대 한 기본 암호화 범위 (지정 된 경우)를 사용 하 여 만들어집니다. 컨테이너에서 blob이 기본 암호화 범위를 사용 해야 하는 경우에는이 섹션을 사용할 수 없습니다.
1. 업로드할 blob에 대해 다른 범위를 지정 하려면 **기존 범위 선택**을 선택한 다음 드롭다운에서 원하는 범위를 선택 합니다.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="암호화 범위를 사용 하 여 blob을 업로드 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 지정 된 암호화 범위를 사용 하 여 blob을 업로드 하려면 [AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 명령을 호출 하 고 blob에 대 한 암호화 범위를 제공 합니다.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용 하 여 지정 된 암호화 범위를 사용 하 여 blob을 업로드 하려면 [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) 명령을 호출 하 고 blob에 대 한 암호화 범위를 제공 합니다.

Azure Cloud Shell를 사용 하는 경우 [Blob 업로드](storage-quickstart-blobs-cli.md#upload-a-blob) 에 설명 된 단계에 따라 루트 디렉터리에 파일을 만듭니다. 그런 다음, 다음 샘플을 사용 하 여이 파일을 blob에 업로드할 수 있습니다.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>범위에 대 한 암호화 키 변경

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal 범위를 보호 하는 키를 변경 하려면 다음 단계를 수행 합니다.

1. **암호화 범위** 탭으로 이동 하 여 저장소 계정에 대 한 암호화 범위 목록을 봅니다.
1. 수정 하려는 범위 옆에 있는 **자세히** 단추를 선택 합니다.
1. **암호화 범위 편집** 창에서 암호화 유형을 Microsoft 관리 키에서 고객 관리 키로 변경 하거나 그 반대로 변경할 수 있습니다.
1. 새 고객 관리 키를 선택 하려면 **새 키 사용** 을 선택 하 고 키 자격 증명 모음, 키 및 키 버전을 지정 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

고객 관리 키에서 PowerShell을 사용 하 여 Microsoft 관리 키로 암호화 범위를 보호 하는 키를 변경 하려면 **AzStorageEncryptionScope** 명령을 호출 하 고 매개 변수를 전달 합니다 `-StorageEncryption` .

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Microsoft 관리 키에서 고객 관리 키로 암호화 범위를 보호 하는 키를 변경 하려면 먼저 저장소 계정에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키를 사용 하도록 설정 했는지 확인 합니다. 자세한 내용은 [PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](../common/storage-encryption-keys-powershell.md)을 참조 하세요. 그런 다음 **AzStorageEncryptionScope** 명령을 호출 하 고 `-KeyUri` 및 매개 변수를 전달 합니다 `-KeyvaultEncryption` .

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용 하 여 고객 관리 키에서 Microsoft 관리 키로 암호화 범위를 보호 하는 키를 변경 하려면 [az storage account encryption scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 명령을 호출 하 고 값을 사용 하 여 `--key-source` 매개 변수를 전달 합니다 `Microsoft.Storage` .

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Microsoft 관리 키에서 고객 관리 키로 암호화 범위를 보호 하는 키를 변경 하려면 먼저 저장소 계정에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키를 사용 하도록 설정 했는지 확인 합니다. 자세한 내용은 [Azure CLI를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](../common/storage-encryption-keys-cli.md)을 참조 하세요. 다음으로 **az storage account encryption-scope update** 명령을 호출 하 고 매개 변수를 전달 하 `--key-uri` 고 `--key-source` 매개 변수를 값으로 전달 합니다 `Microsoft.KeyVault` .

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>암호화 범위를 사용 하지 않도록 설정

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 암호화 범위를 사용 하지 않도록 설정 하려면 저장소 계정에 대 한 **암호화 범위** 설정으로 이동 하 여 원하는 암호화 범위를 선택 하 고 **사용 안 함**을 선택 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 암호화 범위를 사용 하지 않도록 설정 하려면 `-State` `disabled` 다음 예제와 같이 AzStorageEncryptionScope 명령을 호출 하 고 값을 사용 하 여 매개 변수를 포함 합니다. 암호화 범위를 다시 사용 하도록 `-State` 설정 하려면 매개 변수를로 설정 하 여 동일한 명령을 호출 합니다 `enabled` . 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI에서 암호화 범위를 사용 하지 않도록 설정 하려면 다음 예제와 같이 [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 명령을 호출 하 고 `--state` 값이 인 매개 변수를 포함 합니다 `Disabled` . 암호화 범위를 다시 사용 하도록 `--state` 설정 하려면 매개 변수를로 설정 하 여 동일한 명령을 호출 합니다 `Enabled` . 예제의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
- [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화 관리](../common/encryption-customer-managed-keys.md)
