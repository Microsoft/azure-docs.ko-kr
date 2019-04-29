---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366404"
---
이 작업의 단계는 다음 구성 참조 목록의 값을 기반으로 VNet을 사용합니다. 추가 설정 및 이름도 이 목록에 설명되어 있습니다. 이 목록의 값을 기준으로 변수를 추가하지만 어느 단계에서도 이 목록을 직접 사용하지 않습니다. 목록을 복사하여 참조로 사용하고 값을 사용자 값으로 바꿀 수 있습니다.

* Virtual Network 이름 = "TestVNet"
* Virtual Network 주소 공간: 192.168.0.0/16
* 리소스 그룹: "TestRG"
* Subnet1 이름 = "FrontEnd" 
* Subnet1 주소 공간 = "192.168.1.0/24"
* 게이트웨이 서브넷 이름: "GatewaySubnet" 게이트웨이 서브넷의 이름을 항상 *GatewaySubnet*으로 지정해야 합니다.
* 게이트웨이 서브넷 주소 공간 = "192.168.200.0/26"
* 지역 = "미국 동부"
* 게이트웨이 이름 = "GW"
* 게이트웨이 IP 이름 = "GWIP"
* 게이트웨이 IP 구성 이름 = "gwipconf"
* 유형 = "ExpressRoute" 이 유형이 Express 경로 구성에 필요합니다.
* 게이트웨이 공용 IP 이름 = "gwpip"

## <a name="add-a-gateway"></a>게이트웨이를 추가합니다.
1. Azure 구독에 연결합니다.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. 이 연습에 대한 변수를 선언합니다. 사용하려는 설정을 반영하도록 샘플을 편집합니다.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. 가상 네트워크 개체를 변수로 저장합니다.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Virtual Network에 게이트웨이 서브넷을 추가합니다. 게이트웨이 서브넷의 이름을 "GatewaySubnet"으로 지정해야 합니다. /27 이상(/26, /25 등)인 게이트웨이 서브넷을 만들어야 합니다.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. 구성을 설정합니다.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. 게이트웨이 서브넷을 변수로 저장합니다.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. 공용 IP 주소를 요청합니다. 게이트웨이를 만들기 전에 IP 주소를 요청합니다. 사용할 IP 주소를 지정할 수는 없으며 IP 주소는 동적으로 할당됩니다. 다음 구성 섹션에서 이 IP 주소를 사용합니다. AllocationMethod는 동적이어야 합니다.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. 게이트웨이에 대한 구성을 만듭니다. 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 이 단계에서 게이트웨이를 만들 때 사용되는 구성을 지정합니다. 이 단계에서는 실제 게이트웨이 개체를 만들지 않습니다. 아래 샘플을 사용하여 게이트웨이 구성을 만듭니다.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. 게이트웨이를 만듭니다. 이 단계에서는 **-GatewayType** 이 특히 중요합니다. 값 **ExpressRoute**를 사용해야 합니다. 이러한 cmdlet을 실행한 후 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>게이트웨이가 만들어졌는지 확인합니다.
다음 명령을 사용하여 게이트웨이가 만들어졌는지 확인합니다.

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>게이트웨이 크기 조정
다양한 [게이트웨이 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)가 있습니다. 다음 명령을 사용하여 언제든지 게이트웨이 SKU를 변경합니다.

> [!IMPORTANT]
> 이 명령은 UltraPerformance 게이트웨이에는 작동하지 않습니다. 게이트웨이를 UltraPerformance 게이트웨이로 변경하려면 먼저 기존 ExpressRoute 게이트웨이를 제거한 다음 새 UltraPerformance 게이트웨이를 만듭니다. 게이트웨이를 UltraPerformance 게이트웨이에서 다운그레이드하려면 먼저 UltraPerformance 게이트웨이를 제거한 후 새 게이트웨이를 만듭니다.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>게이트웨이 제거
다음 명령을 사용하여 게이트웨이를 제거합니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
