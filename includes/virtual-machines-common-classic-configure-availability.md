---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485354"
---
가용성 집합을 사용하면 유지 관리 중과 같은 가동 중지 시간 동안에도 가상 머신을 계속 사용할 수 있습니다. 가용성 집합 내에 비슷하게 구성된 둘 이상의 가상 머신을 배치하면 가상 머신이 실행하는 서비스나 애플리케이션의 가용성을 유지하는 데 필요한 중복성이 생성됩니다. 이 기능이 작동하는 방식에 대한 자세한 내용은 [가상 머신의 가용성 관리][Manage the availability of virtual machines]를 참조하세요.

애플리케이션을 항상 사용 가능한 상태로 유지하고 효과적으로 실행하려면 가용성 집합과 부하 분산 엔드포인트를 함께 사용하는 것이 가장 좋습니다. 부하가 분산된 엔드포인트에 대한 자세한 내용은 [Azure 인프라 서비스를 위한 부하 분산][Load balancing for Azure infrastructure services]을 참조하세요.

다음 두 옵션 중 하나를 사용하여 클래식 가상 머신을 가용성 집합에 추가할 수 있습니다.

* [옵션 1: 가상 컴퓨터를 만들고 가용성 집합 동시][Option 1: Create a virtual machine and an availability set at the same time]합니다. 그런 다음 새 가상 머신을 만들 때 집합에 가상 머신을 추가합니다.
* [옵션 2: 기존 가상 머신을 가용성 집합에 추가][Option 2: Add an existing virtual machine to an availability set]합니다.

> [!NOTE]
> 클래식 모델에서는 같은 가용성 집합에 배치할 가상 머신은 같은 클라우드 서비스에 속해야 합니다.
> 
> 

## <a id="createset"> </a>옵션 1: 가상 머신과 가용성 집합을 동시에 만들기
Azure 포털 또는 Azure PowerShell 명령을 사용하여 이 작업을 수행할 수 있습니다.

Azure 포털을 사용하려면

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기** > **계산**를 클릭합니다.
3. 사용하려는 Marketplace 가상 머신 이미지를 선택합니다. Linux 또는 Windows 가상 머신을 만들도록 선택할 수 있습니다.
4. 선택한 가상 머신에 대해 배포 모델이 **클래식**으로 설정되어 있는지 확인한 다음 **만들기**를 클릭합니다.
   
    ![대체 이미지 텍스트](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. 가상 머신 이름, 사용자 이름 및 암호(Window 컴퓨터) 또는 SSH 공개 키(Linux 컴퓨터)를 입력합니다. 
6. VM 크기를 선택하고 **선택** 을 클릭하여 진행합니다.
7. **선택적 구성 > 가용성 집합**을 선택하고 가상 머신을 추가하려는 가용성 집합을 선택합니다.
   
    ![대체 이미지 텍스트](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. 구성 설정을 검토합니다. 완료하면 **만들기**를 클릭합니다.
9. Azure에서 가상 머신을 만드는 동안 허브 메뉴의 **Virtual Machines**에서 진행 상황을 추적할 수 있습니다.

Azure PowerShell 명령을 사용하여 Azure 가상 머신을 만들고 새로운 또는 기존 가용성 집합에 추가하려면 [Azure PowerShell을 사용하여 Windows 기반 가상 머신 만들기 및 미리 구성](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>옵션 2: 기존 가상 머신을 가용성 집합에 추가
Azure 포털에서 기존 클래식 가상 머신을 기존 가용성 집합에 추가하거나 가상 머신용으로 새 가용성 집합을 만들 수 있습니다. 동일한 가용성 집합의 가상 머신은 같은 클라우드 서비스에 속해야 합니다. 이 옵션에서도 단계는 거의 동일합니다. Azure PowerShell에서 기존 가용성 집합에 가상 머신을 추가할 수 있습니다.

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines(클래식)** 를 클릭합니다.
   
    ![대체 이미지 텍스트](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. 가상 머신 목록에서 집합에 추가할 가상 머신 이름을 선택합니다.
4. 가상 머신 **설정**에서 **가용성 집합**을 선택합니다.
   
    ![대체 이미지 텍스트](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. 가상 머신을 추가하려는 가용성 집합을 선택합니다. 가상 머신은 가용성 집합과 동일한 클라우드 서비스에 속해야 합니다.
   
    ![대체 이미지 텍스트](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. **저장**을 클릭합니다.

Azure PowerShell 명령을 사용하려면 관리자 수준의 Azure PowerShell 세션을 열고 다음 명령을 실행합니다. 자리 표시자(예: &lt;VmCloudServiceName&gt;)의 경우 < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> 가용성 집합에 추가하는 작업을 완료하기 위해 가상 머신을 다시 시작해야 할 수 있습니다.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

