---
title: 저장소 계정에 사용 중인 암호화 키 모델 확인
titleSuffix: Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 저장소 계정에 대해 암호화 키를 관리 하는 방법을 확인 합니다. 키는 Microsoft (기본값) 또는 고객에 의해 관리 될 수 있습니다. 고객 관리 키는 Azure Key Vault에 저장 되어야 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 11e08427067efa5e7bd33b8d08d84443444a190b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078268"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>저장소 계정에 사용 중인 Azure Storage 암호화 키 모델 확인

저장소 계정의 데이터는 Azure Storage에 의해 자동으로 암호화 됩니다. Azure Storage 암호화는 저장소 계정 수준에서 암호화 키를 관리 하는 두 가지 옵션을 제공 합니다.

- **Microsoft에서 관리 하는 키.** 기본적으로 Microsoft는 저장소 계정을 암호화 하는 데 사용 되는 키를 관리 합니다.
- **고객 관리 키.** 필요에 따라 저장소 계정에 대 한 암호화 키를 관리 하도록 선택할 수 있습니다. 고객 관리 키는 Azure Key Vault에 저장 되어야 합니다.

또한 일부 Blob storage 작업에 대해 개별 요청 수준에서 암호화 키를 제공할 수 있습니다. 요청에 암호화 키를 지정 하면 해당 키는 저장소 계정에서 활성화 된 암호화 키를 재정의 합니다. 자세한 내용은 [Blob 저장소에 대 한 요청에 고객이 제공한 키 지정](../blobs/storage-blob-customer-provided-key.md)을 참조 하세요.

암호화 키에 대 한 자세한 내용은 [미사용 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md)를 참조 하세요.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>저장소 계정에 대 한 암호화 키 모델 확인

저장소 계정에서 암호화를 위해 Microsoft 관리 키 또는 고객이 관리 하는 키를 사용 하 고 있는지 확인 하려면 다음 방법 중 하나를 사용 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 저장소 계정에 대 한 암호화 모델을 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **암호화** 설정을 선택 하 고 설정을 확인 합니다.

다음 이미지는 Microsoft에서 관리 하는 키로 암호화 된 저장소 계정을 보여 줍니다.

![Microsoft에서 관리 하는 키로 암호화 된 계정 보기](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

그리고 다음 이미지는 고객이 관리 하는 키로 암호화 된 저장소 계정을 보여 줍니다.

![Azure Portal에서 암호화 키 설정을 보여 주는 스크린샷](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 암호화 모델을 확인 하려면 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출한 다음 계정에 대 한 **keysource** 속성을 확인 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

**Keysource** 속성의 값이 이면 `Microsoft.Storage` 계정은 Microsoft 관리 키를 사용 하 여 암호화 됩니다. **Keysource** 속성의 값이 이면 `Microsoft.Keyvault` 계정은 고객 관리 키를 사용 하 여 암호화 됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 암호화 모델을 확인 하려면 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 명령을 호출한 다음 계정에 대 한 **keysource** 속성을 확인 합니다.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

**Keysource** 속성의 값이 이면 `Microsoft.Storage` 계정은 Microsoft 관리 키를 사용 하 여 암호화 됩니다. **Keysource** 속성의 값이 이면 `Microsoft.Keyvault` 계정은 고객 관리 키를 사용 하 여 암호화 됩니다.

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화 관리](encryption-customer-managed-keys.md)
