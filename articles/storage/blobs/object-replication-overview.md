---
title: 개체 복제 개요(미리 보기)
titleSuffix: Azure Storage
description: 개체 복제(미리 보기)는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제를 사용하여 읽기 요청에 대한 대기 시간을 최소화하고, 컴퓨팅 워크로드에 대한 효율성을 높이고, 데이터 배포를 최적화하며 비용을 최소화합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 434fe938261c1576a5f0c3c8f08ffa8ed3243a27
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230724"
---
# <a name="object-replication-for-block-blobs-preview"></a>블록 Blob에 대한 개체 복제(미리 보기)

개체 복제(미리 보기)는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제에서 지원하는 몇 가지 시나리오는 다음과 같습니다.

- **대기 시간 최소화.** 개체 복제를 사용하면 클라이언트가 물리적으로 더 근접한 지역의 데이터를 사용할 수 있으므로 읽기 요청 대기 시간을 줄일 수 있습니다.
- **컴퓨팅 작업의 효율성 제고.** 개체 복제를 사용하는 경우 컴퓨팅 워크로드는 서로 다른 지역에서 동일한 블록 Blob 집합을 처리할 수 있습니다.
- **데이터 배포 최적화.** 단일 위치의 데이터를 처리하거나 분석한 후 추가 지역에만 결과를 복제할 수 있습니다.
- **비용 최적화.** 데이터가 복제된 후에는 수명 주기 관리 정책을 사용해 해당 데이터를 보관 계층으로 이동하여 비용을 줄일 수 있습니다.

다음 다이어그램에서는 개체 복제에서 한 지역의 원본 스토리지 계정으로부터 블록 Blob을 두 개의 다른 지역에 있는 대상 계정으로 복제하는 방법을 보여 줍니다.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="개체 복제 작동 방식을 보여 주는 다이어그램":::

개체 복제를 구성하는 방법에 대한 자세한 내용은 [개체 복제 구성(미리 보기)](object-replication-configure.md)을 참조하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="object-replication-policies-and-rules"></a>개체 복제 정책 및 규칙

개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다.

개체 복제를 구성하고 나면 Azure Storage에서 원본 계정에 대한 변경 피드를 정기적으로 확인하고 대상 계정에 대한 쓰기 또는 삭제 작업을 비동기적으로 복제합니다. 복제 대기 시간은 복제 중인 블록 Blob의 크기에 따라 달라집니다.

> [!IMPORTANT]
> 블록 Blob 데이터는 비동기식으로 복제되므로 원본 계정 및 대상 계정이 즉시 동기화되지는 않습니다. 현재 데이터를 대상 계정에 복제하는 데 걸리는 시간에 대한 SLA는 없습니다.

### <a name="replications-policies"></a>복제 정책

개체 복제를 구성하면 Azure Storage 리소스 공급자를 통해 원본 계정 및 대상 계정 모두에 복제 정책이 만들어집니다. 복제 정책은 정책 ID로 식별됩니다. 복제가 발생하려면 원본 및 대상 계정의 정책이 동일한 정책 ID를 가져야 합니다.

스토리지 계정은 최대 두 개의 대상 계정에 대한 원본 계정으로 사용할 수 있습니다. 그리고 대상 계정에는 세 개 이상의 원본 계정이 있을 수 있습니다. 원본 및 대상 계정이 모두 다른 지역에 있을 수 있습니다. 각 대상 계정에 데이터를 복제하도록 별도의 복제 정책을 구성할 수 있습니다.

### <a name="replication-rules"></a>복제 규칙

복제 규칙은 Azure Storage 원본 컨테이너에서 대상 컨테이너로 Blob을 복제하는 방법을 지정합니다. 각 복제 정책에 대해 최대 10개의 복제 규칙을 지정할 수 있습니다. 각 규칙은 단일 원본 및 대상 컨테이너를 정의하며 각 원본 및 대상 컨테이너는 하나의 규칙에만 사용할 수 있습니다.

복제 규칙을 만드는 경우 기본적으로 이후에 원본 컨테이너에 추가되는 새 블록 Blob만 복사됩니다. 신규 및 기존 블록 Blob을 모두 복사하도록 지정하거나 지정된 시간 이후로 생성된 블록 Blob을 복사하도록 사용자 지정 복사 범위를 정의할 수도 있습니다.

하나 이상의 필터를 복제 규칙의 일부로 지정하여 접두사를 기준으로 블록 Blob을 필터링할 수도 있습니다. 접두사를 지정하면 원본 컨테이너에서 해당 접두사와 일치하는 Blob만 대상 컨테이너에 복사됩니다.

원본 및 대상 컨테이너가 모두 있어야 규칙에서 지정할 수 있습니다. 복제 정책을 만든 후에 대상 컨테이너는 읽기 전용이 됩니다. 대상 컨테이너에 대한 쓰기 시도가 실패하고 오류 코드 409(충돌)가 발생합니다. 그러나 대상 컨테이너의 blob에 대 한 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출 하 여 보관 계층으로 이동할 수 있습니다. 보관 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md#archive-access-tier)을 참조 하세요.

## <a name="about-the-preview"></a>미리 보기 정보

개체 복제는 범용 v2 스토리지 계정에 대해서만 지원됩니다. 개체 복제는 다음 지역에서 미리 보기로 제공됩니다.

- 프랑스 중부
- 캐나다 동부
- 캐나다 중부
- 미국 동부 2
- 미국 중부

개체 복제를 사용하려면 원본 및 대상 계정이 둘 다 이러한 지역 중 하나에 있어야 합니다. 계정은 두 개의 다른 지역에 있을 수 있습니다.

미리 보기 중에는 스토리지 계정 간에 데이터를 복제하는 것과 관련된 추가 비용이 없습니다.

> [!IMPORTANT]
> 개체 복제 미리 보기는 프로덕션 이외 용도로만 사용해야 합니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

### <a name="prerequisites-for-object-replication"></a>개체 복제를 위한 필수 구성 요소

개체를 복제하려면 다음 Azure Storage 기능을 사용하도록 설정해야 합니다. 
- [변경 피드](storage-blob-change-feed.md)
- [버전 관리](versioning-overview.md)

개체 복제를 구성하기 전에 해당 필수 구성 요소를 사용하도록 설정합니다. 원본 계정에서 변경 피드를 사용하도록 설정하고 원본 및 대상 계정 모두에서 Blob 버전 관리를 사용하도록 설정해야 합니다. 이러한 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [변경 피드를 사용하거나 사용하지 않도록 설정](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)

변경 피드 및 Blob 버전 관리 미리 보기를 사용하도록 설정하기 전에 먼저 등록해야 합니다.

변경 피드 및 Blob 버전 관리를 사용하도록 설정하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

### <a name="register-for-the-preview"></a>미리 보기에 등록

PowerShell 또는 Azure CLI를 사용하여 개체 복제 미리 보기에 등록할 수 있습니다. 변경 피드 및 Blob 버전 관리 미리 보기를 아직 등록하지 않은 경우에도 등록해야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 미리 보기에 등록하려면 다음 명령을 실행합니다.

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for Blob versioning
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 미리 보기에 등록하려면 다음 명령을 실행합니다.

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>등록 상태 확인

PowerShell 또는 Azure CLI를 사용하여 등록 요청의 상태를 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 등록 요청의 상태를 확인하려면 다음 명령을 실행합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 등록 요청의 상태를 확인하려면 다음 명령을 실행합니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>질문하기 또는 피드백 제공

개체 복제 미리 보기에 대해 질문을 하거나 피드백을 제공하려면 Microsoft에 AzureStorageFeedback@microsoft.com으로 문의하세요. Azure Storage에 대한 아이디어와 제안은 [Azure Storage 피드백 포럼](https://feedback.azure.com/forums/217298-storage)에 언제든지 남겨주세요.

## <a name="next-steps"></a>다음 단계

- [개체 복제 구성(미리 보기)](object-replication-configure.md)
- [Azure Blob 스토리지의 변경 피드 지원(미리 보기)](storage-blob-change-feed.md)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
