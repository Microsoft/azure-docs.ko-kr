---
title: 개체 복제(미리 보기) 구성
titleSuffix: Azure Storage
description: 한 스토리지 계정의 컨테이너에 있는 블록 blob을 다른 스토리지 계정과 비동기식으로 복사하도록 개체 복제를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c28e869bff1d0e921a1e5a952dbfcb21ee97d16b
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228327"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>블록 blob에 대한 개체 복제(미리 보기) 구성

개체 복제(미리 보기)는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제에 대 한 자세한 내용은 [개체 복제(미리 보기)](object-replication-overview.md)를 참조하세요.

개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에 대한 개체 복제를 구성하는 방법을 설명합니다. Azure Storage 리소스 공급자 클라이언트 라이브러리 중 하나를 사용하여 개체 복제를 구성할 수도 있습니다.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>복제 정책 및 규칙 만들기

개체 복제를 구성하기 전에 원본 및 대상 스토리지 계정이 아직 없는 경우 만듭니다. 두 계정 모두 범용 v2 스토리지 계정이어야 합니다. 자세한 내용은 [Azure Storage 계정 만들기](../common/storage-account-create.md)를 참조하세요.

스토리지 계정은 최대 두 개의 대상 계정에 대한 원본 계정으로 사용할 수 있습니다. 그리고 대상 계정에는 세 개 이상의 원본 계정이 있을 수 있습니다. 원본 및 대상 계정이 모두 다른 지역에 있을 수 있습니다. 각 대상 계정에 데이터를 복제하도록 별도의 복제 정책을 구성할 수 있습니다.

시작 하기 전에 다음 기능 미리 보기에 등록 했는지 확인 합니다.

- [개체 복제(미리 보기)](object-replication-overview.md)
- [Blob 버전 관리](versioning-overview.md)
- [Azure Blob 스토리지의 변경 피드 지원(미리 보기)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 개체 복제를 구성하기 전에 해당 스토리지 계정에 원본 및 대상 컨테이너가 아직 없는 경우 새로 만듭니다. 또한 원본 계정에서 blob 버전 관리 및 변경 피드를 사용하도록 설정하고 대상 계정에서 blob 버전 관리를 사용하도록 설정합니다.

Azure Portal에서 복제 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **Blob service**에서 **개체 복제**를 선택 합니다.
1. **복제 규칙 설정**을 선택 합니다.
1. 대상 구독 및 스토리지 계정을 선택합니다.
1. **컨테이너 쌍** 섹션에서 원본 계정에서 원본 컨테이너를 선택하고 대상 계정에서 대상 컨테이너를 선택합니다. 복제 정책에 따라 최대 10개의 컨테이너 쌍을 만들 수 있습니다.

    다음 이미지는 일련의 복제 규칙을 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Azure Portal의 복제 규칙을 보여 주는 스크린샷":::

1. 원할 경우 접두사 패턴과 일치하는 blob만 복사하도록 하나 이상의 필터를 지정합니다. 예를 들어, `b` 접두사를 지정하면 해당 문자로 시작하는 이름의 blob만 복제됩니다. 가상 디렉터리를 접두사의 일부로 지정할 수 있습니다. 접두사 문자열은 와일드 카드 문자를 지원 하지 않습니다.

    다음 이미지는 복제 규칙의 일부로 복사할 blob을 제한하는 필터를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="복제 규칙에 대한 필터를 보여 주는 스크린샷":::

1. 기본적으로 복사 범위는 새 개체만 복사하도록 설정됩니다. 컨테이너의 모든 개체를 복사하거나 사용자 지정 날짜 및 시간에 시작된 개체를 복사하려면 **변경** 링크를 선택하고 컨테이너 쌍의 복사 범위를 구성합니다.

    다음 그림은 사용자 지정 복사 범위를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="개체 복제에 대한 사용자 지정 복사 범위를 보여 주는 스크린샷":::

1. **저장 및 적용**을 선택하여 복제 정책을 만들고 데이터 복제를 시작합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 복제 정책을 만들려면 먼저 Az. Storage PowerShell 모듈의 버전 [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) 이상을 설치 합니다. 다음 단계에 따라 미리 보기 모듈을 설치합니다.

1. **설정**에 있는 **앱 및 기능** 설정을 사용하여 이전에 설치한 Azure PowerShell이 있으면 Windows에서 제거합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Az.Storage 미리 보기 모듈 설치

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음 예제에서는 원본 및 대상 계정에 대한 복제 정책을 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 복제 정책을 만들려면 먼저 Azure Storage에 대한 미리 보기 확장을 설치합니다.

```azurecli
az extension add -n storage-or-preview
```

다음으로, Azure 자격 증명을 사용하여 로그인합니다.

```azurecli
az login
```

원본 및 대상 스토리지 계정에서 blob 버전 관리를 사용하도록 설정하고 원본 계정에서 변경 피드를 사용하도록 설정합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

각 스토리지 계정에 원본 및 대상 컨테이너를 만듭니다.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

대상 계정에 새 복제 정책 및 연결된 규칙을 만듭니다.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

정책 ID를 사용하여 원본 계정에 정책을 만듭니다.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>복제 정책 제거

복제 정책 및 관련 규칙을 제거하려면 Azure Portal, PowerShell 또는 CLI를 사용합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 복제 정책을 제거하려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **설정**에서 **개체 복제**를 선택합니다.
1. 정책 이름 옆에 있는 **더 보기** 단추를 클릭합니다.
1. **규칙 삭제**를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>다음 단계

- [개체 복제 개요(미리 보기)](object-replication-overview.md)
