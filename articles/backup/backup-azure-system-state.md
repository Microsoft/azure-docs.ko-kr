---
title: Azure에 Windows 시스템 상태 백업
description: Windows Server 컴퓨터의 시스템 상태를 Azure에 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 14ca17284d48355260cdeda6ef3b20b4794a0c4f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181632"
---
# <a name="back-up-windows-system-state-to-azure"></a>Azure에 Windows 시스템 상태 백업

이 문서에서는 Azure에 Windows 서버 시스템 상태를 백업하는 방법을 설명합니다. 기본 사항을 안내 합니다.

Azure Backup에 대해 자세히 알아보려면 이 [개요](backup-overview.md)를 읽어보세요.

Azure 구독이 없는 경우 모든 Azure 서비스에 액세스할 수 있는 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>자격 증명 모음에 스토리지 중복 설정

Recovery Services 자격 증명 모음을 만드는 경우 스토리지 중복을 원하는 대로 구성해야 합니다.

1. **Recovery Services 자격** 증명 모음 창에서 새 자격 증명 모음을 선택 합니다.

    ![Recovery Services 자격 증명 모음 목록에서 새 자격 증명 모음 선택](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    자격 증명 모음을 선택 하면 **Recovery Services 자격 증명 모음** 창이 축소 되 고 설정 창 (*맨 위에 자격 증명 모음의 이름이 있음*) 및 자격 증명 모음 세부 정보 창이 열립니다.

    ![새 자격 증명 모음의 스토리지 구성 보기](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 새 자격 증명 모음 설정 창에서 세로 슬라이드를 사용 하 여 관리 섹션으로 스크롤하고 **백업 인프라**를 선택 합니다.
    백업 인프라 창이 열립니다.
3. 백업 인프라 창에서 백업 **구성** 을 선택 하 여 **백업 구성** 창을 엽니다.

    ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 자격 증명 모음에 대해 적절한 스토리지 복제 옵션을 선택합니다.

    ![저장소 구성 선택](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    기본적으로 사용자 자격 증명 모음에는 지역 중복 스토리지가 있습니다. Azure를 기본 백업 스토리지 엔드포인트로 사용하는 경우 **지역 중복**을 계속 사용합니다. Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다. [지역 중복](../storage/common/storage-redundancy.md) 및 [로컬 중복](../storage/common/storage-redundancy.md) 스토리지 옵션에 대한 자세한 내용은 [스토리지 중복 개요](../storage/common/storage-redundancy.md)를 참조하세요.

이제 자격 증명 모음을 만들었으므로 Windows 시스템 상태를 백업하도록 구성합니다.

## <a name="configure-the-vault"></a>자격 증명 모음 구성

1. Recovery Services 자격 증명 모음 창에서 (방금 만든 자격 증명 모음에 대해) 시작 섹션에서 **백업**을 선택 하 고 **백업 시작** 창에서 **백업 목표**를 선택 합니다.

    ![백업 설정 열기](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    **백업 목표** 창이 열립니다.

    ![백업 목표 창 열기](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. **작업이 실행되는 위치** 드롭다운 메뉴에서 **온-프레미스**를 선택합니다.

    Windows Server 또는 Windows 컴퓨터가 Azure에 없는 물리적 컴퓨터 이므로 **온-프레미스** 를 선택 합니다.

3. **백업할 항목** 을 선택 하십시오. 메뉴에서 **시스템 상태**를 선택 하 고 **확인**을 선택 합니다.

    ![파일 및 폴더 구성](./media/backup-azure-system-state/backup-goal-system-state.png)

    확인을 클릭 하면 **백업 목표**옆에 확인 표시가 나타나고 **인프라 준비** 창이 열립니다.

    ![Backup 목표 구성, 다음으로 인프라 준비](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **인프라 준비** 창에서 **Windows Server 또는 windows 클라이언트에 대 한 에이전트 다운로드**를 선택 합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Windows Server 필수를 사용 하는 경우 Windows Server 용 에이전트를 다운로드 하도록 선택 합니다. MARSAgentInstaller.exe를 실행하거나 저장하라는 팝업 메뉴가 나타납니다.

    ![MARSAgentInstaller 대화 상자](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 다운로드 팝업 메뉴에서 **저장**을 선택 합니다.

    기본적으로 **MARSagentinstaller.exe** 파일이 다운로드 폴더에 저장됩니다. 설치 관리자가 완료 되 면 설치 관리자를 실행할지 아니면 폴더를 열지 묻는 팝업이 표시 됩니다.

    ![MARS 설치 관리자가 완료 되었습니다.](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    에이전트를 아직 설치할 필요가 없습니다. 자격 증명 모음 자격 증명을 다운로드 한 후에 에이전트를 설치할 수 있습니다.

6. **인프라 준비** 창에서 **다운로드**를 선택 합니다.

    ![자격 증명 모음 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    자격 증명 모음 자격 증명은 **다운로드 폴더에 다운로드 됩니다** . 자격 증명 모음 다운로드를 완료 한 후 자격 증명을 열거나 저장할지 묻는 팝업이 표시 됩니다. **저장**을 선택합니다. 실수로 **열기**를 선택 하는 경우 자격 증명 모음 자격 증명을 열려고 시도 하는 대화 상자에서 오류가 발생 합니다. 자격 증명 모음 자격 증명을 열 수 없습니다. 다음 단계를 계속 합니다. 자격 증명 모음 자격 증명은 **다운로드** 폴더에 있습니다.

    ![자격 증명 모음 자격 증명 다운로드 완료](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > 자격 증명 모음 자격 증명은 에이전트를 사용 하려는 Windows Server의 로컬 위치에만 저장 해야 합니다.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

> [!NOTE]
> Azure Portal를 통한 백업을 사용 하도록 설정할 수 없습니다. Microsoft Azure Recovery Services 에이전트를 사용하여 Windows Server 시스템 상태를 백업합니다.
>

1. 다운로드 폴더(또는 기타 저장 위치)에서 **MARSagentinstaller.exe**를 찾아서 두 번 클릭합니다.

    설치 관리자는 Recovery Services 에이전트를 추출, 설치 및 등록할 때 일련의 메시지를 제공합니다.

    ![Recovery Services 에이전트 설치 관리자 자격 증명을 실행](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Microsoft Azure Recovery Services 에이전트 설치 마법사를 완료합니다. 마법사를 완료하려면 다음 작업을 수행해야 합니다.

   * 설치 및 캐시 폴더의 위치를 선택합니다.
   * 프록시 서버를 사용하여 인터넷에 연결하는 경우에는 프록시 서버 정보를 입력합니다.
   * 인증된 프록시를 사용하는 경우에는 사용자 이름 및 암호 세부 정보를 입력합니다.
   * 다운로드한 자격 증명 모음 제공
   * 암호화 암호를 안전한 위치에 저장합니다.

     > [!NOTE]
     > 암호를 분실 하거나 잊은 경우 Microsoft는 백업 데이터를 복구 하는 데 도움을 줍니다. 파일을 안전한 위치에 저장하세요. 백업을 복원 해야 합니다.
     >
     >

이제 에이전트가 설치되었고 컴퓨터가 자격 증명 모음에 등록되었습니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="back-up-windows-server-system-state"></a>Windows Server 시스템 상태 백업

초기 백업에는 다음 두 가지 작업이 포함됩니다.

* 백업 예약
* 처음으로 시스템 상태 백업

최초 백업을 완료하려면 Microsoft Azure Recovery Services 에이전트를 사용합니다.

> [!NOTE]
> Windows Server 2008 R2~Windows Server 2016에서 시스템 상태를 백업할 수 있습니다. 시스템 상태 백업은 클라이언트 Sku에서 지원 되지 않습니다. 시스템 상태는 Windows 클라이언트 또는 Windows Server 2008 SP2 컴퓨터에 대 한 옵션으로 표시 되지 않습니다.
>
>

### <a name="to-schedule-the-backup-job"></a>백업 작업을 예약하려면

1. Microsoft Azure Recovery Services 에이전트를 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.

    ![Azure Recovery Services 에이전트 시작](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Recovery Services 에이전트에서 **백업 예약**을 선택 합니다.

    ![Windows Server 백업 예약](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 백업 예약 마법사의 **시작** 페이지에서 **다음**을 선택 합니다.

4. **백업할 항목 선택** 페이지에서 **항목 추가**를 선택 합니다.

5. **시스템 상태** 를 선택 하 고 **확인**을 선택 합니다.

6. **다음**을 선택합니다.

7. 이후 페이지에서 시스템 상태 백업에 대해 필요한 백업 빈도와 보존 정책을 선택합니다.

8. 확인 페이지에서 정보를 검토 한 다음 **마침**을 선택 합니다.

9. 마법사가 백업 일정 생성을 완료하면 **닫기**를 선택합니다.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>처음으로 Windows Server 시스템 상태를 백업하려면

1. 다시 부팅해야 하는 Windows Server에 보류 중인 업데이트가 없어야 합니다.

2. Recovery Services 에이전트에서 **지금** 백업을 선택 하 여 네트워크를 통한 초기 시드를 완료 합니다.

    ![지금 Windows Server 백업](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 표시 되는 **백업 항목 선택** 화면에서 **시스템 상태** 를 선택 하 고 **다음**을 선택 합니다.

4. 확인 페이지에서 컴퓨터를 백업하는 데 지금 백업 마법사가 사용할 설정을 검토합니다. 그런 다음, **백업**을 선택합니다.

5. **닫기**를 선택하여 마법사를 닫습니다. 백업 프로세스가 완료되기 전에 마법사를 닫으면 마법사가 백그라운드에서 계속 실행됩니다.
    > [!NOTE]
    > MARS 에이전트는 `SFC /verifyonly` 모든 시스템 상태 백업 전에 사전 검사의 일부로 트리거됩니다. 이는 시스템 상태의 일부로 백업 된 파일에 Windows 버전에 해당 하는 올바른 버전이 있는지 확인 하는 것입니다. [이 문서의](/windows-server/administration/windows-commands/sfc)SFC (시스템 파일 검사기)에 대해 자세히 알아보세요.
    >

초기 백업 작업이 완료되면 Backup 콘솔에 **작업 완료** 상태가 표시됩니다.

  ![IR 완료](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>궁금한 점이 더 있나요?

질문이 있으면 [사용자 의견을 보내 주시기](https://feedback.azure.com/forums/258995-azure-backup)바랍니다.

## <a name="next-steps"></a>다음 단계

* [Windows 컴퓨터 백업](backup-windows-with-mars-agent.md)에 대해 자세히 알아보세요.
* 이제 Windows Server 시스템 상태를 백업했으므로 [자격 증명 모음 및 서버를 관리](backup-azure-manage-windows-server.md)할 수 있습니다.
* 백업을 복원해야 하는 경우 이 문서를 참조하여 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)할 수 있습니다.
