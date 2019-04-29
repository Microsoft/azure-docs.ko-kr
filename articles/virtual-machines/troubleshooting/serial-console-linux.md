---
title: Linux용 Azure 가상 머신 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신용 양방향 직렬 콘솔입니다.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: f407d87249c44ad3a4773b2cd8fc85ee09506ceb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921413"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Linux용 가상 머신 직렬 콘솔

Azure Portal의 VM(가상 머신) 직렬 콘솔은 Linux 가상 머신용 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 이 직렬 연결은 가상 머신의 COM1 직렬 포트에 연결되며, 가상 머신의 네트워크 또는 운영 체제 상태와 독립적으로 가상 머신에 대한 액세스를 제공합니다. 가상 머신의 직렬 콘솔에 대한 액세스는 Azure Portal을 사용해야만 수행할 수 있습니다. 가상 머신에 대해 Virtual Machine Contributor 이상의 액세스 역할을 가지고 있는 사용자에게만 허용됩니다.

Windows VM에 대한 직렬 콘솔 설명서는 [Windows용 가상 머신 직렬 콘솔](../windows/serial-console.md)을 참조하세요.

> [!NOTE]
> 가상 머신용 직렬 콘솔은 일반적으로 글로벌 Azure 지역에서 지원됩니다. 아직 Azure Government 또는 Azure 중국 클라우드에서는 지원되지 않습니다.


## <a name="prerequisites"></a>필수 조건

- 직렬 콘솔에 액세스하는 VM에서는 리소스 관리 배포 모델을 사용해야 합니다. 클래식 배포는 지원되지 않습니다.

- 직렬 콘솔에 액세스하는 VM에서는 [부트 진단](boot-diagnostics.md)을 사용하도록 설정해야 합니다.

    ![부트 진단 설정](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 직렬 콘솔을 사용하는 계정에는 VM에 대한 [Virtual Machine Contributor 역할](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)과 [부트 진단](boot-diagnostics.md) 저장소 계정이 있어야 합니다.

- 직렬 콘솔에 액세스하는 VM에는 암호 기반 계정이 있어야 합니다. VM 액세스 확장의 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능으로 이러한 계정을 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.

- Linux 배포에 관련된 설정은 [직렬 콘솔 Linux 배포 가용성](#serial-console-linux-distribution-availability)을 참조하세요.



## <a name="get-started-with-the-serial-console"></a>직렬 콘솔 시작하기
가상 머신의 직렬 콘솔은 Azure Portal을 통해서만 액세스할 수 있습니다.

  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. 왼쪽 메뉴에서 **Virtual Machines**를 선택합니다.

  1. 목록에서 VM을 선택합니다. VM에 대한 개요 페이지가 열립니다.

  1. **지원 + 문제 해결** 섹션까지 아래로 스크롤하여 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![Linux 직렬 콘솔 창](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> 직렬 콘솔에는 암호가 구성된 로컬 사용자가 필요합니다. SSH 공개 키로만 구성된 VM은 직렬 콘솔에 로그인할 수 없습니다. 암호가 구성된 로컬 사용자를 만들려면 Azure Portal에서 **암호 재설정**을 선택하여 포털에서 사용 가능한 [VM 액세스 확장](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)을 사용하고 암호를 사용하여 로컬 사용자를 만듭니다.
> [단일 사용자 모드로 부팅하려면 GRUB를 사용](./serial-console-grub-single-user-mode.md)하여 계정에서 관리자 암호를 재설정할 수도 있습니다.

## <a name="serial-console-linux-distribution-availability"></a>직렬 콘솔 Linux 배포 가용성
직렬 콘솔이 제대로 작동하려면 게스트 운영 체제가 콘솔 메시지를 읽고 직렬 포트에 쓰도록 구성되어야 합니다. 대부분의 [Azure Linux 보증 배포](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)에는 기본적으로 직렬 콘솔이 구성됩니다. Azure Portal의 **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택하면 직렬 콘솔에 대한 액세스를 제공합니다.

배포      | 직렬 콘솔 액세스
:-----------|:---------------------
Red Hat Enterprise Linux    | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
CentOS      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
Ubuntu      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
CoreOS      | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
SUSE        | Azure에서 사용 가능한 최신 SLES 이미지는 직렬 콘솔 액세스가 기본적으로 활성화되어 있습니다. Azure에서 이전 버전의 SLES(10 이하)를 사용하는 경우[기술 자료 문서](https://www.novell.com/support/kb/doc.php?id=3456486)를 참고하여 직렬 콘솔을 사용하도록 설정합니다.
Oracle Linux        | 직렬 콘솔 액세스를 기본적으로 사용하도록 설정합니다.
사용자 지정 Linux 이미지     | 사용자 지정 Linux VM 이미지에 대해 직렬 콘솔을 사용하도록 설정하려면 */etc/inittab* 파일에서 콘솔 액세스를 사용하도록 설정하여 `ttyS0`에서 터미널을 실행합니다. 예: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 사용자 지정 이미지를 올바르게 만드는 방법에 대한 자세한 내용은 [Azure에서 Linux VHD 생성 및 업로드](https://aka.ms/createuploadvhd)를 참조하세요. 사용자 지정 커널을 빌드하는 경우 `CONFIG_SERIAL_8250=y` 및 `CONFIG_MAGIC_SYSRQ_SERIAL=y` 커널 플래그를 사용하는 것이 좋습니다. 일반적으로 구성 파일은 */boot/* 경로에 위치합니다.

> [!NOTE]
> 직렬 콘솔에 아무 것도 표시되지 않으면 VM에서 부트 진단이 사용하도록 설정되어 있는지 확인합니다.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>직렬 콘솔에 액세스하는 일반적인 시나리오

시나리오          | 직렬 콘솔의 작업
:------------------|:-----------------------------------------
*FSTAB* 파일 손상 | **Enter** 키를 눌러 계속하고 텍스트 편집기를 사용하여 *FSTAB* 파일을 수정합니다. 이 작업을 수행하려면 단일 사용자 모드여야 합니다. 자세한 내용은의 직렬 콘솔 섹션을 참조 하세요 [fstab 문제를 해결 하는 방법](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) 하 고 [GRUB 및 단일 사용자 모드 액세스를 사용 하 여 직렬 콘솔](serial-console-grub-single-user-mode.md)합니다.
잘못된 방화벽 규칙 |  SSH 연결을 차단 하도록 iptables를 구성한 경우에 SSH 필요 없이 VM을 사용 하 여 상호 작용 하도록 직렬 콘솔을 사용할 수 있습니다. 자세한 세부 정보를 찾을 수 있습니다 합니다 [iptables man 페이지](https://linux.die.net/man/8/iptables)합니다. 마찬가지로, 있습니다 firewalld SSH 액세스를 차단 하는 경우 직렬 콘솔을 통해 VM에 액세스 하 firewalld를 다시 구성 합니다. 자세한 세부 정보를 찾을 수 있습니다 합니다 [firewalld 설명서](https://firewalld.org/documentation/)합니다.
파일 시스템 손상/검사 | 직렬 콘솔 섹션을 참조 하세요 [파일 시스템 오류로 인해 Azure Linux VM를 시작할 수 없습니다](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 자세한 문제 해결에 대 한 세부 정보에는 직렬 콘솔을 사용 하 여 파일 시스템 손상 되었습니다.
SSH 구성 문제 | 직렬 콘솔에 액세스하여 설정을 변경합니다. 직렬 콘솔 VM이 작동 하려면 네트워크 연결이 필요 하지 않습니다는 VM의 SSH 구성에 관계 없이 사용할 수 있습니다. 문제 해결 가이드에서 제공 됩니다 [SSH 연결 문제 해결 실패, 오류 또는 거부 되는 Azure Linux VM에](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)입니다. 자세한 세부 사항은 [자세한 SSH 문제 해결 Azure에서 Linux VM에 연결 하는 문제에 대 한 단계](./detailed-troubleshoot-ssh-connection.md)
부팅 로더와 상호 작용 | 직렬 콘솔 블레이드 내에서 VM을 다시 시작하여 Linux VM의 GRUB에 액세스합니다. 자세한 세부 정보 및 배포 별 정보를 참조 하세요 [GRUB 및 단일 사용자 모드 액세스를 사용 하 여 직렬 콘솔](serial-console-grub-single-user-mode.md)합니다.

## <a name="disable-the-serial-console"></a>직렬 콘솔 사용 안 함
기본적으로 모든 구독에는 모든 VM에 대한 직렬 콘솔 액세스가 활성화되어 있습니다. 구독 수준 또는 VM 수준에서 직렬 콘솔을 비활성화할 수 있습니다.

> [!NOTE]
> 직렬 콘솔을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 이러한 사용 권한은 관리자 또는 소유자 역할을 포함합니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

### <a name="subscription-level-disable"></a>구독 수준에서 비활성화
[콘솔 REST API 호출 비활성화](/rest/api/serialconsole/console/disableconsole)를 통해 전체 구독의 직렬 콘솔을 비활성화할 수 있습니다. API 설명서 페이지에서 지원되는 **사용해 보기** 기능을 사용하여 구독의 직렬 콘솔을 비활성화하거나 활성화할 수 있습니다. **subscriptionId**에 대한 구독 ID를 입력하고, **기본값**에 대한 **기본값**을 입력한 다음, **실행**을 선택합니다. Azure CLI 명령은 아직 지원되지 않습니다.

![REST API 사용해 보기](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

또는 Cloud Shell에서 다음 bash 명령 집합을 사용하여 구독에 대한 직렬 콘솔의 비활성화된 상태를 비활성화하고, 활성화하고, 확인할 수 있습니다.

* 구독의 비활성화된 직렬 콘솔 상태를 가져오려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* 구독의 직렬 콘솔을 비활성화하려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* 구독의 직렬 콘솔을 활성화하려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM 수준에서 비활성화
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔을 비활성화할 수 있습니다. Azure Portal에서 부트 진단을 꺼서 VM의 직렬 콘솔을 비활성화합니다.

## <a name="serial-console-security"></a>직렬 콘솔 보안

### <a name="access-security"></a>액세스 보안
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스 역할을 가지고 있는 사용자로 제한됩니다. Azure Active Directory 테넌트에는 MFA(Multi-Factor Authentication)가 필요한 경우 직렬 콘솔이 [Azure Portal](https://portal.azure.com)을 통해 액세스되었기 때문에 직렬 콘솔에 대한 액세스에는 MFA가 필요합니다.

### <a name="channel-security"></a>채널 보안
주고받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.

> [!CAUTION]
> 콘솔에 대한 액세스 암호는 기록되지 않습니다. 그러나 콘솔 내에서 실행되는 명령이 암호, 비밀, 사용자 이름 또는 기타 형태의 PII(개인 식별 정보)를 포함하거나 출력하는 경우 해당 정보는 VM 부트 진단 로그에 작성됩니다. 또한 직렬 콘솔의 스크롤 백 기능을 구현하는 일부로 모든 다른 표시되는 텍스트와 함께 작성됩니다. 이러한 로그는 순환되며 진단 스토리지 계정에 대한 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 그러나 비밀 및/또는 PII가 포함될 수 있는 항목에 대해 원격 데스크톱을 사용하는 모범 사례를 따르는 것이 좋습니다.

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 동일한 세션에 연결됩니다.

> [!CAUTION]
> 그렇다고 연결이 끊어진 사용자가 로그아웃되지는 않습니다. SIGHUP 또는 유사한 메커니즘을 사용하여 연결 해제 시 로그아웃을 강제 적용하는 기능은 아직 계획 중입니다. Windows의 경우 SAC(특별 관리 콘솔)에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 이렇게 하려면 사용자 콘솔에 로그인하는 데 사용하는 사용자에게 *.bash_profile* 또는 *.profile* 파일에서 `export TMOUT=600`을 추가합니다. 이 설정으로 인해 10분 후에 세션이 시간 초과하게 됩니다.

## <a name="accessibility"></a>접근성
접근성은 Azure 직렬 콘솔의 핵심 기능입니다. 이를 위해 직렬 콘솔에 완전히 액세스할 수 있는지 확인했습니다.

### <a name="keyboard-navigation"></a>키보드 탐색
키보드의 **탭** 키를 사용하여 Azure Portal 내의 직렬 콘솔 인터페이스를 탐색합니다. 사용자 위치는 화면에서 강조 표시됩니다. 직렬 콘솔 블레이드 외부로 포커스를 이동하려면 키보드에서 **Ctrl**+**F6** 키를 누릅니다.

### <a name="use-the-serial-console-with-a-screen-reader"></a>화면 reader에서 직렬 콘솔 사용
직렬 콘솔은 화면 reader 지원을 기본 제공합니다. 화면 읽기 프로그램을 켠 상태로 탐색하면 화면 읽기 프로그램은 현재 선택한 단추에 대한 alt 텍스트를 큰 소리로 읽을 수 있습니다.

## <a name="errors"></a>오류
대부분의 오류는 일시적이므로 연결을 다시 시도하면 해결되는 경우가 많습니다. 아래 표에서는 오류 및 해결 방법 목록을 보여줍니다.

오류                            |   해결 방법
:---------------------------------|:--------------------------------------------|
*&lt;VMNAME&gt;* 에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다.
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 VM이 시작된 상태여야 합니다.
직렬 콘솔에서 이 VM을 사용하는 데 필요한 사용 권한이 없습니다. Virtual Machine Contributor 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 사용 권한이 필요합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.
부트 진단 스토리지 계정인 *&lt;STORAGEACCOUNTNAME&gt;* 에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 사용 권한이 필요합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.
웹 소켓이 닫혀 있거나 열 수 없습니다. | `*.console.azure.com`을 허용 목록에 추가해야 할 수 있습니다. 더 자세하지만 더 오래 걸리는 방법은 매우 정기적으로 변경되는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용 목록에 추가하는 것입니다.
이 VM의 부트 진단 저장소 계정에 액세스할 경우 "사용할 수 없음" 응답이 발생했습니다. | 부트 진단에 계정 방화벽이 없는지 확인하세요. 직렬 콘솔이 작동하려면 액세스 가능한 부트 진단 저장소 계정이 필요합니다.

## <a name="known-issues"></a>알려진 문제
직렬 콘솔에 관한 몇 가지 문제를 인식하고 있습니다. 이러한 문제 목록 및 완화 단계는 다음과 같습니다.

문제                           |   해결 방법
:---------------------------------|:--------------------------------------------|
연결 배너에서 로그인 프롬프트가 표시되지 않으면 **Enter** 키를 누릅니다. | 자세한 내용은 [Enter를 누르면 아무 작업도 수행되지 않습니다](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)를 참조하세요. 이런 문제는 Linux가 직렬 포트에 제대로 연결하지 못하게 하는 사용자 지정 VM, 강화된 어플라이언스 또는 GRUB 구성을 실행하는 경우 발생할 수 있습니다.
직렬 콘솔 텍스트는 화면 크기의 일부만 차지합니다(종종 텍스트 편집기를 사용한 이후). | 직렬 콘솔은 창 크기([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))에 대한 협상을 지원하지 않습니다. 즉, 화면 크기를 업데이트하기 위해 전송된 SIGWINCH 신호가 없으며, VM은 사용자 터미널 크기를 모른다는 것입니다. `resize` 명령에서 제공된 xterm 또는 유사한 유틸리티를 설치한 다음, `resize`를 실행합니다.
긴 문자열을 붙여넣는 작업이 작동하지 않습니다. | 직렬 콘솔은 터미널에 붙여넣는 문자열의 길이를 2048자로 제한하여 직렬 포트 대역폭을 오버로드하지 않도록 방지합니다.
직렬 콘솔은 스토리지 계정 방화벽에서 작동하지 않습니다. | 직렬 콘솔은 기본적으로 부트 진단 스토리지 계정에서 사용하도록 설정된 스토리지 계정 방화벽과 함께 작동할 수 없습니다.


## <a name="frequently-asked-questions"></a>질문과 대답

**Q. 피드백을 보내려면 어떻게 해야 하나요?**

a. https://aka.ms/serialconsolefeedback에서 GitHub 문제를 만들어 피드백을 제공해주세요. 또는 (대안으로) azserialhelp@microsoft.com을 통해 또는 https://feedback.azure.com의 가상 머신 범주에 피드백을 보낼 수 있습니다.

**Q. 직렬 콘솔이 복사/붙여넣기를 지원하나요?**

a. 예. **Ctrl**+**Shift**+**C** 및 **Ctrl**+**Shift**+**V**를 사용하여 복사하고 터미널에 붙여넣습니다.

**Q. SSH 연결 대신 직렬 콘솔을 사용할 수 있나요?**

a. 이렇게 사용하는 것이 기술적으로는 가능해 보일 수 있지만, 직렬 콘솔은 SSH를 통한 연결이 가능하지 않은 상황에서 문제 해결 도구로 주로 사용하도록 디자인되었습니다. 다음 이유 때문에 직렬 콘솔을 SSH 대신 사용하는 것이 좋습니다.

- 직렬 콘솔에는 SSH와 같은 대역폭이 없습니다. 텍스트 전용 연결이므로 추가로 많은 GUI 작업 상호 작용이 어렵습니다.
- 직렬 콘솔 액세스는 현재 사용자 이름 및 암호를 사용해서만 가능합니다. SSH 키는 사용자 이름/암호 조합보다 훨씬 더 안전하기 때문에 로그인 보안 관점에서는 직렬 콘솔보다 SSH를 사용하는 것이 좋습니다.

**Q. 내 구독에 직렬 콘솔을 사용하거나 사용하지 않도록 설정할 수 있나요?**

a. 전체 구독 수준에서 직렬 콘솔을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 쓰기 권한이 있는 역할에는 관리자 또는 소유자 역할이 포함됩니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

**Q. 내 VM의 직렬 콘솔에 액세스할 수 있는 사용자는 누구인가요?**

a. VM의 직렬 콘솔에 액세스하려면 VM의 Virtual Machine Contributor 역할 이상이 있어야 합니다.

**Q. 내 직렬 콘솔에 아무 것도 표시되지 않으면 어떻게 하나요?**

a. 사용자의 이미지가 직렬 콘솔 액세스에 대해 잘못 구성되었습니다. 직렬 콘솔을 사용하도록 이미지를 구성하는 방법에 대한 자세한 내용은 [직렬 콘솔 Linux 배포 가용성](#serial-console-linux-distribution-availability)을 참조하세요.

**Q. 가상 머신 확장 집합에 대해 직렬 콘솔을 사용할 수 있나요?**

a. 현재는 가상 머신 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.

**Q. SSH 키 인증만을 사용하여 VM을 설정한 경우 여전히 직렬 콘솔을 사용하여 VM에 연결할 수 있나요?**

a. 예. 직렬 콘솔에 SSH 키가 필요하지 않기 때문에 사용자 이름/암호 조합을 설정하는 수밖에 없습니다. Azure Portal에서 **암호 재설정**을 선택하고 직렬 콘솔에 로그인하는 데 해당 자격 증명을 사용하여 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 직렬 콘솔을 사용하여 [GRUB 및 단일 사용자 모드 액세스](serial-console-grub-single-user-mode.md)
* [NMI 및 SysRq 호출](serial-console-nmi-sysrq.md)에 직렬 콘솔 사용
* 직렬 콘솔을 사용하여 [다양한 배포에서 GRUB을 사용하도록 설정](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)하는 방법을 알아봅니다.
* 직렬 콘솔은 [Windows VM](../windows/serial-console.md)에서도 지원됩니다.
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아봅니다.

