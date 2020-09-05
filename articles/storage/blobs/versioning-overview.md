---
title: Blob 버전 관리
titleSuffix: Azure Storage
description: Blob 저장소 버전 관리에서는 개체의 이전 버전을 자동으로 유지 관리 하 고 타임 스탬프를 사용 하 여 식별 합니다. 잘못 수정 되거나 삭제 된 경우 이전 버전의 blob을 복원 하 여 데이터를 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 72597d445be41ede47d043d11653df139bc52d0d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226266"
---
# <a name="blob-versioning"></a>Blob 버전 관리

Blob 저장소 버전 관리를 사용 하도록 설정 하 여 이전 버전의 개체를 자동으로 유지 관리할 수 있습니다.  Blob 버전 관리를 사용 하는 경우 데이터를 잘못 수정 하거나 삭제 한 경우 이전 버전의 blob을 복원 하 여 데이터를 복구할 수 있습니다.

Blob 버전 관리는 저장소 계정에서 사용 하도록 설정 되며 저장소 계정의 모든 blob에 적용 됩니다. 저장소 계정에 대해 blob 버전 관리를 사용 하도록 설정 하면 Azure Storage는 저장소 계정의 모든 blob에 대 한 버전을 자동으로 유지 관리 합니다.

뛰어난 데이터 보호를 위해 blob 버전 관리를 사용 하 여 이전 버전의 blob을 유지 하는 것이 좋습니다. 가능 하면 blob 스냅숏 대신 blob 버전 관리를 사용 하 여 이전 버전을 유지 관리 합니다. Blob 스냅숏은 이전 버전의 blob을 유지 하는 것과 유사한 기능을 제공 하지만 응용 프로그램에서 스냅숏을 수동으로 유지 관리 해야 합니다.

Blob 버전 관리를 사용 하도록 설정 하는 방법을 알아보려면 [blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조 하세요.

> [!IMPORTANT]
> Blob 버전 관리를 통해 저장소 계정이 나 컨테이너를 실수로 삭제 하는 경우를 복구할 수 없습니다. 저장소 계정이 실수로 삭제 되지 않도록 하려면 저장소 계정 리소스에 대해 **Cannotdelete** 잠금을 구성 합니다. Azure 리소스 잠금에 대 한 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Blob 버전 관리 작동 방법

버전은 지정 된 시점에 blob의 상태를 캡처합니다. 저장소 계정에 blob 버전 관리를 사용 하도록 설정 하면 Azure Storage는 blob이 수정 되거나 삭제 될 때마다 blob의 새 버전을 자동으로 만듭니다.

버전 관리를 사용 하도록 설정 된 blob을 만들 때 새 blob은 blob (또는 기본 blob)의 현재 버전입니다. 이후에 해당 blob을 수정 하면 Azure Storage는 blob의 상태를 수정 하기 전에 캡처하는 버전을 만듭니다. 수정 된 blob은 새로운 현재 버전이 됩니다. 새 버전은 blob를 수정할 때마다 생성 됩니다.

버전 관리를 사용 하는 blob을 삭제 하면 Azure Storage는 blob의 상태를 삭제 하기 전에 캡처하는 버전을 만듭니다. 그러면 blob의 현재 버전이 삭제 되지만 blob의 버전은 유지 되므로 필요한 경우 다시 만들 수 있습니다. 

Blob 버전은 변경할 수 없습니다. 기존 blob 버전의 콘텐츠나 메타 데이터는 수정할 수 없습니다.

Blob 버전 관리는 범용 v2, 블록 blob 및 Blob 저장소 계정에 사용할 수 있습니다. Azure Data Lake Storage Gen2에서 사용 하도록 설정 된 계층적 네임 스페이스를 사용 하는 저장소 계정은 현재 지원 되지 않습니다.

2019-10-10 이상의 Azure Storage REST API는 blob 버전 관리를 지원 합니다.

### <a name="version-id"></a>버전 ID

각 blob 버전은 버전 ID로 식별 됩니다. 버전 ID 값은 blob이 작성 또는 업데이트 된 타임 스탬프입니다. 버전 ID는 버전이 생성 될 때 할당 됩니다.

버전 ID를 제공 하 여 특정 버전의 blob에 대해 읽기 또는 삭제 작업을 수행할 수 있습니다. 버전 ID를 생략 하는 경우이 작업은 현재 버전 (기본 blob)에 대해 작동 합니다.

Blob을 만들거나 수정 하기 위해 쓰기 작업을 호출 하는 경우 Azure Storage는 응답에서 *x m s-버전 id* 헤더를 반환 합니다. 이 헤더에는 쓰기 작업으로 만들어진 blob의 현재 버전에 대 한 버전 ID가 포함 됩니다.

버전 ID는 버전의 수명 동안 동일 하 게 유지 됩니다.

### <a name="versioning-on-write-operations"></a>쓰기 작업에 대 한 버전 관리

Blob 버전 관리를 켜면 blob에 대 한 각 쓰기 작업이 새 버전을 만듭니다. 쓰기 작업에는 [Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list), [Blob 복사](/rest/api/storageservices/copy-blob)및 [blob 메타 데이터 설정이](/rest/api/storageservices/set-blob-metadata)포함 됩니다.

쓰기 작업에서 새 blob을 만드는 경우 결과 blob은 blob의 현재 버전입니다. 쓰기 작업에서 기존 blob을 수정 하면 새 데이터가 최신 버전인 업데이트 된 blob에 캡처되고 Azure Storage는 blob의 이전 상태를 저장 하는 버전을 만듭니다.

간단히 하기 위해이 문서에 표시 된 다이어그램은 버전 ID를 간단한 정수 값으로 표시 합니다. 실제로 버전 ID는 타임 스탬프입니다. 현재 버전은 파란색으로 표시 되 고 이전 버전은 회색으로 표시 됩니다.

다음 다이어그램에서는 쓰기 작업이 blob 버전에 미치는 영향을 보여 줍니다. Blob이 생성 될 때 해당 blob는 현재 버전입니다. 동일한 blob이 수정 되 면 blob의 이전 상태를 저장 하기 위해 새 버전이 만들어지고 업데이트 된 blob이 현재 버전이 됩니다.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="쓰기 작업이 버전이 지정 된 blob에 미치는 영향을 보여 주는 다이어그램":::

> [!NOTE]
> 저장소 계정에 대 한 버전 관리를 사용 하도록 설정 하기 전에 만든 blob에 버전 ID가 없습니다. Blob이 수정 되 면 수정 된 blob이 현재 버전이 되 고 업데이트 전에 blob의 상태를 저장 하는 버전이 생성 됩니다. 버전에는 생성 시간인 버전 ID가 할당 됩니다.

### <a name="versioning-on-delete-operations"></a>삭제 작업에 대 한 버전 관리

Blob을 삭제 하면 현재 blob 버전이 이전 버전이 되 고 기본 blob이 삭제 됩니다. Blob의 모든 기존 이전 버전은 blob이 삭제 될 때 유지 됩니다.

버전 ID 없이 [Blob 삭제](/rest/api/storageservices/delete-blob) 작업을 호출 하면 기본 Blob이 삭제 됩니다. 특정 버전을 삭제 하려면 삭제 작업에서 해당 버전에 대 한 ID를 제공 합니다.

다음 다이어그램은 버전이 지정 된 blob에 대 한 삭제 작업의 영향을 보여 줍니다.

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="버전이 지정 된 blob의 삭제를 보여 주는 다이어그램":::

새 데이터를 blob에 쓰면 blob의 새 버전이 만들어집니다. 다음 다이어그램에 표시 된 것 처럼 기존 버전은 영향을 받지 않습니다.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="삭제 후 버전이 지정 된 blob의 다시 생성을 보여 주는 다이어그램":::

### <a name="blob-types"></a>Blob 형식

저장소 계정에 blob 버전 관리를 사용 하는 경우 블록 blob에 대 한 모든 쓰기 및 삭제 작업은 [Put 블록](/rest/api/storageservices/put-block) 작업을 제외 하 고 새 버전 만들기를 트리거합니다.

페이지 blob 및 추가 blob의 경우 쓰기 및 삭제 작업의 하위 집합만 버전 생성을 트리거합니다. 다음과 같은 작업이 여기에 포함됩니다.

- [Blob 배치](/rest/api/storageservices/put-blob)
- [블록 목록 배치](/rest/api/storageservices/put-block-list)
- [Blob 삭제](/rest/api/storageservices/delete-blob)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 복사](/rest/api/storageservices/copy-blob)

다음 작업은 새 버전 만들기를 트리거하지 않습니다. 이러한 작업의 변경 내용을 캡처하려면 수동 스냅숏을 만듭니다.

- [Put 페이지](/rest/api/storageservices/put-page) (페이지 blob)
- [블록 추가](/rest/api/storageservices/append-block) (추가 blob)

Blob의 모든 버전은 동일한 blob 유형 이어야 합니다. Blob에 이전 버전이 있는 경우 blob 및 해당 버전을 먼저 삭제 하지 않으면 한 유형의 blob을 다른 유형으로 덮어쓸 수 없습니다.

### <a name="access-tiers"></a>액세스 계층

[Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출 하 여 현재 버전을 비롯 한 모든 블록 blob 버전을 다른 blob 액세스 계층으로 이동할 수 있습니다. 이전 버전의 blob을 쿨 또는 archive 계층으로 이동 하 여 더 낮은 용량의 가격 책정을 활용할 수 있습니다. 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조 하세요.

블록 blob을 적절 한 계층으로 이동 하는 프로세스를 자동화 하려면 blob 수명 주기 관리를 사용 합니다. 수명 주기 관리에 대 한 자세한 내용은 [Azure Blob 저장소 수명 주기 관리](storage-lifecycle-management-concepts.md)를 참조 하세요.

## <a name="enable-or-disable-blob-versioning"></a>Blob 버전 관리 사용 또는 사용 안 함

Blob 버전 관리를 사용 하거나 사용 하지 않도록 설정 하는 방법을 알아보려면 [blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조 하세요.

Blob 버전 관리를 사용 하지 않도록 설정 해도 기존 blob, 버전 또는 스냅숏은 삭제 되지 않습니다. Blob 버전 관리를 해제 하면 기존 버전은 저장소 계정에서 계속 액세스할 수 있습니다. 이후에는 새 버전이 생성 되지 않습니다.

저장소 계정에서 버전 관리를 사용 하지 않도록 설정한 후 blob을 만들거나 수정한 경우 blob을 덮어쓰면 새 버전이 만들어집니다. 업데이트 된 blob이 더 이상 현재 버전이 아니고 버전 ID가 없습니다. Blob에 대 한 모든 후속 업데이트는 이전 상태를 저장 하지 않고 해당 데이터를 덮어씁니다.

버전 관리를 사용 하지 않도록 설정한 후 버전 ID를 사용 하 여 버전을 읽거나 삭제할 수 있습니다. 버전 관리를 사용 하지 않도록 설정한 후 blob 버전을 나열할 수도 있습니다.

다음 다이어그램에서는 버전 관리를 사용 하지 않도록 설정한 후 blob을 수정 하는 방법을 보여 줍니다. Blob와 연결 된 모든 기존 버전은 유지 됩니다.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="버전 관리를 사용 하지 않도록 설정한 후 수정 된 기본 blob을 보여 주는 다이어그램":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 버전 관리 및 일시 삭제

Blob 버전 관리 및 blob 일시 삭제는 함께 작동 하 여 최적의 데이터 보호를 제공 합니다. 일시 삭제를 사용 하도록 설정 하는 경우 일시 삭제 된 blob을 보존할 Azure Storage 기간을 지정 합니다. 일시 삭제 된 blob 버전은 시스템에 남아 있으며 일시 삭제 보존 기간 내에 삭제 취소할 수 있습니다. Blob 일시 삭제에 대 한 자세한 내용은 [Azure Storage blob에 대 한 일시 삭제](storage-blob-soft-delete.md)를 참조 하세요.

### <a name="deleting-a-blob-or-version"></a>Blob 또는 버전 삭제

일시 삭제는 blob 버전 삭제를 위한 추가 보호 기능을 제공 합니다. 저장소 계정에서 버전 관리 및 일시 삭제를 모두 사용 하는 경우 blob을 삭제 하면 새 버전을 만들어 blob의 상태를 삭제 직전으로 저장 하 고 현재 버전을 삭제 Azure Storage. 새 버전은 일시 삭제 되지 않으며 일시 삭제 보존 기간이 만료 되 면 제거 되지 않습니다.

이전 버전의 blob을 삭제 하면 버전이 일시 삭제 됩니다. 일시 삭제 된 버전은 저장소 계정에 대 한 일시 삭제 설정에 지정 된 보존 기간 내내 보존 되며 일시 삭제 보존 기간이 만료 되 면 영구적으로 삭제 됩니다.

이전 버전의 blob을 제거 하려면 버전 ID를 지정 하 여 명시적으로 삭제 합니다.

다음 다이어그램에서는 blob 또는 blob 버전을 삭제할 때 발생 하는 상황을 보여 줍니다.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="일시 삭제를 사용 하는 버전 삭제를 보여 주는 다이어그램":::

저장소 계정에서 버전 관리와 일시 삭제를 모두 사용 하는 경우 blob 또는 blob 버전이 수정 되거나 삭제 될 때 일시 삭제 된 스냅숏은 생성 되지 않습니다.

### <a name="restoring-a-soft-deleted-version"></a>일시 삭제 된 버전 복원

일시 삭제 보존 기간이 적용 되는 동안 버전에서 Blob 삭제 [취소](/rest/api/storageservices/undelete-blob) 작업을 호출 하 여 일시 삭제 된 blob 버전을 복원할 수 있습니다. **Blob 삭제 취소** 작업은 blob의 일시 삭제 된 모든 버전을 복원 합니다.

**Blob 삭제 취소** 작업을 사용 하 여 일시 삭제 된 버전을 복원 해도 모든 버전을 현재 버전으로 승격할 수 없습니다. 현재 버전을 복원 하려면 먼저 일시 삭제 된 모든 버전을 복원한 다음 [Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용 하 여 blob을 복원 하는 이전 버전을 복사 합니다.

다음 다이어그램 **에서는 blob 삭제** 작업을 사용 하 여 일시 삭제 된 blob 버전을 복원 하는 방법과 blob **복사** 작업을 사용 하 여 blob의 현재 버전을 복원 하는 방법을 보여 줍니다.

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="일시 삭제 된 버전을 복원 하는 방법을 보여 주는 다이어그램":::

일시 삭제 보존 기간이 경과한 후에는 일시 삭제 된 blob 버전이 영구적으로 삭제 됩니다.

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 버전 관리 및 blob 스냅숏

Blob 스냅숏은 특정 시점에서 수행 되는 blob의 읽기 전용 복사본입니다. Blob 스냅숏과 blob 버전은 유사 하지만, 사용자 또는 응용 프로그램에서 스냅숏을 수동으로 만들지만 blob 버전이 저장소 계정에 대해 사용 하도록 설정 된 경우 쓰기 또는 삭제 작업 시 자동으로 생성 됩니다.

> [!IMPORTANT]
> Blob 버전 관리를 사용 하도록 설정한 후에도 응용 프로그램을 업데이트 하 여 블록 blob의 스냅숏 작성을 중지 하는 것이 좋습니다. 저장소 계정에 대 한 버전 관리를 사용 하는 경우 모든 블록 blob 업데이트 및 삭제가 버전에 의해 캡처되고 보존 됩니다. Blob 버전 관리를 사용 하는 경우 스냅숏을 만들 때 블록 blob 데이터에 대 한 추가 보호를 제공 하지 않으며 비용과 응용 프로그램 복잡성을 높일 수 있습니다.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>버전 관리를 사용 하는 경우 blob 스냅숏

권장 되지는 않지만 버전이 지정 된 blob의 스냅숏을 만들 수 있습니다. 버전 관리를 사용 하는 경우 blob의 스냅숏 생성을 중지 하도록 응용 프로그램을 업데이트할 수 없는 경우 응용 프로그램에서 스냅숏과 버전을 모두 지원할 수 있습니다.

버전이 지정 된 blob의 스냅숏을 만들면 스냅숏이 생성 될 때 새 버전이 동시에 만들어집니다. 스냅숏이 만들어질 때 새 현재 버전도 만들어집니다.

다음 다이어그램에서는 버전이 지정 된 blob의 스냅숏을 만들 때 발생 하는 상황을 보여 줍니다. 다이어그램에서 버전 ID가 2 및 3 인 blob 버전 및 스냅숏에는 동일한 데이터가 포함 되어 있습니다.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="버전이 지정 된 blob의 스냅숏을 보여 주는 다이어그램 ":::

## <a name="authorize-operations-on-blob-versions"></a>Blob 버전에 대 한 작업 권한 부여

다음 방법 중 하나를 사용 하 여 blob 버전에 대 한 액세스 권한을 부여할 수 있습니다.

- RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Active Directory (Azure AD) 보안 주체에 사용 권한을 부여 합니다. Microsoft에서는 Azure AD를 사용 하 여 보안을 강화 하 고 사용 편의성을 권장 합니다. Blob 작업에 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad.md)
- SAS (공유 액세스 서명)를 사용 하 여 blob 버전에 대 한 액세스를 위임 합니다. `bv`특정 버전에 대 한 작업에 대 한 SAS 토큰을 만들기 위해 blob 버전을 나타내는 서명 된 리소스 종류의 버전 ID를 지정 합니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.
- 계정 액세스 키를 사용 하 여 공유 키로 blob 버전에 대 한 작업에 권한을 부여 합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.

Blob 버전 관리는 실수로 인 한 삭제 또는 악의적인 삭제 로부터 데이터를 보호 하도록 설계 되었습니다. 보호를 강화 하려면 blob 버전을 삭제 하려면 특별 한 권한이 필요 합니다. 다음 섹션에서는 blob 버전을 삭제 하는 데 필요한 권한에 대해 설명 합니다.

### <a name="rbac-action-to-delete-a-blob-version"></a>Blob 버전을 삭제 하는 RBAC 작업

다음 표에서는 blob 또는 blob 버전 삭제를 지 원하는 RBAC 동작을 보여 줍니다.

| Description | Blob service 작업 | RBAC 데이터 작업 필요 | RBAC 기본 제공 역할 지원 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Blob의 현재 버전을 삭제 하는 중 | Blob 삭제 | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Storage Blob 데이터 기여자 |
| 버전 삭제 | Blob 삭제 | **Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/Deleteblob 버전/작업** | Storage Blob 데이터 소유자 |

### <a name="shared-access-signature-sas-parameters"></a>SAS (공유 액세스 서명) 매개 변수

Blob 버전에 대 한 서명 된 리소스는 `bv` 입니다. 자세한 내용은 [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas) 또는 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조 하세요.

다음 표에서는 SAS에서 blob 버전을 삭제 하는 데 필요한 권한을 보여 줍니다.

| **사용 권한** | **URI 기호** | **허용되는 작업** |
|----------------|----------------|------------------------|
| 삭제         | x              | Blob 버전을 삭제 합니다. |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Blob 버전 관리를 사용 하도록 설정 하면 계정에 추가 데이터 저장소 요금이 부과 될 수 있습니다. 애플리케이션을 디자인할 때는 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

Blob 스냅숏과 같은 blob 버전은 활성 데이터와 동일한 속도로 청구 됩니다. 버전을 청구 하는 방법은 기본 blob에 대 한 계층을 명시적으로 설정 했는지 아니면 해당 버전 (또는 스냅숏)에 대해 명시적으로 설정 했는지에 따라 달라 집니다. Blob 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조하세요.

Blob 또는 버전의 계층을 변경 하지 않은 경우 해당 blob에 대 한 고유한 데이터 블록, 해당 버전 및 스냅숏이 있을 수 있는 모든 스냅숏이 청구 됩니다. 자세한 내용은 [blob 계층이 명시적으로 설정 되지 않은 경우 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)를 참조 하세요.

Blob 또는 버전의 계층을 변경한 경우 blob 및 버전이 최종적으로 동일한 계층에 있는지 여부에 관계 없이 전체 개체에 대해 요금이 청구 됩니다. 자세한 내용은 [blob 계층이 명시적으로 설정 된 경우 청구](#billing-when-the-blob-tier-has-been-explicitly-set)를 참조 하세요.

> [!NOTE]
> 자주 덮어쓰는 데이터의 버전 관리를 사용 하도록 설정 하면 저장소 용량 요금이 늘어나고 작업을 나열 하는 동안 대기 시간이 늘어날 수 있습니다. 이러한 문제를 완화 하려면 버전 관리를 사용 하지 않는 별도의 저장소 계정에 자주 덮어쓴 데이터를 저장 합니다.

Blob 스냅숏에 대 한 청구 정보에 대 한 자세한 내용은 [blob 스냅숏](snapshots-overview.md)을 참조 하세요.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Blob 계층이 명시적으로 설정 되지 않은 경우의 요금 청구

기본 blob 또는 해당 버전에 대 한 blob 계층을 명시적으로 설정 하지 않은 경우에는 blob, 해당 버전 및 모든 스냅숏에 대해 고유한 블록이 나 페이지에 대 한 요금이 청구 됩니다. Blob 및 해당 버전에서 공유 되는 데이터는 한 번만 청구 됩니다. Blob이 업데이트 되 면 기본 blob의 데이터는 해당 버전에 저장 된 데이터에서 달라 지므로 되며 고유 데이터는 블록 또는 페이지 별로 요금이 청구 됩니다.

블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록에 이전 버전과 동일한 블록 ID와 동일한 데이터가 있는 경우에도 마찬가지입니다. 블록이 다시 커밋되면 이전 버전의 해당 요소에서 달라 지므로 해당 데이터에 대 한 요금이 청구 됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

Blob 저장소에는 두 블록에 동일한 데이터가 포함 되어 있는지 여부를 확인할 수 있는 방법이 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유 블록에 대해 요금이 부과 되기 때문에 버전 관리를 사용 하는 경우 blob을 업데이트 하면 고유한 블록이 추가로 발생 하 고 추가 요금이 발생 한다는 점을 명심 해야 합니다.

Blob 버전 관리를 사용 하는 경우 블록 blob에 대 한 업데이트 작업을 호출 하 여 가능한 최소 블록 수를 업데이트 합니다. 블록에 대 한 세분화 된 제어를 허용 하는 쓰기 작업은 [put](/rest/api/storageservices/put-block) 블록 및 [put 블록 목록](/rest/api/storageservices/put-block-list)입니다. 반면 [Blob 배치](/rest/api/storageservices/put-blob) 작업은 blob의 전체 콘텐츠를 대체 하므로 추가 요금이 발생할 수 있습니다.

다음 시나리오에서는 blob 계층이 명시적으로 설정 되지 않은 경우 블록 blob 및 해당 버전에 대 한 요금을 계산 하는 방법을 보여 줍니다.

#### <a name="scenario-1"></a>시나리오 1

시나리오 1에서 blob에는 이전 버전이 있습니다. 버전이 만들어진 이후에 blob가 업데이트 되지 않았으므로 고유한 블록 1, 2, 3에 대해서만 요금이 부과 됩니다.

![기본 blob 및 이전 버전의 고유 블록에 대 한 청구를 보여 주는 다이어그램 1](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>시나리오 2

시나리오 2에서는 blob의 한 블록 (다이어그램의 블록 3)이 업데이트 되었습니다. 업데이트 된 블록에 동일한 데이터와 동일한 ID가 포함 된 경우에도 이전 버전의 블록 3과는 다릅니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![다이어그램 2 기본 blob 및 이전 버전의 고유 블록에 대 한 요금 청구 표시](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>시나리오 3

시나리오 3에서 blob은 업데이트 되었지만 버전은 업데이트 되지 않았습니다. 기본 blob에서 블록 3이 블록 4로 대체 되었지만 이전 버전은 여전히 블록 3을 반영 합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 blob 및 이전 버전의 고유 블록에 대 한 청구를 보여 주는 다이어그램 3](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>시나리오 4

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 기본 blob에서 4 개의 고유 블록 &mdash; 4 개와 이전 버전 4 개에 대 한 요금이 계정에 청구 됩니다. 이 시나리오는 blob [배치](/rest/api/storageservices/put-blob) 작업을 사용 하 여 blob에 쓰는 경우에 발생할 수 있습니다 .이 경우 기본 blob의 전체 콘텐츠가 대체 되기 때문입니다.

![다이어그램 4 기본 blob 및 이전 버전의 고유 블록에 대 한 요금 청구 표시](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Blob 계층이 명시적으로 설정 된 경우 요금 청구

Blob 또는 버전 (또는 스냅숏)에 대 한 blob 계층을 명시적으로 설정한 경우, 원본 계층의 개체와 블록을 공유 하는지 여부에 관계 없이 새 계층에서 개체의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다. 또한 원본 계층에서 가장 오래 된 버전의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다. 원본 계층에 남아 있는 다른 모든 이전 버전 또는 스냅숏은 [blob 계층이 명시적으로 설정 되지 않은 경우의 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명 된 대로 공유 될 수 있는 고유한 블록에 대해 요금이 청구 됩니다.

#### <a name="moving-a-blob-to-a-new-tier"></a>새 계층으로 blob 이동

다음 표에서는 새 계층으로 이동할 때 blob 또는 버전에 대 한 청구 동작을 설명 합니다.

| Blob 계층이 명시적으로 설정 된 경우 | 다음에 대 한 요금이 청구 됩니다. |
|-|-|
| 이전 버전의 기본 blob | 새 계층의 기본 blob 및 원본 계층의 가장 오래 된 버전과 다른 버전의 모든 고유 블록 <sup>1</sup> |
| 이전 버전 및 스냅숏이 있는 기본 blob | 새 계층의 기본 blob, 원래 계층의 가장 오래 된 버전 및 원본 계층의 가장 오래 된 스냅숏과 다른 버전 또는 스냅숏<sup>1</sup>의 고유한 블록 |
| 이전 버전 | 새 계층의 버전과 원래 계층의 기본 blob에는 다른 버전의 모든 고유 블록이 추가 됩니다. <sup>1</sup> |

<sup>1</sup> 원본 계층에서 이동 되지 않은 다른 이전 버전 또는 스냅숏이 있는 경우 해당 버전 또는 스냅숏은 [blob 계층이 명시적으로 설정 되지 않은 경우 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명 된 대로 포함 된 고유 블록 수에 따라 요금이 청구 됩니다.

Blob, 버전 또는 스냅숏에 대 한 계층을 명시적으로 설정 하는 작업은 취소할 수 없습니다. 새 계층으로 blob을 이동한 다음 원래 계층으로 다시 이동 하는 경우 원본 계층의 다른 개체와 블록을 공유 하는 경우에도 개체의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다.

Blob, 버전 또는 스냅숏의 계층을 명시적으로 설정 하는 작업은 다음과 같습니다.

- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- 계층을 지정 하 여 [Blob 배치](/rest/api/storageservices/put-blob)
- 지정 된 계층을 사용 하 여 [블록 목록 배치](/rest/api/storageservices/put-block-list)
- 계층을 지정 하 여 [Blob 복사](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>일시 삭제를 사용 하는 경우 blob 삭제

Blob 일시 삭제를 사용 하는 경우 계층을 명시적으로 설정한 기본 blob을 삭제 하거나 덮어쓰면 모든 이전 버전의 일시 삭제 된 blob은 전체 콘텐츠 길이로 청구 됩니다. Blob 버전 관리와 일시 삭제를 함께 사용 하는 방법에 대 한 자세한 내용은 [blob 버전 관리 및 일시 삭제](#blob-versioning-and-soft-delete)를 참조 하세요.

다음 표에서는 버전 관리의 사용 여부에 따라 일시 삭제 된 blob에 대 한 청구 동작을 설명 합니다. 버전 관리를 사용 하는 경우 blob이 일시 삭제 되 면 버전이 생성 됩니다. 버전 관리를 사용 하지 않도록 설정 하면 blob을 일시 삭제 하면 일시 삭제 스냅숏이 생성 됩니다.

| 계층을 사용 하 여 기본 blob를 덮어쓸 때를 명시적으로 설정 하는 경우 ... | 다음에 대 한 요금이 청구 됩니다. |
|-|-|
| Blob 일시 삭제 및 버전 관리를 모두 사용 하는 경우 | 계층에 관계 없이 모든 기존 버전은 전체 콘텐츠 길이입니다. |
| Blob 일시 삭제를 사용 하도록 설정 했지만 버전 관리를 사용 하지 않는 경우 | 계층에 관계 없이 모든 기존 일시 삭제 스냅숏이 전체 콘텐츠 길이에 있습니다. |

## <a name="see-also"></a>참고 항목

- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
- [Blob의 스냅숏 만들기](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure Storage Blob에 대 한 일시 삭제](storage-blob-soft-delete.md)
