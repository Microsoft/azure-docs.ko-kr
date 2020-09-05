---
title: Azure Virtual Machines에 RDP로 연결할 때 내부 오류 발생 | Microsoft Docs
description: Microsoft Azure에서 RDP 내부 오류를 해결하는 방법을 알아봅니다. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 299bbfa31584b260f85dfa7bafddea268084f876
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235165"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>원격 데스크톱을 통해 Azure VM에 연결하려고 할 때 내부 오류 발생

이 문서에서는 Microsoft Azure에서 VM(가상 머신)에 연결하려고 할 때 발생할 수 있는 오류에 대해 설명합니다.


## <a name="symptoms"></a>증상

RDP(원격 데스크톱 프로토콜)를 사용하여 Azure VM에 연결할 수 없습니다. "원격 구성" 섹션에서 연결이 중지되거나 다음과 같은 오류 메시지가 표시됩니다.

- RDP 내부 오류
- 내부 오류가 발생했습니다.
- 이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다. 다시 연결해 보세요. 문제가 계속되면 원격 컴퓨터의 소유자 또는 네트워크 관리자에게 문의하세요.


## <a name="cause"></a>원인

이 문제는 다음과 같은 이유로 발생할 수 있습니다.

- 로컬 RSA 암호화 키에 액세스할 수 없습니다.
- TLS 프로토콜이 사용되지 않도록 설정되어 있습니다.
- 인증서가 손상되었거나 만료되었습니다.

## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

이 문제를 해결하려면 직렬 콘솔을 사용하거나 VM의 OS 디스크를 복구 VM에 연결하여 [오프라인으로 VM을 복구](#repair-the-vm-offline)합니다.


### <a name="use-serial-control"></a>직렬 콘솔 사용

[직렬 콘솔에 연결하고 PowerShell 인스턴스를 엽니다](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). VM에서 직렬 콘솔을 사용하지 않도록 설정한 경우 [오프라인으로 VM 복구](#repair-the-vm-offline) 섹션으로 이동합니다.

#### <a name="step-1-check-the-rdp-port"></a>1단계: RDP 포트 확인

1. PowerShell 인스턴스에서 [NETSTAT](/windows-server/administration/windows-commands/netstat) 를 사용 하 여 다른 응용 프로그램에서 포트 3389을 사용 하는지 여부를 확인 합니다.

    ```powershell
    Netstat -anob |more
    ```

2. Termservice.exe에서 3389 포트를 사용 하는 경우 2 단계로 이동 합니다. Termservice.exe 이외의 다른 서비스나 응용 프로그램에서 3389 포트를 사용 하는 경우 다음 단계를 수행 합니다.

    1. 3389 서비스를 사용하는 애플리케이션에 대한 서비스를 중지합니다.

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. 터미널 서비스를 시작합니다.

        ```powershell
        Start-Service -Name Termservice
        ```

2. 애플리케이션을 중지할 수 없거나 이 방법이 사용자에게 적용되지 않으면 RDP에 대한 포트를 변경합니다.

    1. 포트를 변경합니다.

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. 새 포트에 대한 방화벽을 설정합니다.

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. Azure Portal RDP 포트에서 [새 포트에 대한 네트워크 보안 그룹을 업데이트](../../virtual-network/security-overview.md)합니다.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>2단계: RDP 자체 서명된 인증서에 대해 올바른 권한 설정

1. PowerShell 인스턴스에서 다음 명령을 하나씩 실행하여 RDP 자체 서명된 인증서를 갱신합니다.

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. 이 방법으로 인증서를 갱신할 수 없으면 RDP 자체 서명된 인증서를 원격으로 갱신합니다.

    1. 작동하는 VM이 문제가 있는 VM에 연결되어 있으면 **실행** 상자에서 **mmc**를 입력하여 Microsoft Management Console을 엽니다.
    2. **파일** 메뉴에서 **스냅인 추가/제거**를 선택하고 **인증서**를 선택한 후 **추가**를 선택합니다.
    3. **컴퓨터 계정**을 선택하고 **다른 컴퓨터**를 선택한 후 문제가 있는 VM의 IP 주소를 추가합니다.
    4. **원격 데스크톱\인증서** 폴더로 이동하고 인증서를 마우스 오른쪽 단추로 클릭한 후 **삭제**를 선택합니다.
    5. 직렬 콘솔의 PowerShell 인스턴스에서 원격 데스크톱 구성 서비스를 다시 시작합니다.

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. MachineKeys 폴더에 대한 권한을 다시 설정합니다.

    ```powershell
    remove-module psreadline icacls

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. VM을 다시 시작한 후 VM에 대한 원격 데스크톱 연결을 시작합니다. 오류가 여전히 발생하는 경우 다음 단계로 이동합니다.

#### <a name="step-3-enable-all-supported-tls-versions"></a>3단계: 지원되는 모든 TLS 버전 사용

RDP 클라이언트는 TLS 1.0을 기본 프로토콜로 사용합니다. 그러나 이 프로토콜을 TLS 1.1로 변경하여 새로운 표준으로 지정할 수 있습니다. VM에서 TLS 1.1을 사용하지 않도록 설정하면 연결이 실패합니다.

1. CMD 인스턴스에서 TLS 프로토콜을 사용하도록 설정합니다.

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. AD 정책이 변경 내용을 덮어쓰지 않게 하려면 그룹 정책 업데이트를 일시적으로 중지합니다.

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. 변경 내용이 적용되도록 VM을 다시 시작합니다. 문제가 해결되면 다음 명령을 실행하여 그룹 정책을 다시 사용하도록 설정합니다.

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    변경 내용이 되돌려지면 회사 도메인에 Active Directory 정책이 있는 것입니다. 문제가 재발되지 않도록 해당 정책을 변경해야 합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. [OS 디스크를 복구 VM에 연결](./troubleshoot-recovery-disks-portal-windows.md)합니다.
2. OS 디스크가 복구 VM에 연결되면 디스크 관리 콘솔에서 디스크의 플래그가 **온라인**으로 지정되어 있는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.
3. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

#### <a name="enable-dump-log-and-serial-console"></a>덤프 로그 및 직렬 콘솔을 사용하도록 설정

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션 (**관리자 권한으로 실행**)을 엽니다.
2. 다음 스크립트를 실행합니다.

    이 스크립트에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>MachineKeys 폴더에 대한 권한 다시 설정

1. 관리자 권한 명령 프롬프트 세션 (**관리자 권한으로 실행**)을 엽니다.
2. 다음 스크립트를 실행합니다. 이 스크립트에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>지원되는 모든 TLS 버전 사용

1. 관리자 권한 명령 프롬프트 세션(**관리자 권한으로 실행**)을 열고 다음 명령을 실행합니다. 다음 스크립트는 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.
2. TLS가 사용하도록 설정되었는지를 확인합니다.

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. 키가 없거나 해당 값이 **0**이면 다음 스크립트를 실행하여 프로토콜을 사용하도록 설정합니다.

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. NLA 사용:

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. [OS 디스크를 분리하고 VM을 다시 만든 후](./troubleshoot-recovery-disks-portal-windows.md) 문제가 해결되었는지 확인합니다.
