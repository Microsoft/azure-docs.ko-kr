---
title: Azure Portal의 Azure 파일 공유 백업
description: Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938099"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 Azure 파일 공유 백업

이 문서에서는 Azure Portal를 사용 하 여 [Azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* Recovery Services 자격 증명 모음을 만듭니다.
* 파일 공유를 검색 하 고 백업을 구성 합니다.
* 주문형 백업 작업을 실행 하 여 복원 지점을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* 파일 공유를 호스트 하는 저장소 계정과 동일한 지역에서 [Recovery Services 자격 증명 모음](#create-a-recovery-services-vault) 을 식별 하거나 만듭니다.
* 파일 공유가 지원 되는 [저장소 계정 유형](#limitations-for-azure-file-share-backup-during-preview)중 하나에 있는지 확인 하세요.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>미리 보는 동안 Azure 파일 공유 백업을 위한 제한 사항

Azure 파일 공유를 위한 백업은 미리 보기에 있습니다. 범용 v1 및 범용 v2 스토리지 계정 둘 다에서 Azure 파일 공유가 지원됩니다. Azure 파일 공유 백업에 대한 제한 사항은 다음과 같습니다.

* ZRS ( [영역 중복 저장소](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ) 복제를 사용 하는 저장소 계정에서 Azure 파일 공유의 백업 지원은 현재 [이러한 지역](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)으로 제한 됩니다.
* 현재 Azure Backup는 Azure 파일 공유의 매일 예약 된 백업 구성을 지원 합니다.
* 일별 최대 예약 백업의 수는 1개입니다.
* 일별 최대 주문형 백업의 수는 4개입니다.
* 스토리지 계정에서 [리소스 잠금](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)을 사용하면 Recovery Services 자격 증명 모음에서 Backup이 실수로 삭제되는 것을 방지할 수 있습니다.
* Azure Backup에서 만든 스냅숏은 삭제 하지 마세요. 스냅숏을 삭제 하면 복구 지점이 손실 되거나 복원 오류가 발생할 수 있습니다.
* Azure Backup로 보호 되는 파일 공유는 삭제 하지 마세요. 현재 솔루션은 파일 공유를 삭제 한 후 Azure Backup에서 수행 하는 모든 스냅숏을 삭제 하므로 모든 복원 지점이 손실 됩니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 자격 증명 모음은 [GRS (지역 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)를 사용 합니다.

* 자격 증명 모음이 기본 백업 메커니즘인 경우 GRS를 사용 하는 것이 좋습니다.
* [LRS (로컬 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 를 저렴 한 옵션으로 사용할 수 있습니다.

저장소 복제 유형을 수정 하려면:

1. 새 자격 증명 모음에서 **설정** 섹션의 **속성** 을 선택 합니다.

1. **속성** 페이지의 **백업 구성**에서 **업데이트**를 선택 합니다.

1. 저장소 복제 유형을 선택 하 고 **저장**을 선택 합니다.

    ![백업 구성 업데이트](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 자격 증명 모음을 설정 하 고 백업 항목을 포함 한 후에는 저장소 복제 유형을 수정할 수 없습니다. 이 작업을 수행 하려는 경우 자격 증명 모음을 다시 만들어야 합니다.
>

## <a name="discover-file-shares-and-configure-backup"></a>파일 공유 검색 및 백업 구성

1. [Azure Portal](https://portal.azure.com/)에서 파일 공유를 백업 하는 데 사용할 Recovery Services 자격 증명 모음을 엽니다.

1. **Recovery Services 자격 증명 모음** 대시보드에서 **+ 백업**을 선택 합니다.

   ![Recovery Services 자격 증명 모음](./media/backup-afs/recovery-services-vault.png)

    a. **백업 목표**에서 **워크 로드가 실행 되는 위치** 를 **Azure**로 설정 합니다.

    ![Azure 파일 공유를 백업 목표로 선택](./media/backup-afs/backup-goal.png)

    b.  **백업할 항목**에서 드롭다운 목록에서 **Azure 파일 공유** 를 선택 합니다.

    다.  **백업** 을 선택 하 여 자격 증명 모음에 Azure 파일 공유 확장을 등록 합니다.

    ![백업을 선택 하 여 Azure 파일 공유를 자격 증명 모음과 연결 합니다.](./media/backup-afs/register-extension.png)

1. **백업**을 선택한 후 **백업** 창이 열리고 검색 된 지원 저장소 계정 목록에서 저장소 계정을 선택 하 라는 메시지가 표시 됩니다. 이 자격 증명 모음과 연결 되거나 자격 증명 모음과 동일한 지역에 있지만 아직 Recovery Services 자격 증명 모음에 연결 되지 않습니다.

   ![스토리지 계정 선택](./media/backup-afs/select-storage-account.png)

1. 검색 된 저장소 계정 목록에서 계정을 선택 하 고 **확인**을 선택 합니다. Azure는 저장소 계정에서 백업할 수 있는 파일 공유를 검색 합니다. 최근에 파일 공유를 추가 하 고 목록에 표시 되지 않는 경우 파일 공유가 나타날 때까지 잠시 기다립니다.

    ![파일 공유 검색](./media/backup-afs/discovering-file-shares.png)

1. **파일 공유** 목록에서 백업 하려는 파일 공유를 하나 이상 선택 합니다. **확인**을 선택합니다.

1. 파일 공유를 선택한 후 **백업 메뉴는** **백업 정책**으로 전환 됩니다. 이 메뉴에서 기존 백업 정책을 선택 하거나 새로 만듭니다. 그런 다음 **백업 사용**을 선택 합니다.

    ![백업 정책 선택](./media/backup-afs/select-backup-policy.png)

백업 정책을 설정 하면 예약 된 시간에 파일 공유의 스냅숏이 생성 됩니다. 또한 복구 지점은 선택한 기간 동안 보존 됩니다.

## <a name="create-an-on-demand-backup"></a>주문형 백업 만들기

경우에 따라 백업 정책에서 예약 된 시간 외에 백업 스냅숏 또는 복구 지점을 생성할 수 있습니다. 요청 시 백업을 생성 하는 일반적인 이유는 백업 정책을 구성 하는 것입니다. 백업 정책의 일정에 따라 스냅숏이 생성 될 때까지 몇 시간 또는 몇 일이 걸릴 수 있습니다. 백업 정책이 적용될 때까지 데이터를 보호하려면 주문형 백업을 시작합니다. 파일 공유에 대한 계획 된 변경을 수행 하기 전에 주문형 백업 만들기를 수행 해야 하는 경우가 종종 있습니다.

### <a name="create-a-backup-job-on-demand"></a>주문형 백업 작업 만들기

1. 파일 공유를 백업 하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. **개요** 창의 **보호 된 항목** 섹션에서 **백업 항목** 을 선택 합니다.

   ![백업 항목 선택](./media/backup-afs/backup-items.png)

1. **백업 항목**을 선택 하면 모든 **백업 관리 유형을** 나열 하는 새 창이 **개요** 창 옆에 표시 됩니다.

   ![백업 관리 유형 목록](./media/backup-afs/backup-management-types.png)

1. **백업 관리 유형** 목록에서 **Azure Storage (Azure Files)** 를 선택 합니다. 이 자격 증명 모음을 사용 하 여 백업 된 모든 파일 공유 및 해당 저장소 계정 목록이 표시 됩니다.

   ![Azure Storage (Azure Files) 백업 항목](./media/backup-afs/azure-files-backup-items.png)

1. Azure 파일 공유 목록에서 원하는 파일 공유를 선택 합니다. **백업 항목** 정보가 표시 됩니다. **백업 항목** 메뉴에서 **지금 백업**을 선택 합니다. 이 백업 작업은 요청 시 이므로 복구 지점과 연결 된 보존 정책이 없습니다.

   ![지금 백업을 선택 합니다.](./media/backup-afs/backup-now.png)

1. **지금 Backup** 창이 열립니다. 복구 지점을 보존할 마지막 날을 지정합니다. 요청 시 백업을 위해 최대 10 년까지 보존할 수 있습니다.

   ![보존 날짜 선택](./media/backup-afs/retention-date.png)

1. **확인** 을 선택 하 여 실행 되는 주문형 백업 작업을 확인 합니다.

1. 포털 알림을 모니터링 하 여 백업 작업 실행 완료에 대한 추적을 유지 합니다. 자격 증명 모음 대시보드에서 작업 진행률을 모니터링할 수 있습니다. **진행**중인 **백업 작업** > 선택 합니다.

## <a name="next-steps"></a>다음 단계

방법 알아보기
* [Azure 파일 공유 복원](restore-afs.md)
* [Azure 파일 공유 백업 관리](manage-afs-backup.md)
