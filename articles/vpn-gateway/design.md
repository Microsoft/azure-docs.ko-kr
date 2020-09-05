---
title: Azure VPN Gateway 디자인 정보
description: Azure 가상 네트워크에 연결 하기 위해 VPN gateway 토폴로지를 디자인 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/01/2020
ms.author: cherylmc
ms.openlocfilehash: d0bcd0608796545a4982f72f276399d5f692e765
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852701"
---
# <a name="vpn-gateway-design"></a>VPN Gateway 디자인

VPN Gateway 연결에 사용할 수 있는 다양한 구성이 있다는 사실을 꼭 기억하시기 바랍니다. 그 중에서 필요에 맞는 최상의 구성을 결정해야 합니다. 아래 섹션에서는 다음 VPN gateway 연결에 대 한 디자인 정보 및 토폴로지 다이어그램을 볼 수 있습니다. 다이어그램 및 설명을 사용하여 요구 사항에 맞게 연결 토폴로지를 선택하도록 도울 수 있습니다. 다이어그램은 기본 기본 토폴로지를 표시 하지만 다이어그램을 지침으로 사용 하 여 더 복잡 한 구성을 빌드할 수 있습니다.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>사이트 간 및 다중 사이트(IPsec/IKE VPN 터널)

### <a name="site-to-site"></a><a name="S2S"></a>사이트 간

S2S(사이트 간) VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통한 연결입니다. S2S 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다. S2S 연결에는 공용 IP 주소가 할당된 온-프레미스에 있는 VPN 디바이스가 필요합니다. VPN 디바이스 선택에 대한 자세한 내용은 [VPN Gateway FAQ - VPN 디바이스](vpn-gateway-vpn-faq.md#s2s)를 참조하세요.

![Azure VPN Gateway 사이트 간 연결 예제](./media/design/vpngateway-site-to-site-connection-diagram.png)

하나의 공용 IP를 사용 하 여 활성-대기 모드에서 또는 두 개의 공용 IP를 사용 하는 활성-활성 모드에서 VPN Gateway를 구성할 수 있습니다. 활성-대기 모드에서는 하나의 IPsec 터널이 활성 상태이 고 다른 터널은 대기 상태입니다. 이 설정에서는 트래픽이 활성 터널을 통해 전달 되 고,이 터널에서 문제가 발생 하는 경우 트래픽이 대기 터널로 전환 됩니다. 활성-활성 모드에서 VPN Gateway를 설정 하는 것이 좋습니다. 두 IPsec 터널 모두 동시에 활성 상태이 고 동시에 두 터널을 통해 데이터를 이동 하는 *것이 좋습니다* . 활성-활성 모드의 추가 장점은 고객이 더 높은 처리량을 경험 하는 것입니다.

### <a name="multi-site"></a><a name="Multi"></a>다중 사이트

사이트 간 연결에서 변형된 연결 유형입니다. 가상 네트워크 게이트웨이에서 일반적으로 여러 온-프레미스 사이트에 연결하는 둘 이상의 VPN 연결을 만듭니다. 여러 연결을 사용하는 경우 경로 기반 VPN 유형(클래식 VNet을 사용하는 경우 동적 게이트웨이)을 사용해야 합니다. 각 가상 네트워크가 하나의 VPN Gateway만 사용할 수 있으므로 게이트웨이를 통한 모든 연결은 사용 가능한 대역폭을 공유합니다. 이러한 유형의 연결은 흔히 "다중 사이트" 연결이라고 합니다.

![Azure VPN Gateway 다중 사이트 연결 예제](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>사이트 간 및 다중 사이트의 배포 모델 및 메서드

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>지점 및 사이트 간 VPN

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의실과 같은 원격 위치에서 Azure VNet에 연결하려는 재택 근무자에게 유용합니다. 또한 P2S VPN은 VNet에 연결해야 하는 클라이언트가 몇 개만 있는 경우 S2S VPN 대신 사용할 수 있는 유용한 솔루션입니다.

S2S 연결과 달리 P2S 연결은 온-프레미스 공용 IP 주소 또는 VPN 디바이스가 필요하지 않습니다. P2S 연결과 S2S 연결에 대한 구성 요구 사항이 모두 충족될 경우 동일한 VPN Gateway를 통해 두 연결을 함께 사용할 수 있습니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.

![Azure VPN Gateway 지점 및 사이트 연결 예제](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>P2S의 배포 모델 및 메서드

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>VNet 간 연결(IPsec/IKE VPN 터널)

가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. VNet 간 통신을 다중 사이트 연결 구성과 통합할 수도 있습니다. 이렇게 하면 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

연결할 VNet의 상태는 다음과 같습니다.

* 동일하거나 다른 지역에 위치
* 동일하거나 다른 구독에 위치 
* 동일하거나 다른 배포 모델

![Azure VPN Gateway VNet 간 연결 예제](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>배포 모델 간의 연결

Azure에는 현재 클래식 및 Resource Manager 등 두 개의 배포 모델이 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 이후의 VM 및 역할 인스턴스는 Resource Manager에서 만들 VNet에서 실행되고 있을 것입니다. VNet 간의 연결을 만들어 어떤 VNet의 리소스가 다른 리소스와 서로 직접 통신하도록 할 수 있습니다.

### <a name="vnet-peering"></a>VNet 피어링

가상 네트워크가 특정 요구 사항을 충족하면 VNet 피어링을 사용하여 연결을 만들 수 있습니다. VNet 피어링은 가상 네트워크 게이트웨이를 사용하지 않습니다. 자세한 내용은 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>VNet 간 배포 모델 및 메서드

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute(프라이빗 연결)

ExpressRoute를 사용하면 연결 공급자가 지원하는 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365, CRM Online과 같은 Microsoft 클라우드 서비스에 대한 연결을 설정하거나, 연결 공급자를 통한 연결 공급자를 통한 지점 간 이더넷 네트워크 또는 임의 (IP VPN) 네트워크에서의 연결을 사용할 수 있습니다.

ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 이 기능을 사용하면 ExpressRoute 연결은 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

ExpressRoute 연결은 필수 구성의 일부분으로 가상 네트워크 게이트웨이를 사용합니다. ExpressRoute 연결에서 가상 네트워크 게이트웨이는 'Vpn'이 아닌 'ExpressRoute' 게이트웨이 유형으로 구성됩니다. ExpressRoute 회로를 통해 전송되는 트래픽은 기본적으로 암호화되지 않으므로, ExpressRoute 회로를 통해 암호화된 트래픽을 보낼 수 있는 솔루션을 만듭니다. Express 경로에 대 한 자세한 내용은 [express 경로 기술 개요](../expressroute/expressroute-introduction.md)를 참조 하세요.

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>사이트 간 및 ExpressRoute 공존 연결

ExpressRoute는 공용 인터넷을 사용하지 않는 WAN에서 Azure를 비롯한 Microsoft 서비스에 대한 프라이빗 직접 연결입니다. 사이트 간 VPN 트래픽은 공용 인터넷을 통해 암호화되어 이동합니다. 동일한 가상 네트워크에 대한 사이트 간 VPN 및 ExpressRoute 연결을 구성할 수 있으면 여러 장점이 있습니다.

사이트 간 VPN을 ExpressRoute에 대한 보안 장애 조치(failover) 경로로 구성하거나 사이트 간 VPN을 사용하여 사용자 네트워크의 일부가 아니지만 ExpressRoute를 통해 연결된 사이트에 연결할 수 있습니다. 이 구성에는 동일한 가상 네트워크에 대한 두 개의 가상 네트워크 게이트웨이가 필요합니다. 하나는 'Vpn' 게이트웨이 유형을 사용하고 다른 하나는 'ExpressRoute' 게이트웨이 유형을 사용합니다.

![ExpressRoute 및 VPN Gateway 공존 연결 예제](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>S2S 및 ExpressRoute 공존의 배포 모델 및 메서드

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>항상 사용 가능한 연결

항상 사용 가능한 연결에 대 한 계획 및 디자인은 [항상 사용 가능한 연결](vpn-gateway-highlyavailable.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md)를 참조하세요.

* [VPN Gateway 구성 설정](vpn-gateway-about-vpn-gateway-settings.md)에 대해 자세히 알아보세요.

* VPN Gateway BGP 고려 사항은 [Bgp 정보](vpn-gateway-bgp-overview.md)를 참조 하세요.

* [구독 및 서비스 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)를 참조하세요.

* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.
