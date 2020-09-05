---
title: 'Azure VPN Gateway: S2S VPN 연결 만들기 및 관리: 자습서'
description: 자습서 - Azure PowerShell 모듈을 사용하여 S2S VPN 연결을 만들고 관리
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 18c6188e1b13c35a4c28a5f9e7fc863f00798eed
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "80616393"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>자습서: PowerShell을 사용하여 S2S VPN 연결 만들기 및 관리

Azure S2S VPN 연결은 고객 프레미스와 Azure 사이에 안전한 프레미스 간 연결을 제공합니다. 이 자습서에서는 S2S VPN 연결을 만들고 관리하는 등의 IPsec S2S VPN 연결 수명 주기를 연습합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * S2S VPN 연결 만들기
> * 연결 속성 업데이트: 미리 공유한 키, BGP, IPsec/IKE 정책
> * VPN 연결 추가
> * VPN 연결 삭제

다음 다이어그램은 이 자습서의 토폴로지를 보여줍니다.

![사이트 간 VPN 연결 다이어그램](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell 및 Azure PowerShell 사용

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>요구 사항

첫 번째 자습서: [Azure PowerShell을 사용하여 VPN 게이트웨이 만들기](vpn-gateway-tutorial-create-gateway-powershell.md)를 완료하여 다음 리소스를 만듭니다.

1. 리소스 그룹(TestRG1), 가상 네트워크(VNet1) 및 GatewaySubnet
2. VPN 게이트웨이(VNet1GW)

가상 네트워크 매개 변수 값은 아래에 나열되어 있습니다. 온-프레미스 네트워크를 나타내는 로컬 네트워크 게이트웨이의 추가 값을 확인합니다. 환경 및 네트워크 설정에 따라 아래 값을 변경한 다음, 값을 복사하고 붙여넣어 이 자습서의 변수를 설정합니다. Cloud Shell 세션 시간이 초과되거나 다른 PowerShell 창을 사용해야 하는 경우 변수를 복사하여 새 세션에 붙여넣고 자습서를 계속 진행합니다.

>[!NOTE]
> 이 방법으로 연결을 설정하는 경우 온-프레미스 네트워크와 일치하도록 값을 변경해야 합니다. 이러한 단계를 단지 자습서로 실행하는 경우에는 변경할 필요가 없지만, 연결이 작동하지 않습니다.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

S2S VPN 연결을 만드는 워크플로는 간단합니다.

1. 온-프레미스 네트워크를 나타내는 로컬 네트워크 게이트웨이 만들기
2. Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이 간에 연결 만들기

## <a name="create-a-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 온-프레미스 네트워크를 나타냅니다. 다음을 포함하여 로컬 네트워크 게이트웨이에서 온-프레미스 네트워크의 속성을 지정할 수 있습니다.

* VPN 디바이스의 공용 IP 주소
* 온-프레미스 주소 공간
* (선택 사항) BGP 특성(BGP 피어 IP 주소 및 AS 번호)

[New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) 명령을 사용하여 로컬 네트워크 게이트웨이를 만듭니다.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location $Location1 -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN 연결 만들기

다음으로, [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) 명령을 사용하여 가상 네트워크 게이트웨이와 VPN 디바이스 사이에 사이트 간 VPN 연결을 만듭니다. 사이트 간 VPN에 대한 '-ConnectionType'은 *IPsec*입니다.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

BGP를 사용하는 경우 연결에 BGP를 사용하려면 선택적 " **-EnableBGP $True**" 속성을 추가합니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 매개 변수 '-ConnectionProtocol'은 기본적으로 IKEv2를 사용하는 선택 사항입니다. **-ConnectionProtocol IKEv1**을 지정하여 IKEv1 프로토콜과의 연결을 만들 수 있습니다.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>VPN 연결 미리 공유한 키, BGP 및 IPsec/IKE 정책 업데이트

### <a name="view-and-update-your-pre-shared-key"></a>미리 공유한 키 보기 및 업데이트

Azure S2S VPN 연결은 미리 공유한 키(비밀)를 사용하여 온-프레미스 VPN 디바이스와 Azure VPN 게이트웨이 사이에서 인증합니다. [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) 및 [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey) 명령을 사용하여 연결에 대해 미리 공유한 키를 보고 업데이트할 수 있습니다.

> [!IMPORTANT]
> 미리 공유한 키는 **인쇄 가능한 ASCII 문자**로 구성된 128자 미만의 문자열입니다.

이 명령은 연결에 대해 미리 공유한 키를 보여줍니다.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

출력은 위의 예제에 따라 "**Azure\@!b2C3**"입니다. 아래 명령을 사용하여 미리 공유한 키 값을 "**Azure\@!_b2=C3**"로 변경합니다.

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>VPN 연결에서 BGP를 사용하도록 설정

Azure VPN 게이트웨이는 BGP 동적 라우팅 프로토콜을 지원합니다. 온-프레미스 네트워크 및 디바이스에 BGP를 사용하는지 여부에 따라 개별 연결에 BGP를 사용할 수 있습니다. 다음 BGP 속성을 지정한 후 연결에서 BGP를 사용하도록 설정합니다.

* Azure VPN ASN(자치 시스템 번호)
* 온-프레미스 로컬 네트워크 게이트웨이 ASN
* 온-프레미스 로컬 네트워크 게이트웨이 BGP 피어 IP 주소

BGP 속성을 구성하지 않은 경우 [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) 및 [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway) 명령은 VPN 게이트웨이 및 로컬 네트워크 게이트웨이에 이러한 속성을 추가합니다.

BGP 속성을 구성하려면 다음 예제를 사용합니다.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

[Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection) 명령을 사용하여 BGP를 설정합니다.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

"-EnableBGP" 속성 값을 **$False**로 변경하여 BGP를 해제할 수 있습니다. Azure VPN 게이트웨이의 BGP에 대한 자세한 설명은 [Azure VPN 게이트웨이의 BGP](vpn-gateway-bgp-resource-manager-ps.md)를 참조하세요.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>연결에서 사용자 지정 IPsec/IKE 정책 적용

[기본 제한값](vpn-gateway-about-vpn-devices.md#ipsec)을 사용하는 대신 연결에 IPsec/IKE 암호화 알고리즘과 키 강도의 정확한 조합을 지정하는 선택적 IPsec/IKE 정책을 적용할 수 있습니다. 다음 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 다른 IPsec/IKE 정책을 만듭니다.

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, SA 수명 14,400초 및 102,400,000KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

알고리즘 및 지침의 전체 목록은 [S2S 또는 VNet-VNet 연결에 대한 IPsec/IKE 정책](vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.

## <a name="add-another-s2s-vpn-connection"></a>또 다른 S2S VPN 연결 추가

동일한 VPN 게이트웨이에 추가 S2S VPN 연결을 추가하고, 또 다른 로컬 네트워크 게이트웨이를 만들고, 새 로컬 네트워크 게이트웨이와 VPN 게이트웨이 사이에 새 연결을 만듭니다. 다음 예제를 사용하되, 사용자 고유의 네트워크 구성을 반영하도록 변수를 수정합니다.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Azure VPN 게이트웨이에 대한 S2S VPN 연결 2개가 있습니다.

![다중 사이트 VPN 연결](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>S2S VPN 연결 삭제

[Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection) 명령을 사용하여 S2S VPN 연결을 삭제합니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

로컬 네트워크 게이트웨이가 더 이상 필요 없으면 삭제합니다. 로컬 네트워크 게이트웨이에 다른 연결이 설정되어 있으면 삭제할 수 없습니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>리소스 정리

이 구성이 프로토타입, 테스트 또는 개념 증명 배포의 일부인 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VPN 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 S2S VPN 연결을 만들고 관리하는 방법을 배웠습니다.

> [!div class="checklist"]
> * S2S VPN 연결 만들기
> * 연결 속성 업데이트: 미리 공유한 키, BGP, IPsec/IKE 정책
> * VPN 연결 추가
> * VPN 연결 삭제

그 다음 자습서로 넘어가서 S2S, VNet-VNet 연결 및 P2S 연결에 대해 알아보세요.

> [!div class="nextstepaction"]
> * [VNet-VNet 연결 만들기](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 연결 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
