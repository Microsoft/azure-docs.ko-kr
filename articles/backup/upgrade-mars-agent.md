---
title: MARS (Microsoft Azure Recovery Services) 에이전트 업그레이드
description: MARS (Microsoft Azure Recovery Services) 에이전트를 업그레이드 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181479"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>MARS (Microsoft Azure Recovery Services) 에이전트 업그레이드

이 문서에서는 다음 방법을 설명합니다.

* 이전 버전의 MARS 에이전트를 사용 하는 서버 식별
* 해당 서버에서 MARS 설치 업데이트

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>이전 버전의 MARS 에이전트를 사용 하는 서버 식별

Azure Backup 에이전트 및 Azure Backup server 설치의 경우:

1. 이전 버전의 에이전트가 백업할 가능성이 있는 서버를 등록 한 Recovery Services 자격 증명 모음으로 이동 합니다. Azure에서 경고 Azure Backup 업데이트에서 이전 Azure Backup 에이전트가 있는 자격 증명 모음에 대 한 대표 목록을 찾을 수 있습니다.
1. Recovery Services 자격 증명 모음의 왼쪽 **설정** 섹션에서 **관리** 섹션 아래에 있는 **백업 인프라** 를 선택 합니다.
1. Azure Backup server 설치의 일부로 설치 된 Azure Backup 에이전트를 검색 하려면 **관리 서버**아래의 **백업 관리 서버** 로 이동 합니다. 그러면 연결 된 Azure Backup 에이전트의 버전 번호와 함께 Azure Backup 서버를 설치한 서버가 나열 됩니다.

    ![Azure Backup server 설치의 일부로 설치 된 MARS 에이전트 목록](./media/upgrade-mars-agent/backup-management-servers.png)

1. MARS (에이전트 버전 Microsoft Azure Recovery Services) 에이전트 설치 또는 Azure Backup 에이전트를 확인 하려면 **관리 서버**아래에 있는 **보호 된 서버** 로 이동 합니다. 그런 다음 백업 관리 유형에서 **Azure Backup 에이전트** 를 선택 합니다. 설치에 대 한 버전 번호와 함께 Azure Backup 에이전트 설치를 포함 하는 서버가 나열 됩니다.

    ![MARS 에이전트가 설치 된 서버 목록](./media/upgrade-mars-agent/protected-servers.png)

1. MARS 에이전트 설치에 대 한 **에이전트 버전** 열을 선택 하거나 Azure Backup Server 설치의 **Azure Backup 에이전트 버전** 열을 선택 하 여 Azure Backup 에이전트 버전 열을 정렬 합니다.

1. 이전 단계에서는 2.0.9083.0이 아닌 버전의 Azure Backup 에이전트가 있는 서버 목록과 공백으로 나열 된 에이전트 버전을 제공 합니다. Azure Backup 에이전트를 업데이트 해야 하는 서버입니다.

## <a name="update-the-mars-agent-installation-on-the-server"></a>서버에서 MARS 에이전트 설치 업데이트

Azure Backup 에이전트 업데이트가 필요한 서버를 확인 한 후에는 식별 된 각 서버에 대해 다음 단계를 수행 합니다 (Azure Backup 서버 또는 MARS 에이전트 사용). 다음 단계를 수행 하기 전에 [최신 버전의 Azure Backup 에이전트를 다운로드](https://aka.ms/azurebackup_agent) 합니다.

1. Azure Backup 에이전트가 2.0.9083.0 보다 낮거나 비어 있는 행을 선택 하십시오. 그러면 서버 세부 정보 화면이 열립니다.

    ![최신 에이전트 버전이 포함 된 보호 된 서버](./media/upgrade-mars-agent/old-agent-version.png)

    ![최신 에이전트 버전이 있는 Azure Backup 서버](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. **연결** 을 선택 하 여 서버에 연결 하거나 서버에서 원격 데스크톱 연결을 통해 서버에 직접 연결 하는 원격 데스크톱 연결 파일을 수신 합니다.

    ![원격 데스크톱 연결을 통해 서버에 연결](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 나열 된 서버가 없거나 서비스 해제 된 경우 아래 나머지 단계를 무시 하 고 다음 서버로 건너뛸 수 있습니다.

1. 관리자 로그인 세부 정보를 입력 하 고 로그인 합니다.

1. 서버 또는 서버 프록시의 인터넷 액세스가 제한 된 경우 서버/프록시의 방화벽 설정이 사용 중인 Azure 클라우드에 해당 하는 URL을 허용 하도록 구성 되어 있는지 확인 합니다.

    Azure 클라우드 | URL
    --- | ---
    Azure 클라우드 (공용) |   `https://login.windows.net`
    Azure 중국 21Vianet 클라우드   | `https://login.chinacloudapi.cn`
    Azure US Government 클라우드 |   `https://login.microsoftonline.us`
    Azure German Cloud  |  `https://login.microsoftonline.de`

1. Azure Backup 에이전트 업데이트 설치 관리자를 서버에 복사 합니다.

    ![서버에 Azure Backup 에이전트 업데이트 설치 관리자 복사](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 설치 관리자를 실행합니다. Microsoft Azure Recovery Services 에이전트 업그레이드 마법사가 열립니다.

    ![Microsoft Azure Recovery Services 에이전트 업그레이드 마법사](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. **다음**을 선택합니다.

1. **업그레이드**를 선택 합니다.

    ![Microsoft Azure Recovery Services 에이전트 설치](./media/upgrade-mars-agent/upgrade-installation.png)

1. 최종 확인 화면은 Azure Backup 에이전트가 성공적으로 업데이트 되었음을 나타냅니다.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>System Center Data Protection Manager (SC DPM) 고객의 경우

SC DPM (System Center Data Protection Manager) 서버에 Azure Backup 에이전트를 설치한 경우 다음 단계를 수행 하 여 DPM 서버에 Azure Backup 에이전트 업데이트가 필요한 지 여부를 확인 해야 합니다.

1. SC DPM 서버에 관리자로 로그인 합니다.
2. DPM 콘솔을 엽니다.
3. 콘솔의 왼쪽 아래 탐색에서 **관리** 를 선택 합니다.
4. 왼쪽 탐색에 표시 되는 정보에서 Azure Backup 에이전트 버전 정보를 확인 합니다.
5. 버전이 2.0.9083.0 보다 낮은 경우 최신 Azure Backup 에이전트 설치 관리자를 다운로드 하 고 DPM 서버에서 설치 관리자를 실행 하 여 Azure Backup 에이전트를 업데이트 합니다.

사용자 환경의 모든 DPM 서버에 대해 위의 단계를 반복 합니다.

## <a name="next-steps"></a>다음 단계

[MARS 에이전트 Azure Backup를 사용 하 여 Windows 컴퓨터를 백업](backup-windows-with-mars-agent.md) 하는 방법을 알아봅니다.
