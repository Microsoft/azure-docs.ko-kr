---
title: 계정 선택키 관리
titleSuffix: Azure Storage
description: 저장소 계정 액세스 키를 보고 관리 하 고 회전 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069662"
---
# <a name="manage-storage-account-access-keys"></a>저장소 계정 액세스 키 관리

스토리지 계정을 만들 때 Azure는 두 개의 512비트 스토리지 계정 액세스 키를 생성합니다. 이러한 키를 사용 하 여 공유 키 권한 부여를 통해 저장소 계정의 데이터에 대 한 액세스 권한을 부여할 수 있습니다.

Azure Key Vault를 사용 하 여 액세스 키를 관리 하 고 키를 정기적으로 회전 하 고 다시 생성 하는 것이 좋습니다. Azure Key Vault를 사용 하면 응용 프로그램을 중단 하지 않고 쉽게 키를 회전할 수 있습니다. 키를 수동으로 회전할 수도 있습니다.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>계정 액세스 키 보기

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 계정 액세스 키를 보고 복사할 수 있습니다. 또한 Azure Portal는 복사할 수 있는 저장소 계정에 대 한 연결 문자열을 제공 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 저장소 계정 액세스 키 또는 연결 문자열을 보고 복사 하려면:

1. [Azure Portal](https://portal.azure.com)의 스토리지 계정으로 이동합니다.
1. **설정**에서 **액세스 키**를 선택합니다. 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.
1. **Key1**에서 **키** 값을 찾고 **복사** 단추를 클릭 하 여 계정 키를 복사 합니다.
1. 또는 전체 연결 문자열을 복사할 수 있습니다. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Azure Portal에서 액세스 키를 보는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 계정 액세스 키를 검색 하려면 [AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) 명령을 호출 합니다.

다음 예에서는 첫 번째 키를 검색 합니다. 두 번째 키를 검색 하려면 `Value[1]` 대신을 사용 `Value[0]` 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 계정 액세스 키를 나열 하려면 다음 예제와 같이 [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) 명령을 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

두 키 중 하나를 사용 하 여 Azure Storage에 액세스할 수 있지만 일반적으로 첫 번째 키를 사용 하 고 키를 회전할 때 두 번째 키의 사용을 예약 하는 것이 좋습니다.

계정의 액세스 키를 보거나 읽으려면 사용자가 서비스 관리자 이거나 **Microsoft. Storage/storageAccounts/listkeys/action**을 포함 하는 Azure 역할을 할당 받아야 합니다. 이 작업을 포함 하는 일부 Azure 기본 제공 역할은 **소유자**, **참가자**및 **저장소 계정 키 운영자 서비스 역할** 역할입니다. 서비스 관리자 역할에 대 한 자세한 내용은 [클래식 구독 관리자 역할, azure 역할 및 AZURE AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요. Azure Storage의 기본 제공 역할에 대 한 자세한 내용은 [AZURE RBAC에 대 한 azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **저장소** 섹션을 참조 하세요.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault를 사용 하 여 액세스 키 관리

Azure Key Vault 사용 하 여 액세스 키를 관리 하 고 회전 하는 것이 좋습니다. 응용 프로그램은 Key Vault의 키에 안전 하 게 액세스할 수 있으므로 응용 프로그램 코드를 사용 하 여 해당 키를 저장 하지 않을 수 있습니다. 키 관리에 Key Vault를 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Key Vault 및 PowerShell을 사용 하 여 저장소 계정 키 관리](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>수동으로 액세스 키 회전

저장소 계정을 안전 하 게 유지 하기 위해 액세스 키를 정기적으로 회전 하는 것이 좋습니다. 가능 하면 Azure Key Vault를 사용 하 여 액세스 키를 관리 합니다. Key Vault 사용 하지 않는 경우 수동으로 키를 회전 해야 합니다.

키를 회전할 수 있도록 두 개의 액세스 키가 할당됩니다. 두 개의 키가 있으면 응용 프로그램이 프로세스 전체에서 Azure Storage에 대 한 액세스를 유지 관리할 수 있습니다.

> [!WARNING]
> 액세스 키를 다시 생성하면 스토리지 계정 키에 종속된 모든 애플리케이션과 Azure 서비스에 영향을 미칠 수 있습니다. 계정 키를 사용하여 스토리지 계정에 액세스하는 모든 클라이언트는 미디어 서비스, 클라우드, 데스크톱 및 모바일 애플리케이션, Azure Storage용 그래픽 사용자 인터페이스 애플리케이션(예: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/))을 포함하여 새로운 키를 사용하도록 업데이트되어야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 저장소 계정 액세스 키를 회전 하려면:

1. 저장소 계정에 대 한 보조 액세스 키를 참조 하도록 응용 프로그램 코드의 연결 문자열을 업데이트 합니다.
1. [Azure Portal](https://portal.azure.com)의 스토리지 계정으로 이동합니다.
1. **설정**에서 **액세스 키**를 선택합니다.
1. 저장소 계정에 대 한 기본 액세스 키를 다시 생성 하려면 기본 액세스 키 옆에 있는 **다시 생성** 단추를 선택 합니다.
1. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
1. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 저장소 계정 액세스 키를 회전 하려면:

1. 저장소 계정에 대 한 보조 액세스 키를 참조 하도록 응용 프로그램 코드의 연결 문자열을 업데이트 합니다.
1. 다음 예제와 같이 [AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) 명령을 호출 하 여 기본 액세스 키를 다시 생성 합니다.

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
1. 같은 방식으로 보조 액세스 키를 다시 생성합니다. 보조 키를 다시 생성 하려면 `key2` 대신 키 이름으로를 사용 `key1` 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정 액세스 키를 회전 하려면:

1. 저장소 계정에 대 한 보조 액세스 키를 참조 하도록 응용 프로그램 코드의 연결 문자열을 업데이트 합니다.
1. 다음 예제와 같이 [az storage account keys 갱신](/cli/azure/storage/account/keys#az-storage-account-keys-renew) 명령을 호출 하 여 기본 액세스 키를 다시 생성 합니다.

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
1. 같은 방식으로 보조 액세스 키를 다시 생성합니다. 보조 키를 다시 생성 하려면 `key2` 대신 키 이름으로를 사용 `key1` 합니다.

---

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

계정의 액세스 키를 회전 하려면 사용자가 서비스 관리자 이거나 **Microsoft. Storage/storageAccounts/regeneratekey/action**을 포함 하는 Azure 역할을 할당 받아야 합니다. 이 작업을 포함 하는 일부 Azure 기본 제공 역할은 **소유자**, **참가자**및 **저장소 계정 키 운영자 서비스 역할** 역할입니다. 서비스 관리자 역할에 대 한 자세한 내용은 [클래식 구독 관리자 역할, azure 역할 및 AZURE AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요. Azure Storage에 대 한 Azure 기본 제공 역할에 대 한 자세한 내용은 azure [RBAC에 대 한 azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **저장소** 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
