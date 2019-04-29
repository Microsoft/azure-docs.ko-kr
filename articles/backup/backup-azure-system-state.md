---
title: Azure에 Windows 시스템 상태 백업
description: Windows Server 및/또는 Windows 컴퓨터의 시스템 상태를 Azure에 백업하는 방법을 알아봅니다.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: 백업 방법; 백업 방법; 파일 및 폴더 백업
ms.service: backup
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: saurse
ms.openlocfilehash: 6d8cbac7eab797662896a96ed588c9d6370cb230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782727"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Resource Manager 배포에서 Windows 시스템 상태 백업
이 문서에서는 Azure에 Windows 서버 시스템 상태를 백업하는 방법을 설명합니다. 기본 사항을 안내하기 위해 마련된 자습서입니다.

Azure Backup에 대해 자세히 알아보려면 이 [개요](backup-overview.md)를 읽어보세요.

Azure 구독이 없는 경우 모든 Azure 서비스에 액세스할 수 있는 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기
Windows Server 시스템 상태를 백업하려면 데이터를 저장하려는 지역에 Recovery Services 자격 증명 모음을 만들어야 합니다. 저장소를 복제할 방식도 결정해야 합니다.

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음을 만들려면
1. [Azure Portal](https://portal.azure.com/) 에 아직 로그인하지 않은 경우 Azure 구독을 사용하여 로그인합니다.
2. 허브 메뉴에서 **모든 서비스**를 클릭하고 리소스 목록에서 **Recovery Services**를 입력한 다음, **Recovery Services 자격 증명 모음**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-system-state/open-rs-vault-list.png) <br/>

    구독에 복구 서비스 자격 증명 모음이 있는 경우 자격 증명 모음이 나열됩니다.
3. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Recovery Services 자격 증명 모음 블레이드가 열리고 **이름**, **구독**, **리소스 그룹** 및 **위치**를 입력하라는 메시지가 표시됩니다.

    ![Recovery Services 자격 증명 모음 만들기 3단계](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.

5. **구독** 섹션에서 드롭다운 메뉴를 사용하여 Azure 구독을 선택합니다. 구독을 하나만 사용하면 해당 구독이 나타나고 다음 단계로 건너뛸 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 조직 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.

6. **리소스 그룹** 섹션에서:

    * 리소스 그룹을 만들려면 **새로 만들기**를 선택합니다.
    또는
    * **기존 그룹 사용**을 선택하고 드롭다운 메뉴를 클릭하여 사용 가능한 리소스 그룹 목록을 봅니다.

   리소스 그룹에 대한 전체 정보는 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

7. **위치** 를 클릭하여 자격 증명 모음에 대한 지리적 지역을 선택합니다. 선택에 따라 백업 데이터가 전송되는 지역이 결정됩니다.

8. Recovery Services 자격 증명 모음 블레이드의 하단에서 **만들기**를 클릭합니다.

    Recovery Services 자격 증명 모음을 만드는 데 몇 분 정도 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다. 몇 분이 지나도 자격 증명 모음이 보이지 않으면 **새로 고침**을 클릭합니다.

    ![새로 고침 단추 클릭](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Recovery Services 자격 증명 모음 목록에 사용자의 자격 증명 모음이 보이면 저장소 중복을 설정할 준비가 된 것입니다.

### <a name="set-storage-redundancy-for-the-vault"></a>자격 증명 모음에 저장소 중복 설정
Recovery Services 자격 증명 모음을 만드는 경우 저장소 중복을 원하는 대로 구성해야 합니다.

1. **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음을 클릭합니다.

    ![Recovery Services 자격 증명 모음 목록에서 새 자격 증명 모음 선택](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    자격 증명 모음을 선택하면 **Recovery Services 자격 증명 모음** 블레이드가 좁아지고 설정 블레이드(*맨 위에 자격 증명 모음 이름이 있음*) 및 자격 증명 모음 세부 정보 블레이드가 열립니다.

    ![새 자격 증명 모음의 저장소 구성 보기](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 새 자격 증명 모음의 설정 블레이드에서 세로 슬라이드를 사용하여 관리 섹션 쪽으로 아래로 스크롤하여 **Backup 인프라**를 클릭합니다.
    [Backup 인프라] 블레이드가 열립니다.
3. [Backup 인프라] 블레이드에서 **Backup 구성**을 클릭하여 **Backup 구성** 블레이드를 엽니다.

    ![새 자격 증명 모음의 저장소 구성 설정](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 자격 증명 모음에 대해 적절한 저장소 복제 옵션을 선택합니다.

    ![저장소 구성 선택 항목](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. Azure를 기본 백업 저장소 엔드포인트로 사용하는 경우 **지역 중복**을 계속 사용합니다. Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다. [지역 중복](../storage/common/storage-redundancy-grs.md) 및 [로컬 중복](../storage/common/storage-redundancy-lrs.md) 저장소 옵션에 대한 자세한 내용은 [저장소 중복 개요](../storage/common/storage-redundancy.md)를 참조하세요.

이제 자격 증명 모음을 만들었으므로 Windows 시스템 상태를 백업하도록 구성합니다.

## <a name="configure-the-vault"></a>자격 증명 모음 구성
1. Recovery Services 자격 증명 모음(방금 만든 자격 증명 모음) 블레이드의 [시작] 섹션에서 **Backup**을 클릭한 다음 **Backup 시작** 블레이드에서 **Backup 목표**를 선택합니다.

    ![백업 목표 블레이드 열기](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    **Backup 목표** 블레이드가 열립니다.

    ![백업 목표 블레이드 열기](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. **작업이 실행되는 위치** 드롭다운 메뉴에서 **온-프레미스**를 선택합니다.

    Windows Server 또는 Windows 컴퓨터가 Azure에 있지 않은 실제 컴퓨터이므로 **온-프레미스**를 선택합니다.

3. **백업할 항목** 메뉴에서 **시스템 상태**를 선택하고 **확인**을 클릭합니다.

    ![파일 및 폴더 구성](./media/backup-azure-system-state/backup-goal-system-state.png)

    [확인]을 클릭하면 **백업 목표** 옆에 확인 표시가 나타나고 **인프라 준비** 블레이드가 열립니다.

    ![Backup 목표 구성, 다음으로 인프라 준비](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **인프라 준비** 블레이드에서 **Windows Server 또는 Windows Client용 에이전트 다운로드**를 클릭합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Windows Server Essential을 사용하는 경우 Windows Server Essential 용 에이전트를 다운로드하도록 선택합니다. MARSAgentInstaller.exe를 실행하거나 저장하라는 팝업 메뉴가 나타납니다.

    ![MARSAgentInstaller 대화 상자](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 다운로드 팝업 메뉴에서 **저장**을 클릭합니다.

    기본적으로 **MARSagentinstaller.exe** 파일이 다운로드 폴더에 저장됩니다. 설치 관리자가 완료되면 설치 관리자를 실행할지 아니면 폴더를 열지 묻는 팝업이 표시됩니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    에이전트를 아직 설치할 필요가 없습니다. 에이전트는 자격 증명 모음을 다운로드한 후에 설치할 수 있습니다.

6. **인프라 준비** 블레이드에서 **다운로드**를 클릭합니다.

    ![자격 증명 모음 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    자격 증명 모음 자격 증명이 [다운로드] 폴더로 다운로드됩니다. 자격 증명 모음 다운로드가 완료되면 자격 증명을 열거나 저장할지 묻는 팝업이 나타납니다. **저장**을 클릭합니다. 실수로 **열기**를 클릭하면 자격 증명 모음 자격 증명을 열려고 하는 대화 상자가 나타나지 않습니다. 자격 증명 모음 자격 증명을 열 수 없습니다. 다음 단계를 진행합니다. 자격 증명 모음은 다운로드 폴더에 있습니다.   

    ![자격 증명 모음 자격 증명 다운로드 완료](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > 자격 증명 모음 자격 증명은 에이전트를 사용하려는 Windows Server의 로컬 위치에만 저장해야 합니다.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

> [!NOTE]
> Azure Portal을 통한 백업 활성화는 아직 사용할 수 없습니다. Microsoft Azure Recovery Services 에이전트를 사용하여 Windows Server 시스템 상태를 백업합니다.
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
     > 암호를 분실하거나 잊어버린 경우 Microsoft에서 백업 데이터의 복구를 도와드릴 수 없습니다. 파일을 안전한 위치에 저장하세요. 백업을 복원할 때 필요합니다.
     >
     >

이제 에이전트가 설치되었고 컴퓨터가 자격 증명 모음에 등록되었습니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="back-up-windows-server-system-state"></a>Windows Server 시스템 상태 백업
초기 백업에는 다음 두 가지 작업이 포함됩니다.

* 백업 예약
* 처음으로 시스템 상태 백업

최초 백업을 완료하려면 Microsoft Azure Recovery Services 에이전트를 사용합니다.

> [!NOTE]
> Windows Server 2008 R2~Windows Server 2016에서 시스템 상태를 백업할 수 있습니다. 클라이언트 SKU에서는 시스템 상태 백업이 지원되지 않습니다. 시스템 상태는 Windows 클라이언트 또는 Windows Server 2008 SP2 컴퓨터에 대한 옵션으로 표시되지 않습니다.
>
>

### <a name="to-schedule-the-backup-job"></a>백업 작업을 예약하려면

1. Microsoft Azure Recovery Services 에이전트를 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.

    ![Azure Recovery Services 에이전트 시작](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Recovery Services 에이전트에서 **Backup 예약**을 클릭합니다.

    ![Windows Server 백업 예약](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Backup 예약 마법사의 시작 페이지에서 **다음**을 클릭합니다.

4. 백업할 항목 선택 페이지에서 **항목 추가**를 클릭합니다.

5. **시스템 상태**를 선택하고 **확인**을 클릭합니다.

6. **다음**을 클릭합니다.

7. 이후 페이지에서 시스템 상태 백업에 대해 필요한 백업 빈도와 보존 정책을 선택합니다.

8. 확인 페이지에서 정보를 검토한 다음 **마침**을 클릭합니다.

9. 마법사가 백업 일정 생성을 완료하면 **닫기**를 클릭합니다.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>처음으로 Windows Server 시스템 상태를 백업하려면

1. 다시 부팅해야 하는 Windows Server에 보류 중인 업데이트가 없어야 합니다.

2. Recovery Services 에이전트에서 **지금 백업** 을 클릭하여 네트워크를 통한 초기 시드 작업을 완료합니다.

    ![Windows Server 지금 백업](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 표시되는 **백업 항목 선택** 화면에서 **시스템 상태**를 선택하고 **다음**을 클릭합니다.

4. 확인 페이지에서 컴퓨터를 백업하는 데 지금 백업 마법사가 사용할 설정을 검토합니다. 그런 다음 **백업**을 클릭합니다.

4. **닫기** 를 클릭하여 마법사를 닫습니다. 백업 프로세스가 완료되기 전에 마법사를 닫으면 마법사가 백그라운드에서 계속 실행됩니다.


초기 백업 작업이 완료되면 Backup 콘솔에 **작업 완료** 상태가 표시됩니다.

  ![IR 완료](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>다음 단계
* [Windows 컴퓨터 백업](backup-configure-vault.md)에 대해 자세히 알아보세요.
* 이제 Windows Server 시스템 상태를 백업했으므로 [자격 증명 모음 및 서버를 관리](backup-azure-manage-windows-server.md)할 수 있습니다.
* 백업을 복원해야 하는 경우 이 문서를 참조하여 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)할 수 있습니다.
