---
title: Azure VM 게스트 OS 방화벽이 인바운드 트래픽 차단 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711574"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM 게스트 OS 방화벽이 인바운드 트래픽 차단

이 문서에서는 게스트 운영 체제가 인바운드 트래픽을 차단할 때 발생하는 RDP(원격 데스크톱 포털) 문제 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

RDP 연결을 사용하여 Azure VM(가상 머신)에 연결할 수 없습니다. 부팅 진단 -> 스크린샷을 보면, 운영 체제가 시작 화면(Ctrl + Alt + Del)에서 완전히 로드되는 것을 볼 수 있습니다.

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

RDP 트래픽을 허용하도록 RDP 규칙이 설정되지 않았습니다.

### <a name="cause-2"></a>원인 2

RDP 트래픽을 포함한 모든 인바운드 연결을 차단하도록 게스트 시스템 방화벽 프로필이 설정되었습니다.

![방화벽 설정](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>해결 방법

다음 단계를 따르기 전에 영향을 받는 VM의 시스템 디스크 스냅숏을 백업으로 만듭니다. 자세한 내용은  [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

문제를 해결하려면 [원격 도구를 사용하여 Azure VM 문제 해결](remote-tools-troubleshoot-azure-vm-issues.md)의 방법 중 하나를 사용하여 VM에 원격으로 연결한 다음, RDP 트래픽을 **허용**하도록 게스트 운영 체제 방화벽 규칙을 편집합니다.

### <a name="online-troubleshooting"></a>온라인 문제 해결

[직렬 콘솔에 연결한 다음, PowerShell 인스턴스를 엽니다](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). VM에서 직렬 콘솔이 비활성화된 경우 [오프라인으로 VM 복구](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)로 이동합니다.

#### <a name="mitigation-1"></a>해결 방법 1

1.  VM에 Azure 에이전트가 설치되어 있고 올바르게 작동하는 경우 VM 메뉴의 **지원 + 문제 해결** > **암호 재설정** 아래에서 "구성만 재설정" 옵션을 사용할 수 있습니다.

2.  이 복구 옵션을 실행하면 다음 작업이 수행됩니다.

    *   RDP 구성 요소가 비활성화된 경우 사용하도록 설정합니다.

    *   모든 Windows 방화벽 프로필을 사용하도록 설정합니다.

    *   Windows 방화벽에서 RDP 규칙이 설정되어 있는지 확인합니다.

    *   이전 단계가 작동하지 않으면 수동으로 방화벽 규칙을 다시 설정합니다. 이렇게 하려면 다음 명령을 실행하여 이름에 "원격 데스크톱"이 포함된 모든 규칙을 쿼리합니다.

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        RDP 포트가 3389 이외의 다른 포트로 설정된 경우 생성된 사용자 지정 규칙을 찾아 이 포트로 설정해야 합니다. 사용자 지정 포트를 사용하는 모든 인바운드 규칙을 쿼리하려면 다음 명령을 실행합니다.

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  규칙이 비활성화되어 있으면 사용하도록 설정합니다. 기본 제공 원격 데스크톱 그룹 같은 전체 그룹을 열려면 다음 명령을 실행합니다.

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    그렇지 않고 특정 원격 데스크톱(TCP-In) 규칙을 열려면 다음 명령을 실행합니다.

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  문제 해결을 위해 방화벽 프로필을 OFF로 설정할 수 있습니다.

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    문제를 해결하고 방화벽을 올바르게 설정한 후에는 방화벽을 다시 사용하도록 설정합니다.

    > [!Note]
    > 이러한 변경 내용을 적용하기 위해 VM을 다시 시작할 필요는 없습니다.

5.  RDP 연결을 사용하여 VM에 액세스합니다.

#### <a name="mitigation-2"></a>해결 방법 2

1.  방화벽 프로필을 쿼리하여 인바운드 방화벽 정책이  *BlockInboundAlways*로 설정되었는지 확인합니다.

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > 다음 지침은 설정 방법에 따라 방화벽 정책에 적용됩니다.
    >    * *BlockInbound*: 트래픽을 허용하는 규칙이 없으면 모든 인바운드 트래픽이 차단됩니다.
    >    * *BlockInboundAlways*: 모든 방화벽 규칙이 무시되고 모든 트래픽이 차단됩니다.

2.   *DefaultInboundAction* 을 편집하여 트래픽을  **허용** 하도록 프로필을 설정합니다. 이렇게 하려면 다음 명령을 실행합니다.

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  프로필을 다시 쿼리하여 변경 작업이 수행되었는지 확인합니다. 이렇게 하려면 다음 명령을 실행합니다.

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > 변경 내용을 적용하기 위해 VM을 다시 시작할 필요는 없습니다.

4.  RDP를 통해 VM에 다시 액세스합니다.

### <a name="offline-mitigations"></a>오프라인 해결 방법

1.  [복구 VM에 시스템 디스크 연결](troubleshoot-recovery-disks-portal-windows.md).

2.  복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

3.  [디스크 관리] 콘솔에서 디스크의 플래그가  **온라인** 으로 지정되었는지 확인합니다. 연결된 시스템 디스크에 할당된 드라이브 문자를 적어 둡니다.

#### <a name="mitigation-1"></a>해결 방법 1

 [게스트 OS에서 방화벽 규칙을 사용/사용하지 않도록 설정하는 방법](enable-disable-firewall-rule-guest-os.md)을 참조합니다.

#### <a name="mitigation-2"></a>해결 방법 2

1.  [복구 VM에 시스템 디스크 연결](troubleshoot-recovery-disks-portal-windows.md).

2.  복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

3.  시스템 디스크가 복구 VM에 연결되면 디스크 관리 콘솔에서 디스크의 플래그가  **온라인** 으로 지정되었는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.

4.  관리자 권한 CMD 인스턴스를 열고 다음 스크립트를 실행합니다.

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [시스템 디스크를 분리하고 VM을 다시 만듭니다](troubleshoot-recovery-disks-portal-windows.md).

6.  문제가 해결되었는지 확인합니다.
