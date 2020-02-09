---
title: Windows 가상 데스크톱 세션 호스트 문제 해결-Azure
description: Windows 가상 데스크톱 세션 호스트 가상 컴퓨터를 구성할 때 발생 하는 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: f8400cbefc514fa01dedb1434a60989b1df0528d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980225"
---
# <a name="session-host-virtual-machine-configuration"></a>세션 호스트 가상 머신 구성

이 문서를 사용 하 여 Windows 가상 데스크톱 세션 호스트 Vm (가상 컴퓨터)을 구성할 때 발생 하는 문제를 해결할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공하기

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="vms-are-not-joined-to-the-domain"></a>Vm이 도메인에 가입 되어 있지 않습니다.

Vm을 도메인에 가입 하는 데 문제가 있는 경우 다음 지침을 따르세요.

- [Windows Server 가상 컴퓨터를 관리 되는 도메인에 가입](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) 또는 [도메인 가입 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)사용의 프로세스를 사용 하 여 수동으로 VM에 연결 합니다.
- VM의 명령줄에서 도메인 이름을 ping 해 봅니다.
- 도메인 가입 오류 [메시지 문제 해결](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)에서 도메인 가입 오류 메시지의 목록을 검토 합니다.

### <a name="error-incorrect-credentials"></a>오류: 잘못 된 자격 증명

**원인:** Azure Resource Manager 템플릿 인터페이스 수정에서 자격 증명을 입력 했을 때 철자가 잘못 되었습니다.

**해결 방법:** 다음 작업 중 하나를 수행 하 여 해결 합니다.

- 수동으로 Vm을 도메인에 추가 합니다.
- 자격 증명이 확인 되 면 템플릿을 다시 배포 합니다. [PowerShell을 사용 하 여 호스트 풀 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)를 참조 하세요.
- [기존 WINDOWS VM을 AD 도메인에 조인](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)하는 템플릿을 사용 하 여 vm을 도메인에 가입 시킵니다.

### <a name="error-timeout-waiting-for-user-input"></a>오류: 사용자 입력을 기다리는 동안 제한 시간이 초과 되었습니다.

**원인:** 도메인 가입을 완료 하는 데 사용 되는 계정에 MFA (multi-factor authentication)가 있을 수 있습니다.

**해결 방법:** 다음 작업 중 하나를 수행 하 여 해결 합니다.

- 계정에 대 한 MFA를 일시적으로 제거 합니다.
- 서비스 계정을 사용 합니다.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>오류: 프로 비전 하는 동안 사용 된 계정에 작업을 완료할 수 있는 권한이 없습니다.

**원인:** 사용 중인 계정에는 규정 준수 및 규정으로 인해 Vm을 도메인에 가입 시킬 수 있는 권한이 없습니다.

**해결 방법:** 다음 작업 중 하나를 수행 하 여 해결 합니다.

- 관리자 그룹의 구성원 인 계정을 사용 합니다.
- 사용 중인 계정에 필요한 사용 권한을 부여 합니다.

### <a name="error-domain-name-doesnt-resolve"></a>오류: 도메인 이름이 확인 되지 않습니다.

**원인 1:** Vm은 도메인이 있는 가상 네트워크 (VNET)와 연결 되지 않은 가상 네트워크에 있습니다.

**수정 1:** Vm이 프로 비전 된 VNET과 도메인 컨트롤러 (DC)가 실행 중인 VNET 간에 VNET 피어 링을 만듭니다. [가상 네트워크 피어 링 만들기-리소스 관리자, 다른 구독을](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)참조 하세요.

**원인 2:** Azure Active Directory Domain Services (Azure AD DS)를 사용 하는 경우 관리 되는 도메인 컨트롤러를 가리키도록 가상 네트워크에 DNS 서버 설정이 업데이트 되지 않습니다.

**수정 2:** Azure AD DS를 포함 하는 가상 네트워크에 대 한 DNS 설정을 업데이트 하려면 [azure virtual network에 대 한 dns 설정 업데이트](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance#update-dns-settings-for-the-azure-virtual-network)를 참조 하세요.

**원인 3:** 네트워크 인터페이스의 DNS 서버 설정이 가상 네트워크의 적절 한 DNS 서버를 가리키지 않습니다.

**수정 3:** [DNS 서버 변경]의 단계에 따라 다음 작업 중 하나를 수행 하 여 해결 합니다.
- [Dns 서버 변경](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers) 의 단계에 따라 네트워크 인터페이스의 dns 서버 설정을 **사용자 지정** 으로 변경 하 고 가상 네트워크에 있는 dns 서버의 개인 IP 주소를 지정 합니다.
- [Dns 서버 변경](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers)의 단계를 사용 하 여 **가상 네트워크에서 상속** 하도록 네트워크 인터페이스의 dns 서버 설정을 변경 하 고 dns 서버 [변경](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers)의 단계를 수행 하 여 가상 네트워크의 dns 서버 설정을 변경 합니다.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 부팅 로더가 설치 되어 있지 않습니다.

Vm을 프로 비전 하는 권장 방법은 **Windows 가상 데스크톱 호스트 풀 만들기 및 프로 비전** 템플릿 Azure Resource Manager을 사용 하는 것입니다. 템플릿은 Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부팅 로더를 자동으로 설치 합니다.

다음 지침에 따라 구성 요소가 설치 되었는지 확인 하 고 오류 메시지를 확인 합니다.

1. **제어판** > **프로그램** > **프로그램 및 기능**을 선택 하 여 두 구성 요소가 설치 되어 있는지 확인 합니다. **Windows 가상 데스크톱 에이전트** 및 **Windows 가상 데스크톱 에이전트 부팅 로더에서** 표시 되지 않으면 VM에 설치 되지 않습니다.
2. **파일 탐색기** 를 열고 **C:\Windows\Temp\ScriptLog.log**로 이동 합니다. 파일이 없는 경우 두 구성 요소를 설치한 PowerShell DSC가 제공 된 보안 컨텍스트에서 실행 될 수 없음을 나타냅니다.
3. **C:\Windows\Temp\ScriptLog.log** 파일이 있는 경우이 파일을 열고 오류 메시지를 확인 합니다.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>오류: Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부팅 로더가 없습니다. C:\Windows\Temp\ScriptLog.log도 없습니다.

**원인 1:** Azure Resource Manager 템플릿에 대 한 입력 중에 제공 된 자격 증명이 잘못 되었거나 사용 권한이 부족 합니다.

**수정 1:** PowerShell을 사용 하 여 [호스트 풀 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)를 사용 하 여 누락 된 구성 요소를 vm에 수동으로 추가 합니다.

**원인 2:** PowerShell DSC를 시작 하 고 실행할 수 있었지만 Windows 가상 데스크톱에 로그인 할 수 없어 필요한 정보를 가져올 수 없어 완료 하지 못했습니다.

**수정 2:** 다음 목록에서 항목을 확인 합니다.

- 계정에 MFA가 없는지 확인 합니다.
- 테넌트 이름이 정확 하 고 테넌트가 Windows 가상 데스크톱에 존재 하는지 확인 합니다.
- 계정에 최소한 RDS 참가자 권한이 있는지 확인 합니다.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>오류: 인증에 실패 했습니다. C:\Windows\Temp\ScriptLog.log에서 오류가 발생 했습니다.

**원인:** PowerShell DSC를 실행할 수 있었지만 Windows 가상 데스크톱에 연결할 수 없습니다.

**해결 방법:** 다음 목록에서 항목을 확인 합니다.

- Windows 가상 데스크톱 서비스를 사용 하 여 Vm을 수동으로 등록 합니다.
- Windows 가상 데스크톱에 연결 하는 데 사용 되는 계정에 테넌트에서 호스트 풀을 만들 수 있는 권한이 있는지 확인 합니다.
- 계정 확인에 MFA가 없습니다.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 가상 데스크톱 에이전트가 Windows 가상 데스크톱 서비스에 등록 되지 않습니다.

Windows 가상 데스크톱 에이전트가 수동으로 또는 Azure Resource Manager 템플릿 및 PowerShell DSC를 통해 세션 호스트 Vm에 설치 되 면 등록 토큰을 제공 합니다. 다음 섹션에서는 Windows 가상 데스크톱 에이전트 및 토큰에 적용 되는 문제를 해결 하는 방법에 대해 설명 합니다.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>오류: RdsSessionHost cmdlet에 있는 상태는 상태를 사용할 수 없음으로 표시 합니다.

![RdsSessionHost cmdlet은 상태를 사용할 수 없음으로 표시 합니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 에이전트는 자신을 새 버전으로 업데이트할 수 없습니다.

**해결 방법:** 에이전트를 수동으로 업데이트 하려면 다음 지침을 따르세요.

1. 세션 호스트 VM에서 새 버전의 에이전트를 다운로드 합니다.
2. 작업 관리자를 시작 하 고 서비스 탭에서 RDAgentBootLoader 서비스를 중지 합니다.
3. 새 버전의 Windows 가상 데스크톱 에이전트에 대 한 설치 관리자를 실행 합니다.
4. 등록 토큰을 입력 하 라는 메시지가 표시 되 면 INVALID_TOKEN 항목을 제거 하 고 다음을 누릅니다 (새 토큰은 필요 하지 않음).
5. 설치 마법사를 완료 합니다.
6. 작업 관리자를 열고 RDAgentBootLoader 서비스를 시작 합니다.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>오류: Windows 가상 데스크톱 에이전트 레지스트리 항목 IsRegistered에는 0 값이 표시 됩니다.

**원인:** 등록 토큰이 만료 되었거나 만료 값 999999를 사용 하 여 생성 되었습니다.

**해결 방법:** 에이전트 레지스트리 오류를 해결 하려면 다음 지침을 따르세요.

1. 등록 토큰이 이미 있는 경우 RDSRegistrationInfo를 사용 하 여 제거 합니다.
2. Rds-NewRegistrationInfo를 사용 하 여 새 토큰을 생성 합니다.
3. -ExpriationHours 매개 변수가 72 (최대값은 99999)로 설정 되어 있는지 확인 합니다.

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>오류: Windows 가상 데스크톱 에이전트가 RdsSessionHost을 실행 하는 경우 하트 비트를 보고 하지 않습니다.

**원인 1:** RDAgentBootLoader 서비스가 중지 되었습니다.

**수정 1:** 작업 관리자를 시작 하 고, 서비스 탭에서 RDAgentBootLoader 서비스에 대해 중지 됨 상태를 보고 하는 경우 서비스를 시작 합니다.

**원인 2:** 포트 443이 닫힐 수 있습니다.

**수정 2:** 다음 지침에 따라 포트 443를 엽니다.

1. [Sysinternal 도구](https://docs.microsoft.com/sysinternals/downloads/psping)에서 PSPing 도구를 다운로드 하 여 포트 443가 열려 있는지 확인 합니다.
2. 에이전트가 실행 되 고 있는 세션 호스트 VM에 PSPing를 설치 합니다.
3. 관리자 권한으로 명령 프롬프트를 열고 다음 명령을 실행 합니다.

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. PSPing가 RDBroker에서 정보를 돌려 받았는지 확인 합니다.

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Windows 가상 데스크톱 side-by-side 스택과 관련 된 문제 해결

Windows 가상 데스크톱 side-by-side 스택은 Windows Server 2019와 함께 자동으로 설치 됩니다. Microsoft Installer (MSI)를 사용 하 여 Microsoft Windows Server 2016 또는 Windows Server 2012 r 2에 side-by-side 스택을 설치 합니다. Microsoft Windows 10의 경우 **enablesxstackrs**를 사용 하 여 Windows 가상 데스크톱 side-by-side 스택을 사용 하도록 설정 합니다.

Side-by-side 스택은 세션 호스트 풀 Vm에서 설치 또는 사용 하도록 설정 하는 세 가지 주요 방법이 있습니다.

- Azure Resource Manager **새 Windows 가상 데스크톱 호스트 풀 템플릿 만들기 및 프로 비전**
- 마스터 이미지에 포함 및 활성화
- 각 VM에서 수동으로 설치 또는 사용 하도록 설정 (또는 확장/PowerShell 사용)

Windows 가상 데스크톱 side-by-side 스택에 문제가 있는 경우 명령 프롬프트에서 **qwinsta** 명령을 입력 하 여 side-by-side 스택을 설치 하거나 사용 하도록 설정 했는지 확인 합니다.

Side-by-side 스택을 설치 하 고 사용 하도록 설정 하는 경우 **qwinsta** 의 출력은 출력에 **rdp-sxs** 를 나열 합니다.

![Qwinsta와 함께 설치 되거나 사용 하도록 설정 된 side-by-side 스택에는 출력에 rdp-sxs로 나열 됩니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

아래에 나열 된 레지스트리 항목을 확인 하 고 해당 값이 일치 하는지 확인 합니다. 레지스트리 키가 없거나 값이 일치 하지 않으면 side-by-side 스택을 다시 설치 하는 방법에 대 한 [PowerShell을 사용 하 여 호스트 풀 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) 의 지침을 따르세요.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>오류: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 오류 코드입니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** Side-by-side 스택이 세션 호스트 VM에 설치 되어 있지 않습니다.

**해결 방법:** 세션 호스트 VM에 side-by-side 스택을 설치 하려면 다음 지침을 따르세요.

1. RDP (원격 데스크톱 프로토콜)를 사용 하 여 로컬 관리자로 세션 호스트 VM에 직접 가져올 수 있습니다.
2. PowerShell 세션에서 사용할 [Windows 가상 데스크톱 powershell 모듈](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 을 다운로드 하 여 가져온 다음 (아직 없는 경우)이 cmdlet을 실행 하 여 계정에 로그인 합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. PowerShell을 사용 하 여 [호스트 풀 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)를 사용 하 여 side-by-side 스택을 설치 합니다.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>오작동 하는 Windows 가상 데스크톱 side-by-side 스택을 수정 하는 방법

병렬 스택이 오작동을 일으킬 수 있는 알려진 상황이 있습니다.

- Side-by-side 스택을 사용 하도록 설정 하는 단계의 올바른 순서를 따르지 않습니다.
- Windows 10 향상 된 다용도 디스크에 대 한 자동 업데이트 (EVD)
- RDSH (원격 데스크톱 세션 호스트) 역할이 없습니다.
- Enablesxsstackrc을 여러 번 실행
- 로컬 관리자 권한이 없는 계정에서 enablesxsstackrc를 실행 하 고 있습니다.

이 섹션의 지침을 참조 하 여 Windows 가상 데스크톱 side-by-side 스택을 제거할 수 있습니다. Side-by-side 스택을 제거한 후 PowerShell을 사용 하 여 [호스트 풀 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) 의 "Windows 가상 데스크톱 호스트 풀을 사용 하 여 VM 등록"으로 이동 하 여 side-by-side 스택을 다시 설치 합니다.

관리를 실행 하는 데 사용 되는 VM은 병렬 스택이 제대로 작동 하지 않는 VM과 동일한 서브넷 및 도메인에 있어야 합니다.

다음 지침에 따라 동일한 서브넷과 도메인에서 업데이트 관리를 실행 합니다.

1. 수정이 적용 되는 VM에 RDP (standard 원격 데스크톱 프로토콜)를 연결 합니다.
2. https://docs.microsoft.com/sysinternals/downloads/psexec 에서 PsExec를 다운로드 합니다.
3. 다운로드 한 파일의 압축을 풉니다.
4. 로컬 관리자 권한으로 명령 프롬프트를 시작 합니다.
5. PsExec 압축을 푼 폴더로 이동 합니다.
6. 명령 프롬프트에서 다음 명령을 사용 합니다.

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname는 side-by-side 스택에서 작동 하지 않는 VM의 컴퓨터 이름입니다.

7. 동의를 클릭 하 여 PsExec 사용권 계약에 동의 합니다.

    ![소프트웨어 사용권 계약 스크린샷.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >이 대화 상자는 처음 PsExec가 실행 될 때만 표시 됩니다.

8. 명령 프롬프트 세션이 작동 하지 않는 side-by-side 스택을 사용 하 여 VM에서 열리면 qwinsta을 실행 하 고 rdp-sxs 라는 항목을 사용할 수 있는지 확인 합니다. 그렇지 않은 경우 side-by-side 스택은 VM에 존재 하지 않으므로이는 side-by-side 스택에 연결 되지 않습니다.

    ![관리자 명령 프롬프트](media/AdministratorCommandPrompt.png)

9. 다음 명령을 실행 합니다 .이 명령을 실행 하면 VM에 설치 된 Microsoft 구성 요소가 함께 작동 하 여 병렬 스택이 작동 하지 않습니다.

    ```cmd
        wmic product get name
    ```

10. 위 단계에서 제품 이름으로 아래 명령을 실행 합니다.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "원격 데스크톱"으로 시작 하는 모든 제품을 제거 합니다.

12. 모든 Windows 가상 데스크톱 구성 요소가 제거 된 후에는 운영 체제에 대 한 지침을 따르세요.

13. 운영 체제가 Windows Server 인 경우에는 Azure Portal 또는 PsExec 도구를 사용 하 여 side-by-side 스택이 제대로 작동 하지 않는 VM을 다시 시작 합니다.

운영 체제가 Microsoft Windows 10 인 경우 다음 지침을 계속 진행 합니다.

14. PsExec를 실행 하는 VM에서 파일 탐색기를 열고 disablesxsstackrc을 실행 하지 않는 side-by-side 스택을 사용 하 여 VM의 시스템 드라이브로 복사 합니다.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname는 side-by-side 스택에서 작동 하지 않는 VM의 컴퓨터 이름입니다.

15. 권장 프로세스: PsExec 도구에서 PowerShell을 시작 하 고 이전 단계에서 폴더로 이동한 후 disablesxsstackrc를 실행 합니다. 또는 다음 cmdlet을 실행할 수 있습니다.

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Cmdlet을 실행 한 후에는 작동 하지 않는 side-by-side 스택을 사용 하 여 VM을 다시 시작 합니다.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>원격 데스크톱 라이선스 모드가 구성 되지 않았습니다.

관리 계정을 사용 하 여 Windows 10 Enterprise 다중 세션에 로그인 하는 경우 "원격 데스크톱 라이선스 모드가 구성 되지 않았습니다. 원격 데스크톱 서비스 X 일 후에 작동 하지 않습니다. 라는 알림이 표시 될 수 있습니다. 연결 브로커 서버에서 서버 관리자를 사용 하 여 원격 데스크톱 라이선싱 모드를 지정 합니다.

시간 제한이 만료 되 면 "이 컴퓨터에 사용할 수 있는 원격 데스크톱 클라이언트 액세스 라이선스가 없어 원격 세션의 연결을 끊었습니다." 라는 오류 메시지가 표시 됩니다.

이러한 메시지 중 하나가 표시 되 면 이미지에 최신 Windows 업데이트가 설치 되어 있지 않거나 그룹 정책을 통해 원격 데스크톱 라이선스 모드를 설정 하는 것입니다. 다음 섹션의 단계에 따라 그룹 정책 설정을 확인 하 고, Windows 10 Enterprise 다중 세션 버전을 확인 하 고, 해당 업데이트를 설치 합니다.  

>[!NOTE]
>Windows 가상 데스크톱에는 호스트 풀에 Windows Server 세션 호스트가 포함 된 경우 RDS CAL (클라이언트 액세스 라이선스)만 필요 합니다. RDS CAL를 구성 하는 방법에 대 한 자세한 내용은 [클라이언트 액세스 라이선스를 사용 하 여 RDS 배포 라이선스](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-client-access-license)를 참조 하세요.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>원격 데스크톱 라이선싱 모드 그룹 정책 설정 사용 안 함

VM에서 그룹 정책 편집기를 열고 **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** ** > 원격 데스크톱 세션 호스트** > **라이선스** > **원격 데스크톱 라이선싱 모드를 설정**하 여 그룹 정책 설정을 확인 합니다. 그룹 정책 설정을 **사용**하는 경우 **사용 안 함**으로 변경 합니다. 이미 사용 하지 않도록 설정 되어 있으면 그대로 둡니다.

>[!NOTE]
>도메인을 통해 그룹 정책을 설정 하는 경우 이러한 Windows 10 Enterprise 다중 세션 Vm을 대상으로 하는 정책에서이 설정을 사용 하지 않도록 설정 합니다.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>사용 중인 Windows 10 Enterprise 다중 세션 버전 확인

보유 한 Windows 10 Enterprise 다중 세션 버전을 확인 하려면 다음을 수행 합니다.

1. 관리자 계정으로 로그인 합니다.
2. 시작 메뉴 옆의 검색 표시줄에 "About"을 입력 합니다.
3. **PC 정보를**선택 합니다.
4. "버전" 옆에 있는 숫자를 확인 합니다. 다음 그림에 표시 된 것 처럼 숫자는 "1809" 또는 "1903" 중 하나 여야 합니다.

    ![Windows 사양 창의 스크린샷 버전 번호는 파란색으로 강조 표시 됩니다.](media/windows-specifications.png)

이제 버전 번호를 알고 있으므로 관련 섹션으로 건너뜁니다.

### <a name="version-1809"></a>버전 1809

버전 번호가 "1809" 인 경우 [KB4516077 업데이트를](https://support.microsoft.com/help/4516077)설치 합니다.

### <a name="version-1903"></a>버전 1903

Azure 갤러리에서 최신 버전의 Windows 10 버전 1903 이미지를 사용 하 여 호스트 운영 체제를 다시 배포 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 문제 해결 자습서를 진행 하려면 [자습서: 템플릿 배포 리소스 관리자 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조 하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
