---
title: Blob 스냅숏
titleSuffix: Azure Storage
description: Blob 스냅숏이 작동 하는 방법 및 이러한 스냅숏이 어떻게 청구 되는지 이해 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8a1c61b77ab799cead319bfaf6cfa7ebd6af431b
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230336"
---
# <a name="blob-snapshots"></a>Blob 스냅숏

스냅샷은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다.

> [!NOTE]
> Blob 버전 관리는 blob의 이전 버전을 유지 관리 하는 뛰어난 방법을 제공 합니다. 자세한 내용은 [Blob 버전 관리](versioning-overview.md)를 참조 하세요.

## <a name="about-blob-snapshots"></a>Blob 스냅숏 정보

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Blob URI에 스냅샷이 만들어진 시점의 시간을 나타내는 Blob URI에 추가된 **DateTime** 값이 있다는 점을 제외하고 Blob의 스냅샷은 해당 Blob와 동일합니다. 예를 들어 페이지 Blob URI가 `http://storagesample.core.blob.windows.net/mydrives/myvhd`이면 스냅샷 URI는 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`과 유사합니다.

> [!NOTE]
> 모든 스냅샷은 기본 Blob의 URI를 공유합니다. 기본 Blob와 스냅샷의 유일한 차이는 추가된 **DateTime** 값입니다.

Blob 하나에 여러 스냅샷이 있을 수 있습니다. 스냅숏은 독립적으로 또는 기본 blob에 대 한 [Blob 삭제](/rest/api/storageservices/delete-blob) 작업의 일부로 명시적으로 삭제 될 때까지 유지 됩니다. 기본 Blob와 연결된 스냅샷을 열거하여 현재 스냅샷을 추적할 수 있습니다.

Blob의 스냅샷을 만들면 blob의 시스템 속성이 같은 값으로 스냅샷에 복사됩니다. 만들 때 스냅샷에 대한 별도의 메타데이터를 지정하지 않으면 기본 Blob의 메타데이터가 스냅샷에 복사됩니다. 스냅샷을 만든 후에 읽기, 복사 또는 삭제할 수 있지만 수정할 수 없습니다.

기본 blob와 연결된 모든 임대는 스냅샷에 영향을 주지 않습니다. 스냅샷에 대해 임대를 가져올 수 없습니다.

VM 디스크에 대한 현재 정보 및 상태를 저장하는 데 VHD 파일을 사용합니다. VM 내에서 디스크를 분리하거나 VM을 종료하고, 해당 VHD 파일의 스냅샷을 만들 수 있습니다. 해당 스냅샷 파일을 나중에 사용하여 해당 시점에 VHD 파일을 검색하여 VM을 다시 만들 수 있습니다.

## <a name="understand-how-snapshots-accrue-charges"></a>스냅샷 요금 청구 방법 이해

### <a name="important-billing-considerations"></a>청구 관련 중요 고려 사항

아래 목록에는 스냅샷을 만들 때 고려할 주요 사항이 나와 있습니다.

- 스토리지 계정에서는 고유 블록이나 페이지가 Blob에 있는지 혹은 스냅샷에 있는지에 관계없이 요금이 발생합니다. 스냅샷의 기준이 되는 Blob을 업데이트할 때까지는 해당 Blob와 연결된 스냅샷에 대해 계정에 추가 요금이 부과되지 않습니다. 기본 Blob를 업데이트한 후에 해당 스냅샷과 달라집니다. 이 경우에 Blob 또는 스냅샷 각각에서 고유한 블록이나 페이지에 대한 요금이 청구됩니다.
- 블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅샷에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅샷의 해당 항목과 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.
- Blob의 전체 콘텐츠를 덮어쓰는 메서드를 호출 하 여 블록 blob을 업데이트 하면 blob의 모든 블록이 바뀝니다. 해당 Blob에 스냅샷이 연결되어 있으면 스냅샷과 기본 Blob의 모든 블록이 달라지므로 두 Blob의 모든 블록에 대해 요금이 부과됩니다. 이는 기본 Blob와 스냅샷의 데이터가 동일하게 유지되더라도 마찬가지입니다.
- Azure Blob service에서는 두 블록이 같은 데이터를 포함하는지를 확인할 수 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해서는 비용이 부과되므로 스냅샷이 포함된 Blob을 업데이트하면 고유한 블록이 추가로 생성되어 추가 비용이 발생함을 고려해야 합니다.

### <a name="minimize-costs-with-snapshot-management"></a>스냅숏 관리를 사용 하 여 비용 최소화

추가 비용을 방지하기 위해서는 스냅샷을 신중하게 관리하는 것이 좋습니다. 스냅샷 스토리지으로 인해 발생하는 비용을 최소화하려면 다음 모범 사례를 따를 수 있습니다.

- 애플리케이션 디자인상 스냅샷을 유지해야 하는 경우가 아니면, Blob을 업데이트할 때마다 같은 데이터로 업데이트하더라도 해당 Blob에 연결된 스냅샷을 삭제한 후에 다시 만듭니다. Blob의 스냅샷을 삭제한 후에 다시 만들면 Blob와 스냅샷이 달라지지 않습니다.
- Blob에 대 한 스냅숏을 유지 관리 하는 경우 blob을 업데이트할 때 전체 blob을 덮어쓰는 메서드를 호출 하지 마세요. 대신, 비용을 낮게 유지 하기 위해 가능한 적은 수의 블록을 업데이트 합니다.

### <a name="snapshot-billing-scenarios"></a>스냅샷 청구 시나리오

다음 시나리오에서는 블록 Blob와 해당 스냅샷에 대해 비용이 청구되는 방법을 보여 줍니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Blob의 읽기 전용 복사본인 스냅샷을 만들면 계정에 데이터 스토리지 요금이 추가로 부과될 수 있습니다. 애플리케이션을 디자인할 때는 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

Blob 스냅숏 (예: blob 버전)은 활성 데이터와 동일한 속도로 청구 됩니다. 스냅숏이 청구 되는 방법은 기본 blob 또는 해당 스냅숏 (또는 버전)에 대해 계층을 명시적으로 설정 했는지 여부에 따라 달라 집니다. Blob 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조하세요.

Blob 또는 스냅숏의 계층을 변경 하지 않은 경우 해당 blob에 대 한 고유한 데이터 블록, 해당 스냅숏 및 모든 버전에 대 한 요금이 청구 됩니다. 자세한 내용은 [blob 계층이 명시적으로 설정 되지 않은 경우 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)를 참조 하세요.

Blob 또는 스냅숏의 계층을 변경한 경우 blob 및 스냅숏이 최종적으로 동일한 계층에 있는지 여부에 관계 없이 전체 개체에 대해 요금이 청구 됩니다. 자세한 내용은 [blob 계층이 명시적으로 설정 된 경우 청구](#billing-when-the-blob-tier-has-been-explicitly-set)를 참조 하세요.

Blob 버전의 요금 청구 정보에 대 한 자세한 내용은 [blob 버전 관리](versioning-overview.md)를 참조 하세요.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Blob 계층이 명시적으로 설정 되지 않은 경우의 요금 청구

기본 blob 또는 해당 스냅숏에 대 한 blob 계층을 명시적으로 설정 하지 않은 경우에는 blob, 해당 스냅숏 및 그에 포함 될 수 있는 모든 버전에서 고유한 블록이 나 페이지에 대해 요금이 청구 됩니다. Blob 및 해당 스냅숏에 대해 공유 되는 데이터는 한 번만 청구 됩니다. Blob이 업데이트 되 면 기본 blob의 데이터는 해당 스냅숏에 저장 된 데이터에서 달라 지므로 되며 고유 데이터는 블록 또는 페이지 단위로 요금이 부과 됩니다.

블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅샷에서와 같더라도 마찬가지입니다. 블록이 다시 커밋된 후에는 스냅숏의 해당 요소에서 달라 지므로 해당 데이터에 대 한 요금이 청구 됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

Blob 저장소에는 두 블록에 동일한 데이터가 포함 되어 있는지 여부를 확인할 수 있는 방법이 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유 블록에 대해 요금이 부과 되기 때문에 blob의 스냅숏 또는 버전이 있으면 blob을 업데이트 하면 고유한 블록이 추가로 발생 하 고 추가 요금이 발생 합니다.

Blob에 스냅숏이 있으면 블록 blob에 대 한 업데이트 작업을 호출 하 여 가장 가능성이 낮은 블록 수를 업데이트 합니다. 블록에 대 한 세분화 된 제어를 허용 하는 쓰기 작업은 [put](/rest/api/storageservices/put-block) 블록 및 [put 블록 목록](/rest/api/storageservices/put-block-list)입니다. 반면 [Blob 배치](/rest/api/storageservices/put-blob) 작업은 blob의 전체 콘텐츠를 대체 하므로 추가 요금이 발생할 수 있습니다.

다음 시나리오에서는 blob 계층이 명시적으로 설정 되지 않은 경우 블록 blob 및 해당 스냅숏에 대 한 요금을 계산 하는 방법을 보여 줍니다.

#### <a name="scenario-1"></a>시나리오 1

시나리오 1에서는 스냅샷을 생성한 후 기본 Blob을 업데이트하지 않았으므로 고유 블록 1, 2, 3에 대해서만 비용이 청구됩니다.

![기본 blob 및 스냅숏의 고유 블록에 대 한 청구를 보여 주는 다이어그램 1](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>시나리오 2

시나리오 2에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 블록 3이 업데이트되고 동일한 데이터와 동일한 ID를 가지고 있지만 스냅샷의 블록 3과 같지는 않습니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![다이어그램 2 기본 blob 및 스냅숏의 고유 블록에 대 한 요금 청구 표시](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>시나리오 3

시나리오 3에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 스냅샷은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 blob 및 스냅숏의 고유 블록에 대 한 청구를 보여 주는 다이어그램 3](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>시나리오 4

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 8개 고유 블록 모두에 대한 요금이 계정에 청구됩니다.

![다이어그램 4 기본 blob 및 스냅숏의 고유 블록에 대 한 청구 표시](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> 전체 blob을 덮어쓰는 메서드를 호출 하지 않고 비용을 낮게 유지 하기 위해 개별 블록을 업데이트 합니다.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Blob 계층이 명시적으로 설정 된 경우 요금 청구

Blob 또는 스냅숏 (또는 버전)에 대 한 blob 계층을 명시적으로 설정한 경우, 원본 계층의 개체와 블록을 공유 하는지 여부에 관계 없이 새 계층에서 개체의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다. 또한 원본 계층에서 가장 오래 된 버전의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다. 원본 계층에 남아 있는 모든 버전 또는 스냅숏은 [blob 계층이 명시적으로 설정 되지 않은 경우 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명 된 대로 공유 될 수 있는 고유한 블록에 대해 요금이 청구 됩니다.

#### <a name="moving-a-blob-to-a-new-tier"></a>새 계층으로 blob 이동

다음 표에서는 새 계층으로 이동할 때 blob 또는 스냅숏의 청구 동작에 대해 설명 합니다.

| Blob 계층이 명시적으로 설정 된 경우 | 다음에 대 한 요금이 청구 됩니다. |
|-|-|
| 스냅숏이 포함 된 기본 blob | 새 계층의 기본 blob 및 원본 계층의 가장 오래 된 스냅숏과 다른 스냅숏의 모든 고유 블록 <sup>1</sup> |
| 이전 버전 및 스냅숏이 있는 기본 blob | 새 계층의 기본 blob, 원래 계층의 가장 오래 된 버전 및 원본 계층의 가장 오래 된 스냅숏과 다른 버전 또는 스냅숏<sup>1</sup>의 고유한 블록 |
| 스냅숏 | 새 계층의 스냅숏과 원래 계층의 기본 blob에는 다른 스냅숏의 모든 고유 블록이 추가 됩니다. <sup>1</sup> |

<sup>1</sup> 원본 계층에서 이동 되지 않은 다른 이전 버전 또는 스냅숏이 있는 경우 해당 버전 또는 스냅숏은 [blob 계층이 명시적으로 설정 되지 않은 경우 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명 된 대로 포함 된 고유 블록 수에 따라 요금이 청구 됩니다.

Blob, 버전 또는 스냅숏에 대 한 계층을 명시적으로 설정 하는 작업은 취소할 수 없습니다. 새 계층으로 blob을 이동한 다음 원래 계층으로 다시 이동 하는 경우 원본 계층의 다른 개체와 블록을 공유 하는 경우에도 개체의 전체 콘텐츠 길이에 대 한 요금이 청구 됩니다.

Blob, 버전 또는 스냅숏의 계층을 명시적으로 설정 하는 작업은 다음과 같습니다.

- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- 계층을 지정 하 여 [Blob 배치](/rest/api/storageservices/put-blob)
- 지정 된 계층을 사용 하 여 [블록 목록 배치](/rest/api/storageservices/put-block-list)
- 계층을 지정 하 여 [Blob 복사](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>일시 삭제를 사용 하는 경우 blob 삭제

Blob 일시 삭제를 사용 하는 경우 계층을 명시적으로 설정한 기본 blob을 삭제 하거나 덮어쓰는 경우 일시 삭제 된 blob의 모든 이전 버전 또는 스냅숏은 전체 콘텐츠 길이로 청구 됩니다. Blob 버전 관리와 일시 삭제를 함께 사용 하는 방법에 대 한 자세한 내용은 [blob 버전 관리 및 일시 삭제](versioning-overview.md#blob-versioning-and-soft-delete)를 참조 하세요.

다음 표에서는 버전 관리의 사용 여부에 따라 일시 삭제 된 blob에 대 한 청구 동작을 설명 합니다. 버전 관리를 사용 하도록 설정 하면 blob이 일시 삭제 될 때 새 버전이 만들어집니다. 버전 관리를 사용 하지 않도록 설정 하면 blob을 일시 삭제 하면 일시 삭제 스냅숏이 생성 됩니다.

| 계층을 사용 하 여 기본 blob를 덮어쓸 때를 명시적으로 설정 하는 경우 ... | 다음에 대 한 요금이 청구 됩니다. |
|-|-|
| Blob 일시 삭제 및 버전 관리를 모두 사용 하는 경우 | 계층에 관계 없이 모든 기존 버전은 전체 콘텐츠 길이입니다. |
| Blob 일시 삭제를 사용 하도록 설정 했지만 버전 관리를 사용 하지 않는 경우 | 계층에 관계 없이 모든 기존 일시 삭제 스냅숏이 전체 콘텐츠 길이에 있습니다. |

## <a name="next-steps"></a>다음 단계

- [Blob 버전 관리](versioning-overview.md)
- [.NET에서 Blob 스냅샷 만들기 및 관리](snapshots-manage-dotnet.md)
- [증분 스냅샷을 사용하여 Azure 관리되지 않는 VM 디스크 백업](../../virtual-machines/windows/incremental-snapshots.md)
