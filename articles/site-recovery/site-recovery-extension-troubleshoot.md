---
title: Azure Site Recovery 에이전트를 사용하여 문제 해결 | Microsoft Docs
description: Azure Site Recovery 에이전트 오류의 증상, 원인 및 해결 방법에 대한 정보를 제공합니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280674"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Azure Site Recovery 에이전트를 사용하여 문제 해결

이 문서에서는 VM 에이전트 및 확장과 관련된 Azure Site Recovery 오류를 해결하는 데 도움이 될 수 있는 문제 해결 단계를 제공합니다.


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery 확장 시간 초과  

오류 메시지: “시작할 확장 작업을 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151076”

 Azure Site Recovery에서는 보호 사용 작업의 일부로 가상 머신에 확장을 설치합니다. 다음 조건 중 하나라도 충족되지 않을 경우 보호 기능이 트리거되지 않고 작업이 실패할 수 있습니다. 다음 문제 해결 단계를 완료한 후, 작업을 다시 시도하세요.

**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [Site Recovery 확장을 업데이트 또는 로드하지 못합니다.](#the-site-recovery-extension-fails-to-update-or-load)**  

오류 메시지: “이전 Site Recovery 확장 작업이 예상보다 더 오래 걸립니다.”<br>
오류 코드: “150066”<br>

**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [Site Recovery 확장 상태가 잘못되었습니다.](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>VM 에이전트가 응답하지 않으므로 보호 기능이 실패합니다.

오류 메시지: “시작할 확장 작업을 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151099”<br>

이 오류는 가상 머신의 Azure 게스트 에이전트가 준비 상태가 아닌 경우에 발생할 수 있습니다.
[Azure Portal](https://portal.azure.com/)에서 Azure 게스트 에이전트의 상태를 확인할 수 있습니다. 보호하려는 가상 머신으로 이동한 다음, “VM > 설정 > 속성 > 에이전트 상태”에서 상태를 확인합니다. 가상 머신을 다시 부팅한 후에는 대체로 에이전트가 준비 상태가 됩니다. 그러나 다시 부팅할 수 없거나 여전히 문제가 발생하는 경우 다음 문제 해결 단계를 완료합니다.

**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


오류 메시지: “시작할 확장 작업을 추적하는 동안 작업 실행 시간이 초과되었습니다.”<br>
오류 코드: “151095”<br>

이 오류는 Linux 머신의 에이전트 버전이 오래된 경우에 발생합니다. 다음 문제 해결 단계를 완료합니다.<br>
  **원인 1: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>해결 방법
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 “Microsoft Azure 게스트 에이전트 서비스”가 실행 중인지 확인합니다. “Microsoft Azure 게스트 에이전트 서비스”를 다시 시작합니다.    
2. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되지 않으면 제어판에서 **프로그램 및 기능**으로 이동하여 Windows 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
4. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능**에 표시되면 Windows 게스트 에이전트를 제거합니다.
5. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
6. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
7. 보호 작업을 다시 시작합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. .NET 4.5는 VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   * Ubuntu의 경우: `service walinuxagent start`
   * 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 가상 머신의 보호를 사용하도록 설정합니다.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery 확장을 업데이트 또는 로드하지 못함
확장 상태가 ‘Empty’, ’NotReady’ 또는 ‘Transitioning’인 경우입니다.

#### <a name="solution"></a>해결 방법

확장을 제거하고 작업을 다시 시작합니다.

확장을 제거하려면

1. [Azure Portal](https://portal.azure.com/)에서 백업 실패가 발생하는 VM으로 이동합니다.
2. **설정**을 선택합니다.
3. **확장**을 섡택합니다.
4. **Site Recovery 확장**을 선택합니다.
5. **제거**를 선택합니다.

Linux VM의 경우 VMSnapshot 확장이 Azure Portal에 표시되지 않으면 [Azure Linux 에이전트를 업데이트](../virtual-machines/linux/update-agent.md)한 다음, 보호 기능을 실행합니다. 

이러한 단계를 완료하면 보호 기능을 사용하는 동안 확장이 다시 설치됩니다.


