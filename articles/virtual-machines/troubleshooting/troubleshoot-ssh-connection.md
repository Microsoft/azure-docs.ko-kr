---
title: Azure VM에 대한 SSH 연결 문제 해결 | Microsoft Docs
description: Linux를 실행하는 Azure VM에 대해 'SSH 연결 실패' 또는 'SSH 연결 거부'와 같은 문제를 해결하는 방법입니다.
keywords: ssh 연결 거부, ssh 오류, azure ssh, SSH 연결 실패
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 81e00c4a3b9490a05667d58952f7bdf8945bacdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61405257"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>실패하거나 오류가 발생하거나 거부되는 Azure Linux VM에 대한 SSH 연결 문제 해결
이 문서는 Linux VM(가상 머신)에 연결하려고 할 때 SSH(Secure Shell) 오류, SSH 연결 실패 또는 SSH 연결 거부 문제로 인해 발생하는 문제를 찾고 수정하도록 돕습니다. Azure Portal, Azure CLI 또는 Linux용 VM 액세스 확장을 사용하여 연결 문제를 해결할 수 있습니다.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. [SSH 구성을 다시 설정](#reset-config)합니다.
2. 사용자의 [자격 증명을 다시 설정](#reset-credentials)합니다.
3. [네트워크 보안 그룹](../../virtual-network/security-overview.md) 규칙이 SSH 트래픽을 허용하는지 확인합니다.
   * [네트워크 보안 그룹 규칙](#security-rules)이 SSH 트래픽을 허용하기 위해 존재하는지 확인합니다(기본적으로 TCP 포트 22).
   * Azure Load Balancer를 사용하지 않고 포트 리디렉션/매핑을 사용할 수 없습니다.
4. [VM 리소스 상태](../../resource-health/resource-health-overview.md)를 확인합니다.
   * VM이 정상으로 보고하는지 확인합니다.
   * [부팅 진단을 사용하도록 설정](boot-diagnostics.md)하는 경우 VM이 로그에서 부팅 오류를 보고하지 않는지 확인합니다.
5. [VM을 다시 시작](#restart-vm)합니다.
6. [VM을 다시 배포](#redeploy-vm)합니다.

자세한 문제 해결 단계와 설명이 필요한 경우 계속 읽어보세요.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>SSH 연결 문제 해결에 사용할 수 있는 방법
다음 방법 중 하나를 사용하여 자격 증명 또는 SSH 구성을 다시 설정할 수 있습니다.

* [Azure Portal](#use-the-azure-portal) - SSH 구성 또는 SSH 키를 신속하게 다시 설정해야 하는데 Azure 도구가 설치되지 않은 경우에 매우 유용합니다.
* [Azure VM의 직렬 콘솔](https://aka.ms/serialconsolelinux) -직렬 콘솔 VM SSH 구성에 관계 없이 작동 하 고 하면 대화형 콘솔을 사용 하 여 VM에 있습니다. 사실 "SSH 수 없습니다." 상황은 특히 직렬 콘솔 던 해결 하기 위해 설계 되었습니다. 자세한 내용은 아래입니다.
* [Azure CLI](#use-the-azure-cli) - 명령줄에 이미 도달한 경우 SSH 구성 또는 자격 증명을 신속하게 다시 설정합니다. 클래식 VM을 사용하는 경우 [Azure 클래식 CLI](#use-the-azure-classic-cli)를 사용할 수 있습니다.
* [Azure VMAccessForLinux 확장](#use-the-vmaccess-extension) - json 정의 파일을 만들고 다시 사용하여 SSH 구성 또는 사용자 자격 증명을 다시 설정합니다.

각 문제 해결 단계 후 VM에 다시 연결을 시도합니다. 그래도 연결할 수 없으면 다음 단계를 시도합니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용
Azure Portal은 로컬 컴퓨터에 도구를 설치하지 않고 SSH 구성 또는 사용자 자격 증명을 다시 설정하는 빠른 방법을 제공합니다.

시작하려면 Azure Portal에서 VM을 선택합니다. **지원 + 문제 해결** 섹션까지 아래로 스크롤하고 다음 예제와 같이 **암호 재설정**을 선택합니다.

![Azure Portal에서 SSH 구성 또는 자격 증명 다시 설정](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />SSH 구성 다시 설정
SSH 구성을 다시 설정하려면 이전 스크린샷과 같이 **모드** 섹션에서 `Reset configuration only`를 선택한 다음, **업데이트**를 선택합니다. 이 작업이 완료되면 VM에 다시 액세스하려고 합니다.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />사용자용 SSH 자격 증명 다시 설정
기존 사용자의 자격 증명을 다시 설정하려면 이전 스크린샷과 같이 **모드** 섹션에서 `Reset SSH public key` 또는 `Reset password`를 선택합니다. 사용자 이름 및 SSH 키 또는 새 암호를 지정한 다음, **업데이트**를 선택합니다.

이 메뉴에서 VM에 대해 sudo 권한이 있는 사용자를 만들 수도 있습니다. 새 사용자 이름 및 연결된 암호 또는 SSH 키를 입력한 다음, **업데이트**를 선택합니다.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />보안 규칙 확인

[IP 흐름 확인](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)을 사용하여 네트워크 보안 그룹의 규칙이 가상 머신으로 들어가고 나가는 트래픽을 차단하는지를 확인합니다. 효과적인 보안 그룹 규칙을 검토하여 인바운드 "허용" NSG 규칙이 있는지와 해당 규칙이 SSH 포트(기본값: 22)에 우선적으로 사용되도록 설정되어 있는지 확인합니다. 자세한 내용은 [효과적인 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결](../../virtual-network/diagnose-network-traffic-filter-problem.md)을 참조하세요.

### <a name="check-routing"></a>라우팅 확인

Network Watcher의 [다음 홉](../../network-watcher/network-watcher-check-next-hop-portal.md) 기능을 사용하여 트래픽이 가상 머신으로 들어가거나 나가도록 라우팅하는 데 경로가 방해가 되지 않는지 확인합니다. 네트워크 인터페이스의 유효 경로를 모두 볼 수 있도록 유효 경로를 검토할 수도 있습니다. 자세한 내용은 [유효 경로를 사용하여 VM 트래픽 흐름 문제 해결](../../virtual-network/diagnose-network-routing-problem.md)을 참조하세요.

## <a name="use-the-azure-vm-serial-console"></a>Azure VM 직렬 콘솔 사용
합니다 [Azure VM의 직렬 콘솔](./serial-console-linux.md) Linux 가상 머신에 대 한 텍스트 기반 콘솔에 대 한 액세스를 제공 합니다. 대화형 셸로 SSH 연결 문제를 해결 하려면 콘솔을 사용할 수 있습니다. 충족 했는지 확인 합니다 [필수 구성 요소](./serial-console-linux.md#prerequisites) 자세히 명령을 시도 직렬 콘솔을 사용 하 여에 대 한 SSH 연결 문제 해결.

### <a name="check-that-ssh-is-running"></a>SSH를 실행 중인 검사
SSH를 VM에서 실행 중인지 여부를 확인 하려면 다음 명령을 사용할 수 있습니다.
```
$ ps -aux | grep ssh
```
출력의 경우 SSH는를 실행 합니다.

### <a name="check-which-port-ssh-is-running-on"></a>SSH에서 실행 되는 포트를 확인 합니다.
SSH에서 실행 되는 포트를 확인 하려면 다음 명령을 사용할 수 있습니다.
```
$ sudo grep Port /etc/ssh/sshd_config
```
출력 같이 표시 됩니다.
```
Port 22
```

## <a name="use-the-azure-cli"></a>Azure CLI 사용
아직 설치하지 않은 경우 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.

사용자 지정 Linux 디스크 이미지를 만들고 업로드한 경우 [Microsoft Azure Linux 에이전트](../extensions/agent-windows.md) 버전 2.0.5 이상을 설치해야 합니다. 갤러리 이미지를 사용하여 만든 VM의 경우 이 액세스 확장이 이미 설치되어 자동으로 구성됩니다.

### <a name="reset-ssh-configuration"></a>SSH 구성 다시 설정
처음에 SSH 구성을 기본값으로 다시 설정하고 VM에서 SSH 서버를 다시 부팅할 수 있습니다. 사용자 계정 이름, 암호 또는 SSH 키는 변경되지 않습니다.
다음 예제에서는 [az vm user reset-ssh](/cli/azure/vm/user)를 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 SSH 구성을 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
다음 예제에서는 [az vm user update](/cli/azure/vm/user)를 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH 키 인증을 사용하는 경우 지정된 사용자의 SSH 키를 다시 설정할 수 있습니다. 다음 예제에서는 **az vm access set-linux-user**를 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 `~/.ssh/id_rsa.pub`이라는 사용자의 `myUsername`에 저장된 SSH 키를 업데이트합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>VMAccess 확장 사용
Linux용 VM 액세스 확장은 수행하는 작업을 정의하는 json 파일에서 읽습니다. 이러한 작업에는 SSHD 다시 설정, SSH 키를 다시 설정 또는 사용자 추가가 포함됩니다. Azure CLI를 사용하여 VMAccess 확장을 호출할 수 있지만 원하는 경우 여러 VM에 걸쳐 json 파일을 재사용할 수 있습니다. 이 방법을 통해 주어진 시나리오에 대해 호출될 수 있는 json 파일의 레포지토리를 만들 수 있습니다.

### <a name="reset-sshd"></a>SSHD 재설정
다음과 같은 내용으로 `settings.json`라는 파일을 만듭니다.

```json
{
    "reset_ssh":"True"
}
```

Azure CLI를 사용하여 json 파일을 지정하여 SSHD 연결을 다시 설정하도록 `VMAccessForLinux` 확장을 호출합니다. 다음 예제에서는 [az vm extension set](/cli/azure/vm/extension)을 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 SSHD를 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
SSHD가 정상적으로 작동하는 것으로 나타나면 지정된 사용자의 자격 증명을 다시 설정할 수 있습니다. 사용자에 대한 암호를 재설정하려면 `settings.json`라는 파일을 만듭니다. 다음 예제에서는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 고유한 값을 사용하여 다음 줄을 `settings.json` 파일에 입력합니다.

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

또는 사용자에 대한 SSH 키를 다시 설정하려면 먼저 `settings.json` 파일을 만듭니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 고유한 값을 사용하여 다음 줄을 `settings.json` 파일에 입력합니다.

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

json 파일을 만든 후에 json 파일을 지정하여 SSH 사용자 자격 증명을 다시 설정하는 `VMAccessForLinux` 확장을 호출하도록 Azure CLI를 사용합니다. 다음 예제에서는 `myResourceGroup`의 VM `myVM`에서 자격 증명을 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Azure 클래식 CLI 사용
아직 수행하지 않은 경우 [Azure 클래식 CLI를 설치하고 Azure 구독에 연결](../../cli-install-nodejs.md)합니다. 다음과 같이 Resource Manager 모드를 사용하는지 확인합니다.

```azurecli
azure config mode arm
```

사용자 지정 Linux 디스크 이미지를 만들고 업로드한 경우 [Microsoft Azure Linux 에이전트](../extensions/agent-windows.md) 버전 2.0.5 이상을 설치해야 합니다. 갤러리 이미지를 사용하여 만든 VM의 경우 이 액세스 확장이 이미 설치되어 자동으로 구성됩니다.

### <a name="reset-ssh-configuration"></a>SSH 구성 다시 설정
SSHD 구성 자체가 잘못 구성되었거나 서비스에서 오류가 발생했습니다. SSH 구성 자체가 올바르도록 SSHD를 다시 설정할 수 있습니다. SSHD 다시 설정은 수행한 첫 번째 문제 해결 단계여야 합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에서 SSHD를 다시 설정합니다. 다음과 같이 고유한 VM 및 리소스 그룹 이름을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
SSHD가 정상적으로 작동하는 것으로 나타나면 지정된 사용자의 암호를 다시 설정할 수 있습니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH 키 인증을 사용하는 경우 지정된 사용자의 SSH 키를 다시 설정할 수 있습니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername` 사용자를 위해 `~/.ssh/id_rsa.pub`에 저장된 SSH 키를 업데이트합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />VM 다시 시작
SSH 구성 및 사용자 자격 증명을 다시 설정했거나 그 과정에서 오류가 발생한 경우 VM을 다시 시작하여 기본 계산 문제를 해결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal
Azure Portal을 사용하여 VM을 다시 시작하려면 다음 예제와 같이 VM을 선택한 다음, **다시 시작**을 선택합니다.

![Azure Portal에서 VM 다시 시작](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
다음 예제에서는 [az vm restart](/cli/azure/vm)를 사용하여 `myResourceGroup`이라는 리소스 그룹의 `myVM`이라는 VM을 다시 시작합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 클래식 CLI
다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 시작합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />VM 다시 배포
Azure 내의 다른 노드로 VM을 재배포하여 기본 네트워킹 문제를 해결할 수 있습니다. VM을 다시 배포하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 머신 다시 배포](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

> [!NOTE]
> 이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 머신과 연결된 동적 IP 주소가 업데이트됩니다.
>
>

### <a name="azure-portal"></a>Azure portal
Azure Portal을 사용하여 VM을 다시 배포하려면 VM을 선택하고 **지원 + 문제 해결** 섹션까지 아래로 스크롤합니다. 다음 예제와 같이 **다시 배포**를 선택합니다.

![Azure Portal에서 VM 다시 배포](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
다음 예제에서는 [az vm redeploy](/cli/azure/vm)를 사용하여 `myResourceGroup`이라는 리소스 그룹의 `myVM`이라는 VM을 다시 배포합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 클래식 CLI
다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 배포합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 VM
클래식 배포 모델을 사용하여 만든 VM의 보다 일반적인 SSH 연결 오류를 해결하려면 다음 단계를 시도합니다. 각 단계 후 VM에 다시 연결을 시도합니다.

* [Azure Portal](https://portal.azure.com)에서 원격 액세스를 다시 설정합니다. Azure Portal에서 VM을 선택한 다음, **원격 다시 설정...** 을 선택합니다.
* VM을 다시 시작합니다. [Azure Portal](https://portal.azure.com)에서 VM을 선택하고 **다시 시작**을 선택합니다.

* 새 Azure 노드에 VM을 다시 배포합니다. VM을 다시 배포하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 머신 다시 배포](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

    이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
* [Linux 기반 가상 머신의 암호 또는 SSH를 다시 설정하는 방법](../linux/classic/reset-access-classic.md)의 지침을 따르세요.

  * 암호 또는 SSH 키를 재설정합니다.
  * 새 *sudo* 사용자 계정을 만듭니다.
  * SSH 구성을 재설정합니다.
* VM 리소스 상태에 플랫폼 문제가 있는지 확인합니다.<br>
     VM을 선택하고 **설정** > **상태 확인**까지 아래로 스크롤합니다.

## <a name="additional-resources"></a>추가 리소스
* 후속 단계를 수행한 후에도 VM에 대해 SSH를 사용할 수 없는 경우 [자세한 문제 해결 단계](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하여 단계를 검토하고 문제를 해결할 수 있습니다.
* 애플리케이션 액세스 문제를 해결하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 실행 중인 애플리케이션에 대한 액세스 문제 해결](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
* 클래식 배포 모델을 사용하여 만든 가상 머신의 문제 해결 방법에 대한 자세한 내용은 [Linux 기반 가상 머신의 암호 또는 SSH를 다시 설정하는 방법](../linux/classic/reset-access-classic.md)을 참조하세요.
