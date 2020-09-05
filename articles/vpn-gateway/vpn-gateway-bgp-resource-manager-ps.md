---
title: 'Azure VPN Gateway: BGP 구성: PowerShell'
description: 이 문서에서는 Azure Resource Manager 및 PowerShell을 사용하여 Azure VPN Gateway로 BGP를 구성하는 방법을 안내합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/12/2017
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb2bc7046ba8e81fae9beeda6557964fa34e477
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078438"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>PowerShell을 사용하여 Azure VPN Gateway에서 BGP를 구성하는 방법
이 문서에서는 리소스 관리자 배포 모델 및 PowerShell을 사용하여 프레미스 간 S2S(사이트 간) VPN 연결 및 VNet 간 연결에서 BGP를 사용하도록 설정하는 단계를 안내합니다.



## <a name="about-bgp"></a>BGP 정보
BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. BGP를 통해 Azure VPN Gateway 및 온-프레미스 VPN 디바이스(BGP 피어 또는 인접이라고 함)는 관련된 게이트웨이 또는 라우터를 거치도록 해당 접두사의 가용성 및 연결 가능성에 대한 정보를 두 게이트웨이에 제공하는 "경로"를 교환할 수 있습니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여 BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다.

BGP의 이점에 대한 자세한 설명과 BGP 사용의 기술 요구 사항 및 고려 사항을 이해하려면 [Azure VPN Gateway에서의 BGP 개요](vpn-gateway-bgp-overview.md)를 참조하세요.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Azure VPN 게이트웨이에서 BGP 시작하기

이 문서는 다음 작업을 수행하는 단계를 안내합니다.

* [1부 - Azure VPN 게이트웨이에서 BGP 사용](#enablebgp)
* 2부 - BGP를 사용하여 프레미스 간 연결 설정
* [3부 - BGP를 사용하여 VNet 간 연결 설정](#v2vbgp)

지침의 각 부는 네트워크 연결에서 BGP를 사용하기 위한 기본 구성 요소를 형성합니다. 세 부 모두 완료하면 다음 다이어그램에 표시된 대로 토폴로지를 빌드합니다.

![BGP 토폴로지](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

파트를 결합하여 필요에 따라 더 복잡한 다중 홉 전송 네트워크를 빌드할 수 있습니다.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>1부 - Azure VPN Gateway에서 BGP 구성
구성 단계에서는 다음 다이어그램에 표시된 대로 Azure VPN 게이트웨이의 BGP 매개 변수를 설정합니다.

![BGP 게이트웨이](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>시작하기 전에
* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요. 

### <a name="step-1---create-and-configure-vnet1"></a>1단계 - VNet1 만들기 및 구성
#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.
이 연습에서는 먼저 변수를 선언합니다. 다음 예제에서는 이 연습에 대한 값을 사용하여 변수를 선언합니다. 생산을 위해 구성하는 경우 값을 사용자의 값으로 바꾸어야 합니다. 이 구성 유형에 익숙해지기 위해 단계를 차례로 실행하는 경우 이 변수를 사용할 수 있습니다. 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. 구독에 연결 하 고 새 리소스 그룹을 만듭니다.
Resource Manager cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 만들기
다음 샘플에서는 TestVNet1이라는 가상 네트워크와 GatewaySubnet, FrontEnd 및 Backend라는 세 개의 서브넷을 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 GatewaySubnet라고 명시적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2단계 - BGP 매개 변수를 사용하여 TestVNet1용 VPN Gateway 만들기
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. IP 및 서브넷 구성 만들기
VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 필요한 서브넷 및 IP 구성도 정의합니다.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. AS 번호를 사용 하 여 VPN gateway를 만듭니다.
TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. TestVNet1용 ASN(AS 번호)을 설정하려면 추가 매개 변수(-Asn)와 함께 BGP에 경로 기반 VPN 게이트웨이가 필요합니다. ASN 매개 변수를 설정하지 않으면 ASN 65515가 할당됩니다. 게이트웨이 만들기는 꽤 시간이 걸릴 수 있습니다(완료되려면 30분 이상).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Azure BGP 피어 IP 주소 가져오기
게이트웨이를 만든 후 Azure VPN Gateway에서 BGP 피어 IP 주소를 가져와야 합니다. 온-프레미스 VPN 디바이스에 대해 Azure VPN Gateway를 BGP 피어로 구성하려면 이 주소가 필요합니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

마지막 명령을 통해 해당 BGP 구성을 Azure VPN Gateway에 표시합니다. 예를 들어 다음과 같습니다.

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

게이트웨이가 만들어지면 BGP를 사용하여 프레미스 간 연결 또는 VNet 간 연결을 설정하도록 이 게이트웨이를 사용할 수 있습니다. 다음 섹션에서는 연습을 완료하는 단계를 안내합니다.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>2부 - BGP를 사용하여 프레미스 간 연결 설정

프레미스 간 연결을 설정하려면 온-프레미스 VPN 디바이스를 나타내는 로컬 네트워크 게이트웨이를 만들고 VPN 게이트웨이와 로컬 네트워크 게이트웨이를 연결하는 연결을 만들어야 합니다. 이러한 단계를 안내하는 문서가 있지만 이 문서는 BGP 구성 매개 변수를 지정하는 데 필요한 추가 속성을 포함합니다.

![프레미스 간에 대한 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

계속하기 전에 이 연습의 [1부](#enablebgp)를 완료했는지 확인하세요.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1단계 - 로컬 네트워크 게이트웨이 만들기 및 구성

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

이 연습에서는 다이어그램에 표시된 구성을 계속 빌드합니다. 값을 구성에 사용할 값으로 바꾸어야 합니다.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

로컬 네트워크 게이트웨이 매개 변수와 관련하여 몇 가지 주의할 점은 다음과 같습니다.

* 로컬 네트워크 게이트웨이는 VPN 게이트웨이와 같거나 다른 위치 및 리소스 그룹에 있을 수 있습니다. 이 예제에서는 다른 위치의 다른 리소스 그룹에 표시됩니다.
* 로컬 네트워크 게이트웨이에 대해 선언해야 하는 접두사는 VPN 디바이스의 BGP 피어 IP 주소에서 호스트 주소입니다. 이 경우에는 "10.52.255.254/32"의 접두사 /32입니다.
* 다시 확인하면 온-프레미스 네트워크와 Azure VNet 간에는 서로 다른 BGP ASN을 사용해야 합니다. 동일한 경우 온-프레미스 VPN 디바이스가 이미 다른 BGP 인접과의 피어에 ASN을 사용하고 있으면 VNet ASN을 변경해야 합니다.

계속하기 전에 여전히 구독 1에 연결되어 있는지 확인합니다.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Site5에 대 한 로컬 네트워크 게이트웨이 만들기

리소스 그룹이 만들어져 있지 않은 경우 로컬 네트워크 게이트웨이를 만들기 전에 먼저 만들어야 합니다. 로컬 네트워크 게이트웨이에 대한 두 개의 추가 매개 변수(Asn 및 BgpPeerAddress)를 확인합니다.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2단계 - VNet 게이트웨이 및 로컬 네트워크 게이트웨이 연결

#### <a name="1-get-the-two-gateways"></a>1. 두 게이트웨이를 가져옵니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. TestVNet1 to Site5 연결 만들기

이 단계에서는 TestVNet1에서 Site5까지 연결을 만듭니다. 이 연결에 BGP를 사용하려면 "-EnableBGP $True"를 지정해야 합니다. 앞에서 설명한 대로 동일한 Azure VPN Gateway에 대해 BGP와 비BGP를 모두 연결할 수 있습니다. 연결 속성에서 BGP를 사용하도록 설정할 수 없으면 BGP 매개 변수가 두 게이트웨이에 이미 구성된 경우에도 Azure가 이 연결에 대해 BGP를 사용하도록 설정하지 않습니다.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

다음 예제에서 이 연습을 위해 온-프레미스 VPN 디바이스의 BGP 구성 섹션에 입력할 매개 변수를 나열합니다.

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

몇 분 후 연결이 설정되며, IPsec 연결이 설정되면 BGP 피어링 세션이 시작됩니다.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>3부 - BGP를 사용하여 VNet 간 연결 설정

이 섹션에서는 다음 다이어그램에 표시된 대로 BGP를 사용하여 VNet 간 연결을 추가합니다.

![VNet-VNet에 대한 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

다음 지침은 이전 단계에서 계속합니다. BGP를 사용하여 TestVNet1 및 VPN Gateway를 만들고 구성하려면 [1부](#enablebgp)를 완료해야 합니다. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1단계 - TestVNet2 및 VPN 게이트웨이 만들기

새 가상 네트워크 TestVNet2의 IP 주소 공간이 VNet 범위와 겹치지 않는지 확인해야 합니다.

이 예제에서 가상 네트워크는 동일한 구독에 속합니다. 다른 구독 간의 VNet 간 연결을 설정할 수 있습니다. 자세한 내용은 [VNet 간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요. BGP를 사용하는 연결을 만들 때 "-EnableBgp $True"를 추가해야 합니다.

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

값을 구성에 사용할 값으로 바꾸어야 합니다.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. 새 리소스 그룹에서 TestVNet2 만들기

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. BGP 매개 변수를 사용 하 여 TestVNet2에 대 한 VPN 게이트웨이 만들기

VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청하고 필요한 서브넷 및 IP 구성을 정의합니다.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

AS 번호를 사용하여 VPN 게이트웨이를 만듭니다. Azure VPN 게이트웨이에서 기본 ASN을 재정의해야 합니다. BGP 및 전송 라우팅을 사용할 수 있도록 하려면 연결된 VNet용 ASN은 서로 달라야 합니다.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2단계 - TestVNet1 및 TestVNet2 게이트웨이 연결

이 예제에서 두 게이트웨이는 동일한 구독에 있습니다. 동일한 PowerShell 세션에서 이 단계를 완료할 수 있습니다.

#### <a name="1-get-both-gateways"></a>1. 두 게이트웨이 모두 가져오기

로그인하고 구독 1에 연결해야 합니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. 두 연결 모두 만들기

이 단계에서는 TestVNet1에서 TestVNet2까지 및 TestVNet2에서 TestVNet1까지의 연결을 만듭니다.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> 두 연결 모두에 대해 BGP를 사용하도록 설정해야 합니다.
> 
> 

이 단계를 완료한 후 몇 분 후에 연결이 설정됩니다. VNet 간 연결이 완료되면 BGP 피어링 세션이 끝납니다.

이 연습의 세 부를 모두 완료하면 다음 네트워크 토폴로지가 설정됩니다.

![VNet-VNet에 대한 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.
