---
title: Azure Windows VM의 CMD 및 PowerShell | Microsoft Docs
description: Azure Windows VM의 SAC에서 CMD 및 PowerShell 명령을 사용하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167037"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows 명령 - CMD 및 PowerShell

이 섹션에는 RDP 연결 오류 문제를 해결해야 할 때와 같이 Windows VM에 액세스하는 데 SAC를 사용할 수 있는 시나리오에서 일반적인 작업 수행에 대한 명령의 예를 포함합니다.

SAC는 Windows Server 2003만 기본적으로 사용할 수 없게 된 이래 모든 버전의 Windows에 포함되었습니다. SAC는 `sacdrv.sys` 커널 드라이버, `Special Administration Console Helper` 서비스(`sacsvr`) 및 `sacsess.exe` 프로세스에 의존합니다. 자세한 내용은 [응급 관리 서비스 도구 및 설정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))을 참조합니다.

SAC는 직렬 포트를 통해 실행 중인 OS에 연결할 수 있습니다. SAC에서 CMD를 시작할 때 `sacsess.exe`은 실행 중인 OS 내에서 `cmd.exe`를 시작합니다. 작업 관리자에서 RDP가 VM에 연결된 경우 동시에 직렬 콘솔 기능을 통해 SAC에 연결돼 있음을 확인할 수 있습니다. RDP를 통해 연결된 경우 SAC를 통해 액세스하는 CMD는 사용하는 동일한 `cmd.exe`입니다. 해당 CMD 인스턴스에서 PowerShell을 시작하는 기능을 포함하여 모두 동일한 명령 및 도구를 사용할 수 있습니다. SAC와 WinRE(Windows 복구 환경) 사이의 주요 차이점은 WinRE가 다른 Minimal OS로 부팅하는 경우 SAC가 실행 중인 OS를 관리할 수 있게 한다는 것입니다. Azure VM은 직렬 콘솔 기능을 사용하여 WinRE에 액세스하는 기능을 지원하지 않지만 SAC를 통해 Azure VM을 관리할 수 있습니다.

SAC는 스크롤 백 기능이 없는 80x24 화면 버퍼로 제한되므로 명령에 `| more`을 추가해 한 번에 한 페이지씩 출력을 표시합니다. `<spacebar>`를 사용하여 다음 페이지를 보거나 `<enter>`를 사용하여 다음 줄을 봅니다.

`SHIFT+INSERT`은 직렬 콘솔 창에 대한 바로 가기 붙여넣기입니다.

SAC의 제한된 화면 버퍼 때문에 더 긴 명령은 로컬 텍스트 편집기에 입력한 다음, SAC에 붙여넣기가 더 쉬울 수 있습니다.

## <a name="view-and-edit-windows-registry-settings"></a>Windows 레지스트리 설정 보기 및 편집
### <a name="verify-rdp-is-enabled"></a>RDP를 사용하도록 설정했는지 확인
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

관련 그룹 정책 설정이 구성되는 경우 두 번째 키(under\Policies)만 존재하게 됩니다.

### <a name="enable-rdp"></a>RDP를 사용하도록 설정
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

관련 그룹 정책 설정이 구성된 경우 두 번째 키(under\Policies)만 필요하게 됩니다. 값이 그룹 정책에서 구성된 경우 다음 그룹 정책 새로 고침에서 다시 쓰여집니다.

## <a name="manage-windows-services"></a>Windows 서비스 관리

### <a name="view-service-state"></a>서비스 상태 보기
`sc query termservice`
###  <a name="view-service-logon-account"></a>서비스 로그온 계정 보기
`sc qc termservice`
### <a name="set-service-logon-account"></a>서비스 로그온 계정 설정
`sc config termservice obj= "NT Authority\NetworkService"`

등호 기호 뒤에 공백이 필요합니다.
### <a name="set-service-start-type"></a>서비스 시작 유형 설정
`sc config termservice start= demand`

등호 기호 뒤에 공백이 필요합니다. 가능한 시작 값은 `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`을 포함합니다.
### <a name="set-service-dependencies"></a>서비스 종속성 설정
`sc config termservice depend= RPCSS`

등호 기호 뒤에 공백이 필요합니다.
### <a name="start-service"></a>서비스 시작
`net start termservice`

또는

`sc start termservice`
### <a name="stop-service"></a>서비스 중지
`net stop termservice`

또는

`sc stop termservice`
## <a name="manage-networking-features"></a>네트워킹 기능 관리
### <a name="show-nic-properties"></a>NIC 속성 표시
`netsh interface show interface`
### <a name="show-ip-properties"></a>IP 속성 표시
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec 구성 표시
`netsh nap client show configuration`
### <a name="enable-nic"></a>NIC 사용
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>NIC가 DHCP를 사용하도록 설정
`netsh interface ip set address name="<interface name>" source=dhcp`

`netsh`에 대한 자세한 내용은 [여기를 클릭](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts)하세요.

Azure VM은 IP 주소를 얻는 데 DHCP를 사용하려면 항상 게스트 운영 체제에서 구성돼야 합니다. Azure 고정 IP 설정은 여전히 DHCP를 사용하여 고정 IP를 VM에 제공합니다.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>포트 ping
텔넷 클라이언트 설치

`dism /online /Enable-Feature /FeatureName:TelnetClient`

연결 테스트

`telnet bing.com 80`

텔넷 클라이언트를 제거하려면

`dism /online /Disable-Feature /FeatureName:TelnetClient`

기본적으로 Windows에서 사용할 수 있는 방법으로 제한된 경우 PowerShell은 포트 연결을 테스트하기 위한 더 좋은 방법이 될 수 있습니다. 예제는 아래 PowerShell 섹션을 참조하십시오.
### <a name="test-dns-name-resolution"></a>DNS 이름 확인 테스트
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows 방화벽 규칙 표시
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows 방화벽 사용 안 함
`netsh advfirewall set allprofiles state off`

Windows 방화벽을 일시적으로 제외하는 문제를 해결하려면 이 명령을 사용할 수 있습니다. 다음에 다시 시작할 때 사용 하도록 설정 하거나, 아래 명령을 사용 하 여 사용 하도록 설정 합니다. Windows 방화벽을 제외하기 위한 방법으로 Windows 방화벽 서비스(MPSSVC) 또는 기본 필터링 엔진(BFE) 서비스를 중지하지 마십시오. MPSSVC 또는 BFE 중지하면 모든 연결이 차단되게 됩니다.
### <a name="enable-windows-firewall"></a>Windows 방화벽 사용
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>사용자 및 그룹 관리
### <a name="create-local-user-account"></a>로컬 사용자 계정 만들기
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>로컬 그룹에 로컬 사용자 추가
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>사용자 계정이 사용하도록 설정되어 있는지 확인
`net user <username> | find /i "active"`

일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
### <a name="enable-user-account"></a>사용자 계정 사용
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>사용자 계정 속성 보기
`net user <username>`

로컬 관리자 계정에서 관심있는 예제 줄:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>로컬 그룹 보기
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Windows 이벤트 로그 관리
### <a name="query-event-log-errors"></a>이벤트 로그 오류 쿼리
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

`/c:10`을 반환하기 원하는 이벤트 수로 변경하거나 이를 이동하여 필터와 일치하는 모든 이벤트를 반환합니다.
### <a name="query-event-log-by-event-id"></a>이벤트 ID로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>이벤트 ID와 공급자로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>지난 24시간 동안 이벤트 ID와 공급자로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000`을 사용하여 24시간 대신 7일을 되돌아봅니다.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>지난 7 일 이내 이벤트 ID, 공급자 및 EventData로 이벤트 로그 쿼리
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>설치된 애플리케이션 보기 또는 제거
### <a name="list-installed-applications"></a>설치된 애플리케이션 나열
`wmic product get Name,InstallDate | sort /r | more`

`sort /r`은 설치 날짜를 기준으로 내림차순으로 정렬해 최근에 설치된 것을 쉽게 알 수 있습니다. `<spacebar>`를 사용하여 출력의 다음 페이지로 이동하거나 `<enter>`를 사용하여 한 줄씩 이동합니다.
### <a name="uninstall-an-application"></a>애플리케이션 제거
`wmic path win32_product where name="<name>" call uninstall`

제거하려는 애플리케이션에 대해 위의 명령에서 반환된 이름으로 `<name>`을 대체합니다.

## <a name="file-system-management"></a>파일 시스템 관리
### <a name="get-file-version"></a>파일 버전 가져오기
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

이 예제에서는 Windows 버전에 따라 netvsc.sys, netvsc63.sys 또는 netvsc60.sys인 가상 NIC 드라이버의 파일 버전을 반환합니다.
### <a name="scan-for-system-file-corruption"></a>시스템 파일 손상에 대한 검사
`sfc /scannow`

[Windows 이미지 복구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)도 참조합니다.
### <a name="scan-for-system-file-corruption"></a>시스템 파일 손상에 대한 검사
`dism /online /cleanup-image /scanhealth`

[Windows 이미지 복구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)도 참조합니다.
### <a name="export-file-permissions-to-text-file"></a>텍스트 파일에 파일 사용 권한 내보내기
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>ACL 파일에 파일 사용 권한 저장하기
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>ACL 파일에서 파일 사용 권한 복원하기
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

`/restore`을 사용할 때의 경로는 `/save`를 사용할 때 지정한 폴더의 상위 폴더여야 합니다. 이 예제에서 `\RSA`은 위의 예제 `\MachineKeys`에서 지정한 `/save` 폴더의 상위 폴더입니다.
### <a name="take-ntfs-ownership-of-a-folder"></a>폴더의 NTFS 소유권 획득
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>재귀적으로 폴더에 NTFS 소유권 부여하기
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>디바이스 관리
### <a name="remove-non-present-pnp-devices"></a>없는 PNP 디바이스 제거
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>그룹 정책 관리
### <a name="force-group-policy-update"></a>그룹 정책 업데이트 강제
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>기타 작업
### <a name="show-os-version"></a>OS 버전 표시
`ver`

또는

`wmic os get caption,version,buildnumber /format:list`

또는

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>OS 설치 날짜 보기
`systeminfo | find /i "original"`

또는

`wmic os get installdate`
### <a name="view-last-boot-time"></a>마지막 부팅 시간 보기
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>표준 시간대 보기
`systeminfo | find /i "time zone"`

또는

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows 다시 시작
`shutdown /r /t 0`

`/f`을 추가하면 사용자에게 경고 없이 실행 중인 애플리케이션을 강제로 닫습니다.
### <a name="detect-safe-mode-boot"></a>안전 모드 부팅 검색
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Windows 명령 - PowerShell

SAC에서 PowerShell을 실행하려면 CMD 프롬프트가 표시된 후 다음을 입력합니다.

`powershell <enter>`

> [!CAUTION]
> 다른 모든 PowerShell 명령을 실행하기 전에 PowerShell 세션에서 PSReadLine 모듈을 제거합니다. PSReadLine이 SAC의 PowerShell 세션에서 실행 중인 경우 클립보드에서 붙여넣은 텍스트에 추가 문자가 도입될 수 있는 알려진 문제가 있습니다.

먼저 PSReadLine이 로드되었는지 확인합니다. 기본적으로 Windows Server 2016, Windows 10 및 이후 버전의 Windows에서 로드됩니다. 수동으로 설치한 경우 이전 Windows 버전에만 존재합니다.

이 명령이 출력 없이 프롬프트로 반환되는 경우 모듈이 로드되지 않아 정상적으로 SAC에서 PowerShell 세션을 계속 사용할 수 있습니다.

`get-module psreadline`

위의 명령이 PSReadLine 모듈 버전을 반환하는 경우 해당 버전을 언로드하려면 다음 명령을 실행합니다. 이 명령은 모듈을 삭제하거나 제거하지 않고 현재 PowerShell 세션에서 해당 모듈을 언로드만 합니다.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows 레지스트리 설정 보기 및 편집
### <a name="verify-rdp-is-enabled"></a>RDP를 사용하도록 설정했는지 확인
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

관련 그룹 정책 설정이 구성되는 경우 두 번째 키(under\Policies)만 존재하게 됩니다.
### <a name="enable-rdp"></a>RDP를 사용하도록 설정
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

관련 그룹 정책 설정이 구성된 경우 두 번째 키(under\Policies)만 필요하게 됩니다. 값이 그룹 정책에서 구성된 경우 다음 그룹 정책 새로 고침에서 다시 쓰여집니다.
## <a name="manage-windows-services"></a>Windows 서비스 관리
### <a name="view-service-details"></a>서비스 정보 보기
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`은 사용할 수 있지만 서비스 로그온 계정을 포함하지는 않습니다. `Get-WmiObject win32-service`은 포함합니다.
### <a name="set-service-logon-account"></a>서비스 로그온 계정 설정
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

`NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` 또는 `LocalSystem` 이외의 서비스 계정을 사용하는 경우 계정 이름 뒤의 마지막(8) 인수로 계정 암호를 지정합니다.
### <a name="set-service-startup-type"></a>서비스 시작 유형 설정
`set-service termservice -startuptype Manual`

`Set-service`은 시작 유형으로 `Automatic`, `Manual` 또는 `Disabled`를 허용합니다.
### <a name="set-service-dependencies"></a>서비스 종속성 설정
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>서비스 시작
`start-service termservice`
### <a name="stop-service"></a>서비스 중지
`stop-service termservice`
## <a name="manage-networking-features"></a>네트워킹 기능 관리
### <a name="show-nic-properties"></a>NIC 속성 표시
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

또는

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있으며, 2008R2에 대해서는 `Get-WmiObject`를 사용합니다.
### <a name="show-ip-properties"></a>IP 속성 표시
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>NIC 사용
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

또는

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있으며, 2008R2에 대해서는 `Get-WmiObject`를 사용합니다.
### <a name="set-nic-to-use-dhcp"></a>NIC가 DHCP를 사용하도록 설정
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. Azure VM은 IP 주소를 얻는 데 DHCP를 사용하려면 항상 게스트 운영 체제에서 구성돼야 합니다. Azure 고정 IP 설정은 여전히 DHCP를 사용하여 IP를 VM에 제공합니다.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> 쓰기 진행률 cmdlet은이 명령에서 작동 하지 않을 수 있습니다. 완화를 위해 PowerShell에서 `$ProgressPreference = "SilentlyContinue"`를 실행 하 여 진행률 표시줄을 사용 하지 않도록 설정할 수 있습니다.

또는

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

매개 변수가 없는 `Test-Netconnection`은 `internetbeacon.msedge.net`를 ping하려 합니다. 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 두 번째 예제와 같이 `Get-WmiObject`을 사용합니다.
### <a name="port-ping"></a>포트 Ping
`test-netconnection -ComputerName bing.com -Port 80`

또는

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Net.Sockets.TcpClient` 사용
### <a name="test-dns-name-resolution"></a>DNS 이름 확인 테스트
`resolve-dnsname bing.com`

또는

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `System.Net.DNS`을 사용합니다.
### <a name="show-windows-firewall-rule-by-name"></a>이름으로 Windows 방화벽 규칙 표시
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>포트로 Windows 방화벽 규칙 표시
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

또는

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `hnetcfg.fwpolicy2` COM 개체를 사용합니다.
### <a name="disable-windows-firewall"></a>Windows 방화벽 사용 안 함
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 위의 CMD 섹션에서 참조한 대로 `netsh advfirewall`을 사용합니다.
## <a name="manage-users-and-groups"></a>사용자 및 그룹 관리
### <a name="create-local-user-account"></a>로컬 사용자 계정 만들기
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>사용자 계정이 사용하도록 설정되어 있는지 확인
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

또는

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. 이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 보여줍니다. 일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
### <a name="add-local-user-to-local-group"></a>로컬 그룹에 로컬 사용자 추가
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>로컬 사용자 계정 사용
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 사용할 수 있습니다. 일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
### <a name="view-user-account-properties"></a>사용자 계정 속성 보기
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

또는

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. 이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 보여줍니다.
### <a name="view-local-groups"></a>로컬 그룹 보기
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다.
## <a name="manage-the-windows-event-log"></a>Windows 이벤트 로그 관리
### <a name="query-event-log-errors"></a>이벤트 로그 오류 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

`/c:10`을 반환하기 원하는 이벤트 수로 변경하거나 이를 이동하여 필터와 일치하는 모든 이벤트를 반환합니다.
### <a name="query-event-log-by-event-id"></a>이벤트 ID로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>이벤트 ID와 공급자로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>지난 24시간 동안 이벤트 ID와 공급자로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000`을 사용하여 24시간 대신 7일을 되돌아봅니다. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>지난 7 일 이내 이벤트 ID, 공급자 및 EventData로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>설치된 애플리케이션 보기 또는 제거
### <a name="list-installed-software"></a>설치된 소프트웨어 나열하기
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>소프트웨어 제거
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>파일 시스템 관리
### <a name="get-file-version"></a>파일 버전 가져오기
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

이 예제에서는 Windows 버전에 따라 netvsc.sys, netvsc63.sys 또는 netvsc60.sys로 명명된 가상 NIC 드라이버의 파일 버전을 반환합니다.
### <a name="download-and-extract-file"></a>파일 다운로드 및 추출
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

이 예제에서는 `c:\bin` 폴더를 만든 다음, Sysinternals 도구 모음을 `c:\bin`로 다운로드하고 추출합니다.
## <a name="miscellaneous-tasks"></a>기타 작업
### <a name="show-os-version"></a>OS 버전 표시
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>OS 설치 날짜 보기
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>마지막 부팅 시간 보기
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows 작동 시간 보기
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

`<days>:<hours>:<minutes>:<seconds>:<milliseconds>`의 예를 들어 작동 시간을 `49:16:48:00.00`로 반환합니다.
### <a name="restart-windows"></a>Windows 다시 시작
`restart-computer`

`-force`을 추가하면 사용자에게 경고 없이 실행 중인 애플리케이션을 강제로 닫습니다.
## <a name="instance-metadata"></a>인스턴스 메타데이터

osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress 및 publicIpAddress 같은 세부 정보를 보려면 Azure VM 내에서 Azure 인스턴스 메타데이터를 쿼리할 수 있습니다.

인스턴스 메타데이터를 쿼리하려면 Azure 호스트를 통해 인스턴스 메타데이터 서비스로 REST 호출하기 때문에 정상 게스트 네트워크 연결이 필요합니다. 따라서 인스턴스 메타데이터를 쿼리할 수 있으면 게스트는 네트워크를 통해 Azure 호스팅 서비스와 통신할 수 있습니다.

자세한 내용은 [Azure Instance Metadata 서비스](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)를 참조하세요.

### <a name="instance-metadata"></a>인스턴스 메타데이터
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>OS 유형(인스턴스 메타데이터)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>위치(인스턴스 메타데이터)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>크기(인스턴스 메타데이터)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM ID(인스턴스 메타데이터)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM 이름(인스턴스 메타데이터)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>리소스 그룹 이름(인스턴스 메타데이터)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>구독 ID(인스턴스 메타데이터)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>태크(인스턴스 메타데이터)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>배치 그룹 ID(인스턴스 메타데이터)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>플랫폼 장애 도메인(인스턴스 메타데이터)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>플랫폼 업데이트 도메인(인스턴스 메타데이터)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 개인 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 공용 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 서브넷 주소 / 접두사(인스턴스 메타데이터)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC 주소(인스턴스 메타데이터)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>다음 단계
* 주 직렬 콘솔 Windows 설명서 페이지는 [여기](serial-console-windows.md)에 있습니다.
* 이 직렬 콘솔을 [Linux](serial-console-linux.md) VM에서도 사용할 수 있습니다.
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아봅니다.
