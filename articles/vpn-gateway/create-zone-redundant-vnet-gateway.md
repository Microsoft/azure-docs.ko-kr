---
title: Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 만들기
description: 가용성 영역에 VPN Gateway 및 ExpressRoute 게이트웨이 배포
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150175"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 만들기

Azure 가용성 영역에서 VPN 및 ExpressRoute 게이트웨이를 배포할 수 있습니다. 그러면 가상 네트워크 게이트웨이에 복원력, 확장성 및 고가용성이 제공됩니다. Azure Availability Zones에서 게이트웨이를 배포하면 Azure에서 영역 수준 오류로의 온-프레미스 네트워크 연결성을 보호하면서 물리적 및 논리적으로 영역 내 게이트웨이를 구분합니다. 자세한 내용은 [영역 중복 가상 네트워크 게이트웨이 정보](about-zone-redundant-vnet-gateways.md) 및 [Azure 가용성 영역 정보](../availability-zones/az-overview.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="variables"></a>1. 변수를 선언 합니다.

사용할 변수를 선언합니다. 다음 샘플을 사용하여 필요할 때 고유한 값으로 대체합니다. 이 연습을 수행하는 동안 PowerShell/Cloud Shell 세션을 닫게 되는 경우 값을 복사하고 붙여넣어 변수를 다시 선언하세요. 위치를 지정할 경우 지정한 영역이 지원되는지 확인합니다. 자세한 내용은 [FAQ](#faq)을 참조하세요.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. 가상 네트워크 만들기

리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

가상 네트워크를 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. 게이트웨이 서브넷 추가

게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 예약된 IP 주소를 포함합니다. 다음 예제를 사용하여 게이트웨이 서브넷을 추가하고 설정합니다.

게이트웨이 서브넷을 추가합니다.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

가상 네트워크에 대한 게이트웨이 서브넷 구성을 설정합니다.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. 공용 IP 주소를 요청 합니다.
 
이 단계에서는 만들려는 게이트웨이에 적용되는 지침을 선택합니다. 게이트웨이 배포를 위한 영역 선택은 공용 IP 주소에 대해 지정된 영역에 따라 달라집니다.

### <a name="ipzoneredundant"></a>영역 중복 게이트웨이의 경우

**표준** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청하고 모든 영역을 지정하지 않습니다. 이 경우 만들어진 표준 공용 IP 주소는 영역 중복 공용 IP가 됩니다.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>영역 게이트웨이의 경우

**표준** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청합니다. 영역(1, 2 또는 3)을 지정합니다. 이 영역에 모든 게이트웨이 인스턴스가 배포됩니다.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>지역 게이트웨이의 경우

**기본** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청합니다. 이 경우 게이트웨이가 지역 게이트웨이로 배포되며 게이트웨이에 기본 제공된 영역 중복성이 없습니다. 게이트웨이 인스턴스는 각각 모든 영역에서 만들어집니다.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. IP 구성을 만듭니다.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. 게이트웨이 만들기

가상 네트워크 게이트웨이를 만듭니다.

### <a name="for-expressroute"></a>ExpressRoute의 경우

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>VPN Gateway의 경우

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>새 SKU를 배포할 때 변경되는 사항은 무엇인가요?

사용자 관점에서 영역 중복성이 포함된 게이트웨이를 배포할 수 있습니다. 즉, Azure Availability Zones 간에 게이트웨이의 모든 인스턴스가 배포되고, 각 가용성 영역은 다른 장애 및 업데이트 도메인입니다. 이렇게 하면 사용자 게이트웨이가 영역 오류에 대해 더 안정적이고, 사용 가능하며, 복원력이 증가합니다.

### <a name="can-i-use-the-azure-portal"></a>Azure Portal을 사용할 수 있나요?

예, Azure Portal을 사용하여 새 SKU를 배포할 수 있습니다. 그러나 Azure 가용성 영역이 있는 Azure 지역에서만 이러한 새 SKU를 볼 수 있습니다.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>새 SKU를 사용할 수 있는 지역은 어디인가요?

사용 가능한 지역의 최신 목록은 [가용성 영역](../availability-zones/az-overview.md#services-support-by-region) 를 참조 하세요.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>내 기존 가상 네트워크 게이트웨이를 영역 중복 또는 영역 게이트웨이로 변경/마이그레이션/업그레이드할 수 있나요?

영역 중복 또는 영역 게이트웨이로의 기존 가상 네트워크 게이트웨이 마이그레이션은 현재 지원되지 않습니다. 단, 기존 게이트웨이를 삭제하고 영역 중복 또는 영역 게이트웨이를 다시 만들 수는 있습니다.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이를 모두 배포할 수 있나요?

동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이를 함께 사용할 수 있습니다. 그러나 게이트웨이 서브넷에 대해 /27 IP 주소 범위를 예약해야 합니다.
