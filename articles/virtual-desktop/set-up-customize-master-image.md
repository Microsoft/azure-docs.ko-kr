---
title: 준비 및 Azure 마스터 VHD 이미지를 사용자 지정
description: 준비 하 고 사용자 지정 Windows 가상 데스크톱 미리 보기 마스터 이미지를 Azure에 업로드 하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: aff96931f95442c67d08521e72952dd79dad44e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870282"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>마스터 VHD 이미지 준비 및 사용자 지정

이 문서에서는 가상 머신 (Vm) 만들기 및 설치 하 고 에서도 소프트웨어를 구성 하는 방법을 포함 하 여 Azure에 업로드할 마스터 가상 하드 디스크 (VHD) 이미지를 준비 하는 방법을 알려줍니다. 이러한 지침은 조직의 기존 프로세스를 사용 하 여 사용할 수 있는 Windows 가상 데스크톱 미리 보기 전용 구성입니다.

## <a name="create-a-vm"></a>VM 만들기

Windows 10 Enterprise 다중 세션 Azure 이미지 갤러리에서 제공 됩니다. 이 이미지를 사용자 지정 하기 위한 두 가지 옵션이 있습니다.

첫 번째 옵션은 가상 머신 (VM)를 프로 비전 할 Azure에서의 지침에 따라 [관리 되는 이미지에서 VM을 만듭니다](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), 및 다음 건너 뛰 세요 [소프트웨어 준비 및 설치](set-up-customize-master-image.md#software-preparation-and-installation)합니다.

두 번째 옵션은 이미지를 다운로드, Hyper-v VM을 프로 비전 및 다음 섹션에서 다루게 될 사용자 요구에 맞게 사용자 지정 하 여 로컬 이미지를 만드는 것입니다.

### <a name="local-image-creation"></a>로컬 이미지 만들기

이미지는 로컬 위치에 다운로드 한 후 엽니다 **Hyper-v 관리자** 방금 복사한 VHD를 사용 하 여 VM을 만들려고 합니다. 다음은 간단한 버전인 있지만의 자세한 지침을 찾을 수 있습니다 [Hyper-v에서 가상 컴퓨터를 만들](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)합니다.

복사 된 VHD를 사용 하 여 VM을 만들려면:

1. 엽니다는 **새 가상 머신 마법사**합니다.

2. 세대 지정 페이지에서 선택 **1 세대**합니다.

    ![세대 지정 페이지의 스크린샷입니다. "1 세대" 옵션을 선택 합니다.](media/a41174fd41302a181e46385e1e701975.png)

3. 검사점 형식에서 검사점을 사용 하지 않으려면 확인란의 선택을 취소 하 여 합니다.

    ![검사점 유형 섹션 검사점 페이지의 스크린샷.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

또한 검사점을 사용 하지 않도록 설정 하려면 PowerShell에서 다음 cmdlet을 실행할 수 있습니다.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>고정된 디스크

기존 VHD에서 VM을 만든 경우 기본적으로 동적 디스크를 만듭니다. 선택 하 여 고정된 된 디스크를 변경할 수 있습니다 **디스크 편집...**  다음 그림과에서 같이 합니다. 자세한 내용은 참조 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)합니다.

![디스크 편집 옵션의 스크린샷입니다.](media/35772414b5a0f81f06f54065561d1414.png)

또한 디스크를 고정된 디스크로 변경 하려면 다음 PowerShell cmdlet을 실행할 수 있습니다.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>소프트웨어 준비 및 설치

이 섹션에서는 준비 하 고 office 365 ProPlus, OneDrive, FSLogix, Windows Defender 및 기타 일반 응용 프로그램을 설치 하는 방법에 설명 합니다. 사용자를 특정 LOB 응용 프로그램에 액세스 해야 하는 경우이 섹션의이 지침을 완료 한 후 설치 하는 것이 좋습니다.

이 섹션에서는 Azure 또는 Hyper-v 관리자에서 프로 비전 될 지 여부를 하면 액세스 권한이 상승 된 VM에서 가정 합니다.

### <a name="install-office-in-shared-computer-activation-mode"></a>Office 공유 컴퓨터 정품 인증 모드에서 설치

사용 합니다 [Office 배포 도구](https://www.microsoft.com/download/details.aspx?id=49117) Office를 설치 합니다. Windows 10 Enterprise 다중 세션 지원 Office 365 ProPlus의 경우 Office 2019 연속 되지 않습니다.

Office 배포 도구 구성 XML 파일이 필요 합니다. 다음 샘플을 사용자 지정 하려면 참조는 [Office 배포 도구에 대 한 구성 옵션](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)합니다.

이 샘플 구성을 제공 하는 XML을 다음 작업을 수행 합니다.

- 내부 채널에서 Office를 설치 하 고 실행 하는 경우 내부 채널에서 업데이트를 제공 합니다.
- 사용 하 여 x64 아키텍처입니다.
- 자동 업데이트를 사용 하지 않도록 설정 합니다.
- Visio 및 프로젝트 설치 합니다.
- Office의 모든 기존 설치를 제거 하 고 해당 설정을 마이그레이션하십시오.
- 터미널 서버 환경에서 작업에 대 한 라이선스 공유 컴퓨터를 사용 하도록 설정 합니다.

않습니다 샘플 구성 XML이 다음과 같습니다.

- 비즈니스용 Skype를 설치 합니다.
- 사용자별 모드로 OneDrive를 설치 합니다. 자세한 내용은 참조 하세요 [컴퓨터별 모드로 설치 OneDrive](#install-onedrive-in-per-machine-mode)합니다.

>[!NOTE]
>공유 컴퓨터 라이선스 설정할 수 있습니다 그룹 정책 개체 (Gpo) 또는 레지스트리 설정을 통해. GPO에 위치한 **컴퓨터 구성\\정책을\\관리 템플릿\\Microsoft Office 2016 (컴퓨터)\\라이선스 설정**

Office 배포 도구는 setup.exe를 포함합니다. Office를 설치 하려면 명령줄에서 다음 명령을 실행 합니다.

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>샘플 configuration.xml

다음 XML 샘플 라고도 참가자 빠르거나 참가자 Main 참가자 릴리스를 설치 됩니다.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office 팀에 대 한 64 비트 설치를 사용 하도록 권장 합니다 **OfficeClientEdition** 매개 변수입니다.

Office를 설치한 후 기본 Office 동작을 업데이트할 수 있습니다. 개별적으로 또는 동작을 업데이트 하는 배치 파일에서 다음 명령을 실행 합니다.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

### <a name="disable-automatic-updates"></a>자동 업데이트 사용 안 함

로컬 그룹 정책을 통해 자동 업데이트를 사용 하지 않도록 설정 합니다.

1. 오픈 **로컬 그룹 정책 편집기\\관리 템플릿\\Windows 구성 요소\\Windows 업데이트**합니다.
2. 마우스 오른쪽 단추로 클릭 **자동 업데이트 구성** 로 설정 하 고 **비활성**합니다.

또한 자동 업데이트를 사용 하지 않도록 설정 하려면 명령 프롬프트에서 다음 명령을 실행할 수 있습니다.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>(선택 사항) Windows 10 Pc에 대 한 시작 레이아웃을 지정 합니다.

Windows 10 Pc에 대 한 시작 레이아웃을 지정 하려면이 명령을 실행 합니다.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>OneDrive 컴퓨터별 모드에서 설치

OneDrive는 사용자 단위로 정상적으로 설치 합니다. 이 환경에서는 것 컴퓨터별 설치 합니다.

OneDrive 컴퓨터별 모드로 설치 하는 방법을 다음과 같습니다.

1. 먼저 OneDrive 설치 관리자를 준비 하는 위치를 만듭니다. 로컬 디스크 폴더 또는 [\\\\unc] (file://unc) 위치 해도 괜찮습니다.

2. 이 링크를 사용 하 여 스테이징 위치로 OneDriveSetup.exe를 다운로드 합니다. <https://aka.ms/OneDriveWVD-Installer>

3. 생략 하 여 OneDrive를 사용 하 여 office를 설치 하는 경우  **\<ExcludeApp ID = "OneDrive" /\>**, 관리자 권한 명령 프롬프트에서 다음을 실행 하 여 설치 된 기존 OneDrive 사용자별 설치를 제거 명령:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 설정 하려면 관리자 권한 명령 프롬프트에서 다음이 명령을 실행 합니다 **AllUsersInstall** 레지스트리 값:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 컴퓨터별 모드로 OneDrive를 설치 하려면이 명령을 실행 합니다.

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 모든 사용자에 대 한 로그인을 시작 하는 OneDrive를 구성 하려면이 명령을 실행 합니다.

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 사용 하도록 설정 **사용자 계정을 자동으로 구성** 다음 명령을 실행 하 여 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 리디렉션 하 고 다음 명령을 실행 하 여 OneDrive로 폴더를 알려진 Windows를 이동 합니다.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>팀 및 Skype

Windows 가상 데스크톱 지원 하지 않습니다 공식적으로 Skype 비즈니스 및 팀.

### <a name="set-up-user-profile-container-fslogix"></a>사용자 프로필 컨테이너 (FSLogix) 설정

이미지의 일부로 FSLogix 컨테이너를 포함 하려면의 지침을 따릅니다 [호스트 풀에 대 한 사용자 프로필 공유 설정](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)합니다. 사용 하 여 FSLogix 컨테이너의 기능을 테스트할 수 있습니다 [이 빠른 시작](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start)합니다.

### <a name="configure-windows-defender"></a>Windows Defender를 구성 합니다.

Windows Defender를 VM에 구성 된 경우에서 구성 하지 스캔에 VHD 및 VHDX 파일의 전체 내용을 동일한 연결 하는 동안 있는지 확인 합니다.

이 구성만 첨부 파일 하는 동안 VHD 및 VHDX 파일의 검색을 제거 하지만 실시간 검사에 영향을 미치지 않습니다.

자세한 내용을 보려면 Windows Server에서 Windows Defender를 구성 하는 방법에 대 한 지침을 참조 하세요 [Windows Server에서 Windows Defender 바이러스 백신 구성 제외](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus)합니다.

Windows Defender 검사에서 특정 파일을 제외를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요 [구성 파일 확장명과 폴더 위치를 기반으로 하는 제외의 유효성을 검사 하 고](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)입니다.

### <a name="configure-session-timeout-policies"></a>세션 시간 제한 정책 구성

호스트 풀의 모든 Vm은 동일한 보안 그룹의 일부 이므로 원격 세션 정책 그룹 정책 수준에서 적용할 수 있습니다.

원격 세션 정책 구성:

1. 이동할 **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스**  >  **원격 데스크톱 세션 호스트** > **세션 시간 제한**합니다.
2. 오른쪽 패널에서 선택 합니다 **유휴 활성 원격 데스크톱 서비스 세션에 대 한 시간 제한 설정** 정책입니다.
3. 모달 창이 나타나면 변경에서 정책 옵션을 **구성 되어 있지** 에 **Enabled** 정책을 활성화 하려면.
4. 아래 정책 옵션을 사용 하 여 드롭다운 메뉴를 설정 하는 시간의 양 **4 시간**합니다.

또한 다음 명령을 실행 하 여 원격 세션 정책을 수동으로 구성할 수 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>표준 시간대 리디렉션 설정

호스트 풀의 모든 Vm은 동일한 보안 그룹의 일부 이므로 시간대 리디렉션 그룹 정책 수준에서 적용할 수 있습니다.

표준 시간대를 리디렉션하려면:

1. Active Directory 서버에서 엽니다는 **그룹 정책 관리 콘솔**합니다.
2. 사용자 도메인 및 그룹 정책 개체를 확장 합니다.
3. 마우스 오른쪽 단추로 클릭 합니다 **그룹 정책 개체** 선택한 그룹 정책 설정에 대해 만든 **편집**합니다.
4. 에 **그룹 정책 관리 편집기**, 이동할 **컴퓨터 구성** > **정책은** > **관리 템플릿을** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트**   >  **장치 및 리소스 리디렉션**합니다.
5. 사용 하도록 설정 합니다 **표준 시간대의 리디렉션 허용** 설정 합니다.

또한 표준 시간대를 리디렉션할 마스터 이미지에이 명령을 실행할 수 있습니다.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>저장 공간 센스를 사용 하지 않도록 설정

Windows 10 Enterprise 또는 Windows 10 Enterprise 다중 세션을 사용 하는 Windows 가상 데스크톱 세션 호스트에 대 한 저장 공간 센스를 사용 하지 않도록 설정 하는 것이 좋습니다. 설정 메뉴에서 저장 공간 센스를 비활성화할 수 있습니다 **저장소**다음 스크린샷에 표시 된 것 처럼:

![설정에서 저장소 메뉴의 스크린샷입니다. "저장 공간 센스" 옵션이 꺼져 있습니다.](media/storagesense.png)

또한 다음 명령을 실행 하 여 레지스트리를 사용 하 여 설정을 변경할 수 있습니다.

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>추가 언어 지원을 포함

이 문서에서는 언어 및 국가별 지원 구성 하는 방법을 다루지 않습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Windows 이미지에 언어 추가](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [주문형 기능](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [(해당 FOD) 주문형 기능을 언어 및 지역](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>다른 응용 프로그램 및 레지스트리 구성

이 섹션에서는 응용 프로그램 및 운영 체제 구성에 설명 합니다. 이 단원의 모든 구성은 명령줄에서 실행 될 수 있는 레지스트리 항목을 통해 수행 됩니다 및 regedit 도구입니다.

>[!NOTE]
>일반 정책 개체 (Gpo) 또는 레지스트리 가져오기 중 하나를 사용 하 여 구성에서 모범 사례를 구현할 수 있습니다. 관리자는 조직의 요구 사항에 따라 옵션 중 하나를 선택할 수 있습니다.

컬렉션에 대 한 피드백 허브 원격 분석 데이터를 Windows 10 Enterprise 다중 세션에서이 명령을 실행 합니다.

```batch
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Watson 충돌을 해결 하려면 다음 명령을 실행 합니다.

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

5 k 해상도 지원을 해결 하려면 레지스트리 편집기에 다음 명령을 입력 합니다. Side-by-side-스택 수 있도록 하려면 명령을 실행 해야 합니다.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Azure에 업로드할 이미지 준비

한 구성을 완료를 시작한 후 설치 된 모든 응용 프로그램의 지침을 따릅니다 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 이미지 준비를 합니다.

업로드에 대 한 이미지를 준비한 후 VM은 해제 또는 할당 취소 된 상태로 유지 해야 합니다.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure에서 저장소 계정에 마스터 이미지 업로드

이 섹션에서는 마스터 이미지를 로컬로 만들 때에 적용 됩니다.

다음 지침에서는 Azure storage 계정에 마스터 이미지를 업로드 하는 방법을 알려줍니다. Azure storage 계정에 아직 없는 경우의 지침을 따릅니다 [이 문서에서는](https://code.visualstudio.com/tutorials/static-website/create-storage) 만들어야 합니다.

1. 아직 없는 경우 VM 이미지 (VHD) Fixed로 변환 합니다. 이미지를 고정으로 변환 하지 이미지를 만들 수 없습니다.

2. 저장소 계정의 blob 컨테이너에 VHD를 업로드 합니다. 사용 하 여 신속 하 게 업로드할 수는 [Storage 탐색기 도구](https://azure.microsoft.com/features/storage-explorer/)합니다. Storage 탐색기 도구에 대 한 자세한 내용은 참조 하세요 [이 문서에서는](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)합니다.

    ![Microsoft Azure 저장소 탐색기 도구의 검색 창의 스크린샷. "(권장) 하는 페이지 blob으로.vhd 또는 vhdx 파일을 업로드 합니다." 확인란 선택 됩니다.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 다음으로, 브라우저 "이미지입니다."에 대 한 검색에서 Azure portal로 이동 검색 할 수 있어야 합니다 **이미지 만들기** 페이지에서 다음 스크린샷에 표시 된 대로:

    ![Azure portal의 만들기 이미지 페이지의 스크린샷 이미지에 대 한 예제 값으로 채워집니다.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 이미지를 만든 후 다음 스크린샷과에서 같은 알림이 표시 됩니다.

    !["성공적으로 만든된 이미지" 알림의 스크린샷.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>다음 단계

이미지를 만들었으므로 만들 수도 있고 호스트 풀 업데이트. 호스트 풀 만들기 및 업데이트 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.

- [Azure Resource Manager 템플릿을 사용 하 여 호스트 풀 만들기](create-host-pools-arm-template.md)
- [자습서: Azure Marketplace를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용 하 여 호스트 풀 만들기](create-host-pools-powershell.md)
- [호스트 풀에 대 한 사용자 프로필 공유 설정](create-host-pools-user-profile.md)
- [Windows 가상 데스크톱 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
