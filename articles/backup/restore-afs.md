---
title: Azure 파일 공유 복원
description: Azure Portal를 사용 하 여 Azure Backup 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 8ecb5850c90e5a92e59d17f7949bd5b6cd6205f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930860"
---
# <a name="restore-azure-file-shares"></a>Azure 파일 공유 복원

이 문서에서는 Azure Portal를 사용 하 여 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* 전체 Azure 파일 공유를 복원 합니다.
* 개별 파일 또는 폴더를 복원 합니다.
* 복원 작업 상태를 추적 합니다.

## <a name="steps-to-perform-a-restore-operation"></a>복원 작업을 수행 하는 단계
복원 작업을 수행 하려면 다음 단계를 수행 합니다.

### <a name="select-the-file-share-to-restore"></a>복원할 파일 공유를 선택 하십시오.

1. [Azure Portal](https://portal.azure.com/)에서 파일 공유에 대한 백업을 구성 하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다.

1. 개요 창의 **보호 된 항목** 섹션에서 **백업 항목** 을 선택 합니다.

    ![백업 항목 선택](./media/restore-afs/backup-items.png)

1. **백업 항목**을 선택 하면 개요 창 옆에 모든 백업 관리 유형이 나열 된 새 창이 열립니다.

    ![백업 관리 유형](./media/restore-afs/backup-management.png)

1. **백업 항목** 창의 **백업 관리 유형**에서 **Azure Storage (Azure Files)** 를 선택 합니다. 이 자격 증명 모음을 사용 하 여 백업 된 모든 파일 공유 및 해당 저장소 계정 목록이 표시 됩니다.

    ![모든 파일 공유 목록](./media/restore-afs/file-shares.png)

1. Azure 파일 공유 목록에서 복원 작업을 수행 하려는 파일 공유를 선택 합니다.

### <a name="full-share-recovery"></a>전체 공유 복구

이 복원 옵션을 사용 하 여 원래 위치 또는 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

1. 복원할 [파일 공유 선택](#select-the-file-share-to-restore) 섹션의 5 단계에서 복원할 파일 공유를 선택한 후에 표시 되는 **백업 항목** 창에서 **공유 복원** 옵션을 선택 합니다.

   ![공유 복원 선택](./media/restore-afs/restore-share.png)

1. **공유 복원**을 선택 **하면 복원** **지점** 메뉴가 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점 목록이 표시 됩니다.

1. 복원 작업을 수행 하는 데 사용할 복원 지점을 선택 하 고 **확인**을 선택 합니다.

    ![복원 지점 선택](./media/restore-afs/restore-point.png)

1. **확인**을 선택 하면 **복원** 창 메뉴가 **복원 위치로**전환 됩니다. **복원 위치**에서 데이터를 복원할 위치를 지정 합니다. 다음 두 옵션 중 하나를 선택 합니다.

    * **원본 위치**: 원래 원본과 동일한 위치에 전체 파일 공유를 복원 합니다.
    * **대체 위치**: 전체 파일 공유를 대체 위치로 복원 하 고 원래 파일 공유를 그대로 유지 합니다.

#### <a name="restore-to-the-original-location"></a>원래 위치로 복원

1. **원본 위치** 를 **복구 대상**으로 선택 하 고 충돌이 발생 하면 건너뛸지 아니면 덮어쓸지를 선택 합니다. 적절 한 선택을 수행한 후 **확인**을 선택 합니다.

    ![원본 위치 선택](./media/restore-afs/original-location.png)

1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

    ![시작 하려면 [복원]을 선택 하십시오.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. **대체 위치** 를 **복구 대상**으로 선택 합니다.
1. **저장소 계정** 드롭다운 목록에서 백업 된 콘텐츠를 복원 하려는 대상 저장소 계정을 선택 합니다.
1. **파일 공유 선택** 드롭다운 목록에는 2 단계에서 선택한 저장소 계정에 있는 파일 공유가 표시 됩니다. 백업 된 콘텐츠를 복원 하려는 파일 공유를 선택 합니다.
1. **폴더 이름** 상자에서 복원 된 내용이 포함 된 대상 파일 공유에 만들려는 폴더 이름을 지정 합니다.
1. 충돌이 발생 했을 때 건너뛸지 아니면 덮어쓸지를 선택 합니다.
1. 모든 상자에 적절 한 값을 입력 한 다음 **확인**을 선택 합니다.

    ![대체 위치 선택](./media/restore-afs/alternate-location.png)

1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

    ![시작 하려면 [복원]을 선택 하십시오.](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용 하 여 원래 위치 또는 대체 위치에서 개별 파일 또는 폴더를 복원할 수 있습니다.

1. 복원할 파일 [공유 선택](#select-the-file-share-to-restore) 섹션의 5 단계에서 복원할 파일 공유를 선택한 후에 나타나는 **백업 항목** 창에서 **파일 복구** 옵션을 선택 합니다.

    ![파일 복구 선택](./media/restore-afs/file-recovery.png)

1. **파일 복구**를 선택 **하면 복원** **지점** 메뉴가 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점 목록이 표시 됩니다.

1. 복원 작업을 수행 하는 데 사용할 복원 지점을 선택 하 고 **확인**을 선택 합니다.

    ![복원 지점 선택](./media/restore-afs/restore-point.png)

1. **확인**을 선택 하면 복원 창 메뉴가 **복원 위치로**전환 됩니다. **복원 위치**에서 데이터를 복원할 위치를 지정 합니다. 다음 두 옵션 중 하나를 선택 합니다.

    * **원본 위치**: 선택한 파일 또는 폴더를 원본 원본과 동일한 파일 공유로 복원 합니다.
    * **대체 위치**: 선택한 파일 또는 폴더를 대체 위치로 복원 하 고 원래 파일 공유 내용을 그대로 유지 합니다.

#### <a name="restore-to-the-original-location"></a>원래 위치로 복원

1. **원본 위치** 를 **복구 대상**으로 선택 하 고 충돌이 발생 하면 건너뛸지 아니면 덮어쓸지를 선택 합니다.

    ![항목 수준 복구의 원본 위치](./media/restore-afs/original-location-item-level.png)

2. **파일 선택** 을 선택 하 여 복원할 파일이 나 폴더를 선택 합니다.

    ![파일 선택 선택](./media/restore-afs/select-file.png)

1. **파일 선택**을 선택 하면 복원 하도록 선택한 파일 공유 복구 지점의 내용이 파일 공유 창에 표시 됩니다.

1. 복원 하려는 파일이 나 폴더에 해당 하는 확인란을 선택 하 고 **선택**을 선택 합니다.

    ![파일 또는 폴더 선택](./media/restore-afs/select-file-folder.png)

1. 2 ~ 4 단계를 반복 하 여 복원에 사용할 여러 파일 또는 폴더를 선택 합니다.
1. 복원 하려는 모든 항목을 선택한 후 **확인**을 선택 합니다.

    ![복원할 모든 항목을 선택한 후 확인을 선택 합니다.](./media/restore-afs/after-selecting-items.png)

1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

    ![시작 하려면 [복원]을 선택 하십시오.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. **대체 위치** 를 **복구 대상**으로 선택 합니다.
1. **저장소 계정** 드롭다운 목록에서 백업 된 콘텐츠를 복원 하려는 대상 저장소 계정을 선택 합니다.
1. **파일 공유 선택** 드롭다운 목록에는 2 단계에서 선택한 저장소 계정에 있는 파일 공유가 표시 됩니다. 백업 된 콘텐츠를 복원 하려는 파일 공유를 선택 합니다.
1. **폴더 이름** 상자에서 복원 된 내용이 포함 된 대상 파일 공유에 만들려는 폴더 이름을 지정 합니다.
1. 충돌이 발생 했을 때 건너뛸지 아니면 덮어쓸지를 선택 합니다.
1. **파일 선택** 을 선택 하 여 복원할 파일이 나 폴더를 선택 합니다.

    ![대체 위치로 복원할 항목 선택](./media/restore-afs/restore-to-alternate-location.png)

1. **파일 선택**을 선택 하면 복원 하도록 선택한 파일 공유 복구 지점의 내용이 파일 공유 창에 표시 됩니다.
1. 복원 하려는 파일이 나 폴더에 해당 하는 확인란을 선택 하 고 **선택**을 선택 합니다.

    ![복구 대상 선택](./media/restore-afs/recovery-destination.png)

1. 여러 파일 또는 폴더를 복원 하도록 선택 하려면 6 ~ 8 단계를 반복 합니다.
1. 복원 하려는 모든 항목을 선택한 후 **확인**을 선택 합니다.

    ![모든 파일을 선택한 후 확인을 선택 합니다.](./media/restore-afs/after-selecting-all-items.png)

1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

## <a name="track-a-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 작업에 대한 작업을 보려면 알림 하이퍼링크를 선택 합니다.

![알림 선택 하이퍼링크](./media/restore-afs/notifications-link.png)

Recovery Services 자격 증명 모음에서 복원 진행률을 모니터링할 수도 있습니다.

1. 복원 작업을 트리거한 Recovery Services 자격 증명 모음을 엽니다.
1. 개요 창의 **모니터링** 섹션에서 **백업 작업** 을 선택 하 여 다른 작업에 대해 실행 중인 작업의 상태를 확인 합니다.

    ![백업 작업 선택](./media/restore-afs/backup-jobs.png)

1. 파일 공유에 해당 하는 작업 이름을 선택 하 여 **전송 된 데이터** 및 복원 **된 파일 수**와 같이 복원 작업에 대한 자세한 정보를 확인 합니다.

    ![복원 된 세부 정보 보기](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 백업을 관리](manage-afs-backup.md)하는 방법을 알아봅니다.
