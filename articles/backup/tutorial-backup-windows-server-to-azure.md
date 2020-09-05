---
title: 자습서 - Azure에 Windows Server 백업
description: 이 자습서에서는 Recovery Services 자격 증명 모음에 온-프레미스 Windows Server를 백업하는 작업에 대해 자세히 설명합니다.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261908"
---
# <a name="back-up-windows-server-to-azure"></a>Azure에 Windows Server 백업

Azure 백업을 사용하여 손상, 공격 및 재해로부터 Windows Server를 보호할 수 있습니다. Azure 백업에서는 MARS(Microsoft Azure Recovery Services) 에이전트라는 간단한 도구를 제공합니다. MARS 에이전트는 Windows Server에 설치되어 파일과 폴더를 보호하며 Windows Server 시스템 상태를 통해 서버 구성 정보를 보호합니다. 이 자습서에서는 MARS 에이전트를 사용하여 Azure에 Windows Server를 백업하는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * MARS 에이전트 다운로드 및 설정
> * 서버 백업을 위한 백업 시간 및 보존 일정 구성
> * 주문형 백업 수행

## <a name="sign-in-to-azure"></a>Azure에 로그인

<https://portal.azure.com> 에서 Azure Portal에 로그인합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Windows Server를 백업하려면 먼저 백업을 위한 장소, 즉 저장될 복원 지점을 만들어야 합니다. [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 Windows Server의 백업을 저장하는 Azure의 컨테이너입니다. Azure Portal에서 Recovery Services 자격 증명 모음을 만들려면 다음 단계를 수행하세요.

1. 왼쪽 메뉴에서 **모든 서비스**를 선택하고 서비스 목록에서 **Recovery Services**를 입력합니다. **Recovery Services 자격 증명 모음**을 선택합니다.

   ![Recovery Services 자격 증명 모음 열기](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 선택합니다.

   ![자격 증명 모음에 대한 정보 제공](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. **Recovery Services 자격 증명 모음** 메뉴에서

    * **이름**에 *myRecoveryServicesVault*를 입력합니다.
    * 현재 구독 ID가 **구독**에 표시됩니다.
    * **리소스 그룹**에서 **기존 항목 사용**을 선택하고 *myResourceGroup*을 선택합니다. *myResourceGroup*이 없는 경우에는 **새로 만들기**를 선택한 후 *myResourceGroup*을 입력합니다.
    * **위치** 드롭다운 메뉴에서 *서유럽*를 선택합니다.
    * **만들기**를 선택하여 Recovery Services 자격 증명 모음을 만듭니다.

자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다.

## <a name="download-recovery-services-agent"></a>Recovery Services 에이전트 다운로드

MARS(Microsoft Azure Recovery Services) 에이전트는 Windows Server와 Recovery Services 자격 증명 모음 사이의 연결을 만듭니다. 다음 절차에서는 서버에 에이전트를 다운로드하는 방법을 설명합니다.

1. Recovery Services 자격 증명 모음 목록에서 **myRecoveryServicesVault**를 선택하여 해당 대시보드를 엽니다.

   ![자격 증명 모음을 선택하여 대시보드를 엽니다.](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. 자격 증명 모음 대시보드 메뉴에서 **백업**을 선택합니다.

3. **백업 목표** 메뉴에서

   * **작업이 실행되는 위치**로 **온-프레미스**를 선택합니다.
   * **백업할 항목**으로 **파일 및 폴더**와 **시스템 상태**를 선택합니다.

   ![백업 목표 메뉴](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. **인프라 준비**를 선택하여 **인프라 준비** 메뉴를 엽니다.

5. **인프라 준비** 메뉴에서 **Windows Server 또는 Windows Client용 에이전트 다운로드**를 선택하여 *MARSAgentInstaller.exe*를 다운로드합니다.

    ![Windows Server 또는 Windows Client용 에이전트 다운로드](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    설치 관리자가 별도의 브라우저를 열어 **MARSAgentInstaller.exe**를 다운로드합니다.

6. 다운로드한 파일을 실행하려면 먼저 인프라 준비 메뉴에서 **다운로드**를 선택하고 **Vault 자격 증명** 파일을 저장합니다. 보관 자격 증명은 Recovery Services 자격 증명 모음과 MARS 에이전트를 연결하는 데 필요합니다.

    ![저장소 자격 증명 다운로드](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

1. 다운로드한 **MARSagentinstaller.exe**를 찾아 두 번 클릭합니다.
2. **Microsoft Azure Recovery Services 에이전트 설치 마법사**가 나타납니다. 마법사를 진행하면서, 메시지가 표시되면 다음 정보를 제공하고 **등록**을 선택합니다.
   * 설치 및 캐시 폴더의 위치
   * 프록시 서버 정보(프록시 서버를 사용하여 인터넷에 연결하는 경우)
   * 사용자 이름 및 암호 세부 정보(인증된 프록시를 사용하는 경우)

     ![Microsoft Azure 복구 서비스 에이전트 설치 마법사](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. 마법사 끝 단계에서 **등록 진행**을 선택하고 이전 절차에서 다운로드한 **Vault 자격 증명** 파일을 제공합니다.

4. 메시지가 나타나면 Windows Server의 백업을 암호화하는 데 사용할 암호화 암호를 제공합니다. Microsoft는 잃어버린 암호를 복구할 수 없으므로, 암호를 안전한 위치에 저장하세요.

5. **마침**을 선택합니다.

## <a name="configure-backup-and-retention"></a>백업 및 보존 구성

Microsoft Azure Recovery Services 에이전트를 사용하면 Azure에 백업하는 작업이 Windows Server에서 발생하는 시점을 예약할 수 있습니다. 에이전트를 다운로드한 서버에서 다음 단계를 실행합니다.

1. Microsoft Azure Recovery Services 에이전트를 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.

2. Recovery Services 에이전트 콘솔의 **작업 창**에서 **백업 예약**을 선택합니다.

    ![Backup 예약](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. **다음**을 선택하여 **백업할 항목 선택** 페이지로 이동합니다.

4. **항목 추가**를 선택하고, 열리는 대화 상자에서 **시스템 상태** 및 백업할 파일 또는 폴더를 선택합니다. 그런 다음, **확인**을 선택합니다.

5. **다음**을 선택합니다.

6. **백업 일정 지정(시스템 상태)** 페이지에서 시스템 상태에 대해 백업을 트리거해야 하는 시간(일 또는 주)을 지정하고 **다음**을 선택합니다.

7. **보존 정책 선택(시스템 상태)** 페이지에서 시스템 상태에 대한 백업 복사본의 보존 정책을 선택하고 **다음**을 선택합니다.

8. 마찬가지로 선택한 파일 및 폴더에 대한 백업 일정 및 보존 정책을 선택합니다.

9. **초기 백업 형식 선택** 페이지에서 **네트워크를 통해 자동으로**를 선택하고 **다음**을 선택합니다.

10. **확인** 페이지에서 정보를 검토하고 **마침**을 선택합니다.

11. 마법사가 백업 일정 생성을 완료하면 **닫기**를 선택합니다.

## <a name="perform-an-on-demand-backup"></a>주문형 백업 수행

백업 작업을 실행하는 일정을 설정했습니다. 하지만 서버를 백업하지 않았습니다. 재해 복구의 경우 서버의 데이터 복원력을 위해 주문형 백업을 실행하는 것이 가장 좋습니다.

1. Microsoft Azure Recovery Services 에이전트 콘솔에서 **지금 백업**을 선택합니다.

    ![지금 백업](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. **지금 백업** 마법사에서 백업할 **파일 및 폴더** 또는 **시스템 상태** 중 하나를 선택하고 **다음**을 선택합니다.
3. **확인** 페이지에서 서버를 백업하는 데 **지금 백업** 마법사가 사용할 설정을 검토합니다. 그런 다음, **백업**을 선택합니다.
4. **닫기**를 선택하여 마법사를 닫습니다. 백업 프로세스가 완료되기 전에 마법사를 닫으면 마법사가 백그라운드에서 계속 실행됩니다.
5. 초기 백업이 완료되면 MARS 에이전트 콘솔의 **작업** 창에 **작업 완료** 상태가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 다음을 수행했습니다.

> [!div class="checklist"]
>
> * Recovery Services 자격 증명 모음 만들기
> * Microsoft Azure Recovery Services 에이전트 다운로드
> * 에이전트 설치
> * Windows Server용 백업 구성
> * 주문형 백업 수행

다음 자습서에서는 Azure에서 Windows Server로 파일을 복구합니다.

> [!div class="nextstepaction"]
> [Azure에서 Windows Server로 파일 복원](./tutorial-backup-restore-files-windows-server.md)
