---
title: 활성-활성 S2S Azure VPN Gateway 연결 구성
description: 이 문서에서는 Azure Resource Manager 및 PowerShell을 사용하여 Azure VPN Gateway와의 활성-활성 연결을 구성하는 방법을 안내합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152077"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Azure VPN Gateway와의 활성-활성 S2S VPN 연결 구성

이 문서에서는 Resource Manager 배포 모델 및 PowerShell을 사용하여 활성-활성 프레미스 간 및 VNet 간 연결을 만드는 단계를 안내합니다.



## <a name="about-highly-available-cross-premises-connections"></a>고가용성 프레미스 간 연결 정보
크로스-프레미스 및 VNet 간 연결에 대해 고가용성을 달성하려면 여러 VPN Gateway를 배포하고 네트워크와 Azure 간에 여러 병렬 연결을 설정해야 합니다. 연결 옵션 및 토폴로지에 대한 개요는 [고가용성 프레미스 간 연결 및 VNet 간 연결](vpn-gateway-highlyavailable.md)을 참조하세요.

이 문서에서는 활성-활성 프레미스 간 VPN 연결 및 활성-활성 두 가상 네트워크 간 연결을 설정하는 지침을 제공합니다.

* [1부 - 활성-활성 모드로 Azure VPN Gateway 만들기 및 구성](#aagateway)
* [2부 - 활성-활성 프레미스 간 연결 설정](#aacrossprem)
* [3부 - 활성-활성 VNet 간 연결 설정](#aav2v)

VPN 게이트웨이가 이미 있는 경우 다음을 수행할 수 있습니다.
* [기존 VPN 게이트웨이를 활성-대기에서 활성-활성으로 또는 그 반대로 업데이트](#aaupdate)

이러한 요소를 결합하여 필요에 따라 더 복잡하고 가용성이 높은 네트워크 토폴로지를 빌드할 수 있습니다.

> [!IMPORTANT]
> 활성-활성 모드에서 다음 SKU만 사용합니다. 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance(이전 레거시 SKU)

## <a name ="aagateway"></a>1부 - 활성-활성 VPN Gateway 만들기 및 구성
다음 단계에서는 활성-활성 모드로 Azure VPN Gateway를 구성합니다. 활성-활성 및 활성-대기 게이트웨이 간의 주요 차이점:

* 두 개의 공용 IP 주소를 갖는 두 게이트웨이 IP 구성을 만들어야 합니다.
* EnableActiveActiveFeature 플래그를 설정해야 합니다.
* 게이트웨이 SKU는 VpnGw1, VpnGw2, VpnGw3 또는 HighPerformance(레거시 SKU)이어야 합니다.

다른 속성은 비 활성-활성 게이트웨이와 동일합니다. 

### <a name="before-you-begin"></a>시작하기 전에
* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 개요](/powershell/azure/overview)를 참조하세요.

### <a name="step-1---create-and-configure-vnet1"></a>1단계 - VNet1 만들기 및 구성
#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.
이 연습에서는 먼저 변수를 선언합니다. 아래 예제에서는 이 연습에 대한 값을 사용하여 변수를 선언합니다. 생산을 위해 구성하는 경우 값을 사용자의 값으로 바꾸어야 합니다. 이 구성 유형에 익숙해지기 위해 단계를 차례로 실행하는 경우 이 변수를 사용할 수 있습니다. 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
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
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. 구독에 연결 하 고 새 리소스 그룹을 만듭니다.
리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 만들기
아래 샘플은 TestVNet1이라는 가상 네트워크 및 GatewaySubnet, FrontEnd 및 Backend라는 서브넷 세 개를 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 GatewaySubnet라고 명시적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2단계 - 활성-활성 모드로 TestVNet1용 VPN Gateway 만들기
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. 공용 IP 주소 및 게이트웨이 IP 구성을 만듭니다.
VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 2개 요청합니다. 필요한 서브넷 및 IP 구성도 정의합니다.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. 활성-활성 구성을 사용 하 여 VPN 게이트웨이 만들기
TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. GatewayIpConfig 항목이 두 개 있고 EnableActiveActiveFeature 플래그가 설정되어 있습니다. 게이트웨이 만들기는 꽤 시간이 걸릴 수 있습니다(완료되려면 45분 이상).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. 게이트웨이 공용 IP 주소 및 BGP 피어 IP 주소 가져오기
게이트웨이를 만든 후 Azure VPN Gateway에서 BGP 피어 IP 주소를 가져와야 합니다. 온-프레미스 VPN 디바이스에 대해 Azure VPN Gateway를 BGP 피어로 구성하려면 이 주소가 필요합니다.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

다음 cmdlet을 사용하여 VPN Gateway에 할당된 두 개의 공용 IP 주소와 각 게이트웨이 인스턴스에 해당하는 BGP 피어 IP 주소를 표시합니다.

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

게이트웨이 인스턴스에 대한 공용 IP 주소와 해당 BGP 피어링 주소의 순서는 동일합니다. 이 예제에서는 공용 IP가 40.112.190.5인 게이트웨이 VM은 BGP 피어링 주소로 10.12.255.4를 사용하고 138.91.156.129를 갖는 게이트웨이는 10.12.255.5를 사용합니다. 활성-활성 게이트웨이에 연결되는 온-프레미스 VPN 디바이스를 설정할 때 이 정보가 필요합니다. 게이트웨이 및 모든 주소는 다음 다이어그램에 표시됩니다.

![활성-활성 게이트웨이](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

게이트웨이가 만들어지면 이 게이트웨이를 사용하여 활성-활성 프레미스 간 연결 또는 VNet 간 연결을 설정할 수 있습니다. 다음 섹션에서는 연습을 완료하는 단계를 안내합니다.

## <a name ="aacrossprem"></a>2부 - 활성-활성 프레미스 간 연결 설정
프레미스 간 연결을 설정하려면 온-프레미스 VPN 디바이스를 나타내는 로컬 네트워크 게이트웨이를 만들고 Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이를 연결하는 연결을 만들어야 합니다. 이 예제에서 Azure VPN Gateway는 활성-활성 모드입니다. 따라서 온-프레미스 VPN 디바이스(로컬 네트워크 게이트웨이)와 연결 리소스가 하나씩만 있더라도 Azure VPN Gateway 인스턴스는 온-프레미스 디바이스와의 S2S VPN 터널을 설정합니다.

계속하기 전에 이 연습의 [1부](#aagateway) 를 완료했는지 확인하세요.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1단계 - 로컬 네트워크 게이트웨이 만들기 및 구성
#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.
이 연습에서는 다이어그램에 표시된 구성을 계속 빌드합니다. 값을 구성에 사용할 값으로 바꾸어야 합니다.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

로컬 네트워크 게이트웨이 매개 변수와 관련하여 몇 가지 주의할 점은 다음과 같습니다.

* 로컬 네트워크 게이트웨이는 VPN 게이트웨이와 같거나 다른 위치 및 리소스 그룹에 있을 수 있습니다. 이 예제에서는 리소스 그룹은 다르지만 Azure 위치는 같은 게이트웨이를 표시합니다.
* 위와 같이 온-프레미스 VPN 디바이스가 하나뿐이면 BGP 프로토콜 사용 여부에 관계없이 활성-활성 연결이 작동할 수 있습니다. 이 예제에서는 프레미스 간 연결에 대해 BGP를 사용합니다.
* BGP가 사용되도록 설정되면 로컬 네트워크 게이트웨이에 대해 선언해야 하는 접두사는 VPN 디바이스의 BGP 피어 IP 주소의 호스트 주소입니다. 이 경우에는 "10.52.255.253/32"의 접두사 /32입니다.
* 다시 확인하면 온-프레미스 네트워크와 Azure VNet 간에는 서로 다른 BGP ASN을 사용해야 합니다. 동일한 경우 온-프레미스 VPN 디바이스가 이미 다른 BGP 인접과의 피어에 ASN을 사용하고 있으면 VNet ASN을 변경해야 합니다.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Site5에 대 한 로컬 네트워크 게이트웨이 만들기
계속하기 전에 여전히 구독 1에 연결되어 있는지 확인하십시오. 아직 만들지 않은 경우 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2단계 - VNet 게이트웨이 및 로컬 네트워크 게이트웨이 연결
#### <a name="1-get-the-two-gateways"></a>1. 두 게이트웨이를 가져옵니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. TestVNet1 to Site5 연결 만들기
이 단계에서는 "EnableBGP"가 $True로 설정된 TestVNet1에서 Site5_1로의 연결을 만듭니다.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. 온-프레미스 VPN 장치에 대 한 VPN 및 BGP 매개 변수
아래 예제에서 이 연습을 위해 온-프레미스 VPN 디바이스의 BGP 구성 섹션에 입력할 매개 변수를 나열합니다.

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

몇 분 후 연결이 설정되어야 하며, IPsec 연결이 설정되면 BGP 피어링 세션이 시작됩니다. 지금까지 이 예제에서는 하나의 온-프레미스 VPN 디바이스만 구성했으며 그 결과는 아래 다이어그램과 같습니다.

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3단계 - 두 개의 온-프레미스 VPN 디바이스를 활성-활성 VPN Gateway에 연결
두 개의 VPN 디바이스가 동일한 온-프레미스 네트워크에 있는 경우 Azure VPN Gateway를 두 번째 VPN 디바이스에 연결하여 이중 중복성을 얻을 수 있습니다.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Site5에 대 한 두 번째 로컬 네트워크 게이트웨이 만들기
두 번째 로컬 네트워크 게이트웨이에 대한 게이트웨이 IP 주소, 주소 접두사 및 BGP 피어링 주소는 동일한 온-프레미스 네트워크에 대한 이전 로컬 네트워크 게이트웨이와 겹치지 않아야 합니다.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. VNet 게이트웨이 및 두 번째 로컬 네트워크 게이트웨이 연결
"EnableBGP"를 $True로 설정하여 TestVNet1에서 Site5_2까지의 연결을 만듭니다.

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. 두 번째 온-프레미스 VPN 장치에 대 한 VPN 및 BGP 매개 변수
마찬가지로 아래에는 두 번째 VPN 디바이스에 입력하는 매개 변수가 나와 있습니다.

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

연결(터널)이 설정되면 이중 중복 VPN 디바이스와 터널을 통해 온-프레미스 네트워크와 Azure가 연결됩니다.

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>3부 - 활성-활성 VNet 간 연결 설정
이 섹션에서는 BGP를 사용하여 활성-활성 VNet 간 연결을 만듭니다. 

아래 지침은 위에 나열된 이전 단계에서 계속합니다. BGP를 사용하여 TestVNet1 및 VPN Gateway를 만들고 구성하려면 [1부](#aagateway) 를 완료해야 합니다. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1단계 - TestVNet2 및 VPN 게이트웨이 만들기
새 가상 네트워크 TestVNet2의 IP 주소 공간이 VNet 범위와 겹치지 않는지 확인해야 합니다.

이 예제에서 가상 네트워크는 동일한 구독에 속합니다. 다른 구독 간에 VNet 간 연결을 설정할 수 있습니다. 자세한 내용은 [VNet 간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요. BGP를 사용하는 연결을 만들 때 "-EnableBgp $True"를 추가해야 합니다.

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.
값을 구성에 사용할 값으로 바꾸어야 합니다.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
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
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
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

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. TestVNet2에 대 한 활성-활성 VPN 게이트웨이 만들기
VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 2개 요청합니다. 필요한 서브넷 및 IP 구성도 정의합니다.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

AS 번호 및 "EnableActiveActiveFeature" 플래그를 사용하여 VPN Gateway를 만듭니다. Azure VPN 게이트웨이에서 기본 ASN을 재정의해야 합니다. BGP 및 전송 라우팅을 사용할 수 있도록 하려면 연결된 VNet용 ASN은 서로 달라야 합니다.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
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

이러한 단계를 완료하면 몇 분 내에 연결이 설정됩니다. 또한 이중 중복성을 사용하여 VNet 간 연결이 완료되면 BGP 피어링 세션이 시작됩니다.

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>기존 VPN 게이트웨이 업데이트

이 섹션에서는 기존 Azure VPN 게이트웨이를 활성-대기 모드에서 활성-활성 모드로 또는 그 반대로 변경할 수 있습니다.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>활성-대기 게이트웨이를 활성-활성 게이트웨이로 변경

다음 예제에서는 활성-대기 게이트웨이를 활성-활성 게이트웨이로 변환합니다. 활성-대기 게이트웨이가 활성-활성으로 변경되면, 다른 공용 IP 주소를 만든 다음, 두 번째 게이트웨이 IP 구성을 추가합니다.

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

예제에 사용된 다음 매개 변수를 사용자 고유의 구성에 필요한 설정으로 바꾼 다음, 이 변수를 선언합니다.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

변수가 선언되면 이 예제를 복사하여 PowerShell 콘솔에 붙여넣을 수 있습니다.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. 공용 IP 주소를 만든 다음 두 번째 게이트웨이 IP 구성을 추가 합니다.

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. 활성-활성 모드를 사용 하도록 설정 하 고 게이트웨이를 업데이트 합니다.

이 단계에서는 활성-활성 모드를 사용하도록 설정하고 게이트웨이를 업데이트합니다. 이 예제에서 VPN 게이트웨이는 현재 레거시 표준 SKU를 사용하고 있습니다. 그러나 활성-활성은 표준 SKU를 지원하지 않습니다. 레거시 SKU의 크기를 지원되는 크기(이 경우 HighPerformance)로 조정하려면, 사용하려는 지원되는 레거시 SKU를 지정하기만 하면 됩니다.

* 레거시 SKU는 이 단계를 사용하여 새 SKU 중 하나로 변경할 수 없습니다. 레거시 SKU는 지원되는 다른 레거시 SKU로만 크기 조정할 수 있습니다. 예를 들어 표준이 레거시 SKU이고 VpnGw1이 현재 SKU이므로, SKU를 표준에서 VpnGw1로 변경할 수 없습니다(VpnGw1이 활성-활성으로 지원되는 경우에도). SKU 크기 조정 및 마이그레이션에 대한 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.

* 현재 SKU(VpnGw1~VpnGw3)의 크기를 조정하려면, SKU가 동일한 SKU 제품군에 있으므로 이 단계를 사용하여 수행하면 됩니다. 이렇게 하려면 ```-GatewaySku VpnGw3``` 값을 사용합니다.

사용자 환경에서 이 값을 사용할 때 게이트웨이의 크기를 조정할 필요가 없으면 -GatewaySku를 지정할 필요가 없습니다. 이 단계에서는 실제 업데이트를 트리거하도록 PowerShell에서 게이트웨이 개체를 설정해야 합니다. 게이트웨이의 크기를 조정하지 않더라도 이 업데이트에는 30~45분이 걸릴 수 있습니다.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>활성-활성 게이트웨이를 활성-대기 게이트웨이로 변경
#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

예제에 사용된 다음 매개 변수를 사용자 고유의 구성에 필요한 설정으로 바꾼 다음, 이 변수를 선언합니다.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

변수가 선언되면 제거하려는 IP 구성의 이름을 가져옵니다.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. 게이트웨이 IP 구성을 제거 하 고 활성-활성 모드를 사용 하지 않도록 설정

이 예제를 사용하여 게이트웨이 IP 구성을 제거하고 활성-활성 모드를 사용하지 않도록 설정합니다. 실제 업데이트를 트리거하도록 PowerShell에서 게이트웨이 개체를 설정해야 합니다.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

이 업데이트에는 30-45분까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.
