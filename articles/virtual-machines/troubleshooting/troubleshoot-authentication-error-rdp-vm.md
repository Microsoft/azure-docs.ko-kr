---
title: RDP를 사용한 Azure VM 연결 시의 인증 오류 문제 해결 | Microsoft Docs
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
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594932"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>RDP를 사용한 Azure VM 연결 시의 인증 오류 문제 해결

이 문서는 RDP(원격 데스크톱 프로토콜) 연결을 사용하여 Azure 가상 머신(VM)에 연결할 때 발생하는 인증 오류 문제를 해결하는 데 도움이 됩니다.

## <a name="symptoms"></a>증상

시작 화면을 표시하고 운영 체제가 실행 중임을 나타내는 Azure VM의 스크린샷을 캡처합니다. 그러나 원격 데스크톱 연결을 사용하여 VM에 연결하려고 하면 다음 오류 메시지 중 하나가 표시됩니다.

### <a name="error-message-1"></a>오류 메시지 1

**인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.**

### <a name="error-message-2"></a>오류 메시지 2

**연결하려는 원격 컴퓨터에 NLA(네트워크 수준 인증)이 필요하지만, Windows 도메인 컨트롤러에 연결하여 NLA를 수행할 수가 없습니다. 원격 컴퓨터의 관리자인 경우 [시스템 속성] 대화 상자의 [원격] 탭에 있는 옵션을 사용하여 NLA를 사용하지 않도록 설정할 수 있습니다.**

### <a name="error-message-3-generic-connection-error"></a>오류 메시지 3(일반 연결 오류)

**이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다. 다시 연결해 보세요. 문제가 계속되면 원격 컴퓨터의 소유자 또는 네트워크 관리자에게 문의하세요.**

## <a name="cause"></a>원인

NLA에서 VM에 대한 RDP 액세스를 차단할 수 있는 이유는 여러 가지가 있습니다.

### <a name="cause-1"></a>원인 1

VM에서 DC(도메인 컨트롤러)와 통신할 수 없습니다. 이 문제로 인해 도메인 자격 증명을 사용하여 RDP 세션에서 VM에 액세스하지 못할 수 있습니다. 그러나 로컬 관리자 자격 증명을 사용하여 계속 로그온할 수 있습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

1. 이 VM과 DC 간의 Active Directory 보안 채널이 손상되었습니다.

2. VM에는 계정 암호의 이전 복사본이 있고, DC에는 최신 복사본이 있습니다.

3. 이 VM에서 연결하는 DC가 정상 상태가 아닙니다.

### <a name="cause-2"></a>원인 2

VM의 암호화 수준이 클라이언트 컴퓨터에서 사용하는 것보다 더 높습니다.

### <a name="cause-3"></a>원인 3

TLS 1.0, 1.1 또는 1.2(서버) 프로토콜은 VM에서 사용하지 않도록 설정되어 있습니다.

### <a name="cause-4"></a>원인 4

VM에서 도메인 자격 증명을 사용하여 로그온하지 않도록 설정되었으며 LSA(로컬 보안 기관)가 잘못 설정되었습니다.

### <a name="cause-5"></a>원인 5

VM에서 FIPS(Federal Information Processing Standard) 호환 알고리즘 연결만 허용하도록 설정되었습니다. 이 작업은 일반적으로 Active Directory 정책을 사용하여 수행됩니다. 이는 드문 구성이지만 FIPS는 원격 데스크톱 연결에만 적용할 수 있습니다.

## <a name="before-you-troubleshoot"></a>해결하기 전에

### <a name="create-a-backup-snapshot"></a>백업 스냅숏 만들기

백업 스냅숏을 만들려면 [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)의 단계를 수행합니다.

### <a name="connect-to-the-vm-remotely"></a>원격으로 VM에 연결

원격으로 VM에 연결하려면 [원격 도구를 사용하여 Azure VM 문제를 해결하는 방법](remote-tools-troubleshoot-azure-vm-issues.md) 중 하나를 사용합니다.

### <a name="group-policy-client-service"></a>그룹 정책 클라이언트 서비스

도메인에 조인된 VM인 경우 먼저 Active Directory 정책에서 변경 내용을 덮어쓰지 않도록 그룹 정책 클라이언트 서비스를 중지합니다. 이렇게 하려면 다음 명령을 실행합니다.

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

문제가 해결되면 이 VM에서 도메인에 연결하여 도메인의 최신 GPO를 검색하는 기능을 복원합니다. 이렇게 하려면 다음 명령을 실행합니다.

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

변경 내용이 되돌려지면 Active Directory 정책으로 인해 문제가 발생한 것입니다. 

### <a name="workaround"></a>해결 방법

이 문제를 해결하려면 명령 창에서 다음 명령을 실행하여 NLA를 사용하지 않도록 설정합니다.

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

그런 다음, VM을 다시 시작합니다.

NLA를 다시 사용하도록 설정하려면 다음 명령을 실행한 다음, VM을 다시 시작합니다.

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>문제 해결

### <a name="for-domain-joined-vms"></a>도메인에 조인된 VM의 경우

이 문제를 해결하려면 먼저 VM에서 DC에 연결할 수 있는지와 DC의 상태가 "정상"이고 VM의 요청을 처리할 수 있는지를 확인합니다.

>[!Note] 
>DC 상태를 테스트하려면 동일한 로그온 서버를 공유하는 동일한 VNET 및 서브넷에서 다른 VM을 사용할 수 있습니다.

"원격으로 VM에 연결" 섹션의 단계에 따라 직렬 콘솔, 원격 CMD 또는 원격 PowerShell을 사용하여 문제가 있는 VM에 연결합니다.

VM에서 연결하는 DC를 확인하려면 콘솔에서 다음 명령을 실행합니다. 

```cmd
set | find /i "LOGONSERVER"
```

그런 다음, VM과 DC 간의 보안 채널 상태를 확인합니다. 이렇게 하려면 관리자 권한으로 실행된 PowerShell 인스턴스에서 다음 명령을 실행합니다. 이 명령은 보안 채널이 활성 상태인지 여부를 나타내는 부울 플래그를 반환합니다.

```powershell
Test-ComputerSecureChannel -verbose
```

채널이 손상된 경우 다음 명령을 실행하여 복구합니다.

```powershell
Test-ComputerSecureChannel -repair
```

Active Directory의 컴퓨터 계정 암호가 VM과 DC에서 업데이트되었는지 확인합니다.

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

DC와 VM 간의 통신이 양호하지만 DC가 RDP 세션을 열 수 있을 만큼 정상 상태가 아닌 경우 DC를 다시 시작해 볼 수 있습니다.

위의 명령으로 도메인에 대한 통신 문제가 해결되지 않으면 이 VM을 도메인에 다시 조인할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. 다음 내용을 사용하여 Unjoin.ps1이라는 스크립트를 만든 다음, 이 스크립트를 사용자 지정 스크립트 확장으로 Azure Portal에 배포합니다.

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    이 스크립트는 도메인에서 VM을 강제로 가져와서 10초 후에 다시 시작합니다. 그런 다음, 도메인 쪽의 Computer 개체를 정리해야 합니다.

2.  정리가 완료되면 이 VM을 도메인에 다시 조인합니다. 이렇게 하려면 다음 내용을 사용하여 JoinDomain.ps1이라는 스크립트를 만든 다음, 이 스크립트를 사용자 지정 스크립트 확장으로 Azure Portal에 배포합니다. 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >이렇게 하면 지정된 자격 증명을 사용하여 VM이 도메인에 조인됩니다.

Active Directory 채널이 정상 상태이고 컴퓨터 암호가 업데이트되고 도메인 컨트롤러가 예상대로 작동하는 경우 다음 단계를 시도합니다.

문제가 계속되면 도메인 자격 증명을 사용하지 않도록 설정되어 있는지 확인합니다. 이렇게 하려면 관리자 권한으로 실행된 [명령 프롬프트] 창을 연 다음, 다음 명령을 실행하여 VM에 로그온하는 데 도메인 계정을 사용하지 않도록 VM이 설정되었는지 확인합니다.

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

키가 **1**로 설정되면 서버에서 도메인 자격 증명을 허용하지 않도록 설정되었음을 의미합니다. 이 키를 **0**으로 변경합니다.

### <a name="for-standalone-vms"></a>독립 실행형 VM의 경우

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel 확인

CMD 인스턴스에서 다음 명령을 실행하여 **MinEncryptionLevel** 레지스트리 값을 쿼리합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

레지스트리 값에 따라 다음 단계를 수행합니다.

* 4(FIPS): [FIPS 호환 알고리즘 연결 확인](#fips-compliant)으로 이동합니다.

* 3(128비트 암호화): 다음 명령을 실행하여 심각도를 **2**로 설정합니다.

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2(클라이언트가 지정한 대로 가장 강력한 암호화 가능): 다음 명령을 실행하여 암호화를 최솟값(**1**)으로 설정할 수 있습니다.

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
레지스트리에 대한 변경 내용이 적용되도록 VM을 다시 시작합니다.

#### <a name="tls-version"></a>TLS 버전

시스템에 따라 RDP는 TLS 1.0, 1.1 또는 1.2(서버) 프로토콜을 사용합니다. VM에서 이러한 프로토콜을 설정하는 방법을 쿼리하려면 CMD 인스턴스를 열고 다음 명령을 실행합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

반환된 값이 모두 **1**이 아닌 경우 프로토콜을 사용하지 않도록 설정되었음을 의미합니다. 이러한 프로토콜을 사용하도록 설정하려면 다음 명령을 실행합니다.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

다른 프로토콜 버전의 경우 다음 명령을 실행할 수 있습니다.

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> SCHANNEL 오류에 대한 게스트 OS 로그에서 SSH/TLS 버전 x.x를 가져옵니다.

#### <a name="fips-compliant"></a> FIPS 호환 알고리즘 연결 확인

FIPS 호환 알고리즘 연결만 사용하도록 원격 데스크톱을 적용할 수 있습니다. 이는 레지스트리 키를 사용하여 설정할 수 있습니다. 이렇게 하려면 관리자 권한으로 실행된 [명령 프롬프트] 창을 열고 다음 키를 쿼리합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

명령에서 **1**이 반환되면 레지스트리 값을 **0**으로 변경합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

VM에 대한 현재 MinEncryptionLevel을 확인합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

명령에서 **4**가 반환되면 레지스트리 값을 **2**로 변경합니다.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

레지스트리에 대한 변경 내용이 적용되도록 VM을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[Win32_TSGeneralSetting 클래스의 SetEncryptionLevel 메서드](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[서버 인증 및 암호화 수준 구성](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting 클래스](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
