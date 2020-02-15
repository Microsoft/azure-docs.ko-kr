---
title: VNet 피어 링을 사용 하 여 가상 네트워크 연결-Azure PowerShell
description: 이 문서에서는 Azure PowerShell을 사용하여 가상 네트워크 피어링으로 가상 네트워크를 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201308"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>PowerShell을 사용하여 가상 네트워크 피어링으로 가상 네트워크 연결

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

가상 네트워크 피어링을 사용하여 가상 네트워크를 서로 연결할 수 있습니다. 가상 네트워크가 피어링되면 두 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대기 시간 및 대역폭으로 서로 통신할 수 있습니다. 이 문서에서는 다음 방법을 설명합니다.

* 두 가상 네트워크 만들기
* 가상 네트워크 피어링을 사용하여 두 가상 네트워크 연결
* 각 가상 네트워크에 VM(가상 머신) 배포
* VM 간 통신

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork1*이라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

[AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)를 사용 하 여 서브넷 구성을 만듭니다. 다음 예제에서는 10.0.0.0/24 주소 접두사가 포함된 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

서브넷을 만드는 [AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)를 사용 하 여 가상 네트워크에 서브넷 구성을 씁니다.

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

10.1.0.0/16 주소 접두사 및 서브넷 1개를 사용하여 가상 네트워크를 만듭니다.

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>가상 네트워크 피어링

[AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)를 사용 하 여 피어 링을 만듭니다. 다음 예제에서는 *myVirtualNetwork1*을 *myVirtualNetwork2*에 피어링합니다.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

이전 명령 실행 후 반환된 출력에서 **PeeringState**는 *Initiated*로 표시됩니다. *myVirtualNetwork2*에서 *myVirtualNetwork1*으로 피어링을 만들 때까지 해당 피어링은 *Initiated* 상태를 유지합니다. *myVirtualNetwork2*에서 *myVirtualNetwork1*으로 피어링을 만듭니다.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

이전 명령 실행 후 반환된 출력에서 **PeeringState**는 *Connected*로 표시됩니다. 또한 Azure에서 *myVirtualNetwork1-myVirtualNetwork2* 피어링의 피어링 상태가 *Connected*로 변경되었습니다. *MyVirtualNetwork1-myVirtualNetwork2* 피어 링의 피어 링 상태가 [AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering)와 *연결* 됨으로 변경 되었는지 확인 합니다.

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

두 가상 네트워크의 피어링에 대한 **PeeringState**가 *Connected*가 될 때까지, 한 가상 네트워크의 리소스는 다른 가상 네트워크의 리소스와 통신할 수 없습니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 서로 통신할 수 있도록 각 가상 네트워크에서 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다. 다음 예제에서는 *myVirtualNetwork1* 가상 네트워크에 이름이 *myVm1*인 VM을 만듭니다. `-AsJob` 옵션은 백그라운드에서 VM을 만들기 때문에 다음 단계를 계속 진행할 수 있습니다. 메시지가 나타나면 VM에 로그인할 사용자 이름과 암호를 입력합니다.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

VM을 만드는 데 몇 분이 걸립니다. Azure에서 VM을 만들고 출력을 PowerShell로 반환할 때까지 이후 단계를 계속하지 마세요.

## <a name="communicate-between-vms"></a>VM 간 통신

인터넷에서 VM의 공용 IP 주소에 연결할 수 있습니다. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVm1* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 *myVm1* VM과의 원격 데스크톱 세션을 만듭니다. `<publicIpAddress>`를 이전 명령에서 반환된 IP 주소로 바꿉니다.

```
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 마들어지고 컴퓨터에 다운로드된 후 열립니다. 사용자 이름과 암호를 입력(VM을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있음)한 다음, **확인**을 클릭합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

*myVm1* VM에서 Windows 방화벽을 통해 ICMP(Internet Control Message Protocol)를 사용하도록 설정하면 이후 단계에서 PowerShell을 사용하여 *myVm2*에서 이 VM을 ping할 수 있습니다.

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

이 문서에서는 VM 간 통신에 ping이 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.

*myVm2* VM에 연결하려면 *myVm1* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

```
mstsc /v:10.1.0.4
```

*myVm1*에서 ping을 사용하도록 설정했으므로 이제 *myVm2* VM의 명령 프롬프트에서 IP 주소로 ping할 수 있습니다.

```
ping 10.0.0.4
```

4개의 응답을 받게 됩니다. *myVm1* 및 *myVm2*에 대한 RDP 세션 연결을 모두 끊습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 경우 [AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) 를 사용 하 여 리소스 그룹 및 포함 된 모든 리소스를 제거 합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크 피어링을 사용하여 동일한 Azure 지역에 있는 두 네트워크를 연결하는 방법을 배웠습니다. 다른 [지원되는 지역](virtual-network-manage-peering.md#cross-region)과 [다른 Azure 구독](create-peering-different-subscriptions.md#powershell)에 있는 가상 네트워크를 피어링하고 피어링을 사용하여 [허브 및 스포크 네트워크 디자인](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)을 만들 수도 있습니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md) 및 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md)를 참조하세요.

VPN을 통해 [자신의 컴퓨터를 가상 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하고, 가상 네트워크 또는 피어링된 가상 네트워크의 리소스와 상호 작용할 수 있습니다. 가상 네트워크 문서에 설명된 많은 태스크를 완료하는 재사용이 가능한 스크립트는 [스크립트 샘플](powershell-samples.md)을 참조하세요.