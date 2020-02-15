---
title: Linux 용 Azure 직렬 콘솔 | Microsoft Docs
description: Azure Virtual Machines 및 Virtual Machine Scale Sets에 대 한 양방향 직렬 콘솔
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167008"
---
# <a name="azure-serial-console-for-linux"></a>Linux 용 Azure 직렬 콘솔

Azure Portal의 직렬 콘솔은 Linux Vm (가상 머신) 및 가상 머신 확장 집합 인스턴스의 텍스트 기반 콘솔에 대 한 액세스를 제공 합니다. 이 직렬 연결은 VM 또는 가상 머신 확장 집합 인스턴스의 ttys0 직렬 포트에 연결 하 여 네트워크 또는 운영 체제 상태와 무관 하 게 액세스를 제공 합니다. 직렬 콘솔은 Azure Portal를 사용 하 여 액세스할 수 있으며, 참가자 이상의 액세스 역할이 있는 사용자 (VM 또는 가상 머신 확장 집합)에 대해서만 허용 됩니다.

직렬 콘솔은 Vm 및 가상 머신 확장 집합 인스턴스에 대해 동일한 방식으로 작동 합니다. 이 문서에서 Vm에 대 한 모든 멘 션은 별도로 명시 되지 않는 한 가상 머신 확장 집합 인스턴스를 암시적으로 포함 합니다.

Windows 용 직렬 콘솔 설명서는 [windows 용 직렬 콘솔](../windows/serial-console.md)을 참조 하세요.

> [!NOTE]
> 직렬 콘솔은 일반적으로 글로벌 Azure 지역 및 Azure Government의 공개 미리 보기에서 사용할 수 있습니다. Azure 중국 클라우드에서는 아직 사용할 수 없습니다.


## <a name="prerequisites"></a>사전 요구 사항

- VM 또는 가상 머신 확장 집합 인스턴스는 리소스 관리 배포 모델을 사용 해야 합니다. 클래식 배포는 지원되지 않습니다.

- 직렬 콘솔을 사용 하는 계정에는 VM 및 [부트 진단](boot-diagnostics.md) 저장소 계정에 대 한 [가상 머신 참가자 역할이](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 있어야 합니다.

- VM 또는 가상 머신 확장 집합 인스턴스에 암호 기반 사용자가 있어야 합니다. VM 액세스 확장의 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능으로 이러한 계정을 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.

- VM 또는 가상 머신 확장 집합 인스턴스에 [부팅 진단](boot-diagnostics.md) 이 사용 하도록 설정 되어 있어야 합니다.

    ![부트 진단 설정](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Linux 배포에 관련된 설정은 [직렬 콘솔 Linux 배포 가용성](#serial-console-linux-distribution-availability)을 참조하세요.

- VM 또는 가상 머신 확장 집합 인스턴스는 `ttys0`의 직렬 출력에 대해 구성 되어야 합니다. 이는 Azure 이미지에 대 한 기본값 이지만 사용자 지정 이미지에서이를 다시 확인 하는 것이 좋습니다. 자세한 내용은 [아래](#custom-linux-images)를 참조 하세요.


> [!NOTE]
> 직렬 콘솔에는 암호가 구성된 로컬 사용자가 필요합니다. SSH 공개 키로만 구성 된 Vm 또는 가상 머신 확장 집합은 직렬 콘솔에 로그인 할 수 없습니다. 암호가 구성된 로컬 사용자를 만들려면 Azure Portal에서 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)을 선택하여 포털에서 사용 가능한 **VM 액세스 확장**을 사용하고 암호를 사용하여 로컬 사용자를 만듭니다.
> [단일 사용자 모드로 부팅하려면 GRUB를 사용](./serial-console-grub-single-user-mode.md)하여 계정에서 관리자 암호를 재설정할 수도 있습니다.

## <a name="serial-console-linux-distribution-availability"></a>직렬 콘솔 Linux 배포 가용성
직렬 콘솔이 제대로 작동하려면 게스트 운영 체제가 콘솔 메시지를 읽고 직렬 포트에 쓰도록 구성되어야 합니다. 대부분의 [Azure Linux 보증 배포](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)에는 기본적으로 직렬 콘솔이 구성됩니다. Azure Portal의 **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택하면 직렬 콘솔에 대한 액세스를 제공합니다.

> [!NOTE]
> 직렬 콘솔에 아무 것도 표시되지 않으면 VM에서 부트 진단이 사용하도록 설정되어 있는지 확인합니다. **Enter 키** 를 누르면 직렬 콘솔에 아무것도 표시 되지 않는 문제를 해결 하는 경우가 많습니다.

배포      | 직렬 콘솔 액세스
:-----------|:---------------------
Red Hat Enterprise Linux    | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
CentOS      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
Debian      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
Ubuntu      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
CoreOS      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
SUSE        | Azure에서 사용 가능한 최신 SLES 이미지는 직렬 콘솔 액세스가 기본적으로 활성화되어 있습니다. Azure에서 이전 버전의 SLES(10 이하)를 사용하는 경우[기술 자료 문서](https://www.novell.com/support/kb/doc.php?id=3456486)를 참고하여 직렬 콘솔을 사용하도록 설정합니다.
Oracle Linux        | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.

### <a name="custom-linux-images"></a>사용자 지정 Linux 이미지
사용자 지정 Linux VM 이미지에 대해 직렬 콘솔을 사용하도록 설정하려면 */etc/inittab* 파일에서 콘솔 액세스를 사용하도록 설정하여 `ttyS0`에서 터미널을 실행합니다. 예: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102` TtyS0에서 getty를 생성 해야 할 수도 있습니다. `systemctl start serial-getty@ttyS0.service`를 사용 하 여이 작업을 수행할 수 있습니다.

또한 직렬 출력의 대상으로 ttys0을 추가 하려고 합니다. 직렬 콘솔을 사용 하도록 사용자 지정 이미지를 구성 하는 방법에 대 한 자세한 내용은 [Azure에서 LINUX VHD 만들기 및 업로드](https://aka.ms/createuploadvhd#general-linux-system-requirements)에서 일반적인 시스템 요구 사항을 참조 하세요.

사용자 지정 커널을 빌드하는 경우 `CONFIG_SERIAL_8250=y` 및 `CONFIG_MAGIC_SYSRQ_SERIAL=y` 커널 플래그를 사용하는 것이 좋습니다. 일반적으로 구성 파일은 */boot/* 경로에 위치합니다.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>직렬 콘솔에 액세스 하기 위한 일반적인 시나리오

시나리오          | 직렬 콘솔의 작업
:------------------|:-----------------------------------------
*FSTAB* 파일 손상 | **Enter** 키를 눌러 계속하고 텍스트 편집기를 사용하여 *FSTAB* 파일을 수정합니다. 이 작업을 수행하려면 단일 사용자 모드여야 합니다. 자세한 내용은 [fstab 문제를 해결 하는 방법](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) 의 직렬 콘솔 섹션을 참조 하 고 [직렬 콘솔을 사용 하 여 GRUB 및 단일 사용자 모드에 액세스](serial-console-grub-single-user-mode.md)합니다.
잘못된 방화벽 규칙 |  SSH 연결을 차단 하도록 iptables를 구성한 경우 SSH 없이 직렬 콘솔을 사용 하 여 VM과 상호 작용할 수 있습니다. 자세한 내용은 [iptables 매뉴얼 페이지](https://linux.die.net/man/8/iptables)에서 찾을 수 있습니다.<br>마찬가지로 firewalld에서 SSH 액세스를 차단 하는 경우 직렬 콘솔을 통해 VM에 액세스 하 고 firewalld를 다시 구성할 수 있습니다. 자세한 내용은 [firewalld 설명서](https://firewalld.org/documentation/)에서 찾을 수 있습니다.
파일 시스템 손상/검사 | 직렬 콘솔을 사용 하 여 손상 된 파일 시스템 문제 해결에 대 한 자세한 내용은 [파일 시스템 오류로 인해 Azure LINUX VM](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 의 직렬 콘솔 섹션을 시작할 수 없습니다 .를 참조 하세요.
SSH 구성 문제 | 직렬 콘솔에 액세스하여 설정을 변경합니다. Vm의 네트워크 연결이 작동 하지 않아도 되도록 VM의 SSH 구성에 관계 없이 직렬 콘솔를 사용할 수 있습니다. 문제 해결 가이드는 [실패 하거나 오류가 발생 하거나 거부 되는 Azure LINUX VM에 대 한 SSH 연결 문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)에서 사용할 수 있습니다. 자세한 내용은 [Azure에서 LINUX VM에 연결 하는 문제에 대 한 자세한 SSH 문제 해결 단계](./detailed-troubleshoot-ssh-connection.md) 에서 확인할 수 있습니다.
부팅 로더와 상호 작용 | 직렬 콘솔 블레이드 내에서 VM을 다시 시작하여 Linux VM의 GRUB에 액세스합니다. 자세한 내용 및 배포판 관련 정보는 [직렬 콘솔을 사용 하 여 GRUB 및 단일 사용자 모드에 액세스](serial-console-grub-single-user-mode.md)를 참조 하세요.

## <a name="disable-the-serial-console"></a>직렬 콘솔을 사용 하지 않도록 설정

기본적으로 모든 구독은 직렬 콘솔 액세스를 사용 하도록 설정 되어 있습니다. 구독 수준 또는 v m/가상 머신 확장 집합 수준에서 직렬 콘솔을 사용 하지 않도록 설정할 수 있습니다. 자세한 지침은 [Azure 직렬 콘솔 사용 및 사용 안 함](./serial-console-enable-disable.md)을 참조 하세요.

## <a name="serial-console-security"></a>직렬 콘솔 보안

### <a name="access-security"></a>액세스 보안
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스 역할을 가지고 있는 사용자로 제한됩니다. Azure Active Directory 테넌트에는 MFA(Multi-Factor Authentication)가 필요한 경우 직렬 콘솔이 [Azure Portal](https://portal.azure.com)을 통해 액세스되었기 때문에 직렬 콘솔에 대한 액세스에는 MFA가 필요합니다.

### <a name="channel-security"></a>채널 보안
주고받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.

> [!CAUTION]
> 콘솔에 대한 액세스 암호는 기록되지 않습니다. 그러나 콘솔 내에서 실행되는 명령이 암호, 비밀, 사용자 이름 또는 기타 형태의 PII(개인 식별 정보)를 포함하거나 출력하는 경우 해당 정보는 VM 부트 진단 로그에 작성됩니다. 또한 직렬 콘솔의 스크롤 백 기능을 구현하는 일부로 모든 다른 표시되는 텍스트와 함께 작성됩니다. 이러한 로그는 순환되며 진단 스토리지 계정에 대한 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 비밀 또는 PII를 포함 하는으로 명령을 입력 하는 경우 직렬 콘솔이 반드시 필요한 경우를 제외 하 고 SSH를 사용 하는 것이 좋습니다.

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 동일한 세션에 연결됩니다.

> [!CAUTION]
> 즉, 연결을 끊은 사용자는 로그 아웃 되지 않습니다. 연결을 끊을 때 (SITHE UP 또는 유사한 메커니즘을 사용 하 여) 로그 아웃을 적용 하는 기능은 로드맵에 있습니다. Windows의 경우 SAC(특별 관리 콘솔)에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 이렇게 하려면 사용자 콘솔에 로그인하는 데 사용하는 사용자에게 `export TMOUT=600`.bash_profile*또는*.profile*파일에서*을 추가합니다. 이 설정으로 인해 10분 후에 세션이 시간 초과하게 됩니다.

## <a name="accessibility"></a>접근성
접근성은 Azure 직렬 콘솔에 대 한 주요 중심입니다. 이를 위해 직렬 콘솔에 완전히 액세스할 수 있는지 확인했습니다.

### <a name="keyboard-navigation"></a>키보드 탐색
키보드의 **탭** 키를 사용하여 Azure Portal 내의 직렬 콘솔 인터페이스를 탐색합니다. 사용자 위치는 화면에서 강조 표시됩니다. 직렬 콘솔 블레이드 외부로 포커스를 이동하려면 키보드에서 **Ctrl**+**F6** 키를 누릅니다.

### <a name="use-serial-console-with-a-screen-reader"></a>화면 판독기와 함께 직렬 콘솔 사용
직렬 콘솔은 화면 reader 지원을 기본 제공합니다. 화면 읽기 프로그램을 켠 상태로 탐색하면 화면 읽기 프로그램은 현재 선택한 단추에 대한 alt 텍스트를 큰 소리로 읽을 수 있습니다.

## <a name="known-issues"></a>알려진 문제
직렬 콘솔과 VM의 운영 체제에 대 한 몇 가지 문제를 알고 있습니다. 다음은 이러한 문제 및 Linux Vm에 대 한 완화 단계 목록입니다. 이러한 문제와 완화는 Vm과 가상 머신 확장 집합 인스턴스 모두에 적용 됩니다. 표시 되는 오류와 일치 하지 않는 경우 일반적인 직렬 콘솔 서비스 오류에서 [일반적인 직렬 콘솔 오류](./serial-console-errors.md)를 참조 하세요.

문제                           |   완화 방법
:---------------------------------|:--------------------------------------------|
연결 배너에서 로그인 프롬프트가 표시되지 않으면 **Enter** 키를 누릅니다. | GRUB를 올바르게 구성 하지 못할 수 있습니다. 다음 명령을 실행 합니다. `grub2-mkconfig -o /etc/grub2-efi.cfg` 및/또는 `grub2-mkconfig -o /etc/grub2.cfg`. 자세한 내용은 [Enter를 누르면 아무 작업도 수행되지 않습니다](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)를 참조하세요. 이 문제는 Linux가 직렬 포트에 연결 하지 못하는 사용자 지정 VM, 확정 된 어플라이언스 또는 GRUB 구성을 실행 하는 경우 발생할 수 있습니다.
직렬 콘솔 텍스트는 화면 크기의 일부만 차지합니다(종종 텍스트 편집기를 사용한 이후). | 직렬 콘솔은 창 크기([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))에 대한 협상을 지원하지 않습니다. 즉, 화면 크기를 업데이트하기 위해 전송된 SIGWINCH 신호가 없으며, VM은 사용자 터미널 크기를 모른다는 것입니다. `resize` 명령에서 제공된 xterm 또는 유사한 유틸리티를 설치한 다음, `resize`를 실행합니다.
긴 문자열을 붙여넣는 작업이 작동하지 않습니다. | 직렬 콘솔은 터미널에 붙여넣는 문자열의 길이를 2048자로 제한하여 직렬 포트 대역폭을 오버로드하지 않도록 방지합니다.
SLES BYOS 이미지의 키보드 입력이 비정상적입니다. 키보드 입력은 산발적 으로만 인식 됩니다. | Plymouth 패키지에 문제가 있습니다. Plymouth는 시작 화면이 필요 하지 않으므로 Azure에서 실행 해서는 안 되며, Plymouth는 직렬 콘솔을 사용 하는 플랫폼 기능을 방해 합니다. `sudo zypper remove plymouth`를 사용 하 여 Plymouth를 제거한 다음 다시 부팅 합니다. 또는 줄의 끝에 `plymouth.enable=0`을 추가 하 여 GRUB 구성의 커널 줄을 수정 합니다. [부팅 시 부팅 항목을 편집](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)하거나 `/etc/default/grub`에서 GRUB_CMDLINE_LINUX 줄을 편집 하 고, `grub2-mkconfig -o /boot/grub2/grub.cfg`를 사용 하 여 GRUB를 다시 빌드하고, 다시 부팅 하 여이 작업을 수행할 수 있습니다.


## <a name="frequently-asked-questions"></a>질문과 대답

**Q. 사용자 의견을 보내려면 어떻게 해야 하나요?**

A. https://aka.ms/serialconsolefeedback에서 GitHub 문제를 만들어 피드백을 제공해주세요. 또는 (대안으로) azserialhelp@microsoft.com을 통해 또는 https://feedback.azure.com의 가상 머신 범주에 피드백을 보낼 수 있습니다.

**Q. 직렬 콘솔에서 복사/붙여넣기를 지원 하나요?**

A. 예. **Ctrl**+**Shift**+**C** 및 **Ctrl**+**Shift**+**V**를 사용하여 복사하고 터미널에 붙여넣습니다.

**Q. SSH 연결 대신 직렬 콘솔을 사용할 수 있나요?**

A. 이렇게 사용하는 것이 기술적으로는 가능해 보일 수 있지만, 직렬 콘솔은 SSH를 통한 연결이 가능하지 않은 상황에서 문제 해결 도구로 주로 사용하도록 디자인되었습니다. 다음 이유 때문에 직렬 콘솔을 SSH 대신 사용하는 것이 좋습니다.

- 직렬 콘솔에는 SSH와 같은 대역폭이 없습니다. 텍스트 전용 연결이므로 추가로 많은 GUI 작업 상호 작용이 어렵습니다.
- 직렬 콘솔 액세스는 현재 사용자 이름 및 암호를 사용해서만 가능합니다. SSH 키는 사용자 이름/암호 조합보다 훨씬 더 안전하기 때문에 로그인 보안 관점에서는 직렬 콘솔보다 SSH를 사용하는 것이 좋습니다.

**Q. 내 구독에 대해 직렬 콘솔을 사용 하거나 사용 하지 않도록 설정할 수 있는 사용자는 누구 인가요?**

A. 전체 구독 수준에서 직렬 콘솔을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 쓰기 권한이 있는 역할에는 관리자 또는 소유자 역할이 포함됩니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

**Q. 내 v m/가상 머신 확장 집합에 대 한 직렬 콘솔에 액세스할 수 있는 사람은 누구 인가요?**

A. VM 또는 가상 머신 확장 집합에 대 한 가상 머신 참가자 역할이 있어야 직렬 콘솔에 액세스할 수 있습니다.

**Q. 직렬 콘솔에 아무것도 표시 되지 않습니다. 어떻게 해야 하나요?**

A. 사용자의 이미지가 직렬 콘솔 액세스에 대해 잘못 구성되었습니다. 직렬 콘솔을 사용하도록 이미지를 구성하는 방법에 대한 자세한 내용은 [직렬 콘솔 Linux 배포 가용성](#serial-console-linux-distribution-availability)을 참조하세요.

**Q. 직렬 콘솔은 가상 머신 확장 집합에 사용할 수 있나요?**

A. 네, 그래요! [Virtual Machine Scale Sets에 대 한 직렬 콘솔을](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets) 참조 하세요.

**Q. SSH 키 인증만 사용 하 여 VM 또는 가상 머신 확장 집합을 설정 하는 경우 여전히 직렬 콘솔을 사용 하 여 내 v m/가상 머신 확장 집합 인스턴스에 연결할 수 있나요?**

A. 예. 직렬 콘솔에 SSH 키가 필요하지 않기 때문에 사용자 이름/암호 조합을 설정하는 수밖에 없습니다. Azure Portal에서 **암호 재설정**을 선택하고 직렬 콘솔에 로그인하는 데 해당 자격 증명을 사용하여 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 직렬 콘솔을 사용하여 [GRUB 및 단일 사용자 모드 액세스](serial-console-grub-single-user-mode.md)
* [NMI 및 SysRq 호출](serial-console-nmi-sysrq.md)에 직렬 콘솔 사용
* 직렬 콘솔을 사용 하 여 [다양 한 배포판에서 GRUB를 사용 하도록 설정](serial-console-grub-proactive-configuration.md) 하는 방법을 알아봅니다.
* 직렬 콘솔은 [Windows VM](../windows/serial-console.md)에서도 지원됩니다.
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아봅니다.

