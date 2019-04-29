---
title: Azure로 마이그레이션된 워크로드에 대한 네트워킹 설정 모범 사례 | Microsoft Docs
description: Azure로 마이그레이션되면 마이그레이션된 워크로드에 대한 네트워킹을 설정하는 모범 사례를 확인합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 302445038dc9767bd412e232f62fc5249a1a7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296537"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Azure로 마이그레이션된 워크로드에 대한 네트워킹 설정 모범 사례

마이그레이션을 계획하고 설계할 때 마이그레이션 자체 외에도 가장 중요한 단계 중 하나는 Azure 네트워킹의 설계 및 구현입니다. 이 문서에서는 Azure에서 IaaS 및 PaaS 구현으로 마이그레이션할 때 네트워킹에 대한 모범 사례에 대해 설명합니다.

> [!IMPORTANT]
> 이 문서에서 설명하는 모범 사례와 의견은 작성 당시의 사용 가능한 Azure 플랫폼과 서비스 기능을 기반으로 합니다. 특징과 기능은 시간이 지남에 따라 변경됩니다. 일부 추천 사항이 배포에 적용되지 않을 수 있으므로 적합한 추천 사항을 선택합니다.


## <a name="design-virtual-networks"></a>가상 네트워크 설계

Azure에서 제공하는 VNet(가상 네트워크)은 다음과 같습니다.
- Azure 리소스에서 VNet을 통해 비공개적으로, 직접, 안전하게 서로 통신합니다.
- 인터넷 통신이 필요한 VM 및 서비스에 대한 VNet에서 엔드포인트 연결을 구성할 수 있습니다.
- VNet은 구독 전용 Azure 클라우드의 논리적 격리입니다.
- 각 Azure 구독 및 Azure 지역 내에서 여러 VNet을 구현할 수 있습니다.
- 각 VNet은 다른 VNet에서 격리됩니다.
- VNet은 [RFC 1918](https://tools.ietf.org/html/rfc1918)에 정의된 전용 및 공용 IP 주소를 포함할 수 있으며, CIDR 표기법으로 표현됩니다. 공용 IP 주소는 인터넷에서 직접 액세스할 수 없습니다.
- VNet은 VNet 피어링을 사용하여 서로 연결할 수 있습니다. 연결된 VNet은 동일하거나 다른 지역에 있을 수 있습니다. 따라서 한 VNet의 리소스는 다른 VNet의 리소스에 연결할 수 있습니다.
- Azure는 기본적으로 VNet, 연결된 VNet, 온-프레미스 네트워크에 있는 서브넷과 인터넷 간에 트래픽을 라우팅합니다.


IP 주소 공간을 정렬하는 방법, 허브-스포크 네트워크를 구현하는 방법, VNet을 서브넷으로 분할하는 방법, DNS 설정 및 Azure 가용성 영역 구현 등 VNet 토폴로지를 계획할 때 고려해야 할 사항이 많이 있습니다.

## <a name="best-practice-plan-ip-addressing"></a>모범 사례: IP 주소 지정 계획

마이그레이션의 일환으로 VNet을 만들 때는 VNet IP 주소 공간을 계획해야 합니다.

- 각 VNet에 대해 /16의 CIDR 범위보다 크지 않은 주소 공간을 할당해야 합니다. VNet은 65536 IP 주소를 사용할 수 있으며, /16보다 작은 접두사를 할당하면 IP 주소가 손실됩니다. IP 주소가 RFC 1918에 정의된 사설 범위에 있더라도 IP 주소를 낭비하지 않아야 합니다.
- VNet 주소 공간은 온-프레미스 네트워크 범위와 겹치지 않아야 합니다.
- NAT(Network Address Translation)는 사용할 수 없습니다.
- 주소가 겹치면 네트워크에 연결할 수 없으며 라우팅이 제대로 작동하지 않을 수 있습니다. 네트워크가 겹치면 네트워크를 다시 설계하거나 NAT(Network Address Translation)를 사용해야 합니다.

**자세한 정보:**

- Azure VNet [개요 살펴보기](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- 네트워킹 FAQ [참조](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- 네트워킹 제한에 대해 [알아보기](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>모범 사례: 허브-스포크 네트워크 토폴로지 구현

허브-스포크 네트워크 토폴로지는 ID 및 보안과 같은 서비스를 공유하는 동안 워크로드를 격리합니다.
- 허브는 연결의 중심점 역할을 하는 Azure VNet입니다.
- 스포크는 VNet 피어링을 사용하여 허브 VNet에 연결하는 VNet입니다.
- 개별 작업을 스포크로 배포하는 반면 공유 서비스는 허브에 배포됩니다. 

다음을 고려해 보세요.
- Azure에 허브 및 스포크 토폴로지를 구현하면 온-프레미스 네트워크 연결, 방화벽 및 VNet 간 격리와 같은 일반적인 서비스가 중앙 집중화됩니다. 허브 VNet은 온-프레미스 네트워크에 대한 중앙 연결 지점과 스포크 VNet에서 호스팅되는 워크로드의 서비스 사용을 호스팅하는 위치를 제공합니다.
- 허브 및 스포크 구성은 일반적으로 대기업에서 사용됩니다. 더 작은 네트워크에서는 비용과 복잡성을 줄일 수 있는 더 간단한 설계를 고려할 수 있습니다.
- 스포크 VNet을 사용하여 다른 스포크와 별도로 관리되는 각 스포크를 통해 워크로드를 격리할 수 있습니다. 각 워크로드에는 여러 계층과 Azure 부하 분산 장치와 연결된 여러 서브넷이 포함될 수 있습니다.
- 허브 및 스포크 VNet은 서로 다른 리소스 그룹에서 구현할 수 있으며, 심지어 다른 구독에서도 구현할 수 있습니다. 다른 구독에서 가상 네트워크를 피어링하는 경우 구독을 동일하거나 다른 Azure AD(Active Directory) 테넌트에 연결할 수 있습니다. 이렇게 하면 허브 네트워크에서 유지 관리되는 서비스를 공유하는 동안 각 워크로드를 분산 관리할 수 있습니다.

![변경 관리](./media/migrate-best-practices-networking/hub-spoke.png)
*허브 및 스포크 토폴로지*

**자세한 정보:**

- 허브 및 스포크 토폴로지에 대한 [참조](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
- Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) 및 [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm) VM 실행을 위한 네트워크 추천 사항 가져오기
- VNet 피어링에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)


## <a name="best-practice-design-subnets"></a>모범 사례: 서브넷 설계

VNet 내에서 격리를 제공하려면 하나 이상의 서브넷으로 분할하고 VNet 주소 공간의 일부를 각 서브넷에 할당합니다.
- 각 VNet 내에 여러 서브넷을 만들 수 있습니다.
- Azure는 기본적으로 VNet 내의 모든 서브넷 간에 네트워크 트래픽을 라우팅합니다.
- 서브넷은 기술 및 조직 요구 사항에 따라 결정됩니다.  
- CIDR 표기법을 사용하여 서브넷을 만듭니다.
- 서브넷의 네트워크 범위를 결정할 때 Azure는 사용할 수 없는 각 서브넷에서 5개의 IP 주소를 유지한다는 점에 유의해야 합니다. 예를 들어 사용 가능한 가장 작은 서브넷인 /29(8개 IP 주소 포함)를 만들면 Azure에서 5개의 주소를 유지하므로 서브넷의 호스트에 할당할 수 있는 사용 가능한 주소는 3개뿐입니다.
- 대부분의 경우 /28을 가장 작은 서브넷으로 사용하는 것이 좋습니다.

### <a name="example"></a>예

다음 표에는 계획된 마이그레이션을 위해 서브넷으로 분할된 10.245.16.0/20의 주소 공간이 있는 VNet의 예가 나와 있습니다.

**서브넷** | **CIDR** | **주소** | **사용**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | 프런트 엔드/웹 계층 VM
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | 응용 프로그램 계층 VM
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | 데이터베이스 VM

**자세한 정보:**
- 서브넷 설계에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation)
- 가상 회사(Contoso)에서 마이그레이션을 위해 네트워킹 인프라를 준비한 방법 [알아보기](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)


## <a name="best-practice-set-up-a-dns-server"></a>모범 사례: DNS 서버 설정

VNet을 배포하는 경우 Azure에서 기본적으로 DNS 서버를 추가합니다. 이렇게 하면 VNet을 빠르게 구축하고 리소스를 배포할 수 있습니다. 그러나 이 DNS 서버는 해당 VNet의 리소스에 대한 서비스만 제공합니다. 여러 VNet을 함께 연결하거나 VNet에서 온-프레미스 서버에 연결하려면 추가적인 이름 확인 기능이 필요합니다. 예를 들어 가상 네트워크 간에 DNS 이름을 확인하려면 Active Directory가 필요할 수 있습니다. 이렇게 하려면 Azure에 고유한 사용자 지정 DNS 서버를 배포합니다.

- VNet의 DNS 서버는 DNS 쿼리를 Azure의 재귀 확인자에 전달할 수 있습니다. 이렇게 하면 해당 VNet 내에서 호스트 이름을 확인할 수 있습니다. 예를 들어 Azure에서 실행되는 도메인 컨트롤러는 자체 도메인에 대한 DNS 쿼리에 응답하고 다른 모든 쿼리를 Azure에 전달할 수 있습니다.
- VM은 DNS 전달을 통해 온-프레미스 리소스(도메인 컨트롤러를 통해)와 Azure에서 제공하는 호스트 이름(전달자 사용)을 모두 확인할 수 있습니다. Azure의 재귀 확인자에 대한 액세스는 168.63.129.16 가상 IP 주소를 사용하여 제공됩니다.
- 또한 DNS 전달을 통해 VNet 간의 DNS 확인이 가능하며 온-프레미스 머신이 Azure에서 제공하는 호스트 이름을 확인할 수 있습니다.
    - VM 호스트 이름을 확인하려면 DNS 서버 VM이 동일한 VNet에 있어야 하며 호스트 이름 쿼리를 Azure에 전달하도록 구성되어야 합니다.
    - DNS 접미사는 각 VNet마다 다르므로 확인을 위해 조건부 전달 규칙을 사용하여 DNS 쿼리를 올바른 VNet에 보낼 수 있습니다.
- 사용자 고유의 DNS 서버를 사용하는 겨우 각 VNet에 대해 여러 개의 DNS 서버를 지정할 수 있습니다. 또한 네트워크 인터페이스(Azure Resource Manager용) 또는 클라우드 서비스(클래식 배포 모델)당 여러 DNS 서버를 지정할 수도 있습니다.
- 네트워크 인터페이스 또는 클라우드 서비스에 지정된 DNS 서버는 VNet에 지정된 서버보다 우선적으로 적용됩니다.
- Azure Resource Manager 배포 모델에서는 VNet 및 네트워크 인터페이스에 DNS 서버를 지정할 수 있지만 VNet에서만 해당 설정을 사용하는 것이 가장 좋습니다.

    ![DNS 서버](./media/migrate-best-practices-networking/dns2.png) *VNet에 대한 DNS 서버*

**자세한 정보:**
- 사용자 고유의 DNS 서버를 사용하는 경우의 이름 확인에 대해 [알아보기](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)
- DNS 명명 규칙 및 제한 사항에 대해 [알아보기](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions)


## <a name="best-practice-set-up-availability-zones"></a>모범 사례: 가용성 영역 설정

가용성 영역은 고가용성을 높여 데이터 센터 장애로부터 애플리케이션과 데이터를 보호합니다.

- 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다.
- 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다.
- 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다.
- 한 지역 내에서 가용성 영역을 물리적으로 구분하면 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다.
- 영역 중복 서비스는 단일 실패 지점으로부터 보호하기 위해 가용성 영역 전체에서 애플리케이션과 데이터를 복제합니다. Azure는 가용성 영역을 통해 VM 가동 시간에 대한 99.99% SLA를 제공합니다.

    ![가용성 영역](./media/migrate-best-practices-networking/availability-zone.png) *가용성 영역*

- 컴퓨팅, 스토리지, 네트워킹 및 데이터 리소스를 한 영역 내에 배치하고 다른 영역에 이를 복제하여 마이그레이션 아키텍처에 고가용성을 계획하고 구축할 수 있습니다. 가용성 영역을 지원하는 Azure 서비스는 다음 두 가지 범주로 나뉩니다.
    - 영역 서비스: 리소스를 특정 영역에 연결합니다. 예: VM, 관리 디스크, IP 주소
    - 영역 중복 서비스: 리소스가 영역 간에 자동으로 복제됩니다. 예: 영역 중복 스토리지, Azure SQL Database.
- 인터넷 연결 워크로드 또는 애플리케이션 계층을 사용하여 표준 Azure 부하 분산 장치를 배포하여 영역 내결함성을 제공할 수 있습니다.

    ![부하 분산 장치](./media/migrate-best-practices-networking/load-balancer.png) *부하 분산 장치*

**자세한 정보:**
-   가용성 영역 [개요 살펴보기](https://docs.microsoft.com/azure/availability-zones/az-overview)



## <a name="design-hybrid-cloud-networking"></a>하이브리드 클라우드 네트워킹 설계

성공적으로 마이그레이션하려면 온-프레미스 회사 네트워크를 Azure에 연결해야 합니다. 이 경우 서비스가 Azure 클라우드에서 회사 사용자에게 제공되는 하이브리드 클라우드 네트워크라고 하는 상시(always-on) 연결을 만듭니다. 이 유형의 네트워크를 만드는 두 가지 방법은 다음과 같습니다.

- **사이트 간 VPN:** 호환되는 온-프레미스 VPN 디바이스와 VNet에 배포된 Azure VPN 게이트웨이 간에 사이트 간 연결을 설정합니다. 권한이 부여된 온-프레미스 리소스는 VNet에 액세스할 수 있습니다. 사이트 간 통신은 인터넷 경유 암호화된 터널을 통해 보내집니다. 
- **Azure ExpressRoute:** ExpressRoute 파트너를 통해 온-프레미스 네트워크와 Azure 간의 Azure ExpressRoute 연결을 설정합니다. 이 연결은 비공개적이며 인터넷을 통해 이동하지 않습니다.

**자세한 정보:**

- 하이브리드 클라우드 네트워킹에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn)

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>모범 사례: 고가용성 사이트 간 VPN 구현

사이트 간 VPN을 구현하려면 Azure에서 VPN 게이트웨이를 설정합니다.
- VPN 게이트웨이는 공용 인터넷을 통해 Azure VNet과 온-프레미스 위치 간에 암호화된 트래픽을 보내는 데 사용되는 특정 유형의 VNet 게이트웨이입니다.
- VPN 게이트웨이를 사용하여 Microsoft 네트워크를 통해 Azure VNet 간에 암호화된 트래픽을 보낼 수도 있습니다.
- 각 VNet에는 하나의 VPN 게이트웨이만 있을 수 있습니다.
- 동일한 VPN 게이트웨이에 대해 여러 개의 연결을 만들 수 있습니다. 여러 개의 연결을 만들면 모든 VPN 터널에서 사용 가능한 게이트웨이 대역폭을 공유합니다.
- 모든 Azure VPN Gateway는 활성-대기 구성 상태에 있는 두 인스턴스로 구성됩니다.
    - 계획된 유지 관리 또는 활성 인스턴스에 대한 계획되지 않은 중단에서는 장애 조치(failover)가 발생하고, 대기 인스턴스가 자동으로 인계되며, 사이트 간 또는 VNet 간 연결을 다시 시작합니다. 
    - 전환으로 인해 짧은 중단이 발생합니다.
    - 계획된 유지 관리를 위해 연결은 10-15초 내에 복원해야 합니다.
    - 계획되지 않은 문제의 경우 연결을 복구하는 데 더 오래 걸리며, 최악의 경우 1-1.5분 정도 걸립니다.
    - 게이트웨이에 대한 P2S(지점 및 사이트 간) VPN 클라이언트 연결이 끊기므로 사용자는 클라이언트 머신에서 다시 연결해야 합니다.

사이트 간 VPN을 설정하는 경우 다음을 수행합니다.
 - 주소 범위가 VPN에서 연결할 온-프레미스 네트워크와 겹치지 않는 VNet이 필요합니다.
 - 게이트웨이 서브넷을 네트워크에 만듭니다.
 - VPN 게이트웨이를 만들고, 게이트웨이 유형(VPN)을 지정하고,정책 기반 또는 경로 기반 게이트웨이인지를 지정합니다. 경로 기반 VPN을 더 능률적이고 미래 지향적인 방법으로 사용하는 것이 좋습니다.
 - 온-프레미스에서 로컬 네트워크 게이트웨이를 만들고, 온-프레미스 VPN 디바이스를 구성합니다. 
 - VNet 게이트웨이와 온-프레미스 디바이스 간에 장애 조치 사이트 간 VPN 연결을 만듭니다. 경로 기반 VPN을 사용하면 Azure에 대한 활성-수동 또는 활성-활성 연결을 고려합니다. 또한 경로 기반은 사이트 간(모든 컴퓨터에서) 연결과 사이트 간(단일 컴퓨터에서) 연결을 모두 동시에 지원합니다.
 - 사용하려는 게이트웨이 SKU를 지정합니다. 이는 워크로드 요구 사항, 처리량, 기능 및 SLA에 따라 달라집니다.
 - BGP(Border Gateway Protocol)는 Azure ExpressRoute 및 경로 기반 VPN 게이트웨이에서 사용하여 온-프레미스 BGP 경로를 VNet에 전파할 수 있는 선택적 기능입니다.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*사이트 간 VPN*
 
**자세한 정보:**

- 호환되는 온-프레미스 VPN 디바이스 [검토](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)
- VPN 게이트웨이 [개요 살펴보기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
- 고가용성 VPN 연결 정보에 대해 [알아보기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)
- VPN 게이트웨이 계획 및 설계에 대해 [알아보기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)
- VPN 게이트웨이 설정 [검토](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)
- 게이트웨이 SKU [검토](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)
- Azure VPN 게이트웨이를 사용한 BGP 설정에 대한 [참조](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>모범 사례: VPN Gateway에 대한 게이트웨이 구성

Azure에서 VPN 게이트웨이를 만들 때는 GatewaySubnet이라는 특수 서브넷을 사용해야 합니다. 이 서브넷 노트를 만들 때 다음 모범 사례:

- 게이트웨이 서브넷의 접두사 길이는 최대 접두사 길이인 29(예: 10.119.255.248/29)일 수 있습니다. 현재 접두사 길이로 27(예: 10.119.255.224/27)을 사용하는 것이 좋습니다.
- 게이트웨이 서브넷의 주소 공간을 정의할 때는 VNet 주소 공간의 가장 마지막 부분을 사용합니다.
- Azure 게이트웨이 서브넷을 사용하는 경우 VM 또는 다른 디바이스(예: Application Gateway)는 게이트웨이 서브넷에 배포하지 않습니다.
- NSG(네트워크 보안 그룹)는 이 서브넷에 할당하지 않습니다. 할당하는 경우 게이트웨이의 작동이 중지됩니다.

**자세한 정보:**
- IP 주소 공간을 확인하려면 [이 도구](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) 사용

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>모범 사례: 지점에 대한 Azure Virtual WAN 구현

여러 개의 VPN 연결이 있는 경우 Azure Virtual WAN은 Azure를 통해 최적화되고 자동화된 지점 간 연결을 제공하는 네트워킹 서비스입니다.
- Virtual WAN을 사용하여 Azure와 통신하도록 분기 디바이스를 연결 및 구성할 수 있습니다. 이 작업은 수동으로 수행하거나 Virtual WAN 파트너를 통해 기본 설정 공급자 디바이스를 사용하여 수행할 수 있습니다.
- 기본 설정 공급자 디바이스를 사용하면 간단한 사용, 연결 및 구성 관리가 가능합니다.
- Azure WAN 기본 제공 대시보드에서는 시간을 절약할 수 있는 즉각적인 문제 해결 인사이트를 제공하고, 대규모 사이트 간 연결을 쉽게 추적할 수 있는 방법을 제공합니다. 

**자세한 정보:**
Azure Virtual WAN에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about)

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>모범 사례: 중요 업무용 연결에 대한 ExpressRoute 구현

Azure ExpressRoute 서비스를 사용하면 가상 Azure 데이터 센터와 온-프레미스 네트워크 간의 사설 연결을 만들어 온-프레미스 인프라를 Microsoft 클라우드로 확장할 수 있습니다.
- ExpressRoute는 임의(IP VPN) 네트워크, 지점 간 이더넷 네트워크 또는 연결 공급자를 통해 연결될 수 있습니다. 이는 공용 인터넷을 통해 이동하지 않습니다.
- ExpressRoute 연결은 일관된 대기 시간과 함께 뛰어난 보안, 안정성, 최대 10Gbps의 속도를 제공합니다.
- 고객이 개인 연결과 관련된 규정 준수 규칙의 이점을 활용할 수 있으므로 ExpressRoute는 가상 데이터 센터에 유용합니다.
- ExpressRoute Direct를 사용하면 더 넓은 대역폭 요구 사항에 맞게 100Gbps 속도로 Microsoft 라우터에 직접 연결할 수 있습니다.
- ExpressRoute는 BGP를 사용하여 온-프레미스 네트워크, Azure 인스턴스 및 Microsoft 공용 주소 간의 경로를 교환합니다.

ExpressRoute 연결을 배포할 때 일반적으로 ExpressRoute 서비스 공급자와 연계해야 합니다. 빠른 시작을 위해 처음에는 사이트 간 VPN을 사용하여 가상 데이터 센터와 온-프레미스 리소스 간의 연결을 설정한 다음, 서비스 공급자와 물리적 상호 연결이 설정되면 ExpressRoute 연결로 마이그레이션하는 것이 일반적입니다.

**자세한 정보:**
- ExpressRoute [개요 살펴보기](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).
- ExpressRoute Direct에 대해 [알아보기](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about)

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>모범 사례: BGP 커뮤니티를 사용하여 ExpressRoute 라우팅 최적화

여러 개의 ExpressRoute 회로가 있는 경우 Microsoft에 연결되는 하나 이상의 경로가 있습니다. 결과적으로 최적이 아닌 라우팅이 발생할 수 있으며, 트래픽이 Microsoft에 도달하고 Microsoft에서 네트워크에 도달하는 경로가 더 길어질 수 있습니다. 네트워크 경로가 길수록 대기 시간도 늘어납니다. 대기 시간은 애플리케이션 성능 및 사용자 환경에 직접적인 영향을 줍니다.

#### <a name="example"></a>예

예를 들어 살펴보겠습니다.

- 미국에 두 개의 사무실이 있는데, 하나는 로스앤젤레스에, 다른 하나는 뉴욕에 있습니다.
- 사무실은 고유한 백본 네트워크 또는 서비스 공급자의 IP VPN에 연결할 수 있는 WAN에 연결되어 있습니다.
- 두 개의 ExpressRoute가 하나는 미국 서부에, 하나는 미국 동부에 있으며 WAN에도 연결됩니다. 물론 Microsoft 네트워크에 연결되는 두 경로가 있습니다.


 
**문제** 이제 미국 서부와 미국 동부 모두에 Azure 배포(예: Azure App Service)가 있다고 가정합니다.
- 각 사무실의 사용자가 최적의 환경을 위해 가장 가까운 Azure 서비스에 액세스하려고 합니다.
- 따라서 로스엔젤레스의 사용자를 Azure 미국 서부에 연결하고, 뉴욕의 사용자는 Azure 미국 동부에 연결하려고 합니다.
- 이는 서부 해안이 아니라 동부 해안의 사용자에 대해 작동합니다. 문제는 다음과 같습니다.
    - 각 ExpressRoute 회로에서 두 접두사를 모두 Azure 미국 동부(23.100.0.0/16)와 Azure 미국 서부(13.100.0.0/16)에 보급합니다.
    - 어느 지역의 접두사인지 인식하지 못하면 접두사가 다르게 취급되지 않습니다.
    - WAN 네트워크에서는 두 접두사가 미국 서부보다 미국 동부에 더 가깝다고 가정할 수 있습니다. 이에 따라 두 사무실의 사용자를 미국 동부의 ExpressRoute 회로로 라우팅하여 로스엔젤레스 사무실의 사용자에게 최적의 환경을 제공하지 않습니다.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*BGP 커뮤니티에서 최적화 되지 않은 연결*

**해결 방법**

두 사무실의 사용자에 대한 라우팅을 최적화하려면 Azure 미국 서부 및 Azure 미국 동부의 접두사를 알고 있어야 합니다. 이 정보는 BGP 커뮤니티 값을 사용하여 인코딩할 수 있습니다.
- 고유한 BGP 커뮤니티 값을 각 Azure 지역에 할당합니다. 예를 들어 미국 동부에는 12076:51004를, 미국 서부에는 12076:51006을 할당합니다.
- 이제 어떤 접두사가 어느 Azure 지역에 속하는지 명확하게 알고 있으므로 기본 설정 ExpressRoute 회로를 구성할 수 있습니다.
- BGP를 사용하여 라우팅 정보를 교환하므로 BGP의 로컬 기본 설정을 사용하여 라우팅에 영향을 줄 수 있습니다.
- 이 예에서는 미국 동부보다 미국 서부의 13.100.0.0/16에 더 높은 로컬 기본 설정 값을 할당하며, 마찬가지로 미국 서부보다 미국 동부의 23.100.0.0/16에 더 높은 로컬 기본 설정 값을 할당합니다. 
- 이 구성은 Microsoft에 대한 두 경로를 모두 사용할 수 있을 때, 로스엔젤레스의 사용자가 서부 회로를 사용하여 Azure 미국 서부에 연결하고, 뉴욕의 사용자는 동부 회로를 사용하여 Azure 미국 동부에 연결하도록 보장합니다. 라우팅이 양쪽 모두에서 최적화됩니다.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*BGP 커뮤니티에서 최적화된 연결*


**자세한 정보:**
- 라우팅 최적화에 대해 [알아보기](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing)

## <a name="securing-vnets"></a>Vnet 보안

VNET 보안에 대한 책임은 Microsoft와 사용자 간에 공유됩니다. Microsoft는 많은 네트워킹 기능뿐만 아니라 리소스를 안전하게 유지하는 데 도움이 되는 서비스도 제공합니다. VNet에 대한 보안을 설계하는 경우 경계 네트워크 구현, 필터링 및 보안 그룹 사용, 리소스 및 IP 주소에 대한 액세스 보호, 공격 보호 구현을 포함하여 따라야 할 여러 가지 모범 사례가 있습니다.

**자세한 정보:**

- 네트워크 보안에 대한 모범 사례 [개요 살펴보기](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
- 보안 네트워크 설계에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security)

## <a name="best-practice-implement-an-azure-perimeter-network"></a>모범 사례: Azure 경계 네트워크 구현

Microsoft는 클라우드 인프라를 보호하는 데 많은 투자를 하고 있지만, 사용자는 자신의 클라우드 서비스와 리소스 그룹도 보호해야 합니다. 다단계 접근 방식의 보안이 최상의 방어를 제공합니다. 경계 네트워크를 설치하는 것은 방어 전략의 중요한 부분입니다.

- 경계 네트워크는 신뢰할 수 없는 네트워크로부터 내부 네트워크 리소스를 보호합니다. 
- 이 네트워크는 인터넷에 노출되는 가장 바깥쪽 계층입니다. 일반적으로 인터넷과 엔터프라이즈 인프라 사이에 위치하며, 대개 양쪽 모두에 어떤 형태의 보호가 있습니다. 
- 일반적인 엔터프라이즈 네트워크 토폴로지에서 핵심 인프라는 여러 계층의 보안 디바이스를 통해 경계에서 강력히 보호됩니다. 각 계층의 경계는 디바이스와 정책 적용 지점으로 구성됩니다.
- 각 계층에는 방화벽, DoS(서비스 거부) 방지, 침입 탐지/침입 방지 시스템(IDS/IPS) 및 VPN 디바이스를 포함하는 네트워크 보안 솔루션의 조합이 포함될 수 있습니다.
- 경계 네트워크에 대한 정책 적용에서는 방화벽 정책, ACL(액세스 제어 목록) 또는 특정 라우팅을 사용할 수 있습니다.
- 들어오는 트래픽이 인터넷에서 도착하면 공격과 유해한 트래픽을 차단하는 방어 솔루션을 조합하여 차단되고 처리되는 반면, 네트워크에 대한 합법적인 요청이 허용됩니다.
- 들어오는 트래픽은 경계 네트워크의 리소스로 직접 라우팅될 수 있습니다. 경계 네트워크 리소스는 네트워크에서 다른 리소스와 심층적으로 통신할 수 있으며, 유효성 검사 후에 트래픽을 네트워크로 전달합니다.

다음 그림은 두 보안 경계가 있는 회사 네트워크의 단일 서브넷 경계 네트워크 예를 나타냅니다.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*경계 네트워크 배포*

**자세한 정보:**
- Azure와 온-프레미스 데이터 센터 간의 경계 네트워크 배포에 대해 [알아보기](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>모범 사례: NSG를 사용하여 VNet 트래픽 필터링

NSG(네트워크 보안 그룹)에는 리소스에서 들어오고 나가는 트래픽을 필터링하는 여러 개의 인바운드 및 아웃바운드 보안 규칙이 있습니다. 원본/대상 IP 주소, 포트 및 프로토콜을 기준으로 필터링할 수 있습니다. 
- NSG에는 여러 종류의 Azure 리소스로의 인바운드 트래픽 또는 이러한 리소스로부터의 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 있습니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다.
- NSG 규칙은 5개의 튜플 정보(원본, 원본 포트, 대상, 대상 포트 및 프로토콜)를 사용하는 우선 순위를 통해 평가되어 트래픽을 허용하거나 거부합니다.
- 기존 연결에 대한 흐름 레코드가 만들어집니다. 통신은 흐름 레코드의 연결 상태에 따라 허용 또는 거부됩니다.
- 흐름 레코드를 통해 NSG를 상태 저장할 수 있습니다. 예를 들어 80 포트를 통해 임의의 주소에 아웃바운드 보안 규칙을 지정하는 경우 아웃바운드 트래픽에 응답하는 인바운드 보안 규칙이 필요하지 않습니다. 통신이 외부에서 시작된 경우 인바운드 보안 규칙을 지정하기만 하면 됩니다.
- 반대의 경우도 마찬가지입니다. 포트를 통해 인바운드 트래픽이 허용되는 경우 포트를 통해 트래픽에 응답하도록 아웃바운드 보안 규칙을 지정할 필요가 없습니다.
- 흐름을 사용하도록 설정한 보안 규칙을 제거해도 기존 연결은 중단되지 않습니다. 연결이 중지되면 트래픽 흐름이 중단되고 트래픽이 어느 방향으로든 몇 분 이상 흐르지 않습니다.
- NSG를 만드는 경우 가능한 한 적지만 필요한 만큼만 만듭니다.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>모범 사례: 남/북 및 동/서 트래픽 보안

Vnet을 보호하는 경우 공격 벡터를 고려해야 합니다.
- 서브넷 NSG만 사용하면 환경을 간소화하지만 서브넷에 대한 트래픽만 보호할 수 있습니다. 이는 남/북 트래픽이라고 합니다.
- 동일한 서브넷의 VM 간 트래픽은 동/서 트래픽이라고 합니다.
- 두 가지 형태의 보호를 모두 활용하는 것이 중요합니다. 이에 따라 해커가 외부에서 액세스 권한을 획득하면 동일한 서브넷에 있는 머신을 연결하려고 할 때 중지됩니다.

### <a name="use-service-tags-on-nsgs"></a>NSG에서 서비스 태그 사용

서비스 태그는 IP 주소 접두사의 그룹을 나타냅니다. 서비스 태그를 사용하면 NSG 규칙을 만들 때 복잡성을 최소화할 수 있습니다.
- 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.
- Microsoft는 서비스 태그와 연결된 주소 접두사를 관리하고 주소가 변경되면 해당 서비스 태그를 자동으로 업데이트합니다.
- 사용자 고유의 서비스 태그를 직접 만들 수 없거나 태그 내에 포함할 IP 주소를 지정할 수 없습니다.

서비스 태그는 Azure 서비스 그룹에 규칙을 할당하는 수동 작업을 수행합니다. 예를 들어 Azure SQL Database에 대한 웹 서버 액세스 권한이 있는 VNet 서브넷을 허용하려면 1433 포트에 대한 아웃바운드 규칙을 만들고 **Sql** 서비스 태그를 사용할 수 있습니다.
- 이 **Sql** 태그는 Azure SQL Database 및 Azure SQL Data Warehouse 서비스의 주소 접두사를 나타냅니다.
- 값으로 **Sql**을 지정하면 Sql에 대한 트래픽이 허용되거나 거부됩니다.
- 특정 지역에서만 **Sql**에 대한 액세스를 허용하려는 경우 해당 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역의 Azure SQL Database에 대한 액세스만 허용하려면 **Sql.EastUS**를 서비스 태그로 지정할 수 있습니다.
- 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 Azure SQL Database 서비스를 나타내지만, 특정 SQL 데이터베이스 또는 서버를 나타내지는 않습니다.
- 이 태그를 통해 표시되는 모든 주소 접두사는 **인터넷** 태그를 통해서도 표시됩니다.


**자세한 정보:**

- NSG에 대한 [참조](https://docs.microsoft.com/azure/virtual-network/security-overview).
- NSG에 사용할 수 있는 서비스 태그 [검토](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).


## <a name="best-practice-use-application-security-groups"></a>모범 사례: 애플리케이션 보안 그룹 사용

애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연스러운 확장으로 구성할 수 있습니다.

- VM을 그룹화하고, 애플리케이션 보안 그룹을 기반으로 하는 네트워크 보안 정책을 정의할 수 있습니다.
- 애플리케이션 보안 그룹을 사용하면 명시적 IP 주소를 수동으로 유지 관리하지 않고도 보안 정책을 규모에 맞게 다시 사용할 수 있습니다.
- 애플리케이션 보안 그룹은 명시적 IP 주소 및 여러 규칙 세트의 복잡성을 처리하여 사용자가 비즈니스 논리에 집중할 수 있도록 합니다. 

### <a name="example"></a>예

![애플리케이션 보안 그룹](./media/migrate-best-practices-networking/asg.png)
*애플리케이션 보안 그룹 예*

**네트워크 인터페이스** | **애플리케이션 보안 그룹**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- 이 예에서 각 네트워크 인터페이스는 단 하나의 애플리케이션 보안 그룹에 속하지만, 실제로 인터페이스는 Azure 제한에 따라 여러 그룹에 속할 수 있습니다.
- 어떤 네트워크 인터페이스에도 연결된 NSG가 없습니다. NSG1은 두 서브넷에 연결되며 다음 규칙을 포함하고 있습니다.

    **규칙 이름** | **목적** | **세부 정보**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | 인터넷에서 웹 서버로의 트래픽을 허용합니다. 인터넷의 인바운드 트래픽이 DenyAllInbound 기본 보안 규칙에 따라 거부되므로 AsgLogic 또는 AsgDb 애플리케이션 보안 그룹에 추가 규칙이 필요하지 않습니다. | 우선 순위: 100<br/><br/> 원본: 인터넷<br/><br/> 원본 포트: *<br/><br/> 대상: AsgWeb<br/><br/> 대상 포트: 80<br/><br/> 프로토콜: TCP<br/><br/> 액세스: 허용
    Deny-Database-All | AllowVNetInBound 기본 보안 규칙은 동일한 VNet에 있는 리소스 간의 모든 통신을 허용합니다. 이 규칙은 모든 리소스의 트래픽을 거부하는 데 필요합니다. | 우선 순위: 120<br/><br/> 원본: *<br/><br/> 원본 포트: *<br/><br/> 대상: AsgDb<br/><br/> 대상 포트: 1433<br/><br/> 프로토콜: 모두<br/><br/> 액세스: 거부
    Allow-Database-BusinessLogic | AsgLogic 애플리케이션 보안 그룹에서 AsgDb 애플리케이션 보안 그룹으로의 트래픽을 허용합니다. 이 규칙의 우선 순위는 Deny-Database-All 규칙보다 높으며, 해당 규칙보다 먼저 처리되므로 AsgLogic 애플리케이션 보안 그룹의 트래픽이 허용되지만, 다른 모든 트래픽은 차단됩니다. | 우선 순위: 110<br/><br/> 원본: AsgLogic<br/><br/> 원본 포트: *<br/><br/> 대상: AsgDb<br/><br/> 대상 포트: 1433<br/><br/> 프로토콜: TCP<br/><br/> 액세스: 허용

- 원본 또는 대상으로 애플리케이션 보안 그룹을 지정하는 규칙은 애플리케이션 보안 그룹의 멤버인 네트워크 인터페이스에만 적용됩니다. 네트워크 인터페이스가 애플리케이션 보안 그룹의 멤버가 아닌 경우에는 네트워크 보안 그룹이 서브넷과 연결되어도 규칙이 네트워크 인터페이스에 적용되지 않습니다.

**자세한 정보:**

- 애플리케이션 보안 그룹에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>모범 사례: VNet 서비스 엔드포인트를 사용하여 PaaS에 대한 액세스 보안

VNet 서비스 엔드포인트는 직접 연결을 통해 VNet 사설 주소 공간과 ID를 Azure 서비스로 확장합니다.

- 엔드포인트를 사용하면 중요한 Azure 서비스 리소스를 VNet에만 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.
- VNet 사설 주소 공간은 겹칠 수 있으므로 VNet에서 발생한 트래픽을 고유하게 식별하는 데 사용할 수 없습니다.
- VNet에서 서비스 엔드포인트를 사용하도록 설정되면 서비스 리소스에 VNet 규칙을 추가하여 Azure 서비스 리소스를 보호할 수 있습니다. 이렇게 하면 리소스에 대한 공용 인터넷 액세스를 완전히 제거하고 VNet의 트래픽만 허용하여 보안이 향상됩니다.

![서비스 엔드포인트](./media/migrate-best-practices-networking/endpoint.png)
*서비스 엔드포인트*

**자세한 정보:**

- VNet 서비스 엔드포인트에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)


## <a name="best-practice-control-public-ip-addresses"></a>모범 사례: 공용 IP 주소 제어

Azure의 공용 IP 주소는 VM, 부하 분산 장치, 애플리케이션 게이트웨이 및 VPN 게이트웨이와 연결할 수 있습니다.

- 공용 IP 주소를 사용하면 인터넷 리소스에서 Azure 리소스로 인바운드 방식으로 통신하고, Azure 리소스에서 인터넷으로 아웃바운드 방식으로 통신할 수 있습니다.
- 공용 IP 주소는 기본 SKU 또는 표준 SKU를 사용하여 만들어지며, 이들 간에는 여러 가지 차이점이 있습니다. 표준 SKU는 모든 서비스에 할당할 수 있지만, 일반적으로 VM, 부하 분산 장치 및 애플리케이션 게이트웨이에 구성됩니다.
- 기본 공용 IP 주소에는 NSG가 자동으로 구성되지 않는다는 점에 주의해야 합니다. 사용자 고유의 액세스를 구성하고 이 액세스를 제어하는 규칙을 할당해야 합니다. 표준 SKU IP 주소에는 기본적으로 NSG와 규칙이 할당되어 있습니다.
- 모범 사례로, VM은 공용 IP 주소를 사용하여 구성하지 않아야 합니다.
    - 열려 있는 포트가 필요한 경우 80 또는 443 포트와 같은 웹 서비스에만 사용해야 합니다.
    - SSH(22) 및 RDP(3389)와 같은 표준 원격 관리 포트는 NSG를 사용하여 다른 모든 포트와 함께 거부되도록 설정해야 합니다.
- 더 나은 방법으로 VM을 Azure 부하 분산 장치 또는 애플리케이션 게이트웨이 뒤에 배치하는 것이 좋습니다. 그런 다음, 원격 관리 포트에 액세스해야 하는 경우 Azure Security Center에서 JIT(Just-In-Time) VM 액세스를 사용할 수 있습니다.

**자세한 정보:**

- Azure의 공용 IP 주소에 대해 [알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- Azure Security Center의 JIT VM 액세스에 대한 [자세한 참조](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)


## <a name="leverage-azure-security-features-for-networking"></a>네트워킹에 Azure 보안 기능 활용

Azure에는 사용하기 쉽고 일반적인 네트워크 공격에 대해 다양한 대책을 제공하는 플랫폼 보안 기능이 있습니다. 이러한 기능으로 Azure Firewall, 웹 애플리케이션 방화벽 및 Network Watcher가 있습니다.

## <a name="best-practice-deploy-azure-firewall"></a>모범 사례: Azure Firewall 배포

Azure Firewall은 VNet 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 기본적으로 제공되는 완벽한 상태 저장 방화벽 기반 서비스(firewall-as-a-service)입니다.

![서비스 엔드포인트](./media/migrate-best-practices-networking/firewall.png)
*Azure Firewall*

- Azure Firewall에서는 구독 및 VNet 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고, 기록할 수 있습니다.
- Azure Firewall은 VNet 리소스에 고정 공용 IP 주소를 사용하므로 외부 방화벽이 VNet에서 시작된 트래픽을 식별할 수 있습니다.
- Azure Firewall은 로깅 및 분석을 위해 Azure Monitor와 완벽하게 통합됩니다.
- Azure Firewall 규칙을 만드는 경우 FQDN 태그를 사용하여 규칙을 만드는 것이 가장 좋습니다.
    - FQDN 태그는 잘 알려진 Microsoft 서비스와 연결된 FQDN 그룹을 나타냅니다.
    - FQDN 태그를 사용하여 방화벽을 통해 필요한 아웃바운드 네트워크 트래픽을 허용할 수 있습니다.
- 예를 들어 방화벽을 통해 Windows 업데이트 네트워크 트래픽을 수동으로 허용하려면 여러 애플리케이션 규칙을 만들어야 합니다. FQDN 태그를 사용하여 애플리케이션 규칙을 만들고 Windows 업데이트 태그를 포함시킵니다. 이 규칙을 적용하면 Microsoft Windows 업데이트 엔드포인트에 대한 네트워크 트래픽을 방화벽을 통해 전달할 수 있습니다.

**자세한 정보:**

- Azure Firewall [개요 살펴보기](https://docs.microsoft.com/azure/firewall/overview).
- FQDN 태그에 대해 [알아보기](https://docs.microsoft.com/azure/firewall/fqdn-tags).


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>모범 사례: Azure WAF(웹 애플리케이션 방화벽) 배포

웹 애플리케이션은 점점 더 일반적으로 알려진 취약성을 악용하는 악의적인 공격의 대상이 되고 있습니다. 악용에는 SQL 삽입 공격과 사이트 간 스크립팅 공격이 포함됩니다. 애플리케이션 코드에서 이러한 공격을 방지하는 것은 매우 어려울 수 있으며, 애플리케이션 토폴로지의 여러 계층에서 엄격히 유지 관리하고, 패치를 적용하고, 모니터링해야 할 수 있습니다. 중앙 집중식 웹 애플리케이션 방화벽을 통해 보안 관리가 훨씬 간단해지고 애플리케이션 관리자가 위협 또는 침입으로부터 보호할 수 있습니다. 웹 애플리케이션 방화벽은 각각의 개별 웹 애플리케이션을 보호하는 대신 중앙 위치에서 알려진 취약성에 패치를 적용하여 보안 위협에 더 빠르게 대응할 수 있습니다. 기존 Application Gateway는 웹 애플리케이션 방화벽을 사용한 Application Gateway로 쉽게 변환될 수 있습니다.

Azure WAF(웹 애플리케이션 방화벽)는 Azure 애플리케이션 게이트웨이의 기능입니다.
- WAF는 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다.
- WAF는 백 엔드 코드를 수정하지 않고 보호합니다.
- 여러 웹 애플리케이션을 애플리케이션 게이트웨이 뒤에서 동시에 보호할 수 있습니다.
- WAF는 Azure Security Center와 통합됩니다.
- WAF 규칙 및 규칙 그룹을 애플리케이션 요구 사항에 맞게 사용자 지정할 수 있습니다.
- WAF는 Azure VM의 애플리케이션을 포함한 모든 웹 연결 애플리케이션 앞에서 사용하거나 Azure App Service로 사용하는 것이 가장 좋습니다.

**자세한 정보:**
- WAF에 대해 [알아보기](https://docs.microsoft.com/azure/application-gateway/waf-overview)
- WAF 제한 및 제외 [검토](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration)


## <a name="best-practice-implement-azure-network-watcher"></a>모범 사례: Azure Network Watcher 구현

Azure Network Watcher는 Azure VNet의 리소스와 통신을 모니터링하는 도구를 제공합니다. 예를 들어 VM과 엔드포인트(예; 다른 VM 또는 FQDN) 간의 통신을 모니터링하거나, VNet의 리소스 및 리소스 관계를 보거나, 네트워크 트래픽 문제를 진단할 수 있습니다.

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- Network Watcher를 사용하면 VM에 로그인하지 않고도 네트워킹 문제를 모니터링하고 진단할 수 있습니다.
- 경고를 설정하여 패킷 캡처를 트리거하고, 패킷 수준에서 실시간 성능 정보에 액세스할 수 있습니다. 문제가 발견되면 자세히 조사할 수 있습니다.
- Network Watcher를 사용하여 NSG 흐름 로그를 검토하는 것이 가장 좋습니다.
    - Network Watcher의 NSG 흐름 로그를 사용하면 NSG를 통한 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있습니다.
    - 흐름 로그는 json 형식으로 작성됩니다.
    - 흐름 로그는 규칙별 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC(네트워크 인터페이스), 흐름에 대한 5개 튜플 정보(원본/대상 IP, 원본/대상 포트 및 프로토콜) 및 트래픽이 허용되거나 거부되었는지 여부를 보여줍니다.

**자세한 정보:**

- Network Watcher [개요 살펴보기](https://docs.microsoft.com/azure/network-watcher).
- NSG 흐름 로그에 대해 [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview).

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Azure Marketplace의 파트너 도구 사용

더 복잡한 네트워크 토폴로지의 경우 Microsoft 파트너, 특히 NVA(네트워크 가상 어플라이언스)의 보안 제품을 사용할 수 있습니다.

- NVA는 방화벽, WAN 최적화 또는 기타 네트워크 기능과 같은 네트워크 기능을 수행하는 VM입니다.
- NVA는 VNet 보안 및 네트워크 기능을 강화합니다. 즉 고가용성 방화벽, 침입 방지, 침입 탐지, WAF(웹 애플리케이션 방화벽), WAN 최적화, 라우팅, 부하 분산, VPN, 인증서 관리, Active Directory 및 다단계 인증을 위해 배포할 수 있습니다.
- NVA는  [Azure Marketplace](https://azuremarketplace.microsoft.com/)의 수많은 공급업체에서 사용할 수 있습니다. 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>모범 사례: 허브 네트워크에 방화벽 및 NVA 구현

허브에서 인터넷에 액세스하는 경계 네트워크는 일반적으로 Azure Firewall, 방화벽 팜 또는 WAF(웹 애플리케이션 방화벽)를 통해 관리됩니다. 다음 비교를 고려해 보세요.

**방화벽 유형** | **세부 정보**
--- | ---
WAF | 웹 애플리케이션은 널리 알려져 있으며, 취약성과 잠재적 악용으로 어려움을 겪는 경향이 있습니다.<br/><br/> WAF는 일반 방화벽보다 웹 애플리케이션(HTTP/HTTPS)에 대한 공격을 탐지하도록 설계되었습니다.<br/><br/> 전형적인 방화벽 기술과 비교할 때 WAF에는 내부 웹 서버를 위협으로부터 보호하는 일단의 특정 기능이 있습니다.
Azure Firewall | NVA 방화벽 팜과 마찬가지로 Azure Firewall은 일반적인 관리 메커니즘과 스포크 네트워크에서 호스팅되는 워크로드를 보호하고 온-프레미스 네트워크에 대한 액세스를 제어하는 보안 규칙 세트를 사용합니다.<br/><br/> Azure Firewall에는 기본적으로 제공되는 확장성이 있습니다.
NVA 방화벽 | Azure Firewall과 마찬가지로 NVA 방화벽 팜은 일반적인 관리 메커니즘과 스포크 네트워크에서 호스팅되는 워크로드를 보호하고 온-프레미스 네트워크에 대한 액세스를 제어하는 보안 규칙 세트를 사용합니다.<br/><br/> NVA 방화벽은 부하 분산 장치 뒤에서 수동으로 크기 조정할 수 있습니다.<br/><br/> 방화벽 팜은 WAF에 비해 덜 전문화된 소프트웨어를 사용하지만 송신 및 수신 중인 모든 유형의 트래픽을 필터링하고 검사할 수 있는 애플리케이션의 범위가 더 넓습니다.<br/><br/> NVA를 사용하려면 Azure Marketplace에서 NVA를 찾을 수 있습니다.

인터넷에서 시작하는 트래픽에 하나의 Azure Firewalls 세트(또는 NVA)를 사용하고, 온-프레미스에서 시작하는 트래픽에 또 하나의 세트를 사용하는 것이 좋습니다.
- 방화벽 집합 하나를 두 트래픽에 모두 사용하면 두 네트워크 트래픽 집합 사이에 보안 경계가 없어지므로 보안 위험이 초래됩니다.
- 별도의 방화벽 계층을 사용하면 보안 규칙을 검사하는 복잡성이 줄어들고, 들어오는 네트워크 요청에 해당하는 규칙을 명확하게 알 수 있습니다.

**자세한 정보:**
- Azure VNet의 NVA 사용에 대해 [알아보기](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="next-steps"></a>다음 단계 

다른 모범 사례를 검토합니다.

- 마이그레이션 이후의 보안 및 관리에 대한 [모범 사례](migrate-best-practices-security-management.md)
- 마이그레이션 이후의 비용 관리에 대한 [모범 사례](migrate-best-practices-costs.md)
