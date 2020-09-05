---
title: Blob에 대한 일시 삭제
titleSuffix: Azure Storage
description: Blob에 대 한 일시 삭제는 응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 수정 하거나 삭제 하는 경우 데이터를 보다 쉽게 복구할 수 있도록 데이터를 보호 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a6fc1d6b831ae794907c59ab1af3328902f3a70a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230112"
---
# <a name="soft-delete-for-blobs"></a>Blob에 대한 일시 삭제

Blob에 대 한 일시 삭제는 데이터가 실수로 또는 실수로 수정 되거나 삭제 되지 않도록 보호 합니다. Blob에 대해 일시 삭제를 사용 하도록 설정한 경우 저장소 계정에서 blob, blob 버전 및 스냅숏이 삭제 된 후 지정한 보존 기간 내에 복구 될 수 있습니다.

Microsoft는 데이터가 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 실수로 수정 또는 삭제될 가능성이 있다면 일시 삭제를 설정할 것을 권장합니다. 일시 삭제를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [blob에 대 한 일시 삭제 설정 및 관리](soft-delete-enable.md)를 참조 하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Blob에 대 한 일시 삭제 정보

Blob에 대 한 일시 삭제가 저장소 계정에서 사용 하도록 설정 된 경우 지정 된 데이터 보존 기간 내에 삭제 된 후에 개체를 복구할 수 있습니다. 이 보호는 덮어쓰기의 결과로 지워지는 모든 blob (블록 blob, 추가 blob 또는 페이지 blob)로 확장 됩니다.

Blob 일시 삭제를 사용 하도록 설정 하는 동안 기존 blob 또는 스냅숏의 데이터를 삭제 하지만 blob 버전 관리를 사용 하지 않는 경우 덮어쓴 데이터의 상태를 저장 하기 위해 일시 삭제 된 스냅숏이 생성 됩니다. 지정 된 보존 기간이 만료 되 면 개체가 영구적으로 삭제 됩니다.

저장소 계정에서 blob 버전 관리 및 blob 일시 삭제를 모두 사용 하는 경우 blob을 삭제 하면 일시 삭제 된 스냅숏 대신 새 버전이 만들어집니다. 새 버전은 일시 삭제 되지 않으며 일시 삭제 보존 기간이 만료 되 면 제거 되지 않습니다. Blob의 일시 삭제 된 버전은 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출 하 여 보존 기간 내에 복원할 수 있습니다. Blob [복사](/rest/api/storageservices/copy-blob) 작업을 호출 하 여 나중에 해당 버전 중 하나에서 blob를 복원할 수 있습니다. Blob 버전 관리와 일시 삭제를 함께 사용 하는 방법에 대 한 자세한 내용은 [blob 버전 관리 및 일시 삭제](versioning-overview.md#blob-versioning-and-soft-delete)를 참조 하세요.

일시 삭제된 개체는 명시적으로 나열되지 않는 한 표시되지 않습니다.

Blob 일시 삭제는 이전 버전과 호환 되므로 응용 프로그램을 변경할 필요가 없으므로이 기능의 보호를 활용할 수 있습니다. 그러나 [데이터 복구](#recovery)는 새로운 **삭제 취소 Blob** API를 도입합니다.

Blob 일시 삭제는 새로운 범용 v2, 범용 v1 및 Blob storage 계정 모두에 사용할 수 있습니다. Standard 및 premium 계정 유형이 모두 지원 됩니다. Blob 일시 삭제는 핫, 쿨 및 보관을 비롯 한 모든 저장소 계층에 사용할 수 있습니다. 일시 삭제는 관리 디스크에 사용할 수 없는 관리 되지 않는 디스크에 사용할 수 있습니다 .이 디스크는 내부적으로 페이지 blob 이지만 관리 디스크에는 사용할 수 없습니다.

### <a name="configuration-settings"></a>구성 설정

새 계정을 만들 때 일시 삭제는 기본적으로 사용 하지 않도록 설정 됩니다. 일시 삭제는 기존 저장소 계정에 대해서도 기본적으로 사용 하지 않도록 설정 됩니다. 언제 든 지 저장소 계정에 대해 일시 삭제를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

일시 삭제를 사용 하도록 설정 하는 경우 보존 기간을 구성 해야 합니다. 보존 기간은 일시 삭제된 데이터가 저장되고 복구에 사용할 수 있는 기간을 나타냅니다. 명시적으로 삭제 된 개체의 경우 데이터를 삭제할 때 보존 기간 시계가 시작 됩니다. 일시 삭제 된 버전이 나 일시 삭제 기능으로 생성 된 스냅숏에 대해 데이터를 덮어쓸 때 클록은 버전이 나 스냅숏이 생성 될 때 시작 됩니다. 보존 기간은 1 ~ 007e; 365 일 수 있습니다.

언제든지 일시 삭제 보존 기간을 변경할 수 있습니다. 업데이트 된 보존 기간은 새로 삭제 된 데이터에만 적용 됩니다. 이전에 삭제 된 데이터는 해당 데이터를 삭제할 때 구성 된 보존 기간에 따라 만료 됩니다. 일시 삭제 된 개체를 삭제 하는 시도는 만료 시간에 영향을 주지 않습니다.

일시 삭제를 사용 하지 않도록 설정 하면 기능을 사용 하도록 설정 하는 동안 저장 된 저장소 계정에서 일시 삭제 된 데이터에 계속 액세스 하 여 복구할 수 있습니다.

### <a name="saving-deleted-data"></a>삭제된 데이터 저장

일시 삭제는 개체를 삭제 하거나 덮어쓰는 대부분의 경우 데이터를 유지 합니다.

Blob **배치**, **블록 목록 배치**또는 **blob 복사**를 사용 하 여 blob을 덮어쓰는 경우 쓰기 작업 이전에 blob 상태의 버전이 나 스냅숏이 자동으로 생성 됩니다. 일시 삭제 된 개체가 명시적으로 나열 되지 않은 경우이 개체는 표시 되지 않습니다. 일시 삭제된 개체를 나열하는 방법을 알아보려면 [복구](#recovery) 섹션을 참조하세요.

![Blob 추가, 블록 목록 배치 또는 Blob 복사를 사용 하 여 blob의 스냅숏을 덮어쓰는 방법을 보여 주는 다이어그램입니다.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. B0을 B1로 덮어쓰면 일시 삭제 된 B0 스냅숏이 생성 됩니다. B2를 B2로 덮어쓰면 B1의 일시 삭제 된 스냅숏이 생성 됩니다.*

> [!NOTE]  
> 일시 삭제는 대상 Blob의 계정이 설정된 경우, 복사 작업에 대한 덮어쓰기 보호만 제공합니다.

> [!NOTE]  
> 일시 삭제는 보관 계층의 Blob에 대한 덮어쓰기 보호를 제공하지 않습니다. 보관 계층의 Blob이 계층에서 새 Blob으로 덮어쓰여지는 경우 덮어쓰여진 Blob이 영구적으로 만료됩니다.

**Blob 삭제**가 스냅샷에서 호출되는 경우 해당 스냅샷은 일시 삭제됨으로 표시됩니다. 새 스냅샷이 생성되지 않습니다.

![Blob 삭제를 사용할 때 blob 스냅숏이 일시 삭제 되는 방법을 보여 주는 다이어그램입니다.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. **Snapshot Blob** 이 호출 되 면 B0은 스냅숏이 되 고 B1은 blob의 활성 상태입니다. B0 스냅숏이 삭제 되 면 일시 삭제로 표시 됩니다.*

기본 Blob에서 **Blob 삭제**가 호출되는 경우(자체 스냅샷이 아닌 모든 Blob) 해당 Blob은 일시 삭제됨으로 표시됩니다. 이전 동작과 일치하는 활성 스냅샷이 있는 Blob에서 **Blob 삭제**를 호출하면 오류를 반환합니다. 일시 삭제된 스냅샷이 있는 Blob에서 **Blob 삭제**를 호출하면 오류를 반환하지 않습니다. 일시 삭제가 설정되면 단일 작업에서 Blob 및 모든 해당 스냅샷을 여전히 삭제할 수 있습니다. 이렇게 하면 기본 Blob 및 스냅샷을 일시 삭제됨으로 표시합니다.

![기본 blob에서 블로그 삭제가 호출 될 때 발생 하는 결과를 보여 주는 다이어그램](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. 여기서 **Delete Blob** 호출은 B2와 연결 된 모든 스냅숏을 삭제 합니다. 활성 blob, B2 및 연결 된 모든 스냅숏이 일시 삭제로 표시 됩니다.*

> [!NOTE]  
> 일시 삭제된 Blob이 덮어쓰여지는 경우 쓰기 작업 전에 Blob 상태의 일시 삭제된 스냅샷이 자동으로 생성됩니다. 새 Blob은 덮어쓰여진 Blob의 계층을 상속합니다.

일시 삭제는 컨테이너 또는 계정이 삭제되고, Blob 메타데이터 및 Blob 속성이 덮어쓰여지는 경우에 데이터를 저장하지 않습니다. 잘못된 삭제로부터 스토리지 계정을 보호하기 위해 Azure Resource Manager를 사용하여 잠금을 구성할 수 있습니다. 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠금](../../azure-resource-manager/management/lock-resources.md)Azure Resource Manager 문서를 참조 하세요.

다음 표는 일시 삭제가 설정된 경우 예상되는 동작을 자세히 설명합니다.

| REST API 작업 | 리소스 종류 | Description | 동작 변경 |
|--------------------|---------------|-------------|--------------------|
| [Delete](/rest/api/storagerp/StorageAccounts/Delete) | 계정 | 포함하는 모든 컨테이너 및 Blob을 포함하여 스토리지 계정을 삭제합니다.                           | 변경되지 않았습니다. 삭제된 계정의 컨테이너 및 Blob은 복구할 수 없습니다. |
| [컨테이너 삭제](/rest/api/storageservices/delete-container) | 컨테이너 | 포함하는 모든 Blob을 포함하여 컨테이너를 삭제합니다. | 변경되지 않았습니다. 삭제된 컨테이너의 Blob은 복구할 수 없습니다. |
| [Blob 배치](/rest/api/storageservices/put-blob) | 블록, 추가 및 페이지 blob | 새 Blob을 만들거나 컨테이너 내 기존 Blob 교체 | 기존 Blob을 교체하는 데 사용되는 경우, 호출 전에 Blob의 상태 스냅샷이 자동으로 생성됩니다. 이는 동일한 유형의 blob (블록, 추가 또는 페이지)로 대체 되는 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 형식의 Blob으로 교체되는 경우 기존의 모든 일시 삭제된 데이터는 영구적으로 만료됩니다. |
| [Blob 삭제](/rest/api/storageservices/delete-blob) | 블록, 추가 및 페이지 blob | 삭제를 위한 Blob 또는 Blob 스냅샷을 표시합니다. 가비지 수집 중 Blob 또는 스냅샷은 나중에 삭제됩니다. | Blob 스냅샷을 삭제하는 데 사용되는 경우 해당 스냅샷은 일시 삭제됨으로 표시됩니다. Blob을 삭제하는 데 사용되는 경우 해당 스냅숏은 일시 삭제됨으로 표시됩니다. |
| [Blob 복사](/rest/api/storageservices/copy-blob) | 블록, 추가 및 페이지 blob | 동일한 스토리지 계정 또는 다른 스토리지 계정에서 대상 Blob에 원본 Blob을 복사합니다. | 기존 Blob을 교체하는 데 사용되는 경우, 호출 전에 Blob의 상태 스냅샷이 자동으로 생성됩니다. 이는 동일한 유형의 blob (블록, 추가 또는 페이지)로 대체 되는 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 형식의 Blob으로 교체되는 경우 기존의 모든 일시 삭제된 데이터는 영구적으로 만료됩니다. |
| [블록 배치](/rest/api/storageservices/put-block) | 블록 Blob | 블록 Blob의 일부로 커밋될 새 블록을 만듭니다. | 활성 상태인 blob에 블록을 커밋하는 데 사용 되는 경우 변경 내용이 없습니다. 일시 삭제된 Blob에 블록을 커밋하는 데 사용되는 경우 새 Blob이 만들어지고 일시 삭제된 Blob의 상태를 캡처하도록 스냅샷이 자동으로 생성됩니다. |
| [블록 목록 배치](/rest/api/storageservices/put-block-list) | 블록 Blob | 블록 Blob을 구성하는 블록 ID의 집합을 지정하여 Blob을 커밋합니다. | 기존 Blob을 교체하는 데 사용되는 경우, 호출 전에 Blob의 상태 스냅샷이 자동으로 생성됩니다. 이는 블록 blob 인 경우에만 이전에 일시 삭제 된 blob에도 적용 됩니다. 다른 형식의 Blob으로 교체되는 경우 기존의 모든 일시 삭제된 데이터는 영구적으로 만료됩니다. |
| [페이지 배치](/rest/api/storageservices/put-page) | 페이지 Blob | 페이지 blob에 페이지 범위를 씁니다. | 변경되지 않았습니다. 이 작업을 사용 하 여 덮어쓰거나 지운 페이지 blob 데이터는 저장 되지 않으므로 복구할 수 없습니다. |
| [추가 블록](/rest/api/storageservices/append-block) | 추가 Blob | 추가 blob의 끝에 데이터 블록을 씁니다. | 변경되지 않았습니다. |
| [Blob 속성 설정](/rest/api/storageservices/set-blob-properties) | 블록, 추가 및 페이지 blob | Blob에 대해 정의된 시스템 속성에 대한 값을 설정합니다. | 변경되지 않았습니다. 덮어쓰여진 Blob 속성은 복구할 수 없습니다. |
| [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata) | 블록, 추가 및 페이지 blob | 하나 이상의 이름 값 쌍으로 지정된 Blob에 대한 사용자 정의 메타데이터를 설정합니다. | 변경되지 않았습니다. 덮어쓰여진 Blob 메타데이터는 복구할 수 없습니다. |

페이지 blob의 범위를 덮어쓰거나 지우도록 **Put 페이지** 를 호출 하면 스냅숏이 자동으로 생성 되지 않는다는 것을 알 수 있습니다. 가상 컴퓨터 디스크는 페이지 blob에 의해 지원 되며 **Put 페이지** 를 사용 하 여 데이터를 작성 합니다.

### <a name="recovery"></a>복구

일시 삭제 된 기본 blob에서 [blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출 하면 해당 작업 및 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 활성 기본 Blob에 대 한 **Blob 삭제 취소** 작업을 호출 하면 연결 된 모든 일시 삭제 된 스냅숏이 활성으로 복원 됩니다. 스냅샷이 활성으로 복원되는 경우 사용자 생성 스냅샷처럼 보입니다. 이는 기본 Blob을 덮어쓰지 않습니다.

Blob을 일시 삭제 된 특정 스냅숏으로 복원 하려면 기본 blob에서 **삭제 취소 blob** 을 호출 하면 됩니다. 그런 다음, 현재 활성 Blob을 통해 스냅샷을 복사할 수 있습니다. 새 Blob에 스냅샷을 복사할 수도 있습니다.

![삭제 취소 blob을 사용 하는 경우 수행 되는 작업을 보여 주는 다이어그램](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*일시 삭제 된 데이터는 회색 이며 활성 데이터는 파란색입니다. 이전 데이터 아래에 더 최근에 작성 된 데이터가 표시 됩니다. 여기서는 blob B에서 **삭제 취소 blob** 을 호출 하 여 기본 Blob, B1 및 연결 된 모든 스냅숏을 복원 합니다. 여기에서는 B0만 활성으로 복원 합니다. 두 번째 단계에서는 B0이 기본 blob을 통해 복사 됩니다. 이 복사 작업은 B1의 일시 삭제 된 스냅숏을 생성 합니다.*

일시 삭제된 Blob 및 Blob 스냅샷을 보려면 **Blob 나열**에 삭제된 데이터를 포함하도록 선택할 수 있습니다. 일시 삭제된 기본 Blob만을 보거나 일시 삭제된 Blob 스냅샷도 포함하도록 선택할 수 있습니다. 모든 일시 삭제된 데이터의 경우 데이터가 삭제되었던 시간 및 데이터가 영구적으로 만료되기 전까지의 일 수를 볼 수 있습니다.

### <a name="example"></a>예제

다음은 일시 삭제가 설정 된 경우 *HelloWorld* 라는 blob을 업로드, 덮어쓰기, 스냅숏, 삭제 및 복원 하는 .net 스크립트의 콘솔 출력입니다.

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

이 출력을 생성한 애플리케이션에 대한 포인터는 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

일시 삭제된 모든 데이터는 활성 데이터와 동일한 요율로 요금이 청구됩니다. 구성된 보존 기간 후 영구적으로 삭제되는 데이터에 대한 비용은 청구되지 않습니다. 스냅숏에 대 한 심층 분석 및 요금이 부과 되는 방법에 대 한 자세한 내용은 [스냅숏이 요금을 계산 하는 방법 이해](storage-blob-snapshots.md)를 참조 하세요.

스냅샷의 자동 생성과 관련된 트랜잭션에 대해 요금이 청구되지 않습니다. 쓰기 작업에 대 한 속도에서 **삭제 취소 Blob** 트랜잭션에 대 한 요금이 청구 됩니다.

일반적인 Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

일시 삭제를 처음 켤 때 기능이 요금 청구에 미치는 영향을 보다 잘 이해 하려면 짧은 보존 기간을 사용 하는 것이 좋습니다.

자주 덮어쓴 데이터에 대해 일시 삭제를 사용 하도록 설정 하면 blob을 나열할 때 저장소 용량 요금이 증가 하 고 대기 시간이 길어질 수 있습니다. 일시 삭제를 사용 하지 않도록 설정 된 별도의 저장소 계정에 자주 덮어쓴 데이터를 저장 하 여 이러한 추가 비용 및 대기 시간을 줄일 수 있습니다.

## <a name="faq"></a>FAQ

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Blob 계층 API 설정을 사용하여 일시 삭제된 스냅샷과 함께 Blob의 계층을 설정할 수 있습니까?

예. 일시 삭제된 스냅샷은 원본 계층에 유지되지만 기본 Blob은 새 계층으로 이동합니다.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium Storage 계정은 Blob당 100개로 스냅샷을 제한합니다. 일시 삭제된 스냅샷은 이 제한에 포함되나요?

아니요, 일시 삭제된 스냅샷은 이 제한에 포함되지 않습니다.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>일시 삭제가 설정된 상태로 전체 계정 또는 컨테이너를 삭제하는 경우 연결된 모든 Blob 저장되나요?

아니요, 전체 계정 또는 컨테이너를 삭제하면 연결된 모든 Blob은 영구적으로 삭제됩니다. 저장소 계정이 실수로 삭제 되지 않도록 보호 하는 방법에 대 한 자세한 내용은 [리소스를 잠가 예기치 않은 변경 방지](../../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>삭제된 데이터에 대한 용량 메트릭을 볼 수 있나요?

일시 삭제된 데이터는 총 스토리지 계정 용량의 일부분으로 포함됩니다. 저장소 용량 추적 및 모니터링에 대 한 자세한 내용은 [스토리지 분석](../common/storage-analytics.md)를 참조 하세요.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>내 Blob의 일시 삭제된 스냅샷을 읽고 복사할 수 있나요?  

예, 하지만 Blob에서 먼저 삭제 취소를 호출해야 합니다.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>일시 삭제를 가상 머신 디스크에 사용할 수 있나요?  

일시 삭제는 프리미엄 및 표준 관리 되지 않는 디스크 모두에 사용할 수 있습니다. 이러한 디스크는 내부적으로 페이지 blob입니다. 일시 삭제는 **Blob 삭제**, **Blob 배치**, **블록 목록 배치**및 **blob 복사** 작업을 통해 삭제 된 데이터를 복구 하는 데만 도움이 됩니다. **페이지 배치**에 대한 호출로 덮어쓰여진 데이터는 복구할 수 없습니다.

Azure 가상 머신은 **Put 페이지**호출을 사용 하 여 관리 되지 않는 디스크에 기록 되므로, 일시 삭제를 사용 하 여 azure VM에서 관리 되지 않는 디스크에 대 한 쓰기를 실행 취소 하는 것은 지원 되지 않습니다.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>일시 삭제를 사용하려면 기존 애플리케이션을 변경해야 하나요?

사용하는 API 버전에 관계 없이 일시 삭제를 활용할 수 있습니다. 그러나 일시 삭제 된 blob 및 blob 스냅숏을 나열 하 고 복구 하려면 [Azure Storage REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 이상 버전 2017-07-29를 사용 해야 합니다. Microsoft에서 항상 최신 버전의 Azure Storage API를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Blob에 일시 삭제를 사용하도록 설정](soft-delete-enable.md)
- [Blob 버전 관리](versioning-overview.md)
