---
title: 자습서 - Azure VPN Gateway를 사용하여 게이트웨이 만들기 및 관리
description: 이 자습서에 따라 PowerShell을 사용하여 Azure VPN Gateway를 만들고, 배포하고, 관리하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 38b13ddc08b08ce080f1cc9e9b30caeea3b4efdf
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031527"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>자습서: PowerShell을 사용하여 VPN 게이트웨이 만들기 및 관리

Azure VPN 게이트웨이는 고객 프레미스와 Azure 사이에 프레미스 간 연결을 제공합니다. 이 자습서에서는 VPN 게이트웨이 만들기 및 관리 같은 기본적인 Azure VPN 게이트웨이 배포 항목을 다룹니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * VPN 게이트웨이 만들기
> * 공용 IP 주소 보기
> * VPN 게이트웨이 크기 조정
> * VPN Gateway 다시 설정

다음 다이어그램은 이 자습서에서 만든 가상 네트워크 및 VPN 게이트웨이를 보여줍니다.

![VNet 및 VPN 게이트웨이](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell 및 Azure PowerShell 사용

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>일반 네트워크 매개 변수 값

다음은 이 자습서에 사용되는 매개 변수 값입니다. 이 예제의 변수는 다음과 같이 변환됩니다.

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

환경 및 네트워크 설정에 따라 아래 값을 변경한 다음, 값을 복사하고 붙여넣어 이 자습서의 변수를 설정합니다. Cloud Shell 세션 시간이 초과되거나 다른 PowerShell 창을 사용해야 하는 경우 변수를 복사하여 새 세션에 붙여넣고 자습서를 계속 진행합니다.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹을 먼저 만들어야 합니다. 다음 예제에서는 *미국 동부* 지역에 *TestRG1*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure VPN 게이트웨이는 가상 네트워크를 위한 프레미스 간 연결 및 P2S VPN 서버 기능을 제공합니다. 기존 가상 네트워크에 VPN 게이트웨이를 추가하거나 새 가상 네트워크 및 게이트웨이를 만듭니다. 이 예제에서는 게이트웨이 서브넷의 이름을 구체적으로 지정합니다. 게이트웨이 서브넷이 제대로 작동하려면 항상 게이트웨이 서브넷의 이름을 "GatewaySubnet"으로 지정해야 합니다. 이 예제는 세 개의 서브넷이 있는 새로운 가상 네트워크를 만듭니다. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 및 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 프런트 엔드, 백 엔드 및 게이트웨이 서브넷을 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>VPN 게이트웨이에 대한 공용 IP 주소 요청

Azure VPN Gateway는 인터넷을 통해 온-프레미스 VPN 디바이스와 통신하여 IKE(Internet Key Exchange) 협상을 수행하고 IPsec 터널을 설정합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 및 [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) 명령을 사용하여 아래 예제와 같이 공용 IP 주소를 만들어서 VPN 게이트웨이에 할당합니다.

> [!IMPORTANT]
> 현재는 게이트웨이에 동적 공용 IP 주소만 사용할 수 있습니다. 고정 IP 주소는 Azure VPN 게이트웨이에서 지원되지 않습니다.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>VPN 게이트웨이 만들기

VPN 게이트웨이를 만드는 데에는 45분 이상이 걸릴 수 있습니다. 게이트웨이 만들기가 완료되면 가상 네트워크와 VNet 간에 연결을 만들 수 있습니다. 또는 가상 네트워크와 온-프레미스 위치 간에 연결을 만듭니다. [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet을 사용하여 VPN 게이트웨이를 만듭니다.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

키 매개 변수 값:
* GatewayType: 사이트 간 연결 및 VNet-VNet 연결에 **Vpn**을 사용합니다.
* VpnType: **RouteBased**를 사용하여 더 넓은 범위의 VPN 디바이스 및 더 많은 라우팅 기능과 상호 작용합니다.
* GatewaySku: 기본값은 **VpnGw1**입니다. 더 높은 처리량 또는 더 많은 연결이 필요한 경우 다른 VpnGw SKU로 변경합니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

TryIt을 사용하는 경우 세션 시간이 초과될 수 있습니다. 그래도 괜찮습니다. 게이트웨이는 만들기를 계속 진행합니다.

게이트웨이 만들기가 완료되면 가상 네트워크와 다른 VNet 간에 연결을 만들거나 가상 네트워크와 온-프레미스 위치 간에 연결을 만들 수 있습니다. 클라이언트 컴퓨터에서 VNet으로 P2S 연결을 구성할 수도 있습니다.

## <a name="view-the-gateway-public-ip-address"></a>게이트웨이 공용 IP 주소 보기

공용 IP 주소의 이름을 알고 있는 경우 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 게이트웨이에 할당된 공용 IP 주소를 표시합니다.

세션 시간이 초과되면 이 자습서 시작 부분의 일반 네트워크 매개 변수를 세 세션에 복사하고 계속 진행합니다.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>게이트웨이 크기 조정

게이트웨이를 만든 후 VPN 게이트웨이 SKU를 변경할 수 있습니다. 다양한 게이트웨이 SKU가 처리량, 연결 수 등의 다양한 사양을 지원합니다. 다음 예제에서는 [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) 명령을 사용하여 게이트웨이 크기를 VpnGw1에서 VpnGw2로 조정합니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

또한 VPN 게이트웨이 크기 조정 작업이 기존 연결 및 구성을 중단하거나 제거하지는 **않지만**, 완료하는 데 약 30-45분이 걸립니다.

## <a name="reset-a-gateway"></a>게이트웨이 다시 설정

문제 해결 단계의 일부로, VPN 게이트웨이가 IPsec/IKE 터널 구성을 강제로 다시 시작하도록 Azure VPN 게이트웨이를 다시 설정할 수 있습니다. 게이트웨이를 다시 설정하려면 [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) 명령을 사용합니다.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

자세한 내용은 [VPN 게이트웨이 다시 설정](vpn-gateway-resetgw-classic.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[다음 자습서](vpn-gateway-tutorial-vpnconnection-powershell.md)를 이어서 진행하는 경우 이러한 리소스가 계속 필요하므로 그대로 유지하세요.

그러나 게이트웨이가 프로토타입, 테스트 또는 개념 증명 배포의 일부인 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VPN 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같이 기본 VPN 게이트웨이를 만들고 관리하는 방법을 배웠습니다.

> [!div class="checklist"]
> * VPN 게이트웨이 만들기
> * 공용 IP 주소 보기
> * VPN 게이트웨이 크기 조정
> * VPN Gateway 다시 설정

그 다음 자습서로 넘어가서 S2S, VNet-VNet 연결 및 P2S 연결에 대해 알아보세요.

> [!div class="nextstepaction"]
> * [S2S 연결 만들기](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [VNet-VNet 연결 만들기](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 연결 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
