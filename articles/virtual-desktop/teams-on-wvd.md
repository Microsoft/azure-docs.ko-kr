---
title: Windows 가상 데스크톱의 Microsoft 팀-Azure
description: Windows 가상 데스크톱에서 Microsoft 팀을 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 07/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 049b962740abc98a6ac7d029c1419d40aa722165
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922568"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 Microsoft 팀 사용

>[!IMPORTANT]
>팀에 대 한 미디어 최적화는 GCC (Microsoft 365 정부) 환경에서 지원 됩니다. 팀에 대 한 미디어 최적화는 GCC High 또는 DoD에 대해 지원 되지 않습니다.

>[!NOTE]
>Microsoft 팀의 미디어 최적화는 windows 10 컴퓨터의 Windows 데스크톱 클라이언트에만 사용할 수 있습니다. 미디어 최적화를 위해서는 Windows 데스크톱 클라이언트 버전 1.2.1026.0 이상이 필요 합니다.

Windows 가상 데스크톱의 Microsoft 팀은 채팅 및 공동 작업을 지원 합니다. 미디어 최적화를 사용 하면 호출 및 모임 기능도 지원 합니다. VDI (가상 데스크톱 인프라) 환경에서 Microsoft 팀을 사용 하는 방법에 대해 자세히 알아보려면 [가상화 된 데스크톱 인프라 팀](/microsoftteams/teams-for-vdi/)을 참조 하세요.

Microsoft 팀을 위한 미디어 최적화를 사용 하는 Windows 데스크톱 클라이언트는 팀 호출 및 모임에 대해 로컬로 오디오 및 비디오를 처리 합니다. Windows 가상 데스크톱의 Microsoft 팀을 다른 클라이언트와 함께 사용할 수 있습니다. 팀 채팅 및 공동 작업 기능은 모든 플랫폼에서 지원 됩니다. 원격 세션에서 로컬 장치를 리디렉션하려면 [호스트 풀의 사용자 지정 원격 데스크톱 프로토콜 속성](#customize-remote-desktop-protocol-properties-for-a-host-pool)을 선택 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Windows 가상 데스크톱에서 Microsoft 팀을 사용 하려면 먼저 다음 작업을 수행 해야 합니다.

- Microsoft 팀을 위해 [네트워크를 준비](/microsoftteams/prepare-network/) 합니다.
- Windows [PC의 팀에 대 한 Microsoft 팀 하드웨어 요구 사항을](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)충족 하는 windows 10 또는 Windows 10 IoT Enterprise 장치에 [windows 데스크톱 클라이언트](connect-windows-7-10.md) 를 설치 합니다.
- Windows 10 다중 세션 또는 Windows 10 Enterprise VM (가상 머신)에 연결 합니다.
- 컴퓨터 단위 설치를 사용 하 여 호스트에 팀 데스크톱 앱을 [다운로드](https://www.microsoft.com/microsoft-365/microsoft-teams/download-app) 하 고 설치 합니다. Microsoft 팀을 위한 미디어 최적화를 사용 하려면 팀 데스크톱 앱 버전 1.3.00.4461 이상이 필요 합니다.

## <a name="install-the-teams-desktop-app"></a>팀 데스크톱 앱 설치

이 섹션에서는 Windows 10 다중 세션 또는 Windows 10 Enterprise VM 이미지에 팀 데스크톱 앱을 설치 하는 방법을 보여 줍니다. 자세히 알아보려면 [VDI에서 팀 데스크톱 앱 설치 또는 업데이트](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi)를 참조 하세요.

### <a name="prepare-your-image-for-teams"></a>팀을 위한 이미지 준비

팀에 미디어 최적화를 사용 하도록 설정 하려면 호스트에서 다음 레지스트리 키를 설정 합니다.

1. 시작 메뉴에서 관리자 권한으로 **RegEdit** 를 실행 합니다. **HKEY_LOCAL_MACHINE \software\microsoft\teams**로 이동 합니다.
2. 팀 키에 대해 다음 값을 만듭니다.

| 속성             | Type   | 데이터/값  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>팀 WebSocket 서비스 설치

VM 이미지에 최신 [WebSocket 서비스](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) 를 설치 합니다. 설치 오류가 발생 하는 경우 [최신 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) 를 설치 하 고 다시 시도 하세요.

#### <a name="latest-websocket-service-versions"></a>최신 WebSocket 서비스 버전

다음 표에서는 WebSocket 서비스의 최신 버전을 보여 줍니다.

|버전        |릴리스 날짜  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>버전 1.0.2006.11001 업데이트

- 전화 또는 모임 중에 팀 앱을 최소화 하 여 들어오는 비디오를 삭제 하는 문제를 해결 했습니다.
- 다중 모니터 데스크톱 세션에서 공유할 모니터를 하나 선택 하는 기능이 추가 되었습니다.

### <a name="install-microsoft-teams"></a>Microsoft 팀 설치

컴퓨터 단위 또는 사용자별 설치를 사용 하 여 팀 데스크톱 앱을 배포할 수 있습니다. Windows 가상 데스크톱 환경에 Microsoft 팀을 설치 하려면 다음을 수행 합니다.

1. 환경에 맞는 [팀 MSI 패키지](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) 를 다운로드 합니다. 64 비트 운영 체제에서 64 비트 설치 관리자를 사용 하는 것이 좋습니다.

      > [!IMPORTANT]
      > 팀 데스크톱 클라이언트 버전의 최신 업데이트 1.3.00.21759 팀이 채팅, 채널 및 일정에서 UTC 표준 시간대를 표시 하는 문제를 해결 했습니다. 새 버전의 클라이언트는 원격 세션 표준 시간대를 표시 합니다.

2. 다음 명령 중 하나를 실행 하 여 MSI를 호스트 VM에 설치 합니다.

    - 사용자 단위 설치

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        이 프로세스는 **% AppData%** user 폴더에 팀을 설치 하는 기본 설치입니다. 영구적이 지 않은 설치에서는 사용자 단위 설치 시 팀이 제대로 작동 하지 않습니다.

    - 컴퓨터별 설치

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        그러면 팀이 64 비트 운영 체제의 Program Files (x86) 폴더와 32 비트 운영 체제의 Program Files 폴더에 설치 됩니다. 이제 골든 이미지 설정이 완료 되었습니다. 비 영구적인 설치에는 컴퓨터별 팀을 설치 해야 합니다.

        팀을 설치할 때 설정할 수 있는 두 가지 플래그는 **Alluser = 1** 및 **ALLUSERS = 1**입니다. 이러한 매개 변수 간의 차이점을 이해 하는 것이 중요 합니다. **Alluser = 1** 매개 변수는 컴퓨터 단위 설치를 지정 하기 위해 VDI 환경 에서만 사용 됩니다. 비 VDI 및 VDI 환경에서 **ALLUSERS = 1** 매개 변수를 사용할 수 있습니다. 이 매개 변수를 설정 하면 팀 시스템 전체 설치 관리자가 제어판의 프로그램 및 기능 및 Windows 설정의 앱 & 기능에 나타납니다. 컴퓨터에서 관리자 자격 증명을 사용 하는 모든 사용자는 팀을 제거할 수 있습니다.

        > [!NOTE]
        > 사용자 및 관리자는 현재 로그인 하는 동안 팀에 대해 자동 실행을 사용 하지 않도록 설정할 수 없습니다.

3. 호스트 VM에서 MSI를 제거 하려면 다음 명령을 실행 합니다.

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      그러면 운영 체제 환경에 따라 Program Files (x86) 폴더 또는 Program Files 폴더에서 팀이 제거 됩니다.

      > [!NOTE]
      > MSI 설정 ALLUSER = 1을 사용 하 여 팀을 설치 하는 경우 자동 업데이트를 사용할 수 없습니다. 한 달에 한 번 이상 팀을 업데이트 하는 것이 좋습니다. 팀 데스크톱 앱을 배포 하는 방법에 대해 자세히 알아보려면 [팀 데스크톱 앱을 VM에 배포](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)를 확인 하세요.

### <a name="verify-media-optimizations-loaded"></a>미디어 최적화가 로드 되었는지 확인

WebSocket 서비스 및 팀 데스크톱 앱을 설치한 후 다음 단계에 따라 팀 미디어 최적화가 로드 되었는지 확인 합니다.

1. 사용자 프로필 이미지를 선택 하 고 **정보**를 선택 합니다.
2. **버전**을 선택 합니다.

      미디어 최적화가 로드 되 면이 배너에 **Wvd 미디어 최적화**됨이 표시 됩니다. 배너에 **Wvd 미디어가 연결 되지 않은**것으로 표시 되 면 팀 앱을 종료 하 고 다시 시도 하세요.

3. 사용자 프로필 이미지를 선택한 다음 **설정**을 선택 합니다.

      미디어 최적화가 로드 되 면 로컬에서 사용할 수 있는 오디오 장치와 카메라가 장치 메뉴에 열거 됩니다. 메뉴에 **원격 오디오가**표시 되 면 팀 앱을 종료 하 고 다시 시도 합니다. 장치가 메뉴에 여전히 표시 되지 않으면 로컬 PC의 개인 정보 설정을 확인 합니다. **설정**  >  **개인 정보**  >  **앱 사용 권한** 아래에서 **앱에 대 한 액세스 허용** 설정이 전환 되어 **On**있는지 확인 합니다. 원격 세션에서 연결을 끊은 다음 다시 연결 하 여 오디오 및 비디오 장치를 다시 확인 합니다. 통화 및 모임을 비디오와 조인 하려면 앱에서 카메라에 액세스할 수 있는 권한도 부여 해야 합니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

가상화 된 환경에서 팀을 사용 하는 것은 가상화 되지 않은 환경에서 팀을 사용 하는 것과 다릅니다. 가상화 된 환경에서 팀의 제한 사항에 대 한 자세한 내용은 [팀에서 가상화 된 데스크톱 인프라](/microsoftteams/teams-for-vdi#known-issues-and-limitations)를 확인 하세요.

### <a name="client-deployment-installation-and-setup"></a>클라이언트 배포, 설치 및 설정

- 컴퓨터별 설치를 사용 하는 경우 vdi의 팀은 비 VDI 팀 클라이언트의 경우와 동일한 방식으로 자동으로 업데이트 되지 않습니다. 클라이언트를 업데이트 하려면 새 MSI를 설치 하 여 VM 이미지를 업데이트 해야 합니다.
- 팀에 대 한 미디어 최적화는 Windows 10을 실행 하는 컴퓨터의 Windows 데스크톱 클라이언트에 대해서만 지원 됩니다.
- 끝점에 정의 된 명시적 HTTP 프록시의 사용은 지원 되지 않습니다.

### <a name="calls-and-meetings"></a>통화 및 모임

- Windows 가상 데스크톱 환경의 팀 데스크톱 클라이언트는 라이브 이벤트를 지원 하지 않습니다. 지금은 원격 세션의 [팀 웹 클라이언트](https://teams.microsoft.com) 에서 라이브 이벤트를 조인 하는 것이 좋습니다.
- 호출 또는 모임은 현재 응용 프로그램 공유를 지원 하지 않습니다. 데스크톱 세션은 데스크톱 공유를 지원 합니다.
- 컨트롤 제공 및 제어는 현재 지원 되지 않습니다.
- Windows 가상 데스크톱의 팀은 한 번에 하나의 수신 비디오 입력만 지원 합니다. 즉, 누군가가 자신의 화면을 공유 하려고 할 때마다 모임 리더의 화면 대신 화면에 표시 됩니다.
- WebRTC 제한으로 인해 들어오고 나가는 비디오 스트림 해상도는 720p로 제한 됩니다.
- 팀 앱은 HID 단추를 지원 하지 않거나 다른 장치와의 제어를 LED 합니다.

가상화 된 환경과 관련이 없는 알려진 문제 팀에 대해서는 [조직의 지원 팀](/microsoftteams/known-issues) 을 참조 하세요.

## <a name="uservoice-site"></a>UserVoice 사이트

팀 [UserVoice 사이트](https://microsoftteams.uservoice.com/)의 Windows 가상 데스크톱에서 Microsoft 팀에 대 한 피드백을 제공 합니다.

## <a name="collect-teams-logs"></a>팀 로그 수집

Windows 가상 데스크톱 환경에서 팀 데스크톱 앱에 문제가 발생 하는 경우 호스트 VM에서 **% appdata% \Microsoft\Teams\logs.txt** 아래에 있는 클라이언트 로그를 수집 합니다.

통화 및 모임에 문제가 발생 하는 경우 **Ctrl**  +  **Alt**  +  **Shift**  +  **1**키 조합을 사용 하 여 팀 웹 클라이언트 로그를 수집 합니다. 로그는 호스트 VM의 **%Userprofile%\Downloads\MSTeams 진단 로그 DATE_TIME.txt** 에 기록 됩니다.

## <a name="contact-microsoft-teams-support"></a>Microsoft 팀 지원에 문의

Microsoft 팀 지원에 문의 하려면 [관리 센터 Microsoft 365](/microsoft-365/admin/contact-support-for-business-products)로 이동 합니다.

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정

다중 모니터 환경 또는 마이크와 오디오 리디렉션을 사용 하도록 설정 하는 등의 RDP (호스트 풀 원격 데스크톱 프로토콜) 속성을 사용자 지정 하면 요구에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다.

미디어 최적화를 사용 하는 팀을 사용 하는 경우 장치 리디렉션을 사용 하지 않아도 됩니다. 미디어 최적화가 없는 팀을 사용 하는 경우 다음 RDP 속성을 설정 하 여 마이크 및 카메라 리디렉션을 사용 하도록 설정 합니다.

- `audiocapturemode:i:1` 로컬 장치에서 오디오 캡처를 사용 하도록 설정 하 고 원격 세션에서 오디오 응용 프로그램을 리디렉션합니다.
- `audiomode:i:0` 로컬 컴퓨터에서 오디오를 재생 합니다.
- `camerastoredirect:s:*` 모든 카메라를 리디렉션합니다.

자세히 알아보려면 [호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정](customize-rdp-properties.md)을 참조 하세요.
