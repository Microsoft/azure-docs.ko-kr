---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 192a6f4841e9dc3a478da5e4b53594362955ca71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456467"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP가 모든 Azure VPN Gateway SKU를 지원하나요?
아니요. BGP는 Azure **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** 및 **HighPerformance** VPN 게이트웨이에서 지원됩니다. **기본** SKU는 지원되지 않습니다.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Azure 정책 기반 VPN 게이트웨이에 BGP를 사용할 수 있나요?
아니요. BGP는 경로 기반 VPN 게이트웨이에서만 지원됩니다.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>개인 ASN(자치 시스템 번호)을 사용할 수 있나요?
예. 온-프레미스 네트워크와 Azure 가상 네트워크 모두에 자체 공용 ASN 또는 개인 ASN을 사용할 수 있습니다.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>32비트 ASN(자치 시스템 번호)을 사용할 수 있나요?
아니요. Azure VPN Gateway는 현재 16비트 ASN을 지원합니다.

### <a name="are-there-asns-reserved-by-azure"></a>Azure에서 예약된 ASN이 있나요?
예. 다음 ASN은 내부 및 외부 피어링에 대해 Azure에서 예약되어 있습니다.

* 공용 ASN: 8074, 8075, 12076
* 개인 ASN: 65515, 65517, 65518, 65519, 65520

Azure VPN 게이트웨이에 연결할 때 온-프레미스 VPN 디바이스에 대해 이러한 ASN을 지정할 수 없습니다.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>사용할 수 없는 다른 ASN이 있나요?
예, 다음 ASN은 [IANA에서 예약한](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) 것으로, 사용자의 Azure VPN Gateway에서 구성할 수 없습니다.

23456, 64496-64511, 65535-65551 및 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>온-프레미스 VPN 네트워크와 Azure VNet에 동일한 ASN을 사용할 수 있나요?
아니요. 온-프레미스 네트워크와 Azure VNet을 함께 BGP에 연결하려면 서로 다른 ASN을 할당해야 합니다. 크로스 프레미스 연결에 대한 BGP 활성화 여부에 관계없이 Azure VPN Gateway에 할당되는 기본 ASN은 65515입니다. VPN 게이트웨이를 만들 때 다른 ASN을 적용하여 이 기본값을 다시 정의하거나, 게이트웨이를 만든 후 ASN을 변경할 수 있습니다. 해당하는 Azure 로컬 네트워크 게이트웨이에 온-프레미스 ASN을 할당해야 합니다.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 게이트웨이가 나에게 알리는 주소 접두어는 무엇인가요?
Azure VPN 게이트웨이는 온-프레미스 BGP 디바이스에 다음 경로를 알립니다.

* VNet 주소 접두어
* Azure VPN 게이트웨이에 연결된 각 로컬 네트워크 게이트웨이의 주소 접두어
* Azure VPN 게이트웨이에 연결된 다른 BGP 피어링 세션에서 확인한 경로( **기본 경로 또는 다른 VNet 접두어와 겹치는 경로 제외**)

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Azure VPN 게이트웨이에 접두사를 몇 개나 보급할 수 있나요?
최대 4000개의 접두사를 지원합니다. 접두사의 수가 제한을 초과하는 경우 BGP 세션은 삭제됩니다.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Azure VPN 게이트웨이에 기본 경로(0.0.0.0/0)를 보급할 수 있나요?
예.

그러면 모든 VNet 송신 트래픽을 온-프레미스 사이트로 강제하고 VNet VM이 인터넷에서 VM으로 RDP 또는 SSH와 같이 인터넷의 공용 통신을 직접 수락하지 않도록 방지하게 됩니다.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Virtual Network 접두사로 정확한 접두사를 보급할 수 있나요?

아니요. Virtual Network 주소 접두사와 동일한 접두사의 보급은 Azure 플랫폼에서 차단되거나 필터링됩니다. 그러나 Virtual Network 내에 포함된 접두사의 상위 집합에 해당하는 접두사를 보급할 수 있습니다. 

예를 들어 가상 네트워크에서 10.0.0.0/16 주소 공간을 사용하는 경우 10.0.0.0/8은 보급할 수 있지만, 10.0.0.0/16 또는 10.0.0.0/24는 보급할 수 없습니다.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>내 VNet-VNet 연결에 BPG를 사용할 수 있나요?
예. 크로스 프레미스 연결과 VNet-VNet 연결에 모두 BGP를 사용할 수 있습니다.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>BGP를 비 BGP 연결과 혼합하여 내 Azure VPN 게이트웨이에 사용할 수 있나요?
예. BGP와 비 BGP 연결을 동일한 Azure VPN 게이트웨이에 혼합 사용할 수 있습니다.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 게이트웨이가 BGP 전송 라우팅을 지원하나요?
예. BGP 전송 라우팅이 지원됩니다. 단 Azure VPN 게이트웨이가 기본 경로를 타 BGP 피어에 알리지 **않는다**는 점이 다릅니다. 여러 Azure VPN 게이트웨이 간 전송 라우팅을 활성화하려면 모든 중간 VNet-VNet 연결에서 BGP를 활성화해야 합니다.  자세한 내용은 [BGP 정보](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN 게이트웨이와 내 온-프레미스 네트워크 간에 터널이 여러 개 있을 수 있나요?
예, Azure VPN 게이트웨이와 내 온-프레미스 네트워크 간에 S2S VPN 터널을 여러 개 구축할 수 있습니다. 이러한 모든 터널은 Azure VPN 게이트웨이의 총 터널 수와 비교하여 계산되며 두 터널 모두에서 BGP를 활성화해야 합니다.

예를 들어 Azure VPN 게이트웨이와 온-프레미스 네트워크 중 하나 간에 2개의 중복 터널이 있는 경우 총 Azure VPN 게이트웨이 할당량 중 2개 터널을 사용하는 것입니다(표준 10, HighPerformance 30).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>두 Azure VNet과 BGP와 간에 여러 터널이 있을 수 있나요?
예, 하지만 하나 이상의 가상 네트워크 게이트웨이가 active-active 구성에 있어야 합니다.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>ExpressRoute/S2S VPN 동시 존재 구성에서 S2S VPN에 BGP를 사용할 수 있나요?
예. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN 게이트웨이는 BGP 피어 IP에 어떤 주소를 사용하나요?
Azure VPN 게이트웨이는 가상 네트워크에 대해 정의된 게이트웨이 서브넷 범위로부터 단일 IP 주소를 할당합니다. 기본적으로 이 값은 범위 마지막에서 두 번째의 값입니다. 예를 들어 GatewaySubnet이 10.12.255.0/27이고 범위가 10.12.255.0~10.12.255.31이면 Azure VPN 게이트웨이의 BGP 피어 IP 주소는 10.12.255.30이 됩니다. Azure VPN 게이트웨이 정보를 열거할 때 이 정보를 확인할 수 있습니다.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN 디바이스에서 BGP 피어 IP 주소에 대 한 요구 사항은 무엇인가요?
사용자의 온-프레미스 BGP 피어 주소는 VPN 디바이스의 공용 IP 주소와 **같을 수 없습니다**. VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용합니다. 디바이스에서 루프백 인터페이스에 할당된 주소를 사용할 수 있지만 APIPA(169.254.x.x) 주소는 사용할 수 없습니다. 위치를 나타내는 해당 로컬 네트워크 게이트웨이에서 이 주소를 지정합니다.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP를 사용할 때 로컬 네트워크 게이트웨이에 대해 내 주소 접두어로 무엇을 지정해야 하나요?
Azure 로컬 네트워크 게이트웨이는 온-프레미스 네트워크에 대해 초기 주소 접두어를 지정합니다. BGP를 사용할 때는 BGP 피어 IP 주소의 호스트 접두어(/32 접두어)를 온-프레미스 네트워크의 주소 공간으로 할당해야 합니다. BGP 피어 IP가 10.52.255.254라면 이 온-프레미스 네트워크를 나타내는 로컬 네트워크 게이트웨이의 localNetworkAddressSpace로 "10.52.255.254/32"를 지정해야 합니다. 이것은 Azure VPN 게이트웨이가 S2S VPN 터널을 통해 BGP 세션을 수립하도록 하기 위한 것입니다.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP 피어링 세션에 대해 온-프레미스 VPN 디바이스에 무엇을 추가해야 하나요?
IPsec S2S VPN 터널을 가리키는 VPN 디바이스에서 Azure BGP 피어 IP 주소의 호스트 경로를 추가해야 합니다. 예를 들어, Azure VPN 피어 IP가 "10.12.255.30"이라면 VPN 디바이스에서 일치하는 IPsec 터널 인터페이스의 nexthop 인터페이스와 "10.12.255.30"에 대한 호스트 경로를 추가해야 합니다.
