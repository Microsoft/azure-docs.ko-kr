---
title: Azure 네트워킹 | Microsoft Docs
description: Azure 네트워킹 서비스와 기능에 대해 알아봅니다.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 02db9f2b8cb2ec71d23ad077b90eeacb905d2a16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565857"
---
# <a name="azure-networking"></a>Azure 네트워킹

Azure에서는 함께 또는 별도로 사용할 수 있는 다양한 네트워킹 기능을 제공합니다. 다음과 같은 주요 기능에 대해 알아보려면 그 중 하나를 클릭합니다.
- [Azure 리소스 간 연결](#connectivity): 클라우드의 안전한 개인 가상 네트워크에서 Azure 리소스를 서로 연결합니다.
- [인터넷 연결](#internet-connectivity): 인터넷을 통해 Azure 리소스 간에 통신합니다.
- [온-프레미스 연결](#on-premises-connectivity): 인터넷의 VPN(가상 사설망) 또는 전용 Azure 연결을 통해 온-프레미스 네트워크를 Azure 리소스에 연결합니다.
- [부하 분산 및 트래픽 방향](#load-balancing): 동일한 위치에 있는 서버에 대한 트래픽 및 다른 위치에 있는 서버에 대한 직접 트래픽의 부하를 분산합니다.
- [보안](#security): 네트워크 서브넷 또는 개별 VM(가상 머신) 간에 네트워크 트래픽을 필터링합니다.
- [라우팅](#routing): Azure 및 온-프레미스 리소스 간에 기본 라우팅 또는 전체 제어 라우팅을 사용합니다.
- [관리 효율성](#manageability): Azure 네트워킹 리소스를 모니터링하고 관리합니다.
- [배포 및 구성 도구](#tools): 웹 기반 포털 또는 플랫폼 간 명령줄 도구를 사용하여 네트워크 리소스를 배포하고 구성합니다.

## <a name="connectivity"></a>Azure 리소스 간 연결

Virtual Machines, Cloud Services, Virtual Machines Scale Sets, Azure App Service Environment는와 같은 Azure 리소스는 Azure VNet(Virtual Network)를 통해 서로 개별적으로 통신할 수 있습니다. [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json) 전용 Azure 클라우드를 논리적으로 격리한 것이 VNet입니다. 각 Azure 구독 및 Azure [지역](https://azure.microsoft.com/regions) 내에서 여러 VNet을 구현할 수 있습니다. 각 VNet은 다른 VNet에서 격리됩니다. 각 VNet에 대해 다음을 수행할 수 있습니다.

- 공용 및 사설(RFC 1918) 주소를 사용하여 사용자 지정 사설 IP 주소 공간을 지정합니다. Azure에서는 VNet에 연결된 리소스에 사용자가 할당한 주소 공간의 사설 IP 주소를 할당합니다.
- VNet을 하나 이상의 서브넷으로 분할하고 VNet 주소 공간의 일부를 각 서브넷에 할당합니다.
- Azure 제공 이름 확인을 사용하거나 VNet에 연결된 리소스에서 사용할 수 있도록 자체 DNS 서버를 지정합니다.

Azure Virtual Network 서비스에 대해 자세히 알아보려면 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요. VNet을 서로 연결하여 VNet에 연결된 리소스가 VNet을 통해 서로 통신하도록 할 수 있습니다. 다음 옵션 중 하나 또는 둘 다를 사용하여 VNet을 서로 연결할 수 있습니다.

- **피어링:** 동일한 Azure 지역 내 다른 Azure VNet에 연결되어 있는 리소스가 서로 통신할 수 있도록 합니다. 여러 VNet 간의 대역폭 및 대기 시간은 동일한 VNet에 리소스가 연결된 경우와 같습니다. 피어링에 대한 자세한 내용은 [가상 네트워크 피어링 개요](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참조하세요.
- **VPN Gateway:** 다른 Azure 지역 내 다른 Azure VNet에 연결되어 있는 리소스가 서로 통신할 수 있도록 합니다. VNet 간의 트래픽은 Azure VPN Gateway를 통해 전송됩니다. VNet 간의 대역폭은 게이트웨이의 대역폭으로 제한됩니다. VPN Gateway와 VNet을 연결하는 방법에 대한 자세한 내용은 [지역 간에 VNet 간 연결 구성](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참조하세요.

## <a name="internet-connectivity"></a>인터넷 연결

VNet에 연결된 모든 Azure 리소스는 기본적으로 인터넷에 아웃바운드로 연결됩니다. 리소스의 사설 IP 주소는 Azure 인프라를 통해 공용 IP 주소로 SNAT(원본 네트워크 주소 변환)이 이루어집니다. 아웃바운드 인터넷 연결에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참조하세요.

인터넷에서 Azure 리소스에 인바운드로 통신하거나 SNAT 없이 인터넷에 아웃바운드로 통신하려면 리소스에 공용 IP 주소가 할당되어야 합니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조하세요.

## <a name="on-premises-connectivity"></a>온-프레미스 연결

VPN 연결 또는 직접 개별 연결을 통해 VNet의 리소스 액세스할 수 있습니다. Azure Virtual Network와 온-프레미스 네트워크 간에 네트워크 트래픽을 보내려면 가상 네트워크 게이트웨이를 만들어야 합니다. 게이트웨이의 설정을 구성하여 VPN 또는 ExpressRoute 중 하나로 원하는 연결 형식을 만듭니다.

다음 옵션을 조합하여 온-프레미스 네트워크를 VNet에 연결할 수 있습니다.

**지점 및 사이트 간(SSTP를 통한 VPN)**

다음 그림에서는 여러 컴퓨터 및 VNet 간에 개별 지점 및 사이트 연결을 보여줍니다.

![지점 및 사이트 간](./media/networking-overview/point-to-site.png)

단일 컴퓨터와 VNet 간에 이 연결이 설정됩니다. 이 연결 유형은 기존 네트워크를 거의 변경할 필요가 없으므로 Azure 사용을 막 시작하는 사용자나 개발자에게 유용합니다. 또한 컨퍼런스 또는 집과 같은 원격 위치에서 연결하는 경우 편리합니다. 종종 동일한 가상 네트워크 게이트웨이를 통해 사이트 간 연결과 지점 및 사이트 간 연결이 결합됩니다. 이 연결은 SSTP 프로토콜을 사용하여 컴퓨터 및 VNet 간에 인터넷을 통한 암호화된 통신을 제공합니다. 트래픽이 인터넷을 통과하므로 지점 및 사이트 간 VPN의 대기 시간은 예측할 수 없습니다.

**사이트 간(IPsec/IKE VPN 터널)**

![사이트 간](./media/networking-overview/site-to-site.png)

온-프레미스 VPN 디바이스와 Azure VPN Gateway 간에 이 연결이 설정됩니다. 이 연결 형식을 사용하면 권한을 부여한 모든 온-프레미스 리소스에서 VNet에 액세스할 수 있습니다. 이 연결은 온-프레미스 디바이스와 Azure VPN Gateway 간에 인터넷을 통한 암호화된 통신을 제공하는 IPSec/IKE VPN입니다. 동일한 VPN Gateway에 여러 온-프레미스 사이트를 연결할 수 있습니다. 각 사이트의 온-프레미스 VPN 디바이스에는 NAT를 기반으로 하지 않는 외부 연결 공용 IP 주소가 있어야 합니다. 트래픽이 인터넷을 통과하므로 사이트 간 연결의 대기 시간은 예측 가능하지 않습니다.

**ExpressRoute(개별 전용 연결)**

![ExpressRoute](./media/networking-overview/expressroute.png)

이 종류의 연결은 ExpressRoute 파트너를 통해 네트워크와 Azure 간에 설정됩니다. 이 연결은 전용입니다. 트래픽이 인터넷을 트래버스하지 않습니다. 트래픽이 인터넷을 통과하지 않으므로 ExpressRoute 연결에 대한 대기 시간을 예측할 수 있습니다. ExpressRoute는 사이트 간 연결과 함께 사용할 수 있습니다.

모든 이전 연결 옵션에 대한 자세한 내용은 [연결 토폴로지 다이어그램](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 읽어보세요.

## <a name="load-balancing"></a>부하 분산 및 트래픽 방향

Microsoft Azure는 네트워크 트래픽을 분산하고 부하를 분산하는 방법을 관리하는 여러 서비스를 제공합니다. 개별로 또는 함께 다음과 같은 기능을 사용할 수 있습니다.

**DNS 부하 분산**

Azure Traffic Manager 서비스는 전역 DNS 부하 분산을 제공합니다. Traffic Manager는 다음 라우팅 방법 중 하나를 기반으로 하는 정상 엔드포인트의 IP 주소를 포함한 클라이언트에 대응합니다.
- **지리적:** 클라이언트가 해당 DNS 쿼리가 시작된 지리적 위치를 기반으로 하여 특정 엔드포인트(Azure, 외부 또는 중첩됨)에 지정됩니다. 이 메서드를 사용하면 클라이언트의 지리적 지역을 파악하고 이를 기준으로 라우팅하는 중요한 시나리오를 사용할 수 있습니다. 데이터 독립성 지시 사항, 콘텐츠 및 사용자 환경의 지역화를 준수하고 다른 지역의 트래픽을 측정하는 작업을 예로 들 수 있습니다.
- **성능:** 클라이언트에 반환되는 IP 주소는 클라이언트에 “가장 가까운” 주소입니다. '가장 가까운' 엔드포인트가 반드시 지리적 거리를 기준으로 가장 가깝게 지정되는 것은 아닙니다. 대신, 이 메서드는 네트워크 대기 시간을 측정하여 가장 가까운 엔드포인트를 결정합니다. Traffic Manager에는 IP 주소 범위와 각 Azure 데이터 센터 간의 왕복 시간을 추적하는 인터넷 대기 시간 테이블이 있습니다.
- **우선 순위:** 트래픽은 기본(가장 높은 우선 순위) 엔드포인트로 전달됩니다. 기본 엔드포인트를 사용할 수 없는 경우 Traffic Manager는 두 번째 엔드포인트에 트래픽을 라우팅합니다. 목록의 기본 엔드포인트 및 보조 엔드포인트를 모두 사용할 수 없는 경우 트래픽이 세 번째 엔드포인트 등으로 전송됩니다. 엔드포인트의 가용성은 구성된 상태(사용 또는 사용 안 함) 및 지속적인 엔드포인트 모니터링을 기반으로 합니다.
- **가중치 적용 라운드 로빈:** Traffic Manager는 각 요청에 대해 사용 가능한 엔드포인트를 임의로 선택합니다. 엔드포인트를 선택하는 확률은 사용 가능한 모든 엔드포인트에 할당된 가중치를 기반으로 합니다. 모든 엔드포인트 결과에서 동일한 가중치를 사용하면 균등하게 트래픽이 분포됩니다. 특정 엔드포인트에 더 높은(또는 더 낮은) 가중치를 적용하면 해당 엔드포인트가 DNS 응답에서 더(또는 덜) 자주 반환됩니다.

다음 그림에서는 Web App 엔드포인트에 전달되는 웹 애플리케이션의 요청을 보여줍니다. 엔드포인트는 VM 및 Cloud Services와 같은 다른 Azure 서비스일 수도 있습니다.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

클라이언트는 해당 엔드포인트에 직접 연결합니다. Azure Traffic Manager는 엔드포인트가 비정상임을 감지한 다음 클라이언트를 다른 정상적인 엔드포인트로 리디렉션합니다. Traffic Manager에 대해 자세히 알아보려면 [Azure Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.

**애플리케이션 부하 분산**

Azure Application Gateway 서비스는 ADC(Application Deliver Controller)를 서비스로 제공합니다. Application Gateway는 취약점 및 악용으로부터 웹 애플리케이션을 보호하는 웹 애플리케이션 방화벽을 비롯한 애플리케이션에 대한 다양한 계층 7(HTTP/HTTPS) 부하 분산 기능을 제공합니다. Application Gateway를 통해 애플리케이션 게이트웨이에 CPU 집약적인 SSL 종료를 오프로드하여 웹 팜 생산성을 최적화할 수도 있습니다. 

Layer 7의 기타 라우팅 기능으로 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 패스 기반 라우팅 및 단일 Application Gateway 뒤에 여러 웹 사이트를 호스트할 수 있는 기능 등을 포함합니다. Application Gateway는 인터넷 연결 게이트웨이, 내부 전용 게이트웨이 또는 둘의 조합으로 구성될 수 있습니다. Application Gateway는 전적으로 Azure에 의해 관리되고, 확장성 및 고가용성을 제공합니다. 관리 효율성을 향상시키기 위한 풍부한 진단 및 로깅 기능을 제공합니다. Application Gateway에 대한 자세한 내용은 [Application Gateway 개요](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.

다음 그림에서는 Application Gateway를 사용하는 URL 경로 기반 라우팅을 보여줍니다.

![Application Gateway](./media/networking-overview/application-gateway.png)

**네트워크 부하 분산**

Azure Load Balancer는 모든 UDP 및 TCP 프로토콜에 대해 대기 시간이 낮은 고성능 계층 4 부하 분산을 제공합니다. 인바운드 및 아웃 바운드 연결을 관리합니다. 내부 부하가 분산된 공용 엔드포인트를 구성할 수 있습니다. 서비스 가용성 관리 옵션을 검색하는 TCP 및 HTTP 상태를 사용하여 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다. Load Balancer에 대한 자세한 내용은 [Load Balancer 개요](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.

다음 그림에서는 외부 및 내부 부하 분산 장치를 모두 활용하는 인터넷 연결 다중 계층 애플리케이션을 보여줍니다.

![부하 분산 장치](./media/networking-overview/load-balancer.png)

## <a name="security"></a>보안

다음 옵션을 사용하여 Azure 리소스 간에 트래픽을 필터링할 수 있습니다.

- **네트워크:** Azure NSG(네트워크 보안 그룹)를 구현하여 Azure 리소스에 대한 인바운드 및 아웃바운드 트래픽을 필터링할 수 있습니다. 각 NSG는 하나 이상의 인바운드 및 아웃바운드 규칙을 포함합니다. 각 규칙은 트래픽을 필터링하는 원본 IP 주소, 대상 IP 주소, 포트 및 프로토콜을 지정합니다. 개별 서브넷 및 개별 VM에 NSG를 적용할 수 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **애플리케이션:** 웹 애플리케이션 방화벽이 설치된 Application Gateway를 사용하여 취약성 및 악용으로부터 웹 애플리케이션을 보호할 수 있습니다. 일반적인 예로 SQL 주입 공격, 크로스 사이트 스크립팅 및 잘못된 형식의 헤더가 있습니다. Application Gateway는 이 트래픽을 필터링하고 웹 서버에 도달하지 않도록 중지합니다. 사용하려는 규칙을 구성할 수 있습니다. 특정 정책을 사용하지 않을 수 있도록 SSL 협상 정책을 구성하는 기능이 제공됩니다. 웹 애플리케이션 방화벽에 대한 자세한 내용은 [웹 애플리케이션 방화벽](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.

Azure에서 제공하지 않는 네트워크 기능이 필요하거나 온-프레미스를 사용하는 네트워크 애플리케이션을 사용하려면 VM에서 제품을 구현하고 VNet에 연결할 수 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)에는 현재 사용할 수 있는 네트워크 애플리케이션을 사용하여 미리 구성된 여러 가지 다른 VM이 포함됩니다. 이러한 미리 구성된 VM은 일반적으로 NVA(네트워크 가상 어플라이언스)라고 합니다. NVA는 방화벽, WAN 최적화와 같은 애플리케이션과 함께 사용할 수 있습니다.

## <a name="routing"></a>라우팅

Azure는 VNet의 모든 서브넷에 연결된 리소스가 서로 통신할 수 있도록 하는 기본 경로 테이블을 만듭니다. 다음과 같은 종류의 경로 중 하나 또는 둘 다를 구현하여 Azure에서 생성되는 기본 경로를 재정의할 수 있습니다.
- **사용자 정의:** 각 서브넷에 대해 트래픽이 라우팅되는 위치를 제어하는 경로를 사용하여 사용자 지정 경로 테이블을 만들 수 있습니다. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참조하세요.
- **BGP(Border Gateway Protocol):** Azure VPN Gateway 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에 VNet을 연결하는 경우 VNet으로 BGP 경로를 전파할 수 있습니다. BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. Azure Virtual Networks에서 BGP를 통해 Azure VPN Gateway 및 온-프레미스 VPN 디바이스(BGP 피어 또는 인접이라고 함)가 관련된 게이트웨이 또는 라우터를 거치도록 해당 접두사의 가용성 및 연결 가능성에 대한 정보를 두 게이트웨이에 제공하는 "경로"를 교환할 수 있습니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여, BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다. BGP에 대한 자세한 내용은 [Azure VPN Gateway를 사용하는 BGP 개요](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참조하세요.

## <a name="manageability"></a>관리 효율성

Azure에서는 다음과 같은 도구를 제공하여 네트워킹을 모니터링하고 관리합니다.
- **활동 로그:**. 모든 Azure 리소스에는 수행되는 작업, 작업의 상태 및 작업을 시작한 사용자에 대한 정보를 제공하는 활동 로그가 있습니다. 활동 로그에 대한 자세한 내용을 알아보려면 [활동 로그 개요](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **진단 로그:** 정기적이 고 자동적인 이벤트가 네트워크 리소스에서 생성 되 고 Azure 이벤트 허브로 전송 하거나 Azure Monitor 로그로 전송 된 Azure storage 계정에 기록 됩니다. 진단 로그는 리소스 상태에 대한 정보를 제공하며, 진단 로그는 Load Balancer(인터넷 연결), 네트워크 보안 그룹, 경로 및 Application Gateway에서 사용할 수 있습니다. 진단 로그에 대한 자세한 내용은 [진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **메트릭:** 메트릭은 리소스에 대해 일정 기간 동안 수집된 성능 측정 및 카운터입니다. 메트릭을 사용하여 임계값에 기반한 경고를 트리거할 수 있습니다. 현재 메트릭은 Application Gateway에서 사용할 수 있습니다. 메트릭에 대해 자세히 알아보려면 [메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 읽어보세요.
- **문제 해결:** Azure Portal에서 문제 해결 정보에 직접 액세스할 수 있습니다. 정보를 통해 ExpressRoute, VPN Gateway, Application Gateway, 네트워크 보안 로그, 경로, DNS, Load Balancer 및 Traffic Manager에서 발생하는 일반적인 문제를 진단할 수 있습니다.
- **RBAC(역할 기반 액세스 제어):** RBAC(역할 기반 액세스 제어)를 사용하여 네트워킹 리소스를 만들고 관리할 수 있는 사용자를 제어합니다. [RBAC 시작](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하여 RBAC에 대해 자세히 알아봅니다. 
- **패킷 캡처:** Azure Network Watcher 서비스는 VM 내에서 확장을 통해 VM에 대한 패킷 캡처를 실행하는 기능을 제공합니다. 이 기능은 Linux 및 Windows VM에서 사용할 수 있습니다. 패킷 캡처에 대한 자세한 내용은 [패킷 캡처 개요](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **IP 흐름 확인:** Network Watcher를 사용하면 Azure VM과 원격 리소스 간에 IP 흐름을 확인하여 패킷을 허용할지 아니면 거부할지를 결정할 수 있습니다. 이 기능은 관리자에게 신속하게 연결 문제를 진단하는 기능을 제공합니다. IP 흐름을 확인하는 방법에 대한 자세한 내용은 [IP 흐름 확인 개요](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **VPN 연결 문제 해결:** Network Watcher의 VPN 문제 해결사 기능은 연결 또는 게이트웨이를 쿼리하고 리소스의 상태를 확인하는 기능을 제공합니다. VPN 연결 문제를 해결하는 방법에 대한 자세한 내용은 [VPN 연결 문제 해결 개요](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.
- **네트워크 토폴로지 보기:** Network Watcher를 사용하여 VNet에서 네트워크 리소스의 그래픽 표현이 표시됩니다. 네트워크 토폴로지를 보는 방법에 대한 자세한 내용은 [토폴로지 개요](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요.

## <a name="tools"></a>도구 배포 및 구성

다음과 같은 도구를 사용하여 Azure 네트워킹 리소스를 배포하고 구성할 수 있습니다.

- **Azure Portal:** 브라우저에서 실행되는 그래픽 사용자 인터페이스입니다. [Azure Portal](https://portal.azure.com)을 엽니다.
- **Azure PowerShell:** Windows 컴퓨터에서 Azure를 관리하기 위한 명령줄 도구입니다. [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하여 Azure PowerShell에 대해 자세히 알아봅니다.
- **Azure CLI(명령줄 인터페이스):** Linux, macOS 또는 Windows 컴퓨터에서 Azure를 관리하기 위한 명령줄 도구입니다. [Azure CLI 개요](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하여 Azure CLI에 대해 자세히 알아봅니다.
- **Azure Resource Manager 템플릿:** Azure 솔루션의 인프라 및 구성을 정의하는 JSON 형식의 파일입니다. 템플릿을 사용하여 수명 주기 내내 솔루션을 반복적으로 배포하고 안심하고 일관된 상태로 리소스를 배포할 수 있습니다. 템플릿을 작성하는 방법에 대한 자세한 내용은 [템플릿 만들기 위한 모범 사례](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서를 참고하세요. Azure Portal, CLI 또는 PowerShell에서 템플릿을 배포할 수 있습니다. 템플릿을 즉시 사용하기 시작하려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=network) 라이브러리에서 미리 구성된 여러 템플릿 중 하나를 배포합니다. 

## <a name="pricing"></a>가격

다른 서비스를 추가 비용 없이 사용할 수 있는 반면 일부 Azure 네트워킹 서비스에는 요금이 부과됩니다. 자세한 내용은 [Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) 및 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 가격 책정 페이지를 봅니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 가상 네트워크 만들기](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 첫 번째 VNet을 만들고 일부 VM을 연결합니다.
- [지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 VNet에 컴퓨터를 연결합니다.
- [인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 공용 서버에 인터넷 트래픽의 부하를 분산합니다.
