---
title: Azure PowerShell을 사용하여 VM으로 포트 열기
description: Azure Resource Manager 배포 모델 및 Azure PowerShell을 사용하여 Windows VM에 대한 포트를 열고 엔드포인트를 만드는 방법 알아보기
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0eca3f2fea3cf72b05b301ae307c1179f9bca0be
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072369"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>PowerShell을 사용하여 Azure에서 VM의 포트 및 엔드포인트를 여는 방법
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>빠른 명령
네트워크 보안 그룹 및 ACL 규칙을 만들려면 [최신 버전의 Azure PowerShell을 설치](/powershell/azure/)해야 합니다. [Azure 포털을 사용하여 수행할 수도 있습니다](nsg-quickstart-portal.md).

Azure 계정에 로그인합니다.

```powershell
Connect-AzAccount
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myNetworkSecurityGroup* 및 *myVnet*이 포함됩니다.

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 규칙을 만듭니다. 다음 예제에서는 포트 *80*의 *tcp* 트래픽을 허용하도록 *myNetworkSecurityGroupRule*이라는 규칙을 만듭니다.

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

그런 다음, 다음과 같이 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만들고 방금 만든 HTTP 규칙을 할당합니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

이제 서브넷에 네트워크 보안 그룹을 할당합니다. 다음 예제에서는 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)를 사용하여 *myVnet*이라는 기존 가상 네트워크를 *$vnet* 변수에 할당합니다.

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)를 사용하여 네트워크 보안 그룹을 서브넷에 연결합니다. 다음 예제에서는 *mySubnet*이라는 서브넷을 네트워크 보안 그룹에 연결합니다.

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

마지막으로, 변경 내용이 적용되도록 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 가상 네트워크를 업데이트합니다.

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](tutorial-virtual-network.md#secure-network-traffic)에 대해 자세히 읽어보세요.

고가용성 웹 애플리케이션인 경우 VM을 Azure Load Balancer 뒤에 배치해야 합니다. 부하 분산 장치는 트래픽 필터링을 제공하는 네트워크 보안 그룹으로 트래픽을 VM에 분산시킵니다. 자세한 내용은 [Azure의 Linux 가상 머신 부하를 분산하여 고가용성 애플리케이션을 만드는 방법](tutorial-load-balancer.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)
* [네트워크 보안 그룹이란?](../../virtual-network/security-overview.md)
* [Azure Load Balancer 개요](../../load-balancer/load-balancer-overview.md)
