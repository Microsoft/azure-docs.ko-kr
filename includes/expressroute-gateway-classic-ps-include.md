---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60365165"
---
> [!NOTE]
> 이러한 예제가 S2S/ExpressRoute 공존 구성에는 적용되지 않습니다.
> 공존 구성의 게이트웨이 사용에 대한 자세한 내용은 [공존 연결 구성](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)을 참조하세요.

## <a name="add-a-gateway"></a>게이트웨이를 추가합니다.

클래식 리소스 모델을 사용하여 가상 네트워크에 게이트웨이를 추가할 경우 게이트웨이를 만들기 직전에 네트워크 구성 파일을 수정합니다. 게이트웨이를 만들려면 아래 예제의 값이 파일에 있어야 합니다. 이전에 가상 네트워크에 연결된 게이트웨이가 있는 경우 이러한 값 중 일부가 이미 존재할 것입니다. 아래 값을 반영하도록 파일을 수정합니다.

### <a name="download-the-network-configuration-file"></a>네트워크 구성 파일 다운로드

1. [네트워크 구성 파일](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) 문서의 단계를 사용하여 네트워크 구성 파일을 다운로드합니다. 텍스트 편집기를 사용하여 파일을 엽니다.
2. 파일에 로컬 네트워크 사이트를 추가합니다. 올바른 어떤 주소 접두사도 사용 가능합니다. VPN Gateway에 유효한 어떤 IP 주소도 추가할 수 있습니다. 이 섹션의 주소 값은 ExpressRoute 작업에 사용되지 않지만 파일 유효성 검사에 반드시 필요합니다. 예제에서 “branch1”은 사이트의 이름입니다. 다른 이름을 사용할 수 있지만 파일의 게이트웨이 섹션에서 동일한 값을 사용해야 합니다.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. VirtualNetworkSites로 이동한 후 필드를 수정합니다.

   * 가상 네트워크에 대해 게이트웨이 서브넷이 있는지 확인합니다. 없으면 지금 하나를 추가할 수 있습니다. 이름은 "GatewaySubnet"여야 합니다.
   * 파일의 게이트웨이 섹션이 있는지 확인합니다. 없으면 추가합니다. 가상 네트워크를 로컬 네트워크 사이트(연결 중인 네트워크를 나타내는)에 연결하는 데 필요합니다.
   * 연결 형식이 Dedicated인지 확인합니다. ExpressRoute 연결에 필요합니다.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. 파일을 저장하고 Azure에 업로드합니다.

### <a name="create-the-gateway"></a>게이트웨이 만들기

다음 명령을 사용하여 게이트웨이를 만듭니다. 모든 값을 자신의 고유한 값으로 바꿉니다.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>게이트웨이가 만들어졌는지 확인합니다.

아래 명령을 사용하여 게이트웨이가 만들어졌는지 확인합니다. 또한 이 명령은 다른 작업에 필요한 게이트웨이 ID를 검색합니다.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>게이트웨이 크기 조정

다양한 [게이트웨이 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)가 있습니다. 다음 명령을 사용하여 언제든지 게이트웨이 SKU를 변경합니다.

> [!IMPORTANT]
> 이 명령은 UltraPerformance 게이트웨이에는 작동하지 않습니다. 게이트웨이를 UltraPerformance 게이트웨이로 변경하려면 먼저 기존 ExpressRoute 게이트웨이를 제거한 다음 새 UltraPerformance 게이트웨이를 만듭니다. 게이트웨이를 UltraPerformance 게이트웨이에서 다운그레이드하려면 먼저 UltraPerformance 게이트웨이를 제거한 후 새 게이트웨이를 만듭니다.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>게이트웨이 제거

다음 명령을 사용하여 게이트웨이를 제거합니다.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```