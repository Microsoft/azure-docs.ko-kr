---
title: Azure 네트워킹 | Microsoft Docs
description: Azure의 네트워킹 서비스 및 해당 기능에 대해 알아봅니다.
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 857b38693ca85d6ab397cbe850f0cd530fefc88c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598381"
---
# <a name="azure-networking"></a>Azure 네트워킹

Azure의 네트워킹 서비스는 함께 또는 별도로 사용할 수 있는 다양 한 네트워킹 기능을 제공 합니다. 다음과 같은 주요 기능에 대해 알아보려면 그 중 하나를 클릭합니다.
- [**연결 서비스**](#connect): Azure-Virtual Network (VNet), 가상 WAN, Express 경로, VPN Gateway, Azure DNS 또는 Azure 방호에서 이러한 네트워킹 서비스의 조합을 사용 하 여 Azure 리소스 및 온-프레미스 리소스를 연결 합니다.
- [**응용 프로그램 보호 서비스**](#protect) Azure에서 DDoS 보호, 방화벽, 네트워크 보안 그룹, 웹 응용 프로그램 방화벽 또는 Virtual Network 엔드포인트에서 이러한 네트워킹 서비스의 조합을 사용 하 여 응용 프로그램을 보호 합니다.
- [**응용 프로그램 배달 서비스**](#deliver) CDN (Azure-Content Delivery Network), Azure Front 도어 서비스, Traffic Manager, Application Gateway 또는 Load Balancer에서 이러한 네트워킹 서비스의 조합을 사용 하 여 Azure 네트워크에서 응용 프로그램을 제공 합니다.
- [**네트워크 모니터링**](#monitor) – Azure-Network Watcher, Express 경로 모니터, Azure Monitor 또는 VNet 터미널 액세스 지점 (탭)에서 이러한 네트워킹 서비스의 조합을 사용 하 여 네트워크 리소스를 모니터링 합니다.

## <a name="connect"></a>연결 서비스
 
이 섹션에서는 azure 리소스 간 연결을 제공 하 고, 온-프레미스 네트워크에서 Azure 리소스에 연결 하 고, Azure에서 가상 네트워크, Express 경로, VPN Gateway, 가상 WAN, DNS 및 Azure에서 분기 연결에 대 한 분기 연결을 제공 하는 서비스에 대해 설명 합니다. 배스 천.

|서비스|왜 사용 해야 하나요?|시나리오|
|---|---|---|
|[가상 네트워크](#vnet)|Azure 리소스가 서로 안전 하 게 통신 하 고, 인터넷 및 온-프레미스 네트워크를 사용할 수 있습니다.| <p>[네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[네트워크 트래픽 라우팅](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[리소스에 대한 네트워크 액세스 제한](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[가상 네트워크 연결](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|연결 공급자가 쉽게 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장 합니다.|<p>[ExpressRoute 회로 만들기 및 수정](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[ExpressRoute 회로의 피어링 만들기 및 수정](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[VNet을 ExpressRoute 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Express 경로 회로에 대 한 경로 필터 구성 및 관리](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|공용 인터넷을 통해 Azure 가상 네트워크와 온-프레미스 위치 간에 암호화 된 트래픽을 보냅니다.|<p>[사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 간 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[지점 및 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Azure에 대 한 분기 연결을 최적화 하 고 자동화 합니다. Azure 지역은 분기를 연결하도록 선택할 수 있는 허브 역할을 합니다.|<p>[사이트 간 연결](../virtual-wan/virtual-wan-site-to-site-portal.md), [express 경로 연결](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Microsoft Azure 인프라를 사용 하 여 이름 확인을 제공 하는 DNS 도메인을 호스팅합니다.|<p>[Azure DNS에서 도메인 호스트](../dns/dns-delegate-domain-azure-dns.md)</p><p>[웹 앱에 대 한 DNS 레코드 만들기](../dns/dns-web-sites-custom-domain.md)</p> <p>[Traffic Manager에 대 한 별칭 레코드 만들기](../dns/tutorial-alias-tm.md)</p> <p>[공용 IP 주소에 대 한 별칭 레코드 만들기](../dns/tutorial-alias-pip.md)</p> <p>[영역 리소스 레코드에 대 한 별칭 레코드 만들기](../dns/tutorial-alias-rr.md)</p>|
|[Azure 방호 (미리 보기)](#bastion)|SSL을 통해 Azure Portal에서 가상 머신에 안전하고 원활한 RDP/SSH 연결을 직접 구성합니다. Azure 방호를 통해 연결 하는 경우 가상 머신에 공용 IP 주소가 필요 하지 않습니다.|<p>[Azure 방호 호스트 만들기](../bastion/bastion-create-host-portal.md)</p><p>[Linux VM에 SSH를 사용 하 여 연결](../bastion/bastion-connect-vm-ssh.md)</p><p>[RDP를 사용 하 여 Windows VM에 연결](../bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>가상 네트워크

Azure Virtual Network(VNet)는 Azure의 프라이빗 네트워크의 기본 구성 요소입니다. Vnet를 사용 하 여 다음을 수행할 수 있습니다.
- **Azure 리소스 간 통신**: Azure App Service Environment, AKS(Azure Kubernetes Service), Azure Virtual Machine Scale Sets 등의 여러 다른 Azure 리소스 및 VM을 가상 네트워크에 배포할 수 있습니다. 가상 네트워크에 배포할 수 있는 Azure 리소스의 전체 목록을 보려면 [가상 네트워크 서비스 통합](../virtual-network/virtual-network-for-azure-services.md)을 참조하세요.
- 서로 **통신**: 가상 네트워크를 서로 연결하면 각 가상 네트워크의 리소스가 가상 네트워크 피어링을 사용하여 서로 통신할 수 있습니다. 연결한 가상 네트워크는 같은 Azure 지역 또는 다른 Azure 지역에 있을 수 있습니다. 자세한 내용은 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.
- **인터넷 통신**: 기본적으로 VNet의 모든 리소스는 인터넷으로 아웃바운드 통신을 할 수 있습니다. 공용 IP 주소 또는 공용 Load Balancer를 할당하여 리소스에 대해 인바운드로 통신할 수 있습니다. [공용 IP 주소](../virtual-network/virtual-network-public-ip-address.md) 또는 공용 [Load Balancer](../load-balancer/load-balancer-overview.md) 를 사용 하 여 아웃 바운드 연결을 관리할 수도 있습니다.
- **온-프레미스 네트워크와 통신**: [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [express](../expressroute/expressroute-introduction.md)경로를 사용 하 여 온-프레미스 컴퓨터 및 네트워크를 가상 네트워크에 연결할 수 있습니다.

자세한 내용은 [Azure Virtual Network 란?](../virtual-network/virtual-networks-overview.md)을 참조 하세요.

### <a name="expressroute"></a>ExpressRoute
Express 경로를 사용 하면 연결 공급자가 촉진 하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. 이 연결은 프라이빗 전용입니다. 트래픽은 인터넷을 통해 이동하지 않습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.  자세한 내용은 [express 란?](../expressroute/expressroute-introduction.md)을 참조 하세요.

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>VPN Gateway
VPN Gateway를 사용 하 여 온-프레미스 위치에서 가상 네트워크에 대 한 암호화 된 프레미스 간 연결을 만들거나 Vnet 간에 암호화 된 연결을 만들 수 있습니다. 사이트 간, 지점 및 사이트 간 또는 VNet 간 vnet과 같은 VPN Gateway 연결에 대해 다양 한 구성을 사용할 수 있습니다.
다음 다이어그램은 동일한 가상 네트워크에 대 한 여러 사이트 간 VPN 연결을 보여 줍니다.

![사이트 간 Azure VPN Gateway 연결](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

여러 VPN 연결 유형에 대 한 자세한 내용은 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조 하세요.

### <a name="virtualwan"></a>가상 WAN
Azure Virtual WAN은 Azure를 통해 최적화된 자동 분기 연결을 제공하는 네트워킹 서비스입니다. Azure 지역은 분기를 연결하도록 선택할 수 있는 허브 역할을 합니다. 또한 Azure 백본을 활용하여 분기를 연결하고 분기 및 VNet 간 연결을 설정할 수 있습니다. Azure 가상 WAN은 사이트 간 VPN, Express 경로, 지점 및 사이트 간 사용자 VPN과 같은 여러 Azure 클라우드 연결 서비스를 단일 운영 인터페이스로 제공 합니다. Azure VNet에 대한 연결은 가상 네트워크 연결을 사용하여 설정합니다. 자세한 내용은 [Azure 가상 WAN 이란?](../virtual-wan/virtual-wan-about.md)을 참조 하세요.

![Virtual WAN 다이어그램](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>Azure DNS
Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 자세한 내용은 [Azure DNS 란?](../dns/dns-overview.md)을 참조 하세요.

### <a name="bastion"></a>Azure 방호 (미리 보기)
Azure Bastion 서비스는 가상 네트워크 내에서 프로비저닝하는 새로운 완전 플랫폼 관리형 PaaS 서비스입니다. SSL을 통해 Azure Portal에서 직접 가상 머신에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결하는 경우에는 가상 머신에 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Azure 방호 이란?](../bastion/bastion-overview.md)을 참조 하세요.

![Azure 방호 아키텍처](./media/networking-overview/architecture.png)


## <a name="protect"></a>응용 프로그램 보호 서비스

이 섹션에서는 네트워크 리소스 DDoS Protection, 웹 응용 프로그램 방화벽, Azure 방화벽, 네트워크 보안 그룹 및 서비스 엔드포인트을 보호 하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명 합니다.

|서비스|왜 사용 해야 하나요?|시나리오|
|---|---|---|
|[DDoS 보호](#ddosprotection) |과도 한 IP 트래픽 요금을 보호 하는 응용 프로그램에 대 한 고가용성|[Azure DDoS Protection 관리](../virtual-network/manage-ddos-protection.md)|
|[웹 애플리케이션 방화벽](#waf)|<p>[Azure WAF Application Gateway](../application-gateway/waf-overview.md) 는 공용 및 개인 주소 공간에서 엔터티에 대 한 지역 보호를 제공 합니다.</p><p>[프런트 도어를 사용 하는 Azure WAF](../frontdoor/waf-overview.md) 는 공용 엔드포인트에 대 한 네트워크 경계에서 보호를 제공 합니다.</p>|<p>[Bot 보호 규칙 구성](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[사용자 지정 응답 코드 구성](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP 제한 규칙 구성](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Rate limit rule 구성](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.|<p>[Vnet에서 Azure 방화벽 배포](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-하이브리드 네트워크에서 Azure 방화벽 배포](../firewall/tutorial-hybrid-ps.md)</p> <p>[Azure 방화벽 DNAT를 사용 하 여 인바운드 트래픽 필터링](../firewall/tutorial-firewall-dnat.md)</p>|
|[네트워크 보안 그룹](#nsg)|모든 네트워크 트래픽 흐름에 대 한 v m/서브넷의 완전 한 분산 끝 노드 컨트롤|[네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)|
|[가상 네트워크 서비스 엔드포인트](#serviceendpoints)|일부 Azure 서비스 리소스에 대 한 네트워크 액세스를 가상 네트워크 서브넷으로 제한할 수 있습니다.|[PaaS 리소스에 대한 네트워크 액세스 제한](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
|||
### <a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) 은 가장 정교한 DDoS 위협에 대 한 대책을 제공 합니다. 이 서비스는 응용 프로그램 및 가상 네트워크에 배포 된 리소스에 대 한 향상 된 DDoS 완화 기능을 제공 합니다. 또한 Azure DDoS Protection를 사용 하는 고객은 활성 공격 동안 DDoS 전문가에 게 대응할 수 있도록 신속한 응답 지원을 DDoS 수 있습니다.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>웹 응용 프로그램 방화벽

Azure WAF (웹 응용 프로그램 방화벽)는 SQL 삽입, 사이트 간 스크립팅 등의 일반적인 웹 익스플로잇 및 취약성 으로부터 웹 응용 프로그램에 대 한 보호를 제공 합니다. Azure WAF는 관리 되는 규칙을 통해 OWASP 상위 10 개 취약점 으로부터 제공 되는 기본 보호 기능을 제공 합니다. 또한 고객은 사용자 지정 규칙을 구성할 수 있습니다 .이 규칙은 원본 IP 범위에 따라 추가 보호를 제공 하 고 헤더, 쿠키, 양식 데이터 필드 또는 쿼리 문자열 매개 변수와 같은 특성을 요청 하는 고객 관리 규칙입니다.

고객은 공용 및 개인 주소 공간에서 엔터티에 대 한 지역 보호를 제공 하는 [Application Gateway으로 Azure WAF](../application-gateway/waf-overview.md) 를 배포 하도록 선택할 수 있습니다. 또한 고객은 네트워크에 지에서 공용 엔드포인트에 대 한 보호를 제공 하는 [프런트 도어를 사용 하 여 Azure WAF](../frontdoor/waf-overview.md) 를 배포 하도록 선택할 수 있습니다.

![웹 애플리케이션 방화벽](./media/networking-overview/waf-overview.png)


### <a name="firewall"></a>Azure 방화벽
Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. Azure 방화벽을 사용 하 여 구독 및 가상 네트워크에서 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고, 적용 하 고, 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다. 

Azure 방화벽에 대 한 자세한 내용은 [Azure 방화벽 설명서](../firewall/overview.md)를 참조 하세요.

![방화벽 개요](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>네트워크 보안 그룹
네트워크 보안 그룹을 사용 하 여 Azure 가상 네트워크에서 Azure 리소스에 대 한 네트워크 트래픽을 필터링 할 수 있습니다. 자세한 내용은 [보안 개요](../virtual-network/security-overview.md)를 참조 하세요.

### <a name="serviceendpoints"></a>서비스 엔드포인트
VNet(Virtual Network) 서비스 엔드포인트는 직접 연결을 통해 가상 네트워크 프라이빗 주소 공간 및 Azure 서비스에 대한 VNet의 ID를 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

![가상 네트워크 서비스 엔드포인트](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>응용 프로그램 배달 서비스

이 섹션에서는 CDN (응용 프로그램 Content Delivery Network), Azure Front 도어 서비스, Traffic Manager, Application Gateway 및 Load Balancer를 제공 하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명 합니다.

|서비스|왜 사용 해야 하나요?|시나리오|
|---|---|---|
|[Content Delivery Network](#cdn)|사용자에 게 고대역폭 콘텐츠를 제공 합니다. 대기 시간을 최소화 하기 위해 최종 사용자에 게 가까운 POP (지점 위치) 위치에서 캐시 된 콘텐츠를에 지 서버에 저장 합니다.|<p>[웹 앱에 CDN 추가](../cdn/cdn-add-to-web-app.md)</p> <p>[-HTTPS를 통해 Azure CDN 사용자 지정 도메인을 사용 하 여 저장소 blob 액세스](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Azure CDN 엔드포인트에 사용자 지정 도메인 추가](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Azure CDN 사용자 지정 도메인에서 HTTPS 구성](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate).</p>|
|[Azure Front 도어 서비스](#frontdoor)|고가용성을 위한 최상의 성능과 신속한 글로벌 장애 조치를 최적화 하 여 웹 트래픽에 대 한 전역 라우팅을 정의, 관리 및 모니터링할 수 있습니다.|<p>[Azure Front 도어 서비스에 사용자 지정 도메인 추가](../frontdoor/front-door-custom-domain.md)</p> <p>[프런트 도어 사용자 지정 도메인에서 HTTPS 구성](../frontdoor/front-door-custom-domain-https.md)</p><p>[지역 필터링 웹 응용 프로그램 방화벽 정책 설정](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|고가용성 및 응답성을 제공 하는 동시에, 글로벌 Azure 지역에서 서비스에 대 한 DNS 기반 트래픽을 분산 합니다.|<p> [짧은 대기 시간을 위해 트래픽 라우팅](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[우선순위 엔드포인트로 트래픽 라우팅](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [가중된 엔드포인트를 사용하여 트래픽 제어](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[엔드포인트의 지리적 위치에 따라 트래픽 라우팅](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [사용자의 서브넷에 따라 트래픽 라우팅](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|는 가용성 영역 및 Vnet 트래픽을 라우팅 하 여 지역 부하 분산을 제공 합니다. 리소스 간에 트래픽을 라우팅하고 지역 응용 프로그램을 빌드하기 위해 내부 부하 분산을 제공 합니다.|<p> [VM에 내부 트래픽 부하 분산](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[가상 네트워크 내의 Vm 간에 트래픽 부하 분산](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[특정 Vm의 특정 포트에 대 한 포트 전달 트래픽](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [부하 분산 및 아웃 바운드 규칙 구성](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다.|<p>[Azure 애플리케이션 Gateway를 사용 하 여 웹 트래픽 직접 전송](../application-gateway/quick-create-portal.md)</p><p>[SSL 종료를 사용하여 애플리케이션 게이트웨이 구성](../application-gateway/create-ssl-portal.md)</p><p>[URL 경로 기반 리디렉션으로 애플리케이션 게이트웨이 만들기](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>Content Delivery Network
Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다. Azure CDN에 대 한 자세한 내용은 [Azure Content Delivery Network](../cdn/cdn-overview.md) 를 참조 하세요.

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Azure Front 도어 서비스
Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. Front Door를 사용하면 글로벌(다중 지역) 소비자 및 기업 애플리케이션을 글로벌 Azure 잠재 고객에게 도달하는 견고한 고성능의 맞춤형 최신 애플리케이션, API 및 콘텐츠로 변환할 수 있습니다. 자세한 내용은 [Azure Front 도어](../frontdoor/front-door-overview.md)를 참조 하세요.


### <a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager는 트래픽을 전 세계 Azure 지역의 서비스에 적절하게 분산하는 한편, 고가용성과 빠른 응답성을 제공하는 DNS 기반 트래픽 부하 분산 장치입니다. Traffic Manager은 우선 순위, 가중치가 적용 되는 성능, 지리적 위치, 다중 값 또는 서브넷과 같은 트래픽을 분산 하는 다양 한 트래픽 라우팅 방법을 제공 합니다. 트래픽 라우팅 방법에 대 한 자세한 내용은 [Traffic Manager 라우팅 메서드](../traffic-manager/traffic-manager-routing-methods.md)를 참조 하세요.

다음 다이어그램은 Traffic Manager를 사용한 엔드포인트 우선 순위 기반 라우팅을 보여 줍니다.

![Azure Traffic Manager '우선 순위' 트래픽 라우팅 메서드](./media/networking-overview/priority.png)

Traffic Manager에 대 한 자세한 내용은 [Azure Traffic Manager 란?](../traffic-manager/traffic-manager-overview.md) 을 참조 하세요.

### <a name="loadbalancer"></a>Load Balancer
Azure Load Balancer는 모든 UDP 및 TCP 프로토콜에 대해 대기 시간이 낮은 고성능 계층 4 부하 분산을 제공합니다. 인바운드 및 아웃 바운드 연결을 관리합니다. 내부 부하가 분산된 공용 엔드포인트를 구성할 수 있습니다. 서비스 가용성 관리 옵션을 검색하는 TCP 및 HTTP 상태를 사용하여 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다. Load Balancer에 대한 자세한 내용은 [Load Balancer 개요](../load-balancer/load-balancer-overview.md) 문서를 참고하세요.

다음 그림에서는 외부 및 내부 부하 분산 장치를 모두 활용하는 인터넷 연결 다중 계층 애플리케이션을 보여줍니다.

![Azure Load Balancer 예제](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>Application Gateway
Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다. 응용 프로그램에 대 한 다양 한 계층 7 부하 분산 기능을 제공 하는 서비스인 ADC (응용 프로그램 배달 컨트롤러)입니다. 자세한 내용은 [Azure 애플리케이션 게이트웨이 란?](../application-gateway/overview.md)을 참조 하세요.

다음 다이어그램은 Application Gateway를 사용 하 여 url 경로 기반 라우팅을 보여 줍니다.

![Application Gateway 예제](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>네트워크 모니터링 서비스
이 섹션에서는 네트워크 리소스를 모니터링 하는 데 도움이 되는 Azure의 네트워킹 서비스 (Network Watcher, Express 경로 모니터, Azure Monitor 및 Virtual Network 탭을 설명 합니다.

|서비스|왜 사용 해야 하나요?|시나리오|
|---|---|---|
|[Network Watcher](#networkwatcher)|연결 문제를 모니터링 하 고 해결 하며, VPN, NSG 및 라우팅 문제를 진단 하 고, VM에서 패킷을 캡처하고, Azure Functions 및를 사용 하 여 진단 도구를 자동으로 트리거할 수 있습니다 Logic Apps|<p>[VM 트래픽 필터 문제 진단](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[VM 라우팅 문제 진단](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Vm 간의 통신 모니터링](../network-watcher/connection-monitor.md)</p><p>[네트워크 간 통신 문제 진단](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[VM과 주고 받는 네트워크 트래픽 로깅](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute 모니터](#expressroutemonitor)|네트워크 성능, 가용성 및 사용률에 대 한 실시간 모니터링을 제공 하 고, 네트워크 토폴로지 자동 검색을 지원 하 고, 더 빠른 오류 격리를 제공 하 고, 일시적인 네트워크 문제를 감지 하 고, 과거 네트워크 성능을 분석 하는 데 도움이 됩니다. 특성, 다중 구독 지원|<p>[ExpressRoute에 대한 네트워크 성능 모니터 구성](../expressroute/how-to-npm.md)</p><p>[Express 경로 모니터링, 메트릭 및 경고](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|응용 프로그램의 작동 방식을 이해 하 고 해당 응용 프로그램에 영향을 주는 문제 및 사용 중인 리소스를 사전에 식별 하는 데 도움이 됩니다.|<p>[Traffic Manager 메트릭 및 경고](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[표준 Load Balancer에 대 한 Azure monitor 진단](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Azure 방화벽 로그 및 메트릭 모니터링](../firewall/tutorial-diagnostics.md)</p><p>[Azure 웹 애플리케이션 방화벽 모니터링 및 로깅](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network 탭](#vnettap)|패킷 수집기에 대 한 가상 컴퓨터 네트워크 트래픽의 연속 스트리밍을 제공 하 여 네트워크 및 응용 프로그램 성능 관리 솔루션 및 보안 분석 도구를 사용 하도록 설정 합니다.|[VNet 탭 리소스 만들기](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>Network Watcher
Azure Network Watcher는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고 메트릭을 보고 그에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다. 자세한 내용은 [Network Watcher 란?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)을 참조 하세요.
### <a name="expressroutemonitor"></a>Express 경로 모니터
Express 경로 회로 메트릭, 진단 로그 및 경고를 보는 방법에 대해 알아보려면 [express 경로 모니터링, 메트릭 및 경고](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조 하세요.
### <a name="azuremonitor"></a>Azure Monitor
Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석 데이터를 수집, 분석하고 그에 따라 조치를 취하는 포괄적인 솔루션을 제공함으로써 애플리케이션의 성능과 가용성을 최대화합니다. 애플리케이션을 수행하는 방법과 애플리케이션 및 종속된 리소스에 영향을 주는 문제를 사전에 식별하는 방법을 파악할 수 있습니다. 자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조 하세요.
### <a name="vnettap"></a>Virtual Network 탭
Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너를 통해 제공됩니다. 

다음 그림은 가상 네트워크 TAP이 작동하는 방법을 보여 줍니다. 

![가상 네트워크 TAP이 작동하는 방법](./media/networking-overview/virtual-network-tap-architecture.png)

자세한 내용은 [VIRTUAL NETWORK 탭](../virtual-network/virtual-network-tap-overview.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [첫 번째 가상 네트워크 만들기](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 첫 번째 VNet을 만들고 일부 VM을 연결합니다.
- [지점 및 사이트 간 연결 구성 문서의](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)단계를 완료 하 여 VNet에 컴퓨터를 연결 합니다.
- [인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 공용 서버에 인터넷 트래픽의 부하를 분산합니다.
 
 
   
