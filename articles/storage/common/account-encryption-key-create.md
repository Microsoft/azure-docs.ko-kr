---
title: 테이블 및 큐에 대해 고객이 관리 하는 키를 지 원하는 계정 만들기
titleSuffix: Azure Storage
description: 테이블 및 큐에 대 한 고객 관리 키 구성을 지 원하는 저장소 계정을 만드는 방법에 대해 알아봅니다. Azure CLI 또는 Azure Resource Manager 템플릿을 사용 하 여 Azure Storage 암호화를 위한 계정 암호화 키에 의존 하는 저장소 계정을 만듭니다. 그런 다음 계정에 대 한 고객 관리 키를 구성할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: d60a6f9032a39ab4889ce0db154739c5cb3b540b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070499"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>테이블 및 큐에 대해 고객이 관리 하는 키를 지 원하는 계정 만들기

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 Queue storage 및 Table storage는 서비스로 범위가 지정 되 고 Microsoft에서 관리 하는 키를 사용 합니다. 또한 고객이 관리 하는 키를 사용 하 여 큐 또는 테이블 데이터를 암호화할 수 있습니다. 큐 및 테이블에서 고객 관리 키를 사용 하려면 먼저 서비스 대신 계정으로 범위가 한정 된 암호화 키를 사용 하는 저장소 계정을 만들어야 합니다. 큐 및 테이블 데이터에 대 한 계정 암호화 키를 사용 하는 계정을 만든 후에는 해당 저장소 계정에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키를 구성할 수 있습니다.

이 문서에서는 계정으로 범위가 지정 된 키에 의존 하는 저장소 계정을 만드는 방법을 설명 합니다. 계정을 처음 만들 때 Microsoft는 계정 키를 사용 하 여 계정의 데이터를 암호화 하 고 Microsoft는 키를 관리 합니다. 그런 다음 사용자 고유의 키를 제공 하 고, 키 버전을 업데이트 하 고, 키를 회전 하 고, 액세스 제어를 취소할 수 있는 기능을 포함 하 여, 계정에 대 한 고객 관리 키를 구성 하 여 이러한 혜택을 활용할 수 있습니다.

## <a name="about-the-feature"></a>기능 정보

큐 및 테이블 저장소의 계정 암호화 키에 의존 하는 저장소 계정을 만들려면 먼저를 등록 하 여 Azure에서이 기능을 사용 해야 합니다. 제한 된 용량으로 인해 액세스 요청이 승인 되기까지 몇 개월이 걸릴 수 있습니다.

다음 지역에서 큐 및 테이블 저장소의 계정 암호화 키에 의존 하는 저장소 계정을 만들 수 있습니다.

- 미국 동부
- 미국 중남부
- 미국 서부 2  

### <a name="register-to-use-the-account-encryption-key"></a>계정 암호화 키를 사용 하도록 등록

큐 또는 테이블 저장소에 계정 암호화 키를 사용 하도록 등록 하려면 PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 등록 하려면 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 호출 합니다.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 등록 하려면 [az feature register](/cli/azure/feature#az-feature-register) 명령을 호출 합니다.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[템플릿](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>등록 상태를 확인 합니다.

큐 또는 테이블 저장소에 대 한 등록 상태를 확인 하려면 PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 등록 상태를 확인 하려면 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 호출 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 등록 상태를 확인 하려면 [az feature](/cli/azure/feature#az-feature-show) 명령을 호출 합니다.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[템플릿](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Azure Storage 리소스 공급자를 다시 등록 합니다.

등록이 승인 된 후 Azure Storage 리소스 공급자를 다시 등록 해야 합니다. PowerShell 또는 Azure CLI를 사용 하 여 리소스 공급자를 다시 등록 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 리소스 공급자를 다시 등록 하려면 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 호출 합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 리소스 공급자를 다시 등록 하려면 [az provider register](/cli/azure/provider#az-provider-register) 명령을 호출 합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[템플릿](#tab/template)

N/A

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>계정 암호화 키를 사용 하는 계정 만들기

저장소 계정을 만들 때 큐 및 테이블에 대 한 계정 암호화 키를 사용 하도록 새 저장소 계정을 구성 해야 합니다. 계정이 생성 된 후에는 암호화 키의 범위를 변경할 수 없습니다.

저장소 계정은 범용 v2 유형 이어야 합니다. 저장소 계정을 만들고 Azure CLI 또는 Azure Resource Manager 템플릿을 사용 하 여 계정 암호화 키를 사용 하도록 구성할 수 있습니다.

> [!NOTE]
> 저장소 계정을 만들 때 계정 암호화 키를 사용 하 여 데이터를 암호화 하도록 큐 및 테이블 저장소만 선택적으로 구성할 수 있습니다. Blob storage 및 Azure Files 항상 계정 암호화 키를 사용 하 여 데이터를 암호화 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 계정 암호화 키에 의존 하는 저장소 계정을 만들려면 Azure PowerShell module 버전 3.4.0 이상을 설치 했는지 확인 합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조 하세요.

다음으로, [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출 하 고 적절 한 매개 변수를 사용 하 여 범용 v2 저장소 계정을 만듭니다.

- 옵션을 포함 하 `-EncryptionKeyTypeForQueue` 고 해당 값을로 설정 하 여 `Account` 계정 암호화 키를 사용 하 여 Queue storage의 데이터를 암호화 합니다.
- 옵션을 포함 하 `-EncryptionKeyTypeForTable` 고 해당 값을로 설정 하 여 `Account` 계정 암호화 키를 사용 하 여 테이블 저장소의 데이터를 암호화 합니다.

다음 예에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 되 고, 계정 암호화 키를 사용 하 여 큐 및 테이블 저장소의 데이터를 암호화 하는 범용 v2 저장소 계정을 만드는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 계정 암호화 키에 의존 하는 저장소 계정을 만들려면 Azure CLI 버전 2.0.80 이상을 설치 했는지 확인 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로, 다음과 같이 적절 한 매개 변수를 사용 하 여 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 호출 하 여 범용 v2 저장소 계정을 만듭니다.

- 옵션을 포함 하 `--encryption-key-type-for-queue` 고 해당 값을로 설정 하 여 `Account` 계정 암호화 키를 사용 하 여 Queue storage의 데이터를 암호화 합니다.
- 옵션을 포함 하 `--encryption-key-type-for-table` 고 해당 값을로 설정 하 여 `Account` 계정 암호화 키를 사용 하 여 테이블 저장소의 데이터를 암호화 합니다.

다음 예에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 되 고, 계정 암호화 키를 사용 하 여 큐 및 테이블 저장소의 데이터를 암호화 하는 범용 v2 저장소 계정을 만드는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 되 고, 계정 암호화 키를 사용 하 여 큐 및 테이블 저장소에 대 한 데이터를 암호화 하는 범용 v2 저장소 계정을 만듭니다. 꺾쇠 괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

계정 암호화 키를 사용 하는 계정을 만든 후에는 다음 문서 중 하나를 참조 하 여 Azure Key Vault에서 고객 관리 키를 구성 합니다.

- [Azure Portal을 사용하여 Azure Key Vault로 고객 관리형 키 구성](storage-encryption-keys-portal.md)
- [PowerShell을 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키 구성](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>계정 암호화 키 확인

저장소 계정의 서비스가 계정 암호화 키를 사용 하 고 있는지 확인 하려면 Azure CLI [az storage account](/cli/azure/storage/account#az-storage-account-show) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 집합을 반환 합니다. `keyType`암호화 속성 내에서 각 서비스에 대 한 필드를 찾아로 설정 되어 있는지 확인 `Account` 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

저장소 계정의 서비스가 계정 암호화 키를 사용 하 고 있는지 확인 하려면 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 집합을 반환 합니다. `KeyType`속성 내에서 각 서비스에 대 한 필드를 찾아 `Encryption` 로 설정 되어 있는지 확인 `Account` 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

저장소 계정의 서비스가 계정 암호화 키를 사용 하 고 있는지 확인 하려면 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 집합을 반환 합니다. `keyType`암호화 속성 내에서 각 서비스에 대 한 필드를 찾아로 설정 되어 있는지 확인 `Account` 합니다.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md) 
- [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
