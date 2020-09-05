---
title: MARS 에이전트를 사용 하 여 Windows 컴퓨터 백업
description: MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 Windows 컴퓨터를 백업 합니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 55eb8bdf069fd1b5793468ae52f764eb4288f261
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182091"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Azure에 Windows Server 파일 및 폴더 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스 및 MICROSOFT AZURE RECOVERY SERVICES (MARS) 에이전트를 사용 하 여 Windows 컴퓨터를 백업 하는 방법을 설명 합니다. MARS는 Azure Backup 에이전트 라고도 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 필수 구성 요소 확인
> * 백업 정책 및 일정을 만듭니다.
> * 주문형 백업을 수행 합니다.

## <a name="before-you-start"></a>시작하기 전에

* [AZURE BACKUP MARS 에이전트를 사용 하 여 Windows 컴퓨터를 백업](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)하는 방법에 대해 알아봅니다.
* 보조 MABS 또는 Data Protection Manager 서버에서 MARS 에이전트를 실행 하는 [백업 아키텍처](backup-architecture.md#architecture-back-up-to-dpmmabs) 에 대해 알아봅니다.
* 지원 되는 기능과 MARS 에이전트에서 [백업할 수 있는 항목을](backup-support-matrix-mars-agent.md) 검토 합니다.
* 백업 하려는 컴퓨터에서 [인터넷 액세스를 확인](install-mars-agent.md#verify-internet-access) 합니다.
* MARS 에이전트가 설치 되지 않은 경우 [여기](install-mars-agent.md)에 설치 하는 방법을 알아보세요.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 데이터의 스냅숏을 사용 하 여 복구 지점의 생성 시점을 지정 합니다. 복구 지점이 유지 되는 기간도 지정 합니다. MARS 에이전트를 사용 하 여 백업 정책을 구성 합니다.

Azure Backup는 DST (일광 절약 시간)를 자동으로 고려 하지 않습니다. 이 기본값을 통해 실제 시간과 예약 된 백업 시간 사이에 약간의 차이가 발생할 수 있습니다.

백업 정책을 만들려면:

1. MARS 에이전트를 다운로드 하 고 등록 한 후에는 에이전트 콘솔을 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.  

1. **작업**아래에서 **백업 예약**을 선택 합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)
1. 백업 예약 마법사에서 다음 **시작**을 선택  >  **Next**합니다.
1. **백업할 항목 선택**에서 **항목 추가**를 선택 합니다.

    ![백업할 항목 추가](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. **항목 선택** 상자에서 백업할 항목을 선택 하 고 **확인**을 선택 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. **백업할 항목 선택** 페이지에서 **다음**을 선택 합니다.
1. **백업 일정 지정** 페이지에서 매일 또는 매주 백업을 수행할 시간을 지정 합니다. **다음**을 선택합니다.

    * 복구 지점은 백업을 수행할 때 생성 됩니다.
    * 사용자 환경에서 생성 되는 복구 지점의 수는 백업 일정에 따라 다릅니다.
    * 하루에 최대 3 개의 매일 백업을 예약할 수 있습니다. 다음 예에서는 매일 자정에 하나씩, 오후 6:00 시에 1 개의 매일 백업이 수행 됩니다.

        ![매일 백업 일정 설정](./media/backup-configure-vault/day-schedule.png)

    * 주별 백업만 실행할 수 있습니다. 다음 예에서는 모든 대체 일요일과 수요일 오전 9:30 시와 1:00 오전에 백업이 수행 됩니다.

        ![주간 백업 일정 설정](./media/backup-configure-vault/week-schedule.png)

1. **보존 정책 선택** 페이지에서 데이터의 기록 복사본을 저장 하는 방법을 지정 합니다. **다음**을 선택합니다.

    * 보존 설정은 저장할 복구 지점과 저장 기간을 지정 합니다.
    * 매일 보존 설정의 경우 매일 보존에 지정 된 시간에 최신 복구 지점이 지정 된 기간 (일) 동안 보존 됨을 표시 합니다. 또는 매월 30 일에 생성 된 복구 지점을 12 개월 동안 저장 하도록 지정할 수 있습니다.
    * 매일 및 매주 복구 지점의 보존은 일반적으로 백업 일정과 일치 합니다. 따라서 일정에서 백업을 트리거할 때 백업이 만드는 복구 지점은 매일 또는 매주 보존 정책이 지정 하는 기간 동안 저장 됩니다.
    * 다음 예제에서는

        * 자정 및 오후 6:00에 매일 백업은 7 일 동안 유지 됩니다.
        * 토요일 자정 및 오후 6:00에 수행 된 백업은 4 주 동안 유지 됩니다.
        * 매월 마지막 토요일 자정 및 오후 6:00에 수행 된 백업은 12 개월 동안 유지 됩니다.
        * 3 월의 마지막 토요일에 수행 된 백업은 10 년 동안 유지 됩니다.

        ![보존 정책의 예](./media/backup-configure-vault/retention-example.png)

1. **초기 백업 유형 선택** 페이지에서 네트워크를 통해 초기 백업을 수행할 것인지 아니면 오프 라인 백업을 사용할지 결정 합니다. 네트워크를 통해 초기 백업을 수행 하려면 **네트워크를 통해 자동으로**  >  **다음**을 선택 합니다.

    오프 라인 백업에 대 한 자세한 내용은 [오프 라인 백업에 Azure Data Box 사용](offline-backup-azure-data-box.md)을 참조 하세요.

    ![초기 백업 유형 선택](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. **확인** 페이지에서 정보를 검토 한 다음 **마침**을 선택 합니다.

    ![백업 유형 확인](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 마법사가 백업 일정 생성을 완료하면 **닫기**를 선택합니다.

    ![백업 일정 진행률 보기](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

에이전트가 설치 된 각 컴퓨터에서 정책을 만듭니다.

### <a name="do-the-initial-backup-offline"></a>초기 백업 오프 라인 수행

네트워크를 통해 초기 백업을 자동으로 실행 하거나 오프 라인으로 백업할 수 있습니다. 초기 백업에 대 한 오프 라인 시드는 전송에 많은 네트워크 대역폭을 필요로 하는 대량의 데이터가 있는 경우 유용 합니다.

오프 라인 전송 작업을 수행 하려면:

1. 준비 위치에 백업 데이터를 씁니다.
1. AzureOfflineBackupDiskPrep 도구를 사용 하 여 스테이징 위치에서 하나 이상의 SATA 디스크로 데이터를 복사 합니다.

    이 도구는 Azure 가져오기 작업을 만듭니다. 자세한 내용은 [Azure Import/Export 서비스 정의](../storage/common/storage-import-export-service.md)를 참조 하세요.
1. Azure 데이터 센터에 SATA 디스크를 보냅니다.

    데이터 센터에서 디스크 데이터는 Azure storage 계정에 복사 됩니다. Azure Backup 저장소 계정에서 자격 증명 모음으로 데이터를 복사 하 고 증분 백업이 예약 됩니다.

오프 라인 시드에 대 한 자세한 내용은 [Azure Data Box를 사용 하 여 오프 라인 백업에](offline-backup-azure-data-box.md)대 한 사용을 참조 하세요.

### <a name="enable-network-throttling"></a>네트워크 제한 사용

네트워크 제한을 사용 하도록 설정 하 여 MARS 에이전트가 네트워크 대역폭을 사용 하는 방법을 제어할 수 있습니다. 제한은 근무 시간에 데이터를 백업 해야 하지만 백업 및 복원 작업에서 사용 하는 대역폭의 양을 제어 하려는 경우에 유용 합니다.

Azure Backup의 네트워크 제한은 로컬 운영 체제의 [QoS (서비스 품질)](/windows-server/networking/technologies/qos/qos-policy-top) 를 사용 합니다.

백업에 대 한 네트워크 제한은 Windows Server 2012 이상 및 Windows 8 이상에서 사용할 수 있습니다. 운영 체제는 최신 서비스 팩을 실행 해야 합니다.

네트워크 제한을 사용 하려면 다음을 수행 합니다.

1. MARS 에이전트에서 **속성 변경**을 선택 합니다.
1. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택 합니다.

    ![백업 작업에 대 한 네트워크 제한 설정](./media/backup-configure-vault/throttling-dialog.png)
1. 근무 시간 및 근무 외 시간에 허용 되는 대역폭을 지정 합니다. 대역폭 값은 512 Kbps에서 시작 하 여 1023 MBps로 이동 합니다. 그런 다음, **확인**을 선택합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

1. MARS 에이전트에서 **지금 백업**을 선택 합니다.

    ![지금 Windows Server에서 백업](./media/backup-configure-vault/backup-now.png)

1. MARS 에이전트 버전이 2.0.9169.0 이상 이면 사용자 지정 보존 날짜를 설정할 수 있습니다. **백업 보존 기간** 섹션에서 달력의 날짜를 선택 합니다.

   ![달력을 사용 하 여 보존 날짜 사용자 지정](./media/backup-configure-vault/mars-ondemand.png)

1. **확인** 페이지에서 설정을 검토 하 고 **백업**을 선택 합니다.
1. **닫기**를 선택하여 마법사를 닫습니다. 백업이 완료 되기 전에 마법사를 닫으면 마법사가 백그라운드에서 계속 실행 됩니다.

초기 백업이 완료 되 면 백업 콘솔에 **작업 완료 됨** 상태가 표시 됩니다.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>주문형 백업 정책 보존 동작 설정

> [!NOTE]
> 이 정보는 2.0.9169.0 보다 오래 된 MARS 에이전트 버전에만 적용 됩니다.
>

| 백업-일정 옵션 | 데이터 보존 기간
| -- | --
| 일 | **기본 보존**: "일별 백업에 대 한 보존 기간 (일)"에 해당 합니다. <br/><br/> **예외**: 장기 보존 (주, 월 또는 년)에 대해 설정 된 매일 예약 된 백업이 실패 하는 경우 실패 직후 트리거되는 주문형 백업은 장기 보존에 대 한 것으로 간주 됩니다. 그렇지 않으면 다음 예약 된 백업이 장기 보존을 위해 고려 됩니다.<br/><br/> **예제 시나리오**: 목요일 오전 8:00 시에 예약 된 백업이 실패 했습니다. 이 백업은 매주, 매월 또는 매년 보존 되는 것으로 간주 됩니다. 따라서 금요일 오전 8:00에 예약 된 다음 백업 이전에 트리거된 첫 번째 주문형 백업은 매주, 매월 또는 매년 보존에 대해 자동으로 태그가 지정 됩니다. 이 백업은 목요일 8:00 AM 백업을 대체 합니다.
| 주 | **기본 보존**: 1 일 매주 백업 정책이 있는 데이터 원본에 대해 수행 되는 주문형 백업은 다음 날에 삭제 됩니다. 데이터 원본에 대 한 최신 백업 인 경우에도 삭제 됩니다. <br/><br/> **예외**: 장기 보존 (주, 월 또는 년)에 대해 설정 된 주별 예약 된 백업이 실패 하는 경우 실패 직후 트리거되는 주문형 백업은 장기 보존에 대 한 것으로 간주 됩니다. 그렇지 않으면 다음 예약 된 백업이 장기 보존을 위해 고려 됩니다. <br/><br/> **예제 시나리오**: 목요일 오전 8:00 시에 예약 된 백업이 실패 했습니다. 이 백업은 매월 또는 매년 보존 될 때 고려 됩니다. 따라서 목요일 오전 8:00에 예약 된 다음 백업 이전에 트리거되는 첫 번째 요청 시 백업은 매월 또는 매년 보존에 대해 자동으로 태그가 지정 됩니다. 이 백업은 목요일 8:00 AM 백업을 대체 합니다.

자세한 내용은 [백업 정책 만들기](#create-a-backup-policy)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 파일을 복원](backup-azure-restore-windows-server.md)하는 방법을 알아봅니다.
* [파일 및 폴더 백업에 대 한 일반적인 질문](backup-azure-file-folder-backup-faq.md) 찾기
