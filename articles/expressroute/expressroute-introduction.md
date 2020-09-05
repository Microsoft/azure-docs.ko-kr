---
title: 'Azure ExpressRoute 개요: 프라이빗 연결을 통해 연결'
description: ExpressRoute 기술 개요에서는 프라이빗 연결을 통해 온-프레미스 네트워크를 Azure로 확장하기 위해 ExpressRoute 연결이 작동하는 방식에 대해 설명합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 08/25/2020
ms.author: mialdrid
ms.openlocfilehash: 6621a9778025d67046722b7cd60e32a62b574ee5
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852937"
---
# <a name="what-is-azure-expressroute"></a>Azure ExpressRoute란?
ExpressRoute를 사용하면 연결 공급자가 지원하는 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure 및 Office 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

공동 배치 시설에서 연결 공급자를 통해 임의의(IP VPN) 네트워크, 지점간 이더넷 네트워크 또는 가상 간 연결에서 연결할 수 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 이 기능을 사용하면 ExpressRoute 연결은 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 일관되고 보안성이 높습니다. ExpressRoute를 사용하여 네트워크를 Microsoft에 연결하는 방법에 대한 내용은 [ExpressRoute 연결 모델](expressroute-connectivity-models.md)을 참조하세요.

![ExpressRoute 연결 개요](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>주요 이점

* 연결 공급자를 통한 온-프레미스 네트워크와 Microsoft Cloud 간의 3계층 연결입니다. 임의의(IPVPN) 네트워크, 지점간 이더넷 연결, 이더넷 Exchange로 가상 간 연결을 통해 연결할 수 있습니다.
* 모든 지정학적 지역에 걸쳐 Microsoft 클라우드 서비스에 연결합니다.
* ExpressRoute 프리미엄 추가 기능으로 모든 지역에 걸쳐 Microsoft 서비스에 글로벌 연결합니다.
* BGP를 통해 네트워크와 Microsoft 간 동적 라우팅
* 높은 안정성을 위한 모든 피어링 위치의 기본 중복성입니다.
* 연결 가동 시간 [SLA](https://azure.microsoft.com/support/legal/sla/)입니다.
* 비즈니스용 Skype에 대한 QoS 지원.

자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

## <a name="features"></a>기능

### <a name="layer-3-connectivity"></a>3계층 연결
Microsoft에서는 업계 표준 동적 라우팅 프로토콜인 BGP를 사용하여 온-프레미스 네트워크, Azure의 인스턴스 및 Microsoft 공용 주소 간에 경로를 교환합니다. 다른 트래픽 프로필에 네트워크를 사용하여 여러 BGP 세션을 설정합니다. [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md) 문서에서 자세한 정보를 찾을 수 있습니다.

### <a name="redundancy"></a>중복
각 ExpressRoute 회로는 연결 공급자/네트워크 에지에서 [ExpressRoute Location](https://docs.microsoft.com/azure/expressroute/expressroute-locations#expressroute-locations)에 있는 두 가지 Microsoft Enterprise Edge 라우터(MSEE)에 두 개의 연결로 구성됩니다. Microsoft는 MSEE 각각에 하나의 연결 공급자/네트워크 에지 쪽에서 이중 BGP 연결을 요구합니다. 종단에 중복 디바이스/이더넷 회로를 배포하지 않도록 선택할 수도 있습니다. 그러나 연결 공급자는 중복 디바이스를 사용하여 연결이 중복 방식으로 Microsoft에 넘겨지도록 합니다. 중복 3계층 연결 구성은 [SLA](https://azure.microsoft.com/support/legal/sla/) 가 유효한 경우 요구됩니다.

### <a name="connectivity-to-microsoft-cloud-services"></a>Microsoft 클라우드 서비스에 연결
ExpressRoute 연결을 사용하면 다음 서비스에 액세스할 수 있습니다.
* Microsoft Azure 서비스
* Microsoft Office 365 서비스

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

ExpressRoute를 통해 지원되는 서비스에 대한 자세한 목록은 [ExpressRoute FAQ](expressroute-faqs.md) 페이지를 참조하세요.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>모든 지정학적 지역에 연결
[피어링 위치](expressroute-locations.md) 중 하나의 Microsoft에 연결할 수 있고 지정학적 지역 내 지역에 액세스합니다.

예를 들어 ExpressRoute를 통해 암스테르담의 Microsoft에 연결하는 경우 북유럽 및 서유럽에서 호스팅되는 모든 Microsoft Cloud Services에 액세스합니다. 지정학적 지역, 연관된 Microsoft 클라우드 지역 및 해당 ExpressRoute 피어링 위치에 대한 개요는 [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md) 문서를 참조하세요.

### <a name="global-connectivity-with-expressroute-premium"></a>ExpressRoute 프리미엄을 통한 글로벌 연결
[ExpressRoute 프리미엄](expressroute-faqs.md)을 사용하여 지정학적 경계를 넘어 연결을 확장할 수 있습니다. 예를 들어 ExpressRoute를 통해 암스테르담의 Microsoft로 연결하는 경우 전세계 모든 지역에서 호스팅되는 모든 Microsoft 클라우드 서비스에 액세스합니다.(국가별 클라우드는 제외됨) 북유럽 및 서유럽 지역에 액세스하는 것과 동일한 방법으로 남아메리카 또는 오스트레일리아에서 배포된 서비스에 액세스할 수 있습니다.

### <a name="local-connectivity-with-expressroute-local"></a>ExpressRoute Local을 통한 로컬 연결
원하는 Azure 지역 근처의 ExpressRoute 위치로 데이터를 가져올 수 있는 경우 [Local SKU](expressroute-faqs.md)를 활성화하여 효율적 비용으로 데이터를 전송할 수 있습니다. 로컬에서 데이터 전송은 ExpressRoute 포트 요금에 포함됩니다. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>ExpressRoute Global Reach를 사용한 온-프레미스 간 연결
ExpressRoute Global Reach를 사용해 ExpressRoute 회로를 연결하면 온-프레미스 사이트 전체에서 데이터를 교환할 수 있습니다. 예를 들어 캘리포니아의 프라이빗 데이터 센터는 실리콘 밸리의 ExpressRoute에 연결되어 있고 텍사스의 프라이빗 데이터 센터는 댈러스의 ExpressRoute에 연결되어 있는 경우 ExpressRoute Global Reach를 사용하면 ExpressRoute 회로 2개를 통해 두 프라이빗 데이터 센터를 연결할 수 있습니다. 데이터 센터 간 트래픽은 Microsoft 네트워크를 통해 전송됩니다.

자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.
### <a name="rich-connectivity-partner-ecosystem"></a>다양한 연결 파트너 에코시스템
ExpressRoute에서는 연결 공급자 및 시스템 통합자 파트너의 에코시스템이 지속적으로 성장합니다. 최신 정보는 [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md)를 참조하세요.

### <a name="connectivity-to-national-clouds"></a>국가별 클라우드에 연결
Microsoft는 특별한 지리학적 지역 및 고객 세그먼트에 격리된 클라우드 환경을 작동합니다. 국가별 클라우드 및 공급자의 목록은 [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md) 페이지를 참조하세요.

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct는 고객에게 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결할 수 있는 기회를 제공합니다. ExpressRoute Direct는 대규모로 활성/활성 연결을 지원하는 이중 100Gbps 연결을 제공합니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* 스토리지 및 Cosmos DB와 같은 서비스로 대규모 데이터 수집
* 다음과 같이 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리 업계의 물리적 격리입니다.
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

자세한 내용은 [ExpressRoute Direct 정보](https://go.microsoft.com/fwlink/?linkid=2022973)를 참조하세요.

### <a name="bandwidth-options"></a>대역폭 옵션
다양한 범위의 대역폭에 대해 ExpressRoute 회로를 구입할 수 있습니다. 지원되는 대역폭 목록이 아래에 나열됩니다. 연결 공급자와 확인하여 지원하는 대역폭을 확인합니다.

* 50Mbps
* 100Mbps
* 200Mbps
* 500Mbps
* 1Gbps
* 2Gbps
* 5Gbps
* 10Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>대역폭의 동적 크기 조정
연결을 종료하지 않고 ExpressRoute 회로 대역폭을 증가시킬 수 있습니다(최상의 방식으로). 자세한 내용은 [ExpressRoute 회로](expressroute-howto-circuit-portal-resource-manager.md#modify)를 참조하세요.

### <a name="flexible-billing-models"></a>유연한 청구 모델
사용자에게 적합한 청구 모델을 선택할 수 있습니다. 아래에 나열된 청구 모델 중에서 선택합니다. 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

* **무제한 데이터**입니다. 월별 요금으로 청구되며 모든 인바운드 및 아웃바운드 데이터 전송은 무료로 포함됩니다.
* **데이터 요금**입니다. 월별 요금으로 청구되며 모든 인바운드 데이터 전송은 무료입니다. 아웃 바운드 데이터 전송은 데이터 전송량 GB 당 요금이 부과됩니다. 데이터 전송 속도는 지역에 따라 다릅니다.
* **ExpressRoute 프리미엄 추가 기능**입니다. ExpressRoute 프리미엄은 ExpressRoute 회로의 추가 기능입니다. ExpressRoute 프리미엄 추가 기능에서는 다음과 같은 기능을 제공합니다. 
  * Azure 공용 및 Azure 프라이빗 피어링에 대한 경로 제한은 4,000개의 경로에서 10,000개의 경로로 증가됩니다.
  * 서비스에 대한 전역 연결입니다. 모든 지역에서 만든 ExpressRoute 회로(국가별 클라우드 제외)는 전세계의 다른 지역에 걸쳐 리소스에 액세스합니다. 예를 들어 서유럽에서 만든 가상 네트워크는 실리콘밸리에 프로비전된 ExpressRoute 회로를 통해 액세스될 수 있습니다.
  * ExpressRoute 회로 당 VNet 링크의 횟수는 회로의 대역폭에 따라 10에서 큰 제한으로 증가합니다.

## <a name="faq"></a>FAQ
ExpressRoute에 대해 자주 묻는 질문은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

## <a name="whats-new"></a><a name="new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) 페이지에서 최신 ExpressRoute 기능 업데이트를 확인합니다.

## <a name="next-steps"></a>다음 단계
* [ExpressRoute 연결 모델](expressroute-connectivity-models.md)에 대해 알아봅니다.
* ExpressRoute 연결 및 라우팅 도메인에 대해 알아봅니다. [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 서비스 공급자를 찾습니다. [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md)를 확인하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* [라우팅](expressroute-routing.md), [NAT](expressroute-nat.md) 및 [QoS](expressroute-qos.md)에 대한 요구 사항을 참조하세요.
* ExpressRoute 연결을 구성합니다.
  * [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute 회로의 피어링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
  * [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)
* Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보세요.
