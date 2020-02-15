---
title: '지점 및 사이트 간 연결과 RADIUS 인증을 사용하여 가상 네트워크에 컴퓨터 연결: PowerShell | Azure'
description: P2S 및 RADIUS 인증을 사용하여 Windows 및 Mac OS X 클라이언트를 가상 네트워크에 안전하게 연결합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 25bc25d9ec12804cc20baa558dce67fb3f8269a1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149168"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>RADIUS 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성: PowerShell

이 문서에서는 RADIUS 인증을 사용하는 지점 및 사이트 간 연결을 통해 VNet을 만드는 방법에 대해 설명합니다. 이 구성은 Resource Manager 배포 모델에서만 사용할 수 있습니다.

지점 및 사이트 간(P2S) VPN Gateway를 통해 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신하는 경우와 같이 원격 위치에서 VNet에 연결하려는 경우에 유용합니다. VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우에 사이트 간 VPN 대신 P2S VPN을 사용하는 것도 유용한 솔루션입니다.

P2S VPN 연결은 Windows 및 Mac 디바이스에서 시작됩니다. 다음 인증 방법은 클라이언트를 연결하는 데 사용할 수 있습니다. 

* RADIUS 서버
* VPN Gateway 기본 인증서 인증

이 문서에서는 RADIUS 서버를 통해 인증하도록 P2S를 구성하는 방법에 대해 설명합니다. 생성된 인증서와 VPN 게이트웨이 기본 인증서 인증을 대신 사용하여 인증하려면 [VPN 게이트웨이 기본 인증서를 사용하여 VNet에 지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요.

![연결 다이어그램 - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

P2S 연결을 작동하는 데는 VPN 디바이스 또는 공용 IP 주소가 필요하지 않습니다. P2S는 SSTP (Secure Socket Tunneling Protocol), OpenVPN 또는 IKEv2를 통해 VPN 연결을 만듭니다.

* SSTP는 Windows 클라이언트 플랫폼에서만 지원되는 SSL 기반 VPN 터널입니다. 이를 통해 방화벽을 통과할 수 있으므로 어디서나 Azure에 연결할 수 있는 이상적인 옵션입니다. 서버 쪽에서 SSTP 버전 1.0, 1.1 및 1.2를 지원하며, 클라이언트에서 사용할 버전을 결정합니다. Windows 8.1 이상에서는 기본적으로 SSTP 버전 1.2를 사용합니다.

* OpenVPN® 프로토콜, SSL/TLS 기반 VPN 프로토콜입니다. 대부분의 방화벽은 SSL에서 사용 하는 TCP 포트 443 아웃 바운드를 열기 때문에 SSL VPN 솔루션은 방화벽을 통과할 수 있습니다. OpenVPN은 Android, iOS (버전 11.0 이상), Windows, Linux 및 Mac 장치 (OSX 버전 10.13 이상)에서 연결 하는 데 사용할 수 있습니다.

* IKEv2 VPN - 표준 기반 IPsec VPN 솔루션입니다. IKEv2 VPN은 Mac 디바이스(OSX 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다.

P2S 연결에는 다음이 필요합니다.

* RouteBased VPN 게이트웨이입니다. 
* 사용자 인증을 처리하는 RADIUS 서버 - RADIUS 서버는 온-프레미스 또는 Azure VNet에 배포할 수 있습니다.
* VNet에 연결할 Windows 디바이스용 VPN 클라이언트 구성 패키지 - VPN 클라이언트 구성 패키지는 VPN 클라이언트에서 P2S를 통해 연결하는 데 필요한 설정을 제공합니다.

## <a name="aboutad"></a>P2S VPN에 대한 AD(Active Directory) 도메인 인증 정보

AD 도메인 인증을 사용하면 사용자가 자신의 조직 도메인 자격 증명을 사용하여 Azure에 로그인할 수 있습니다. AD 서버와 통합되는 RADIUS 서버가 필요합니다. 또한 조직에서 기존 RADIUS 배포를 활용할 수도 있습니다.
 
RADIUS 서버는 온-프레미스 또는 Azure VNet에 있을 수 있습니다. 인증하는 동안 VPN 게이트웨이에서 통과 역할을 수행하여 RADIUS 서버와 연결된 디바이스 간에 인증 메시지를 전달합니다. VPN 게이트웨이에서 RADIUS 서버에 연결할 수 있어야 합니다. RADIUS 서버가 온-프레미스에 있는 경우 Azure에서 온-프레미스 사이트로의 VPN 사이트 간 연결이 필요합니다.

또한 RADIUS 서버는 Active Directory와는 별도로 다른 외부 ID 시스템과 통합할 수도 있습니다. 이렇게 하면 MFA 옵션을 포함하여 지점 및 사이트 간 VPN에 대한 많은 인증 옵션이 제공됩니다. RADIUS 서버 공급업체 설명서를 확인하여 통합되는 ID 시스템의 목록을 얻습니다.

![연결 다이어그램 - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>VPN 사이트 간 연결만 사용하여 온-프레미스의 RADIUS 서버에 연결할 수 있습니다. ExpressRoute 연결은 사용할 수 없습니다.
>
>

## <a name="before"></a>시작하기 전에

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>예제 값

예제 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다. 단계를 계속 따라가며 값을 변경 없이 사용해도 되고, 사용자 환경을 반영하도록 값을 변경해도 됩니다.

* **이름: VNet1**
* **주소 공간: 192.168.0.0/16** 및 **10.254.0.0/16**<br>이 예제에서는 둘 이상의 주소 공간을 사용하여 이 구성이 여러 주소 공간에서 작동하는 것을 보여 줍니다. 하지만 이 구성에 여러 주소 공간이 반드시 필요한 것은 아닙니다.
* **서브넷 이름: FrontEnd**
  * **서브넷 주소 범위: 192.168.1.0/24**
* **서브넷 이름: BackEnd**
  * **서브넷 주소 범위: 10.254.1.0/24**
* **서브넷 이름: GatewaySubnet**<br>서브넷 이름 *GatewaySubnet*은 VPN Gateway가 작동하기 위한 필수 항목입니다.
  * **GatewaySubnet 주소 범위: 192.168.200.0/24** 
* **VPN 클라이언트 주소 풀: 172.16.201.0/24**<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 VPN 클라이언트 주소 풀에서 IP 주소를 받습니다.
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **DNS 서버:** VNet에 대한 이름 확인에 사용하려는 DNS 서버의 IP 주소 (선택 사항)
* **GW 이름: Vnet1GW**
* **공용 IP 이름: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="signin"></a>1. 변수를 설정 합니다.

사용할 변수를 선언합니다. 다음 샘플을 사용하여 필요할 때 고유한 값으로 대체합니다. 이 연습을 수행하는 동안 PowerShell/Cloud Shell 세션을 닫게 되는 경우 값을 복사하고 붙여넣어 변수를 다시 선언하세요.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## 2. <a name="vnet"> </a>리소스 그룹, VNet 및 공용 IP 주소 만들기

다음 단계에서는 세 개의 서브넷이 있는 리소스 그룹과 리소스 그룹의 가상 네트워크를 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 'GatewaySubnet'이라고 구체적으로 지정해야 합니다. 다른 이름을 지정하면 게이트웨이 만들기가 실패합니다.

1. 리소스 그룹을 만듭니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. 가상 네트워크에 대한 서브넷 구성을 만들고 *FrontEnd*, *BackEnd* 및 *GatewaySubnet*으로 이름을 지정합니다. 이러한 접두사는 선언된 VNet 주소 공간의 일부여야 합니다.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. 가상 네트워크 만들기

   이 예제에서 -DnsServer 서버 매개 변수는 선택 사항입니다. 값을 지정하더라도 새 DNS 서버를 만들지 않습니다. 지정한 DNS 서버 IP 주소는 VNet에서 연결하는 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이 예에서는 개인 IP 주소를 사용하지만 DNS 서버의 IP 주소가 아닐 가능성이 높습니다. 고유한 값을 사용해야 합니다. 지정한 값은 P2S 연결이 아니라 VNet에 배포하는 리소스에서 사용됩니다.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. VPN Gateway에는 공용 IP 주소가 있어야 합니다. 먼저 IP 주소 리소스를 요청하고, 가상 네트워크 게이트웨이를 만들 때 참조합니다. VPN Gateway가 생성될 때 IP 주소는 리소스에 동적으로 할당됩니다. 현재 VPN Gateway는 *동적* 공용 IP 주소 할당만 지원합니다. 고정 공용 IP 주소 할당을 요청할 수 없습니다. 하지만 IP 주소가 VPN Gateway에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

   동적으로 할당된 공용 IP 주소를 요청할 변수를 지정합니다.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## 3. <a name="radius"> </a>RADIUS 서버 설정

가상 네트워크 게이트웨이를 만들고 구성하기 전에 인증을 위한 RADIUS 서버를 올바르게 구성해야 합니다.

1. 배포된 RADIUS 서버가 없는 경우 RADIUS 서버를 배포합니다. 배포 단계는 RADIUS 공급업체에서 제공하는 설치 가이드를 참조하세요.  
2. RADIUS 서버에서 VPN 게이트웨이를 RADIUS 클라이언트로 구성합니다. 이 RADIUS 클라이언트를 추가하는 경우, 생성한 GatewaySubnet 가상 네트워크를 지정합니다. 
3. RADIUS 서버가 설치되면 이 RADIUS 서버의 IP 주소 및 RADIUS 클라이언트에서 이 서버와 통신할 때 사용해야 하는 공유 비밀을 가져옵니다. RADIUS 서버가 Azure VNet에 있으면 RADIUS 서버 VM의 CA IP를 사용합니다.

[NPS(네트워크 정책 서버)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 문서에서는 AD 도메인 인증을 위해 Windows RADIUS 서버(NPS)를 구성하는 방법에 대한 지침을 제공하고 있습니다.

## 4. <a name="creategw"> </a>VPN gateway 만들기

VNet에 대한 VPN 게이트웨이를 구성하고 만듭니다.

* -GatewayType은  'Vpn'이어야 하고 -VpnType은  'RouteBased'여야 합니다.
* VPN 게이트웨이는 선택한  [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) 에 따라 완료하는 데 최대 45분이 걸릴 수 있습니다.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 5. <a name="addradius"> </a>RADIUS 서버 및 클라이언트 주소 풀 추가
 
* -RadiusServer는 이름 또는 IP 주소로 지정할 수 있습니다. 이름을 지정하고 서버가 온-프레미스에 있으면 VPN 게이트웨이에서 해당 이름을 확인할 수 없습니다. 이러한 경우에는 서버의 IP 주소를 지정하는 것이 좋습니다. 
* -RadiusSecret은 RADIUS 서버에 구성된 것과 일치해야 합니다.
* -VpnCientAddressPool은 연결하는 VPN 클라이언트에서 IP 주소를 받는 범위입니다. 연결할 온-프레미스 위치와 겹치지 않는 개인 IP 주소 범위 또는 연결 하려는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용 합니다. 충분한 크기의 주소 풀을 구성했는지 확인합니다.  

1. RADIUS 비밀의 보안 문자열을 만듭니다.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. RADIUS 비밀을 입력하라는 메시지가 표시됩니다. 입력하는 문자는 "*" 문자로 대신 바뀌어 표시됩니다.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. VPN 클라이언트 주소 풀과 RADIUS 서버 정보를 추가합니다.

   SSTP 구성:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   OpenVPN® 구성의 경우:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   IKEv2 구성:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   SSTP + IKEv2 구성:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 6. <a name="vpnclient"> </a>vpn 클라이언트 구성 패키지를 다운로드 하 고 vpn 클라이언트를 설정 합니다.

VPN 클라이언트 구성을 사용하면 P2S 연결을 통해 VNet에 디바이스를 연결할 수 있습니다. Vpn 클라이언트 구성 패키지를 생성 하 고 VPN 클라이언트를 설정 하려면 [RADIUS 인증에 대 한 Vpn 클라이언트 구성 만들기](point-to-site-vpn-client-configuration-radius.md)를 참조 하세요.

## <a name="connect"></a>7. Azure에 연결

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN 클라이언트에서 연결

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. 도메인 자격 증명을 입력하고 '연결'을 클릭합니다. 상승된 권한을 요청하는 팝업 메시지가 표시됩니다. 이 메시지에 동의하고 자격 증명을 입력합니다.

   ![VPN 클라이언트에서 Azure에 연결](./media/point-to-site-how-to-radius-ps/client.png)
2. 연결이 설정되었습니다.

   ![설정된 연결](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Mac VPN 클라이언트에서 연결

[네트워크] 대화 상자에서 사용하려는 클라이언트 프로필을 찾은 다음 **연결**을 클릭합니다.

  ![Mac 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>연결 확인

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음 예제와 비슷합니다.

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

P2S 연결 문제를 해결하려면 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.

## <a name="connectVM"></a>가상 컴퓨터에 연결하려면

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>FAQ

이 FAQ는 RADIUS 인증을 사용하는 P2S에 적용됩니다.

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/)를 참조하세요. 네트워킹 및 가상 머신에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.
