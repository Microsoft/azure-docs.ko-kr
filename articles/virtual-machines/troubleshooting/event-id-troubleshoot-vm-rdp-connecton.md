---
title: 이벤트 ID로 Azure VM RDP 연결 문제 해결 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485508"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>이벤트 ID로 Azure VM RDP 연결 문제 해결 

이 문서에서는 이벤트 ID를 사용하여 Azure VM(Virtual Machine)에 대한 RDP(원격 데스크톱 프로토콜) 연결을 방해하는 문제를 해결하는 방법에 대해 설명합니다.

## <a name="symptoms"></a>증상

RDP(원격 데스크톱 프로토콜) 세션을 사용하여 Azure VM에 연결하려고 합니다. 자격 증명을 입력하면 연결이 실패하고 다음과 같은 오류 메시지가 표시됩니다.

**이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다. 다시 연결해 보세요. 문제가 계속되면 원격 컴퓨터의 소유자 또는 네트워크 관리자에게 문의하세요.**

이 문제를 해결하려면 VM의 이벤트 로그를 검토한 다음, 다음 시나리오를 참조하세요.

## <a name="before-you-troubleshoot"></a>해결하기 전에

### <a name="create-a-backup-snapshot"></a>백업 스냅숏 만들기

백업 스냅숏을 만들려면 [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)의 단계를 수행합니다.

### <a name="connect-to-the-vm-remotely"></a>원격으로 VM에 연결

원격으로 VM에 연결하려면 [원격 도구를 사용하여 Azure VM 문제를 해결하는 방법](remote-tools-troubleshoot-azure-vm-issues.md) 중 하나를 사용합니다.

## <a name="scenario-1"></a>시나리오 1

### <a name="event-logs"></a>이벤트 로그

CMD 인스턴스에서 다음 명령을 실행하여 1058 이벤트 또는 1057 이벤트가 지난 24시간 내에 System 로그에 기록되었는지 확인합니다.

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**로그 이름:**      시스템 <br />
**원본:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**날짜:** *time* <br />
**이벤트 ID:**      1058 <br />
**작업 범주:** 없음 <br />
**수준:**         오류 <br />
**키워드:**      클래식 <br />
**사용자:**          N/A <br />
**컴퓨터:** *컴퓨터* <br />
**설명:** RD 세션 호스트 서버가 SSL 연결에서 RD 세션 호스트 서버 인증에 사용할 자체 서명된 만료 인증서를 바꾸지 못했습니다. 관련 상태 코드는 '액세스가 거부되었습니다.'입니다.

**로그 이름:**      시스템 <br />
**원본:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**날짜:** *time* <br />
**이벤트 ID:**      1058 <br />
**작업 범주:** 없음 <br />
**수준:**         오류 <br />
**키워드:**      클래식 <br />
**사용자:**          N/A <br />
**컴퓨터:** *컴퓨터* <br />
**설명:** RD 세션 호스트 서버가 SSL 연결에서 RD 세션 호스트 서버 인증에 사용할 새 자체 서명된 인증서를 만들지 못했습니다. 관련 상태 코드는 ‘개체가 이미 있습니다’였습니다.

**로그 이름:**      시스템 <br />
**원본:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**날짜:** *time* <br />
**이벤트 ID:**      1057 <br />
**작업 범주:** 없음 <br />
**수준:**         오류 <br />
**키워드:**      클래식 <br />
**사용자:**          N/A <br />
**컴퓨터:** *컴퓨터* <br />
**설명:** RD 세션 호스트 서버가 SSL 연결에서 RD 세션 호스트 서버 인증에 사용할 새 자체 서명된 인증서를 만들지 못했습니다. 관련 상태 코드는 '키 집합이 없습니다.'입니다.

다음 명령을 실행하여 36872 및 36870 SCHANNEL 오류 이벤트를 확인할 수도 있습니다.

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**로그 이름:**      시스템 <br />
**원본:**        SChannel <br />
**날짜:** — <br />
**이벤트 ID:**      36870 <br />
**작업 범주:** 없음 <br />
**수준:**         오류 <br />
**키워드:**       <br />
**사용자:**          SYSTEM <br />
**컴퓨터:** *컴퓨터* <br />
**설명:** SSL 서버 자격 증명 개인 키에 액세스하려 할 때 심각한 오류가 발생했습니다. 암호화 모듈에서 반환된 오류 코드는 0x8009030D입니다.  <br />
내부 오류 상태는 10001입니다.

### <a name="cause"></a>원인
이 문제는 VM의 MachineKeys 폴더에 있는 로컬 RSA 암호화 키에 액세스할 수 없기 때문에 발생합니다. 이 문제는 다음 이유 중 하나로 인해 발생할 수 있습니다.

1. Machinekeys 폴더 또는 RSA 파일의 권한 구성이 잘못되었습니다.

2. RSA 키가 손상되었거나 누락되었습니다.

### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 단계를 사용하여 RDP 인증서에 대한 권한을 올바르게 설정해야 합니다.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>MachineKeys 폴더에 권한 부여

1. 다음 내용을 사용하여 스크립트를 만듭니다.

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  이 스크립트를 실행하여 MachineKey 폴더의 권한을 다시 설정하고 RSA 파일을 기본값으로 다시 설정합니다.

3.  VM에 대한 액세스를 다시 시도합니다.

스크립트가 실행되면 다음 파일에서 권한 문제가 있는지 확인할 수 있습니다.

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>자체 서명된 RDP 인증서 갱신

문제가 계속되면 다음 스크립트를 실행하여 자체 서명된 RDP 인증서가 갱신되었는지 확인합니다.

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

인증서를 갱신할 수 없으면 다음 단계에 따라 해당 인증서를 삭제합니다.

1. 동일한 VNET의 다른 VM에서 **실행** 상자를 열고, **mmc**를 입력한 다음, **확인**을 누릅니다. 

2. **파일** 메뉴에서 **스냅인 추가/제거**를 선택합니다.

3. **사용 가능한 스냅인** 목록에서 **인증서**를 선택한 다음, **추가**를 선택합니다.

4. **컴퓨터 계정**을 선택하고, **다음**을 선택합니다.

5. **다른 컴퓨터**를 선택한 다음, 문제가 있는 VM의 IP 주소를 추가합니다.
   >[!Note]
   >가상 IP 주소를 사용하지 않도록 내부 네트워크를 사용해 보세요.

6. **마침**을 선택한 다음, **확인**을 선택합니다.

   ![컴퓨터 선택](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. 인증서를 펼치고, Remote Desktop\Certificates 폴더로 이동하고, 해당 인증서를 마우스 오른쪽 단추로 클릭한 다음, **삭제**를 선택합니다.

8. 원격 데스크톱 구성 서비스를 다시 시작합니다.

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >이 시점에서 mmc에서 저장소를 새로 고치면 인증서가 다시 나타납니다. 

RDP를 사용하여 VM에 대한 액세스를 다시 시도합니다.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>SSL(Secure Sockets Layer) 인증서 업데이트

SSL 인증서를 사용하도록 VM을 설정하는 경우 다음 명령을 실행하여 지문을 가져옵니다. 그런 다음, 인증서의 지문과 동일한지 확인합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

그렇지 않은 경우 지문을 변경합니다.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

또한 RDP에서 RDP에 대해 자체 서명된 인증서를 사용하도록 키를 삭제할 수도 있습니다.

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>시나리오 2

### <a name="event-log"></a>이벤트 로그

CMD 인스턴스에서 다음 명령을 실행하여 36871 SCHANNEL 오류 이벤트가 지난 24시간 이내에 System 로그에 기록되었는지 확인합니다.

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**로그 이름:**      시스템 <br />
**원본:**        SChannel <br />
**날짜:** — <br />
**이벤트 ID:**      36871 <br />
**작업 범주:** 없음 <br />
**수준:**         오류 <br />
**키워드:**       <br />
**사용자:**          SYSTEM <br />
**컴퓨터:** *컴퓨터* <br />
**설명:** TLS 서버 자격 증명을 만드는 동안 심각한 오류가 발생했습니다. 내부 오류 상태는 10013입니다.
 
### <a name="cause"></a>원인

이 문제는 보안 정책으로 인해 발생합니다. 이전 버전의 TLS(예: 1.0)를 사용하지 않도록 설정하면 RDP 액세스가 실패합니다.

### <a name="resolution"></a>해결 방법

RDP는 TLS 1.0을 기본 프로토콜로 사용합니다. 그러나 이 프로토콜은 새 표준인 TLS 1.1로 변경될 수 있습니다.

이 문제를 해결하려면 [RDP를 사용한 Azure VM 연결 시의 인증 오류 문제 해결](troubleshoot-authentication-error-rdp-vm.md#tls-version)을 참조하세요.

## <a name="scenario-3"></a>시나리오 3

VM에 **원격 데스크톱 연결 브로커** 역할을 설치한 경우 2056 이벤트 또는 1296 이벤트가 지난 24시간 내에 있었는지 확인합니다. CMD 인스턴스에서 다음 명령을 실행합니다. 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**로그 이름:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**원본:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**날짜:** *time* <br />
**이벤트 ID:**      2056 <br />
**작업 범주:** (109) <br />
**수준:**         오류 <br />
**키워드:**       <br />
**사용자:**          NETWORK SERVICE <br />
**컴퓨터:** *computer fqdn* <br />
**설명:** Microsoft-Windows-TerminalServices-SessionBroker 원본에서 2056 이벤트 ID에 대한 설명을 찾을 수 없습니다. 이 이벤트가 발생되는 구성 요소가 로컬 컴퓨터에 설치되지 않았거나 설치가 손상되었습니다. 로컬 컴퓨터에 구성 요소를 설치하거나 복구할 수 있습니다. <br />
이벤트가 다른 컴퓨터에서 시작된 경우 표시 정보는 이벤트와 함께 저장되었어야 합니다. <br />
이벤트에 포함된 정보는 다음과 같습니다. <br />
NULL <br />
NULL <br />
데이터베이스에 로그온하지 못했습니다.

**로그 이름:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**원본:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**날짜:** *time* <br />
**이벤트 ID:**      1296 <br />
**작업 범주:** (104) <br />
**수준:**         오류 <br />
**키워드:**       <br />
**사용자:**          NETWORK SERVICE <br />
**컴퓨터:** *computer fqdn* <br />
**설명:** Microsoft-Windows-TerminalServices-SessionBroker-Client 원본에서 1296 이벤트 ID에 대한 설명을 찾을 수 없습니다. 이 이벤트가 발생되는 구성 요소가 로컬 컴퓨터에 설치되지 않았거나 설치가 손상되었습니다. 로컬 컴퓨터에 구성 요소를 설치하거나 복구할 수 있습니다.
이벤트가 다른 컴퓨터에서 시작된 경우 표시 정보는 이벤트와 함께 저장되었어야 합니다.
이벤트에 포함된 정보는 다음과 같습니다.  <br />
*text* <br />
*text* <br />
원격 데스크톱 연결 브로커에서 RPC 통신을 수행할 준비가 되지 않았습니다.

### <a name="cause"></a>원인

이 문제는 원격 데스크톱 연결 브로커 서버의 호스트 이름이 변경되었지만 이 변경은 지원되지 않기 때문에 발생합니다. 

호스트 이름에는 원격 데스크톱 서비스 팜에서 작업하는 데 필요한 Windows 내부 데이터베이스에 대한 항목과 종속성이 있습니다. 팜이 이미 구축된 후에 호스트 이름을 변경하면 많은 오류가 발생하여 브로커 서버의 작동이 중지될 수 있습니다.

### <a name="resolution"></a>해결 방법 

이 문제를 해결하려면 원격 데스크톱 연결 브로커 역할 및 Windows 내부 데이터베이스를 다시 설치해야 합니다.

## <a name="next-steps"></a>다음 단계

[Schannel 이벤트](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP 기술 개요](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP가 원격 데스크톱 세션 호스트 인증서 및 SSL 통신을 사용하는 1058 이벤트 ID 및 36870 이벤트에서 실패함](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[도메인 컨트롤러의 36872 Schannel 또는 36870 Schannel](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[1058 이벤트 ID - 원격 데스크톱 서비스 인증 및 암호화](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

