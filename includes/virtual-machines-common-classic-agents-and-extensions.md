---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9158e6bfe07fc5d06b0685d77eff26644b594a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485275"
---
VM 확장은 다음과 같은 작업에 도움이 될 수 있습니다.

* 보안 및 ID 기능 수정(예: 계정 값 재설정 및 맬웨어 방지 프로그램)
* 모니터링 및 진단 시작, 중지 또는 구성
* 연결 기능 재설정 또는 설치(예: RDP 및 SSH)
* VM 진단, 모니터링 및 관리

많은 다른 기능도 있습니다. 새 VM 확장 기능은 정기적으로 릴리스됩니다. 이 문서에서는 Windows 및 Linux용 Azure VM 에이전트와, 해당 에이전트의 VM 확장 기능 지원 방법에 대해 설명합니다. 기능 범주별 VM 확장 목록은 [Azure VM 확장 및 기능](../articles/virtual-machines/extensions/features-windows.md)을 참조하세요.

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows 및 Linux 용 Azure VM 에이전트
Azure VM 에이전트(Virtual Machines 에이전트)는 Azure Virtual Machines의 인스턴스에 대한 VM 확장을 설치, 구성 및 제거하는 안전하고 가벼운 프로세스입니다. VM 에이전트는 Azure VM에 대해 안전한 로컬 제어 서비스 형태로 작동합니다. 에이전트가 로드하는 확장은 인스턴스를 사용한 생산성을 증대하기 위한 특정 기능을 제공합니다.

Windows VM용 에이전트와 Linux VM용 에이전트 등, 두 가지 Azure VM 에이전트가 있습니다.

가상 머신 인스턴스에서 하나 이상의 VM 확장을 사용하길 원하는 경우 인스턴스에 VM 에이전트가 설치되어 있어야 합니다. Azure Portal 및 **Marketplace**의 이미지를 사용하여 만든 가상 머신 이미지는 생성 프로세스에서 VM 에이전트를 자동으로 설치합니다. 가상 컴퓨터 인스턴스에 VM 에이전트가 없는 경우 가상 컴퓨터 인스턴스가 만들어진 후 VM 에이전트를 설치할 수 있습니다. 또는 다음으로 업로드하는 사용자 지정 VM 이미지에 에이전트를 설치할 수 있습니다.

> [!IMPORTANT]
> 이러한 VM 에이전트는 가상 머신 인스턴스의 보안 관리를 구현하는 매우 가벼운 서비스입니다. VM 에이전트를 원하지 않는 경우도 있을 수 있습니다. 이 경우에는 Azure CLI 또는 PowerShell을 사용하여 설치된 VM 에이전트가 없는 VM을 만들어야 합니다. VM 에이전트를 물리적으로 제거할 수는 있지만 인스턴스에서 VM 확장의 동작은 정의되지 않은 상태입니다. 결과적으로, 설치된 VM 에이전트의 제거는 지원되지 않습니다.
>

VM 에이전트는 다음과 같은 상황에서 사용됩니다.

* Azure Portal을 사용하고 **Marketplace**에서 이미지를 선택하여 VM의 인스턴스를 만들 때,
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) 또는 [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) cmdlet을 사용하여 VM의 인스턴스를 만들 때. **–DisableGuestAgent** 매개 변수를 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) cmdlet에 추가하면 VM 에이전트 없이 VM을 만들 수 있습니다.

* 기존 VM 인스턴스에서 VM 에이전트를 수동으로 다운로드하여 설치하고 **ProvisionGuestAgent** 값을 **true**로 설정할 때. PowerShell 명령 또는 REST를 호출을 사용하면 Windows 및 Linux 에이전트를 위한 이 명령을 사용할 수 있습니다. (VM 에이전트를 수동으로 설치한 후 **ProvisionGuestAgent** 값을 설정하지 않으면 VM 에이전트의 추가 내용이 제대로 검색되지 않습니다.) 다음 코드 예제에서는 `$svc` 및 `$name` 인수가 이미 결정된 상황에서 PowerShell을 사용하여 이 작업을 수행하는 방법을 보여 줍니다.

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* 설치된 VM 에이전트를 포함하는 VM 이미지를 만들 때. VM 에이전트가 있는 이미지가 존재하면 해당 이미지를 Azure에 업로드할 수 있습니다. Windows VM의 경우 [Windows VM 에이전트 .msi 파일](https://go.microsoft.com/fwlink/?LinkID=394789)을 다운로드하여 VM 에이전트를 설치합니다. Linux VM의 경우 <https://github.com/Azure/WALinuxAgent>에 있는 GitHub 리포지토리에서 VM 에이전트를 설치합니다. Linux에서 VM 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure Linux VM 에이전트 사용자 가이드](../articles/virtual-machines/extensions/agent-linux.md)를 참조하세요.

> [!NOTE]
> PaaS에서는 VM 에이전트를 **WindowsAzureGuestAgent**라고 하며 웹 및 작업자 역할 VM에서 항상 사용할 수 있습니다. (자세한 내용은 [Azure 역할 아키텍처](https://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)를 참조하세요.) 역할 VM에 대한 VM 에이전트는 이제 영구적 Virtual Machines와 동일한 방식으로 클라우드 서비스 VM에 확장을 추가할 수 있습니다. 영구 VM과 역할 VM의 VM 확장 간 가장 큰 차이점은 VM 확장을 추가할 때입니다. 역할 VM의 경우 확장은 클라우드 서비스에 처음 추가된 다음, 해당 클라우드 서비스 내 배포로 이어집니다.
>
> 사용 가능한 모든 역할 VM 확장을 나열하려면 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet을 사용합니다.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>VM 확장 찾기, 추가, 업데이트 및 제거
이러한 작업에 대한 자세한 내용은 [Azure VM 확장 추가, 찾기, 업데이트 및 제거](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.
