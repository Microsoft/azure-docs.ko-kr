---
title: S2S VPN & VNet 간 연결에 대 한 IPsec/IKE 정책
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager 및 PowerShell을 사용하여 Azure VPN Gateways로 S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 구성합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161905"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성

이 문서에서는 Resource Manager 배포 모델 및 PowerShell을 사용하여 사이트 간 VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 구성하는 단계를 안내합니다.



## <a name="about"></a>Azure VPN Gateway에 대한 IPsec 및 IKE 정책 매개 변수 정보
IPsec 및 IKE 프로토콜 표준은 다양하게 결합된 다양한 암호화 알고리즘을 지원합니다. 이러한 지원이 어떻게 프레미스 간 및 VNet 간 연결이 준수 또는 보안 요구 사항을 충족하도록 하는 데 도움이 될 수 있는지를 확인하려면 [암호화 요구 사항 및 Azure VPN Gateway 정보](vpn-gateway-about-compliance-crypto.md)를 참조하세요.

이 문서에서는 IPsec/IKE 정책을 만들고 구성하여 새 연결 또는 기존 연결에 적용하기 위한 지침을 제공합니다.

* [1부 - IPsec/IKE 정책을 만들고 설정하는 워크플로](#workflow)
* [2부 - 지원되는 암호화 알고리즘 및 키 수준](#params)
* [3부 - IPsec/IKE 정책을 사용하여 새 S2S VPN 연결 만들기](#crossprem)
* [4부 - IPsec/IKE 정책을 사용하여 새 VNet 간 연결 만들기](#vnet2vnet)
* [5부 - 연결에 대한 IPsec/IKE 정책 관리(만들기, 추가, 제거)](#managepolicy)

> [!IMPORTANT]
> 1. IPsec/IKE 정책은 다음 게이트웨이 SKU에만 작동합니다.
>    * ***VpnGw1, VpnGw2, VpnGw3***(경로 기반)
>    * ***Standard*** 및 ***HighPerformance***(경로 기반)
> 2. 지정된 연결에 대해 ***하나의*** 정책 조합만 지정할 수 있습니다.
> 3. IKE(주 모드)와 IPsec(빠른 모드) 둘 다에 대한 모든 알고리즘 및 매개 변수를 지정해야 합니다. 부분 정책 지정은 허용되지 않습니다.
> 4. 해당 VPN 디바이스 공급업체 사양을 참조하여 정책이 해당 온-프레미스 VPN 디바이스에서 지원되는지 확인하세요. 정책이 호환되지 않는 경우 S2S 또는 VNet 간 연결을 설정할 수 없습니다.

## <a name ="workflow"></a>1부 - IPsec/IKE 정책을 만들고 설정하는 워크플로
이 섹션에서는 S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 만들고 업데이트하는 워크플로를 설명합니다.
1. 가상 네트워크 및 VPN Gateway 만들기
2. 프레미스 간 연결에 대한 로컬 네트워크 게이트웨이 또는 VNet 간 연결에 대한 다른 가상 네트워크 및 게이트웨이 만들기
3. 선택한 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책 만들기
4. IPsec/IKE 정책을 사용하여 연결(IPsec 또는 VNet 간) 만들기
5. 기존 연결에 대한 IPsec/IKE 정책 추가/업데이트/제거

이 문서의 지침에서는 다음 다이어그램에 표시된 대로 IPsec/IKE 정책을 설정하고 구성하도록 돕습니다.

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>2부 - 지원되는 암호화 알고리즘 및 키 수준

다음 표에는 고객이 구성 가능하도록 지원되는 암호화 알고리즘 및 키 강도가 나와 있습니다.

| **IPsec/IKEv2**  | **옵션**    |
| ---  | --- 
| IKEv2 암호화 | AES256, AES192, AES128, DES3, DES  
| IKEv2 무결성  | SHA384, SHA256, SHA1, MD5  |
| DH 그룹         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, 없음 |
| IPsec 암호화 | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, 없음    |
| IPsec 무결성  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS 그룹        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, 없음 
| QM SA 수명   | (**선택 사항**: 지정되지 않으면 기본값이 사용됨)<br>초(정수, **최소 300**/기본값 27,000초)<br>KB(정수, **최소 1,024**/기본값 102,400,000KB)   |
| 트래픽 선택기 | UsePolicyBasedTrafficSelectors** ($True/$False - **선택 사항**, 지정되지 않으면 기본값 $False)    |
|  |  |

> [!IMPORTANT]
> 1. **온-프레미스 VPN 디바이스 구성은 Azure IPsec/IKE 정책에서 지정한 다음과 같은 알고리즘 및 매개 변수와 일치하거나 해당 항목을 포함해야 합니다.**
>    * IKE 암호화 알고리즘(기본 모드/1단계)
>    * IKE 무결성 알고리즘(기본 모드/1단계)
>    * DH 그룹(기본 모드/1단계)
>    * IPsec 암호화 알고리즘(빠른 모드/2단계)
>    * IPsec 무결성 알고리즘(빠른 모드/2단계)
>    * PFS 그룹(빠른 모드/2단계)
>    * 트래픽 선택기(UsePolicyBasedTrafficSelectors를 사용하는 경우)
>    * SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.
>
> 2. **GCMAES가 IPsec 암호화 알고리즘에 사용되면 IPsec 무결성에 대해 동일한 GCMAES 알고리즘 및 키 길이를 선택해야 합니다(예: 둘 다에 대해 GCMAES128 사용).**
> 3. 위 표에서:
>    * IKEv2는 기본 모드 또는 1단계에 해당합니다.
>    * IPsec은 빠른 모드 또는 2단계에 해당합니다.
>    * DH 그룹은 기본 모드 또는 1단계에서 사용되는 Diffie-Hellmen 그룹을 지정합니다.
>    * PFS 그룹은 빠른 모드 또는 2단계에서 사용되는 Diffie-Hellmen 그룹을 지정했습니다.
> 4. IKEv2 주 모드 SA 수명은 Azure VPN Gateway에서 28,800초로 고정됩니다.
> 5. 연결에 대해 “UsePolicyBasedTrafficSelectors”를 $True로 설정하면 온-프레미스의 정책 기반 VPN 방화벽에 연결되도록 Azure VPN Gateway가 구성됩니다. PolicyBasedTrafficSelectors를 사용하도록 설정한 경우 VPN 디바이스에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사 및 Azure Virtual Network 접두사 간의 모든 조합으로 정의된 일치하는 트래픽 선택기가 있는지 확인해야 합니다. 예를 들어 온-프레미스 네트워크 접두사가 10.1.0.0/16 및 10.2.0.0/16이고 가상 네트워크 접두사가 192.168.0.0/16 및 172.16.0.0/16이면 다음 트래픽 선택기를 지정해야 합니다.
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

다음 표에는 사용자 지정 정책에서 지원하는 해당 Diffie-hellman 그룹이 나열되어 있습니다.

| **Diffie-Hellman 그룹**  | **DHGroup**              | **PFSGroup** | **키 길이** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768비트 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024비트 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048비트 MODP  |
| 19                        | ECP256                   | ECP256       | 256비트 ECP    |
| 20                        | ECP384                   | ECP384       | 384비트 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048비트 MODP  |

자세한 내용은 [RFC3526](https://tools.ietf.org/html/rfc3526) 및 [RFC5114](https://tools.ietf.org/html/rfc5114)를 참조하세요.

## <a name ="crossprem"></a>3부 - IPsec/IKE 정책을 사용하여 새 S2S VPN 연결 만들기

이 섹션에서는 IPsec/IKE 정책을 사용하여 S2S VPN 연결을 만드는 단계를 안내합니다. 다음 단계에서는 다음 다이어그램에 표시된 대로 연결을 만듭니다.

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

S2S VPN 연결을 만드는 자세한 단계별 지침은 [S2S VPN 연결 만들기](vpn-gateway-create-site-to-site-rm-powershell.md)를 참조하세요.

### <a name="before"></a>시작하기 전에

* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 개요](/powershell/azure/overview)를 참조하세요.

### <a name="createvnet1"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

이 연습에서는 먼저 변수를 선언합니다. 생산을 위해 구성하는 경우 값을 사용자의 값으로 바꾸어야 합니다.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. 구독에 연결 하 고 새 리소스 그룹을 만듭니다.

리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. 가상 네트워크, VPN gateway 및 로컬 네트워크 게이트웨이 만들기

아래 샘플은 세 개의 서브넷과 VPN Gateway가 있는 가상 네트워크 TestVNet1을 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 GatewaySubnet라고 명시적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>2단계 - IPsec/IKE 정책을 사용하여 S2S VPN 연결 만들기

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE 정책 만들기

다음 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS 없음, SA 수명 14400초 및 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPsec으로 GCMAES를 사용하는 경우 IPsec 암호화 및 무결성 모두에 대해 동일한 GCMAES 알고리즘 및 키 길이를 사용해야 합니다. 위 예제의 경우 GCMAES256 사용 시 해당 매개 변수는 "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256"이 됩니다.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. IPsec/IKE 정책을 사용 하 여 S2S VPN 연결 만들기

S2S VPN 연결을 만들고 이전에 만든 IPsec/IKE 정책을 적용합니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

필요한 경우 “-UsePolicyBasedTrafficSelectors $True”를 추가하여 연결 cmdlet을 만듦으로써 Azure VPN Gateway가 위에 설명된 대로 온-프레미스의 정책 기반 VPN 디바이스에 연결하도록 할 수 있습니다.

> [!IMPORTANT]
> 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 연결에 대한 온-프레미스 VPN 디바이스에서 정확한 정책 조합을 사용하거나 수락하는지 확인합니다. 그러지 않으면 S2S VPN 터널이 설정되지 않습니다.


## <a name ="vnet2vnet"></a>4부 - IPsec/IKE 정책을 사용하여 새 VNet 간 연결 만들기

IPsec/IKE 정책을 사용하여 VNet 간 연결을 만드는 단계는 S2S VPN 연결을 만드는 단계와 유사합니다. 다음 샘플 스크립트는 다음 다이어그램에 표시된 대로 연결을 만듭니다.

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

VNet 간 연결을 만드는 자세한 단계는 [VNet 간 연결 만들기](vpn-gateway-vnet-vnet-rm-ps.md)를 참조하세요. TestVNet1 및 VPN Gateway를 만들고 구성하려면 [3부](#crossprem)를 완료해야 합니다.

### <a name="createvnet2"></a>1단계 - 두 번째 가상 네트워크 및 VPN 게이트웨이 만들기

#### <a name="1-declare-your-variables"></a>1. 변수를 선언 합니다.

값을 구성에 사용할 값으로 바꾸어야 합니다.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. 새 리소스 그룹에 두 번째 가상 네트워크 및 VPN gateway 만들기

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2단계 - IPsec/IKE 정책을 사용하여 VNet 간 연결 만들기

S2S VPN 연결과 유사하게 IPsec/IKE 정책을 만든 다음 새 연결에 정책을 적용합니다.

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE 정책 만들기

다음 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 다른 IPsec/IKE 정책을 만듭니다.
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA 수명 14400초 및 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. IPsec/IKE 정책을 사용 하 여 VNet 간 연결 만들기

VNet 간 연결을 만들고 만든 IPsec/IKE 정책을 적용합니다. 이 예제에서 두 게이트웨이는 동일한 구독에 있습니다. 따라서 같은 PowerShell 세션에서 같은 IPsec/IKE 정책을 사용하여 두 연결을 만들고 구성할 수 있습니다.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 두 연결에 대한 IPsec 정책이 같은지 확인합니다. 그러지 않으면 VNet 간 연결이 설정되지 않습니다.

이러한 단계를 완료하고 나면 몇 분 후에 연결이 설정되고, 시작 부분에 표시된 대로 다음과 같은 네트워크 토폴로지가 구현됩니다.

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>5부 - 연결에 대한 IPsec/IKE 정책 업데이트

마지막 섹션에서는 기존 S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 관리하는 방법을 보여 줍니다. 아래 연습에서는 연결에 대한 다음 작업을 안내합니다.

1. 연결의 IPsec/IKE 정책 표시
2. 연결에 대한 IPsec/IKE 정책 추가 또는 업데이트
3. 연결에서 IPsec/IKE 정책 제거

같은 단계가 S2S 연결과 VNet 간 연결에도 모두 적용됩니다.

> [!IMPORTANT]
> IPsec/IKE 정책은 *표준* 및 *고성능* 경로 기반 VPN 게이트웨이에서만 지원됩니다. 기본 게이트웨이 SKU 또는 정책 기반 VPN 게이트웨이에서는 작동하지 않습니다.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. 연결의 IPsec/IKE 정책을 표시 합니다.

다음 예제는 연결에 대해 IPsec/IKE 정책을 구성하는 방법을 보여 줍니다. 또한 스크립트는 위의 연습에서 계속됩니다.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

마지막 명령은 연결에 대해 구성된 현재 IPsec/IKE 정책이 있는 경우 이를 나열합니다. 다음은 연결에 대한 샘플 출력입니다.

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

구성 된 IPsec/IKE 정책이 없는 경우 명령 (PS > $connection 6입니다. 정책 정책) 빈 반환을 가져옵니다. 반환되는 내용이 없다고 해서 연결에 대해 IPsec/IKE 정책이 구성되지 않았다는 의미는 아니며, 사용자 지정 IPsec/IKE 정책이 없는 것입니다. 실제 연결은 온-프레미스 VPN 디바이스 및 Azure VPN Gateway 간에 협상된 기본 정책을 사용합니다.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. 연결에 대 한 IPsec/IKE 정책 추가 또는 업데이트

연결에 대한 새 정책을 추가하거나 기존 정책을 업데이트하는 단계는 같습니다. 새 정책을 만든 다음 연결에 새 정책을 적용합니다.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

온-프레미스 정책 기반 VPN 디바이스에 연결할 때 “UsePolicyBasedTrafficSelectors”를 사용하도록 설정하려면 cmdlet에 “-UsePolicyBaseTrafficSelectors” 매개 변수를 추가하거나, 이 매개 변수를 $False로 설정하여 옵션을 사용하지 않도록 설정합니다.

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

연결을 다시 가져와 정책이 업데이트되었는지 확인할 수 있습니다.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

마지막 줄의 출력은 다음 예제와 같습니다.

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. 연결에서 IPsec/IKE 정책을 제거 합니다.

연결에서 사용자 지정 정책을 제거하고 나면 Azure VPN Gateway는 [IPsec/IKE 제안의 기본 목록](vpn-gateway-about-vpn-devices.md)으로 되돌려지고 온-프레미스 VPN 디바이스와 다시 협상합니다.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

같은 스크립트를 사용하여 연결에서 정책이 제거되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.
