---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure PowerShell
titlesuffix: Azure Virtual Network
description: 이 빠른 시작에서는 Azure Portal을 사용하여 가상 네트워크를 만듭니다. 가상 네트워크를 통해 Azure 리소스가 서로 통신하고 인터넷과 통신할 수 있습니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93e459df96d444e71f4b6a15668f80e9d77db5fd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077887"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>빠른 시작: PowerShell을 사용하여 가상 네트워크 만들기

가상 네트워크를 사용하면 VM(가상 머신)과 같은 Azure 리소스가 서로 인터넷을 통해 비공개로 통신할 수 있습니다. 이 빠른 시작에서는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 만든 후에 두 개의 VM을 가상 네트워크에 배포합니다. 그런 다음, 인터넷에서 VM에 연결하고 가상 네트워크를 통해 비공개로 통신합니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 PowerShell을 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상을 사용해야 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 및 업그레이드 정보는 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

마지막으로, PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`도 실행해야 합니다. 해당 명령은 Azure와 연결을 만듭니다.

## <a name="create-a-resource-group-and-a-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기

리소스 그룹 및 가상 네트워크를 구성하기 위해 수행해야 하는 몇 가지 단계가 있습니다.

### <a name="create-the-resource-group"></a>리소스 그룹 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크를 호스트할 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>서브넷 추가

Azure는 가상 네트워크 내의 서브넷에 리소스를 배포하므로 서브넷을 만들어야 합니다. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)를 사용하여 *default*라는 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>가상 네트워크에 서브넷 연결

[Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)를 사용하여 가상 네트워크에 서브넷 구성을 쓸 수 있습니다. 다음 명령은 서브넷을 만듭니다.

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 첫 번째 VM을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. VM의 사용자 이름 및 암호를 입력합니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

`-AsJob` 옵션은 백그라운드에서 VM을 만듭니다. 다음 단계를 계속 진행할 수 있습니다.

Azure가 백그라운드에서 VM을 만들기 시작하면 다음과 같은 메시지가 표시됩니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

다음 명령을 사용하여 두 번째 VM을 만듭니다.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

다른 사용자와 암호를 만들어야 합니다. Azure에서 VM을 만드는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> Azure에서 작업을 마칠 때까지 다음 단계를 진행하지 마세요.  PowerShell에 출력이 반환되면 작업이 완료되었음을 알 수 있습니다.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVm1* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

로컬 컴퓨터에서 명령 프롬프트를 엽니다. `mstsc` 명령을 실행합니다. `<publicIpAddress>`를 마지막 단계에서 반환된 공용 IP 주소로 바꿉니다.

> [!NOTE]
> 로컬 컴퓨터의 PowerShell 프롬프트에서 이러한 명령을 실행했으며 Az PowerShell 모듈 버전 1.0 이상을 사용 중이면 해당 인터페이스에서 계속 진행할 수 있습니다.

```cmd
mstsc /v:<publicIpAddress>
```
1. 메시지가 표시되면 **연결**을 선택합니다.

1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

    > [!NOTE]
    > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 인증서 경고가 표시될 수도 있습니다. 표시되는 경우 **예** 또는 **계속**을 선택합니다.

## <a name="communicate-between-vms"></a>VM 간 통신

1. *myVm1*의 원격 데스크톱에서 PowerShell을 엽니다.

1. `ping myVm2`를 입력합니다.

    다음과 같은 메시지가 반환됩니다.

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    ping이 ICMP(Internet Control Message Protocol)를 사용하기 때문에 ping에 실패합니다. 기본적으로 ICMP는 Windows 방화벽에서 허용되지 않습니다.

1. 이후 단계에서 *myVm2*가 *myVm1*을 ping할 수 있도록 하려면 다음 명령을 입력합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    이 명령은 Windows 방화벽에서 ICMP 인바운드를 허용합니다.

1. *myVm1*에 대한 원격 데스크톱 연결을 닫습니다.

1. [인터넷에서 VM에 연결](#connect-to-a-vm-from-the-internet)의 단계를 반복합니다. 이번에는 *myVm2*에 연결합니다.

1. *myVm2* VM의 명령 프롬프트에서 `ping myvm1`을 입력합니다.

    다음과 같은 메시지가 반환됩니다.

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    이전 단계에서 *myVm1* VM의 Windows 방화벽을 통해 ICMP를 허용했으므로 *myVm1*에서 회신을 받습니다.

1. *myVm2*에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크 및 VM 작업을 마쳤으면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 두 VM 간에 비공개로 통신했습니다.
Azure는 VM 간에 무제한 프라이빗 통신을 허용합니다. 기본적으로 Azure는 인터넷에서 Windows VM으로의 인바운드 원격 데스크톱 연결만 허용합니다. 다양한 유형의 VM 네트워크 통신 구성에 대해 자세히 알아보려면 다음 문서로 이동하세요.
> [!div class="nextstepaction"]
> [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)
