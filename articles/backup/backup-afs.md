---
title: Azure Portal의 Azure 파일 공유 백업
description: Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890351"
---
# <a name="back-up-azure-file-shares"></a>Azure 파일 공유 백업

이 문서에서는 Azure Portal에서 [Azure 파일 공유](../storage/files/storage-files-introduction.md) 를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* Recovery Services 자격 증명 모음을 만듭니다.
* Recovery Services 자격 증명 모음에서 백업 구성
* 파일 공유 창에서 백업 구성
* 주문형 백업 작업을 실행하여 복원 지점 만들기

## <a name="prerequisites"></a>전제 조건

* Azure 파일 공유 스냅숏 기반 백업 솔루션에 [대해 알아봅니다](azure-file-share-backup-overview.md) .
* 파일 공유가 지원 되는 [저장소 계정 유형](azure-file-share-support-matrix.md)중 하나에 있는지 확인 하세요.
* 파일 공유를 호스트 하는 저장소 계정과 동일한 지역에서 [Recovery Services 자격 증명 모음](#create-a-recovery-services-vault) 을 식별 하거나 만듭니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 백업 구성

다음 단계에서는 Recovery Services 자격 증명 모음 창에서 여러 파일 공유에 대 한 백업을 구성 하는 방법을 설명 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 파일 공유에 대 한 백업을 구성 하는 데 사용할 Recovery Services 자격 증명 모음을 엽니다.

1. **Recovery Services 자격 증명 모음** 창의 맨 위에 있는 메뉴에서 **+ 백업을** 선택 합니다.

   ![Recovery Services 자격 증명 모음](./media/backup-afs/recovery-services-vault.png)

    1. **백업 목표** 창에서 드롭다운 목록에서 **azure** 옵션을 선택 하 여 **워크 로드가 실행 되는 위치** 를 **azure** 로 설정 합니다.

          ![작업으로 Azure 선택](./media/backup-afs/backup-goal.png)

    2. **백업할 항목**에서 드롭다운 목록에서 **Azure 파일 공유** 를 선택 합니다.

          ![Azure 파일 공유 선택](./media/backup-afs/select-azure-file-share.png)

    3. **백업** 을 선택 하 여 자격 증명 모음에 Azure 파일 공유 확장을 등록 합니다.

          ![백업을 선택 하 여 Azure 파일 공유를 자격 증명 모음과 연결 합니다.](./media/backup-afs/register-extension.png)

1. **백업**을 선택한 후 **백업** 창이 열립니다. 보호 하려는 파일 공유를 호스트 하는 저장소 계정을 선택 하려면 **Storage 계정** 텍스트 상자 아래에서 링크 텍스트 **선택** 을 선택 합니다.

   ![선택 링크 선택](./media/backup-afs/choose-select-link.png)

1. **저장소 계정 선택 창이** 오른쪽에 열리고 검색 된 지원 저장소 계정 집합이 나열 됩니다. 이 자격 증명 모음과 연결 되거나 자격 증명 모음과 동일한 지역에 있지만 아직 Recovery Services 자격 증명 모음에 연결 되지 않습니다.

1. 검색 된 저장소 계정 목록에서 계정을 선택 하 고 **확인**을 선택 합니다.

   ![검색 된 저장소 계정에서 선택](./media/backup-afs/select-discovered-storage-account.png)

1. 다음 단계는 백업 하려는 파일 공유를 선택 하는 것입니다. **파일 공유 To Backup** 섹션에서 **추가** 단추를 선택 합니다.

   ![백업할 파일 공유를 선택 합니다.](./media/backup-afs/select-file-shares-to-back-up.png)

1. **파일 공유 컨텍스트 선택** 창이 오른쪽에 열립니다. Azure는 저장소 계정에서 백업할 수 있는 파일 공유를 검색 합니다. 최근에 파일 공유를 추가 하 고 목록에 표시 되지 않는 경우 파일 공유가 나타날 때까지 잠시 기다립니다.

1. **파일 공유 선택** 목록에서 백업 하려는 파일 공유를 하나 이상 선택 합니다. **확인**을 선택합니다.

   ![파일 공유 선택](./media/backup-afs/select-file-shares.png)

1. 파일 공유에 대 한 백업 정책을 선택 하려면 다음 세 가지 옵션을 사용할 수 있습니다.

   * 기본 정책을 선택 합니다.<br>
   이 옵션을 사용 하면 30 일 동안 보존 되는 매일 백업을 사용 하도록 설정할 수 있습니다. 자격 증명 모음에 기존 백업 정책이 없으면 기본 정책 설정을 사용 하 여 백업 창이 열립니다. 기본 설정을 선택 하려는 경우 **백업 사용**을 직접 선택할 수 있습니다.

   * 새 정책 만들기 <br>

      1. 파일 공유에 대 한 새 백업 정책을 만들려면 **백업 정책** 섹션의 드롭다운 목록 아래에서 링크 텍스트를 선택 합니다.<br>

         ![새 정책 만들기](./media/backup-afs/create-new-policy.png)

      1. **백업 정책** 컨텍스트 창이 오른쪽에 열립니다. 텍스트 상자에 정책 이름을 지정 하 고 요구 사항에 따라 보존 기간을 선택 합니다. 매일 보존 옵션만 기본적으로 사용 하도록 설정 되어 있습니다. 매주, 매월 또는 매년 보존 하려면 해당 확인란을 선택 하 고 원하는 보존 값을 제공 합니다.

      1. 보존 값과 유효한 정책 이름을 지정한 후 **확인**을 선택 합니다.<br>

         ![정책 이름 및 보존 값 지정](./media/backup-afs/policy-name.png)

   * 기존 백업 정책 중 하나를 선택 합니다. <br>

      보호를 구성 하기 위한 기존 백업 정책 중 하나를 선택 하려면 **백업 정책** 드롭다운 목록에서 원하는 정책을 선택 합니다.<br>

      ![기존 정책 선택](./media/backup-afs/choose-existing-policy.png)

1. **백업 사용** 을 선택 하 여 파일 공유 보호를 시작 합니다.

   ![백업 사용 선택](./media/backup-afs/enable-backup.png)

백업 정책을 설정 하면 예약 된 시간에 파일 공유의 스냅숏이 생성 됩니다. 또한 복구 지점은 선택한 기간 동안 보존 됩니다.

>[!NOTE]
>이제 Azure Backup는 Azure 파일 공유 백업에 대 한 일별/주별/월별/매년 보존이 있는 정책을 지원 합니다.

## <a name="configure-backup-from-the-file-share-pane"></a>파일 공유 창에서 백업 구성

다음 단계는 각 파일 공유 창에서 개별 파일 공유에 대 한 백업을 구성 하는 방법을 설명 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 백업 하려는 파일 공유를 호스트 하는 저장소 계정을 엽니다.

1. 저장소 계정에서 **파일 공유**라는 타일을 선택 합니다. 저장소 계정에 대 한 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

   ![스토리지 계정](./media/backup-afs/storage-account.png)

1. 파일 공유 목록에 저장소 계정에 있는 모든 파일 공유가 표시 되어야 합니다. 백업할 파일 공유를 선택 합니다.

   ![파일 공유 목록](./media/backup-afs/file-shares-list.png)

1. 파일 공유 창의 **작업** 섹션에서 **백업** 을 선택 합니다. **백업 구성** 창이 오른쪽에 로드 됩니다.

   ![백업 창 구성](./media/backup-afs/configure-backup.png)

1. Recovery Services 자격 증명 모음을 선택 하려면 다음 중 하나를 수행 합니다.

    * 자격 증명 모음이 이미 있는 경우 기존 Recovery Services 자격 증명 모음 **선택** 라디오 단추를 선택 하 고 **자격 증명 모음 이름** 드롭다운 메뉴에서 기존 자격 증명 모음 중 하나를 선택 합니다.

       ![기존 자격 증명 모음 선택](./media/backup-afs/select-existing-vault.png)

    * 자격 증명 모음이 없는 경우 새 Recovery Services 자격 증명 모음 **만들기** 라디오 단추를 선택 합니다. 자격 증명 모음의 이름을 지정합니다. 파일 공유와 동일한 지역에 생성 됩니다. 기본적으로 자격 증명 모음은 파일 공유와 동일한 리소스 그룹에 만들어집니다. 다른 리소스 그룹을 선택 하려면 **리소스 유형** 드롭다운에서 새 링크 **만들기** 를 선택 하 고 리소스 그룹의 이름을 지정 합니다. **확인** 을 선택하여 계속합니다.

       ![새 자격 증명 모음 만들기](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >저장소 계정이 자격 증명 모음에 등록 되어 있거나 보호 하려는 파일 공유를 호스트 하는 저장소 계정 내에 보호 된 공유가 거의 없는 경우 Recovery Services 자격 증명 모음 이름이 미리 채워지며 [여기에서 자세한 내용을](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share)편집할 수 없게 됩니다.

1. **백업 정책** 선택에서 다음 중 하나를 수행 합니다.

    * 기본 정책을 그대로 둡니다. 30 일의 보존 기간 동안 매일 백업을 예약 합니다.

    * **백업 정책** 드롭다운 메뉴에서 기존 백업 정책 (있는 경우)을 선택 합니다.

       ![백업 정책 선택](./media/backup-afs/choose-backup-policy.png)

    * 요구 사항에 따라 일별/주별/월별/매년 보존을 사용 하 여 새 정책을 만듭니다.  

         1. **새 정책** 링크 텍스트 만들기를 선택 합니다.

         2. **백업 정책** 컨텍스트 창이 오른쪽에 열립니다. 텍스트 상자에 정책 이름을 지정 하 고 요구 사항에 따라 보존 기간을 선택 합니다. 매일 보존 옵션만 기본적으로 사용 하도록 설정 되어 있습니다. 매주, 매월 또는 매년 보존 하려면 해당 확인란을 선택 하 고 원하는 보존 값을 제공 합니다.

         3. 보존 값과 유효한 정책 이름을 지정한 후 **확인**을 선택 합니다.

            ![새 백업 정책 만들기](./media/backup-afs/create-new-backup-policy.png)

1. **백업 사용** 을 선택 하 여 파일 공유 보호를 시작 합니다.

   ![백업 사용 선택](./media/backup-afs/select-enable-backup.png)

1. 포털 알림에서 구성 진행률을 추적 하거나, 사용 중인 자격 증명 모음에서 백업 작업을 모니터링 하 여 파일 공유를 보호할 수 있습니다.

   ![포털 알림](./media/backup-afs/portal-notifications.png)

1. 백업 구성 작업이 완료 되 면 파일 공유 창의 **작업** 섹션에서 **백업** 을 선택 합니다. **자격 증명 모음** 을 나열 하는 컨텍스트 창이 오른쪽에 로드 됩니다. 여기에서 주문형 백업 및 복원 작업을 트리거할 수 있습니다.

   ![자격 증명 모음 essentials](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>주문형 백업 작업 실행

경우에 따라 백업 정책에서 예약 된 시간 외에 백업 스냅숏 또는 복구 지점을 생성할 수 있습니다. 요청 시 백업을 생성 하는 일반적인 이유는 백업 정책을 구성 하는 것입니다. 백업 정책의 일정에 따라 스냅숏이 생성 될 때까지 몇 시간 또는 몇 일이 걸릴 수 있습니다. 백업 정책이 적용될 때까지 데이터를 보호하려면 주문형 백업을 시작합니다. 파일 공유에 대 한 계획 된 변경을 수행 하기 전에 주문형 백업 만들기를 수행 해야 하는 경우가 종종 있습니다.

### <a name="from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서

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

1. 포털 알림을 모니터링 하 여 백업 작업 실행 완료에 대 한 추적을 유지 합니다. 자격 증명 모음 대시보드에서 작업 진행률을 모니터링할 수 있습니다. 진행 중인 **백업 작업**  >  **을**선택 합니다.

### <a name="from-the-file-share-pane"></a>파일 공유 창에서

1. 주문형 백업을 수행 하려는 파일 공유의 **개요** 창을 엽니다.

1. **작업** 섹션에서 **백업** 을 선택 합니다. **자격 증명 모음** 을 나열 하는 컨텍스트 창이 오른쪽에 로드 됩니다. **지금 백업** 을 선택 하 여 요청 시 백업을 수행 합니다.

   ![지금 백업을 선택 합니다.](./media/backup-afs/select-backup-now.png)

1. **지금 Backup** 창이 열립니다. 복구 지점에 대 한 보존 기간을 지정 합니다. 요청 시 백업을 위해 최대 10 년까지 보존할 수 있습니다.

   ![백업 날짜 보존](./media/backup-afs/retain-backup-date.png)

1. **확인**을 선택하여 확인합니다.

>[!NOTE]
>Azure Backup는 해당 계정의 파일 공유에 대 한 보호를 구성할 때 저장소 계정을 잠급니다. 이렇게 하면 백업 된 파일 공유와 함께 저장소 계정이 실수로 삭제 되는 것을 방지할 수가 있습니다.

## <a name="best-practices"></a>모범 사례

* Azure Backup에서 만든 스냅숏은 삭제 하지 마세요. 스냅샷을 삭제하면 복구 지점이 손실되거나 복원이 실패할 수 있습니다.

* Azure Backup 하 여 저장소 계정에서 수행 된 잠금을 제거 하지 마세요. 잠금을 삭제 하면 저장소 계정이 실수로 삭제 될 가능성이 높으며 삭제 된 경우 스냅숏 또는 백업이 손실 됩니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:

* [Azure 파일 공유 복원](restore-afs.md)
* [Azure 파일 공유 백업 관리](manage-afs-backup.md)
