---
title: Azure 파일 공유에 대 한 실수로 인 한 삭제 방지
description: 일시 삭제 하는 방법에 대 한 자세한 내용은 실수로 삭제 되지 않도록 Azure 파일 공유를 보호할 수 있습니다.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179915"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Azure Backup를 사용 하 여 Azure 파일 공유에 대 한 실수로 인 한 삭제 방지

사이버 공격 또는 실수로 인 한 삭제를 방지 하기 위해 각 저장소 계정의 파일 공유에 대 한 백업을 구성할 때 저장소 계정의 모든 파일 공유에 대해 [일시 삭제](../storage/files/storage-files-prevent-file-share-deletion.md) 를 사용할 수 있습니다. 일시 삭제를 사용 하면 악의적인 행위자가 파일 공유를 삭제 하는 경우에도 파일 공유의 내용과 복구 지점은 최소 14 일 동안 보존 되므로 데이터 손실 없이 파일 공유를 복구할 수 있습니다.  일시 삭제는 standard 및 premium storage 계정에 대해 지원 되며, 백업 된 파일 공유를 호스트 하는 모든 저장소 계정에 대 한 Azure Backup 하 여 설정을 사용할 수 있습니다.

다음 순서도는 저장소 계정의 파일 공유에 대해 일시 삭제를 사용 하도록 설정한 경우 백업 항목의 여러 단계와 상태를 보여 줍니다.

 ![일시 삭제 흐름 차트](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>저장소 계정의 파일 공유에 대해 일시 삭제를 언제 사용할 수 있나요?

저장소 계정의 모든 파일 공유에 대해 처음으로 백업을 구성 하는 경우 Azure Backup 서비스는 각 저장소 계정의 모든 파일 공유에 대해 일시 삭제를 사용 하도록 설정 합니다.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>파일 공유를 삭제 한 후 내 스냅숏과 복원 지점이 일시 삭제 된 상태로 유지 되는 일 수를 구성할 수 있나요?

예, 요구 사항에 따라 보존 기간을 설정할 수 있습니다. [이 문서](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) 에서는 보존 기간을 구성 하는 단계에 대해 설명 합니다. 백업 된 파일 공유가 있는 저장소 계정의 경우 최소 보존 설정은 14 일입니다.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>14 일 미만으로 구성 했으므로 보존 설정 Azure Backup 다시 설정 하 시겠습니까?

보안 관점에서 백업 된 파일 공유와 함께 저장소 계정에 대해 최소 보존 기간을 14 일로 설정 하는 것이 좋습니다. 따라서 각 백업 작업이 실행 될 때, Azure Backup 14 일 미만으로 설정을 식별 하는 경우 14 일로 다시 설정 합니다.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>보존 기간 중에 발생 하는 비용은 얼마 인가요?

일시 삭제 된 기간 동안 보호 된 인스턴스 비용 및 스냅숏 저장소 비용은 그대로 유지 됩니다.  또한 표준 파일 공유에 대 한 일반 요금으로 사용 된 용량과 프리미엄 파일 공유에 대 한 스냅숏 저장소 요금에 대 한 요금이 청구 됩니다.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>데이터가 일시 삭제 된 상태에 있을 때 복원 작업을 수행할 수 있나요?

복원 작업을 수행 하려면 먼저 일시 삭제 된 파일 공유의 삭제를 취소 해야 합니다. 삭제 취소 작업은 파일 공유를 특정 시점으로 복원할 수 있는 백업 된 상태로 전환 합니다. 파일 공유의 삭제를 취소 하는 방법을 알아보려면 [이 링크](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 를 방문 하거나 [삭제 취소 파일 공유 스크립트](./scripts/backup-powershell-script-undelete-file-share.md)를 참조 하세요.

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>하나 이상의 보호 된 파일 공유가 있는 저장소 계정에서 파일 공유의 데이터를 제거 하려면 어떻게 해야 하나요?

저장소 계정에 보호 된 파일 공유가 하나 이상 있는 경우이는 해당 계정의 모든 파일 공유에 대해 일시 삭제를 사용 하도록 설정 하 고, 삭제 작업 후 14 일 동안 데이터가 보존 됨을 의미 합니다. 그러나 데이터를 즉시 제거 하 고 유지 하지 않으려는 경우 다음 단계를 수행 합니다.

1. 일시 삭제를 사용 하도록 설정한 상태에서 파일 공유를 이미 삭제 한 경우에는 먼저 파일 [포털](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 에서 파일 공유의 삭제를 취소 하거나 [파일 공유 취소 스크립트](./scripts/backup-powershell-script-undelete-file-share.md)를 사용 하 여 파일 공유의 삭제를 취소 합니다.
2. [이 문서](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)에 언급 된 단계를 수행 하 여 저장소 계정의 파일 공유에 대해 일시 삭제를 사용 하지 않도록 설정 합니다.
3. 이제 콘텐츠를 즉시 제거 하려는 파일 공유를 삭제 합니다.

>[!NOTE]
>저장소 계정의 보호 된 파일 공유에 대해 다음 예약 된 백업 작업이 실행 되기 전에 2 단계를 수행 해야 합니다. 백업 작업이 실행 될 때마다 저장소 계정의 모든 파일 공유에 대해 일시 삭제를 다시 사용 하도록 설정 합니다.

>[!WARNING]
>2 단계에서 일시 삭제를 사용 하지 않도록 설정한 후 파일 공유에 대해 수행 되는 삭제 작업은 영구적 삭제 작업입니다. 따라서 일시 삭제를 사용 하지 않도록 설정한 후 백업 된 파일 공유를 실수로 삭제 하면 모든 스냅숏이 손실 되며 데이터를 복구할 수 없습니다.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>파일 공유의 일시 삭제 설정 컨텍스트에서 저장소 계정의 등록을 취소할 때 Azure Backup 변경 작업은 무엇 인가요?

등록 Azure Backup을 취소 하는 시점에는 파일 공유에 대 한 보존 기간 설정을 확인 하 고 14 일 이내 이거나 14 일 보다 작은 경우 보존을 그대로 유지 합니다. 그러나 보존 기간이 14 일인 경우 Azure Backup 하 여이를 사용 하도록 설정 하는 것이 좋습니다. 그러면 등록 취소 과정에서 일시 삭제를 사용 하지 않도록 설정 합니다. 보존 설정을 그대로 유지 하는 동안 저장소 계정의 등록을 취소 하려면 등록 취소를 완료 한 후 저장소 계정 창에서 다시 사용 하도록 설정 합니다. 구성 단계에 대해서는 [이 링크](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal에서 Azure File 공유를 백업](backup-afs.md)하는 방법 알아보기
