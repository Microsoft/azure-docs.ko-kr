---
title: 컨테이너에 대 한 일시 삭제 (미리 보기)
titleSuffix: Azure Storage
description: 컨테이너에 대 한 일시 삭제 (미리 보기)는 데이터를 보호 하 여 응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 수정 하거나 삭제 하는 경우 더 쉽게 데이터를 복구할 수 있도록 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: f41caf61ac23b05ba0ff95d785be2e7449adf75d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228633"
---
# <a name="soft-delete-for-containers-preview"></a>컨테이너에 대 한 일시 삭제 (미리 보기)

컨테이너에 대 한 일시 삭제 (미리 보기)는 데이터가 실수로 또는 실수로 수정 되거나 삭제 되지 않도록 보호 합니다. 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 하면 삭제 된 모든 컨테이너와 해당 내용이 지정 된 기간 동안 Azure Storage에 보존 됩니다. 보존 기간 중에 이전에 삭제 한 컨테이너와 해당 컨테이너 내에서 blob를 복원할 수 있습니다.

Blob 데이터에 대 한 종단 간 보호의 경우 다음과 같은 데이터 보호 기능을 사용 하도록 설정 하는 것이 좋습니다.

- 컨테이너 일시 삭제-컨테이너를 실수로 삭제 하거나 덮어쓰는 것을 방지 합니다. 컨테이너 일시 삭제를 사용 하도록 설정 하는 방법을 알아보려면 [컨테이너에 대해 일시 삭제 사용 및 관리](soft-delete-container-enable.md)를 참조 하세요.
- Blob 일시 삭제-개별 blob의 실수로 삭제 하거나 덮어쓰는 것을 방지 합니다. Blob 일시 삭제를 사용 하도록 설정 하는 방법을 알아보려면 [blob에 대 한 일시 삭제](soft-delete-blob-overview.md)를 참조 하세요.
- Blob 버전 관리-blob의 이전 버전을 자동으로 유지 관리 합니다. Blob 버전 관리를 사용 하는 경우 데이터를 잘못 수정 하거나 삭제 한 경우 이전 버전의 blob을 복원 하 여 데이터를 복구할 수 있습니다. Blob 버전 관리를 사용 하도록 설정 하는 방법을 알아보려면 [blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조 하세요.

> [!WARNING]
> 저장소 계정 삭제는 실행 취소할 수 없습니다. 일시 삭제는 저장소 계정 삭제를 방지 하지 않습니다. 저장소 계정이 실수로 삭제 되지 않도록 하려면 저장소 계정 리소스에 대해 **Cannotdelete** 잠금을 구성 합니다. Azure 리소스 잠금에 대 한 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

## <a name="how-container-soft-delete-works"></a>컨테이너 일시 삭제 작동 방식

컨테이너 일시 삭제를 사용 하도록 설정 하는 경우 1 일에서 365 일 사이의 삭제 된 컨테이너에 대 한 보존 기간을 지정할 수 있습니다. 기본 보존 기간은 7 일입니다. 보존 기간 중에 삭제 **취소 컨테이너** 작업을 호출 하 여 삭제 된 컨테이너를 복구할 수 있습니다.

컨테이너를 복원 하는 경우 해당 이름을 다시 사용 하지 않은 경우 원래 이름으로 복원할 수 있습니다. 원래 컨테이너 이름을 사용한 경우에는 새 이름으로 컨테이너를 복원할 수 있습니다.

보존 기간이 만료 되 면 컨테이너는 Azure Storage에서 영구적으로 삭제 되며 복구할 수 없습니다. 클록은 컨테이너가 삭제 된 지점의 보존 기간에 시작 됩니다. 언제 든 지 보존 기간을 변경할 수 있지만 업데이트 된 보존 기간은 새로 삭제 된 컨테이너에만 적용 된다는 점에 유의 하세요. 이전에 삭제 된 컨테이너는 컨테이너가 삭제 된 시간에 적용 된 보존 기간을 기준으로 영구적으로 삭제 됩니다.

컨테이너 일시 삭제를 사용 하지 않도록 설정 해도 이전에 일시 삭제 된 컨테이너는 영구적으로 삭제 되지 않습니다. 일시 삭제 된 모든 컨테이너는 컨테이너가 삭제 된 시간에 적용 된 보존 기간이 만료 되 면 영구적으로 삭제 됩니다.

## <a name="about-the-preview"></a>미리 보기 정보

컨테이너 일시 삭제는 다음 지역에서 미리 보기로 제공 됩니다.

- 프랑스 중부
- 캐나다 동부
- 캐나다 중부

> [!IMPORTANT]
> 컨테이너 일시 삭제 미리 보기는 비프로덕션 용도로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

Azure Storage REST API 버전 2019-12-12 이상에서는 컨테이너 일시 삭제를 지원 합니다.

### <a name="storage-account-support"></a>스토리지 계정 지원

컨테이너 일시 삭제는 다음과 같은 유형의 저장소 계정에 사용할 수 있습니다.

- 범용 v2 저장소 계정
- Blob storage 계정 차단
- Blob Storage 계정

저장소 계정이 범용 v1 계정인 경우 Azure Portal를 사용 하 여 범용 v2 계정으로 업그레이드 합니다. 저장소 계정에 대 한 자세한 내용은 [Azure storage 계정 개요](../common/storage-account-overview.md)를 참조 하세요.

Azure Data Lake Storage Gen2 사용 하도록 설정 된 계층적 네임 스페이스를 사용 하는 저장소 계정도 지원 됩니다.

### <a name="register-for-the-preview"></a>미리 보기에 등록

컨테이너 일시 삭제에 대 한 미리 보기에 등록 하려면 PowerShell 또는 Azure CLI를 사용 하 여 구독에 기능을 등록 하는 요청을 제출 합니다. 요청이 승인 되 면 새롭거나 기존 범용 v2, Blob storage 또는 premium block Blob storage 계정으로 컨테이너 일시 삭제를 사용 하도록 설정할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 등록 하려면 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 호출 합니다.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 등록 하려면 [az feature register](/cli/azure/feature#az-feature-register) 명령을 호출 합니다.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>등록 상태를 확인 합니다.

등록 상태를 확인 하려면 PowerShell 또는 Azure CLI를 사용 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 등록 상태를 확인 하려면 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 호출 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 등록 상태를 확인 하려면 [az feature](/cli/azure/feature#az-feature-show) 명령을 호출 합니다.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

컨테이너 일시 삭제를 사용 하도록 설정 하는 추가 요금은 부과 되지 않습니다. 일시 삭제 된 컨테이너의 데이터는 활성 데이터와 동일한 속도로 청구 됩니다.

## <a name="next-steps"></a>다음 단계

- [컨테이너 일시 삭제 구성](soft-delete-container-enable.md)
- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [Blob 버전 관리](versioning-overview.md)
