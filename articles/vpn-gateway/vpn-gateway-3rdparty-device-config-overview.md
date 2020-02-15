---
title: Azure VPN gateway에 연결 하기 위한 파트너 VPN 장치 구성
description: 이 문서에서는 Azure VPN Gateway에 연결하기 위한 파트너 VPN 디바이스 구성에 대한 개요를 제공합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148335"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>파트너 VPN 디바이스 구성의 개요
이 문서에서는 Azure VPN Gateway에 연결하기 위한 온-프레미스 VPN 디바이스를 구성하는 개요를 제공합니다. 동일한 매개 변수를 사용하여 다양한 온-프레미스 VPN 디바이스 구성에 연결하는 방법을 보여주는 데 샘플 Azure Virtual Network 및 VPN Gateway 설정을 사용합니다.



## <a name="device-requirements"></a>디바이스 요구 사항
Azure VPN Gateway는 표준 IPsec/IKE 프로토콜 도구 모음을 사용하여 S2S(사이트 간) VPN 터널을 설정합니다. Azure VPN Gateway에 대한 IPsec/IKE 매개 변수 및 암호화 알고리즘 목록은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. 또한 [암호화 요구 사항 정보](vpn-gateway-about-compliance-crypto.md)에 설명된 대로 특정 연결에 대한 정확한 알고리즘 및 주요 장점을 지정할 수 있습니다.

## <a name ="singletunnel"></a>단일 VPN 터널
샘플의 첫 번째 구성은 Azure VPN Gateway와 온-프레미스 VPN 디바이스 간에 단일 S2S VPN 터널로 구성됩니다. 필요에 따라 [VPN 터널에 BGP(경계 게이트웨이 프로토콜)](#bgp)를 구성할 수 있습니다.

![단일 S2S VPN 터널의 다이어그램](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

단일 VPN 터널을 설정하는 단계별 지침은 [사이트 간 연결 구성](vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 참조하세요. 다음 섹션에서는 샘플 구성에 대한 연결 매개 변수를 지정하고 시작할 수 있도록 하는 PowerShell 스크립트를 제공합니다.

### <a name="connection-parameters"></a>연결 매개 변수
이 섹션은 이전 섹션에서 설명한 예제의 매개 변수를 나열합니다.

| **매개 변수**                | **값**                    |
| ---                          | ---                          |
| 가상 네트워크 주소 접두사        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 게이트웨이 IP         | Azure VPN Gateway IP         |
| 온-프레미스 주소 접두사 | 10.51.0.0/16<br>10.52.0.0/16 |
| 온-프레미스 VPN 디바이스 IP    | 온-프레미스 VPN 디바이스 IP    |
| *가상 네트워크 BGP ASN                | 65010                        |
| *Azure BGP 피어 IP           | 10.12.255.30                 |
| *온-프레미스 BGP ASN         | 65050                        |
| *온-프레미스 BGP 피어 IP     | 10.52.255.254                |

\*BGP에만 해당하는 선택적 매개 변수

### <a name="sample-powershell-script"></a>예제 PowerShell 스크립트
이 섹션에서는 시작하는 데 도움이 되도록 예제 스크립트를 제공합니다. 자세한 지침은 [PowerShell을 사용하여 S2S VPN 연결 만들기](vpn-gateway-create-site-to-site-rm-powershell.md)를 참조하세요.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(선택 사항)UsePolicyBasedTrafficSelectors로 사용자 지정 IPsec/IKE 정책 사용
VPN 디바이스에서 임의 트래픽 선택기(예: 경로 기반/VTI 기반 구성)를 지원하지 않는 경우 [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) 옵션을 사용하여 사용자 지정 IPsec/IKE 정책을 만듭니다.

> [!IMPORTANT]
> 연결에서 **UsePolicyBasedTrafficSelectors** 옵션을 사용하기 위해 IPsec/IKE 정책을 만들어야 합니다.


샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Lifetime 7,200초 및 20,480,000KB(20GB)

이 스크립트는 IPsec/IKE 정책을 적용하고 연결에서 **UsePolicyBasedTrafficSelectors** 옵션을 사용합니다.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(선택 사항)S2S VPN 연결에 BGP 사용
S2S VPN 연결을 만들 경우 선택적으로 [VPN Gateway의 BGP](vpn-gateway-bgp-resource-manager-ps.md)를 사용할 수 있습니다. 이 방법에는 두 가지 차이점이 있습니다.

* 온-프레미스 주소 접두사는 단일 호스트 주소일 수 있습니다. 온-프레미스 BGP 피어 IP 주소는 다음과 같이 지정됩니다.

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* 연결을 만들 경우 **-EnableBGP** 옵션을 $True로 설정해야 합니다.

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>다음 단계
활성-활성 VPN Gateway를 설정하는 단계별 지침은 [크로스-프레미스 및 VNet 간 연결에 대한 활성-활성 VPN Gateway 구성](vpn-gateway-activeactive-rm-powershell.md)을 참조하세요.

