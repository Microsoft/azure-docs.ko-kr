---
title: '자습서: Azure PowerShell을 사용하여 Windows VM 만들기 및 관리'
description: 이 자습서에서는 Azure PowerShell을 사용하여 Azure에서 Windows VM을 만들고 관리하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 65116f979131590a66ff2f08896e7a89ae075303
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074154"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows VM 만들기 및 관리

Azure Virtual Machines는 완전하게 구성할 수 있고 유연한 컴퓨팅 환경을 제공합니다. 이 자습서에서는 VM 크기 선택, VM 이미지 선택 및 VM 배포 등 기본적인 Azure VM(가상 머신) 배포 작업을 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신보다 먼저 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *EastUS* 지역에 *myResourceGroupVM*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

리소스 그룹은 VM을 만들거나 수정할 때 지정되며 이 자습서 전체에서 확인할 수 있습니다.

## <a name="create-a-vm"></a>VM 만들기

VM을 만들 때 운영 체제 이미지, 네트워크 구성 및 관리 자격 증명과 같은 몇 가지 옵션을 사용할 수 있습니다. 이 예에서는 Windows Server 2016 Datacenter의 기본 버전을 실행하는 *myVM*이라는 VM을 만듭니다.

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6)을 사용하여 VM의 컴퓨터의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>VM에 연결

배포가 완료된 후 VM에 대한 원격 데스크톱 연결을 만듭니다.

다음 명령을 실행하여 VM의 공용 IP 주소를 반환합니다. 다음 단계에서 웹 연결을 테스트하기 위해 브라우저와 연결할 수 있도록 이 IP 주소를 기록해 둡니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

다음 명령을 사용하여 로컬 머신에서 VM과의 원격 데스크톱 세션을 만듭니다. IP 주소를 VM의 *publicIPAddress*로 바꿉니다. VM을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```powershell
mstsc /v:<publicIpAddress>
```

**Windows 보안** 창에서 **추가 선택 사항** 및 **다른 계정 사용**을 차례로 선택합니다. VM에 대해 만든 사용자 이름 및 암호를 입력한 다음, **확인**을 클릭합니다.

## <a name="understand-marketplace-images"></a>마켓플레이스 이미지 이해

Azure Marketplace에는 새 VM을 만드는 데 사용할 수 있는 여러 VM 이미지가 포함되어 있습니다. 이전 단계에서는 Windows Server 2016 Datacenter 이미지를 사용하여 VM을 만들었습니다. 이 단계에서는 PowerShell 모듈을 사용하여 Marketplace에서 새 VM의 기반이 될 수도 있는 다른 Windows 이미지를 검색합니다. 이 프로세스는 이미지를 [식별](cli-ps-findimage.md#terminology)하기 위한 게시자, 제품, SKU 및 버전 번호(선택 사항)로 구성되어 있습니다.

[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 명령을 사용하여 이미지 게시자 목록을 반환합니다.

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

이미지 제안 목록을 반환하려면 [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)를 사용합니다. 이 명령을 사용하면 반환된 목록이 `MicrosoftWindowsServer`라는 지정된 게시자를 기준으로 필터링됩니다.

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

결과가 다음 예제와 같이 표시됩니다. 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

그런 다음, [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) 명령으로 게시자 및 제안 이름을 기준으로 필터링하여 이미지 이름 목록을 반환합니다.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

결과가 다음 예제와 같이 표시됩니다. 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

이 정보를 사용하여 특정 이미지가 있는 VM을 배포할 수 있습니다. 이 예제에서는 컨테이너 이미지가 있는 Windows Server 2016의 최신 버전을 사용하여 VM을 배포합니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

`-AsJob` 매개 변수는 VM을 백그라운드 작업으로 만들므로 PowerShell 프롬프트가 반환됩니다. `Get-Job` cmdlet을 사용하여 백그라운드 작업의 세부 정보를 볼 수 있습니다.

## <a name="understand-vm-sizes"></a>VM 크기 이해

VM 크기에 따라 CPU, GPU, 메모리 등 VM에 사용할 수 있는 컴퓨팅 리소스의 양이 결정됩니다. 가상 머신은 워크로드에 맞는 VM 크기로 만들어야 합니다. 워크로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수도 있습니다.

### <a name="vm-sizes"></a>VM 크기

다음 표에서는 크기를 사용 사례로 분류합니다.  

| Type                     | 일반적인 크기           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [범용](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU 대 메모리 비율이 적당합니다. 개발/테스트와 소규모에서 중간 정도의 애플리케이션 및 데이터 솔루션에 적합합니다.  |
| [컴퓨팅 최적화](../sizes-compute.md)   | Fsv2          | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 애플리케이션, 네트워크 어플라이언스 및 일괄 처리 프로세스에 적합합니다.        |
| [메모리에 최적화](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | 메모리 대 코어 비율이 높습니다. 관계형 데이터베이스, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](../sizes-storage.md)      | Lsv2, Ls              | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 VM입니다.       |
| [고성능](../sizes-hpc.md) | H        | 당사의 가장 강력한 CPU VM으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. |

### <a name="find-available-vm-sizes"></a>사용 가능한 VM 크기 찾기

특정 영역에서 사용할 수 있는 VM 크기의 목록을 보려면 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>VM 크기 조정

VM을 배포한 후에 크기를 조정하여 리소스 할당을 늘리거나 줄일 수 있습니다.

VM의 크기를 조정하기 원하는 크기를 현재 VM 클러스터에서 사용 가능한지 확인합니다. [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 명령은 크기 목록을 반환합니다.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

크기를 사용할 수 있는 경우 전원이 켜진 상태에서 VM 크기를 조정할 수 있지만 작업 중 다시 부팅됩니다.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

원하는 크기를 현재 클러스터에 사용할 수 없는 경우 VM 할당을 취소해야 크기 조정 작업이 가능합니다. VM 할당을 취소하면 임시 디스크의 모든 데이터가 제거되고 고정 IP 주소를 사용하지 않는 한 공용 IP 주소가 변경됩니다.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>VM 전원 상태

Azure VM의 전원 상태는 여러 상태 중 하나일 수 있습니다. 


| 전원 상태 | Description
|----|----|
| 시작 중 | 가상 머신이 시작되고 있습니다. |
| 실행 중 | 가상 머신이 실행되고 있습니다. |
| 중지 중 | 가상 머신이 중지되고 있습니다. |
| 중지됨 | VM이 중지됩니다. 중지 상태의 가상 머신에도 여전히 컴퓨팅 요금이 발생됩니다.  |
| 할당 취소 중 | VM의 할당이 취소되고 있습니다. |
| 할당 취소됨 | VM이 하이퍼바이저에서 제거되었지만 제어 영역에서 계속 사용할 수 있음을 나타냅니다. `Deallocated` 상태의 가상 머신에는 컴퓨팅 요금이 발생하지 않습니다. |
| - | VM의 전원 상태를 알 수 없습니다. |


특정 VM의 상태를 검색하려면 [Get-AzVM](/powershell/module/az.compute/get-azvm) 명령을 사용합니다. VM 및 리소스 그룹에 대한 올바른 이름을 지정해야 합니다.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

출력은 다음 예와 같이 표시됩니다.

```powershell
Status
------
PowerState/running
```

구독에 있는 모든 VM의 전원 상태를 검색하려면 매개 변수 **statusOnly**가 *true*로 설정된 [Virtual Machines - 모든 API 목록](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)을 사용합니다.

## <a name="management-tasks"></a>관리 작업

VM의 수명 주기 동안 VM 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 스크립트를 만들 수도 있습니다. Azure PowerShell을 사용하면 명령줄이나 스크립트로 여러 가지 일반적인 관리 작업을 실행할 수 있습니다.

### <a name="stop-a-vm"></a>VM 중지

[Stop-AzVM](/powershell/module/az.compute/stop-azvm)을 사용하여 VM을 중지하고 할당을 취소합니다.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

VM을 프로비전된 상태로 유지하려면 -StayProvisioned 매개 변수를 사용합니다.

### <a name="start-a-vm"></a>VM 시작

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>리소스 그룹 삭제

리소스 그룹을 삭제하면 리소스 그룹 내부의 모든 항목이 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법과 같이 기본 VM을 만들고 관리하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * VM 크기 조정
> * VM 상태 보기 및 이해

VM 디스크에 대해 자세히 알아보려면 다음 자습서로 이동합니다.  

> [!div class="nextstepaction"]
> [VM 디스크 만들기 및 관리](./tutorial-manage-data-disk.md)
