---
title: '라우팅 최적화 - ExpressRoute 회로: Azure | Microsoft Docs'
description: 이 페이지에서는 사용자에게 Microsoft 및 회사 네트워크에 연결되는 하나 이상의 ExpressRoute 회로가 있는 경우 라우팅을 최적화하는 방법에 대한 자세한 정보를 제공합니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883570"
---
# <a name="optimize-expressroute-routing"></a>ExpressRoute 라우팅 최적화
여러 개의 ExpressRoute 회로가 있는 경우 Microsoft에 연결되는 하나 이상의 경로가 있습니다. 결과적으로 최적이 아닌 라우팅이 발생할 수 있습니다. 즉, 트래픽이 Microsoft에, Microsoft에서 다시 네트워크로 도달하는 경로가 더 길어질 수 있습니다. 네트워크 경로가 길어질수록 대기 시간도 늘어납니다. 대기 시간은 애플리케이션 성능 및 사용자 환경에 직접적인 영향을 줍니다. 이 문서에서는 이 문제를 보여 주고 표준 라우팅 기술을 사용하여 라우팅을 최적화하는 방법을 설명합니다.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>고객으로부터 Microsoft에 이르는 최적이 아닌 라우팅
예제를 통해 라우팅 문제를 자세히 살펴보겠습니다. 미국에 사무실이 두 곳 있는데, 한 곳은 로스앤젤레스에, 한 곳은 뉴욕에 있다고 가정해 보겠습니다. 사무실은 고유한 백본 네트워크 또는 서비스 공급자의 IP VPN에 연결할 수 있는 WAN(광역 네트워크)에 연결됩니다. 두 개의 ExpressRoute가 하나는 미국 서부에, 하나는 미국 동부에 있으며 WAN에도 연결됩니다. 물론 Microsoft 네트워크에 연결되는 두 경로가 있습니다. 이제 미국 서부와 동부 모두에서 Azure 배포(예: Azure App Service)가 있다고 가정해 보겠습니다. 서비스 관리자는 최적의 환경을 위해 각 사무실의 사용자가 가까운 Azure 서비스에 액세스하도록 하므로 로스앤젤레스에 있는 사용자를 Azure 미국 서부에, 뉴욕에 있는 사용자를 Azure 미국 동부에 연결하고 싶습니다. 아쉽게도 이 계획은 동부 연안 사용자에게는 잘 작동하지만 서부 연안 사용자에게는 그렇지 않습니다. 이 문제의 원인은 다음과 같습니다. 각 ExpressRoute 회로에서 Azure 미국 동부 접두사(23.100.0.0/16) 및 Azure 미국 서부 접두사(13.100.0.0/16)를 모두 보급합니다. 어느 지역의 접두사인지 모르면 다르게 처리할 수 없습니다. WAN 네트워크는 접두사가 미국 서부보다 미국 동부에 더 가깝다고 생각할 수 있으므로 두 사무실 사용자를 미국 동부의 ExpressRoute 회로에 라우팅할 수 있습니다. 결과적으로는 로스앤젤레스 사무실의 많은 사용자들이 불편해집니다.

![ExpressRoute 사례 1 문제 - 고객으로부터 Microsoft에 이르는 최적이 아닌 라우팅](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>해결 방법: BGP 커뮤니티 사용
두 사무실 사용자에 대한 라우팅을 최적화하려면 접두사가 Azure 미국 서부의 것인지 Azure 미국 동부의 것인지 알아야 합니다. 이 정보는 [BGP 커뮤니티 값](expressroute-routing.md)을 사용하여 인코딩합니다. 각 Azure 지역에 고유한 BGP 커뮤니티 값을 할당했습니다. 예를 들어 미국 동부는 "12076:51004"를, 미국 서부는 "12076:51006"을 할당했습니다. 이제 어떤 접두사가 어떤 Azure 지역의 것인지 알았으므로 어떤 ExpressRoute를 기본 설정할지 구성할 수 있습니다. BGP를 사용하여 라우팅 정보를 교환하므로 라우팅에 영향을 주는 BGP의 로컬 기본 설정을 사용할 수 있습니다. 이 예제에서는 미국 동부보다 미국 서부의 13.100.0.0/16에 더 높은 로컬 기본 설정 값을 할당할 수 있으며 마찬가지로 미국 서부보다 미국 동부의 23.100.0.0/16에 더 높은 로컬 기본 설정 값을 할당할 수 있습니다. 이 구성은 Microsoft에 대한 두 경로를 사용할 수 있는 경우 로스앤젤레스의 사용자가 미국 서부에서 ExpressRoute 회로를 사용하여 Azure 미국 서부에 연결하는 반면 뉴욕의 사용자는 미국 동부의 ExpressRoute를 사용하여 Azure 미국 동부에 연결할 수 있도록 합니다. 라우팅이 양쪽 모두에서 최적화됩니다. 

![ExpressRoute 사례 1 솔루션 - BGP 커뮤니티 사용](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> 고객으로부터 Azure Virtual Network로의 라우팅에 로컬 기본 설정을 사용하는 동일한 기법을 적용할 수 있습니다. BGP 커뮤니티 값을 Azure에서 네트워크에 보급된 접두사에 태그하지 않습니다. 그러나 사무실에 가까운 Virtual Network 배포를 알고 있으므로 라우터가 하나의 ExpressRoute 회로를 다른 것보다 선호하도록 적절하게 구성할 수 있습니다.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Microsoft에서 고객에 이르는 최적이 아닌 라우팅
다음은 Microsoft의 연결에서 더 긴 경로로 네트워크에 연결하는 다른 예입니다. 이 경우 [하이브리드 환경](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)에서 온-프레미스 Exchange Server와 Exchange Online을 사용합니다. 사무실이 WAN에 연결됩니다. 두 ExpressRoute 회로를 통해 두 사무실의 온-프레미스 서버 접두사를 Microsoft에 알립니다. Exchange Online은 사서함 마이그레이션과 같은 경우에 온-프레미스 서버로의 연결을 시작합니다. 아쉽게도 로스앤젤레스 사무실에 대한 연결은 다시 전체 대륙을 서부 연안으로 탐색하기 전에 미국 동부 ExpressRoute 회로로 라우팅됩니다. 문제의 원인은 첫 번째 예와 비슷합니다. 어떠한 힌트도 없이 Microsoft 네트워크는 어떤 고객 접두사가 미국 동부에 근접하고 어떤 고객 접두사가 미국 서부에 근접한지를 알려줄 수 없습니다. 로스앤젤레스의 사무실에 대한 잘못된 경로를 선택하는 일이 발생합니다.

![ExpressRoute 사례 2 - Microsoft에서 고객에 이르는 최적이 아닌 라우팅](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>해결 방법: AS PATH 앞에 추가 사용
문제에 대한 두 가지 해결 방법이 있습니다. 첫 번째 방법은 단순히 로스앤젤레스 사무실에 대한 온-프레미스 접두사 177.2.0.0/31를 미국 서부 ExpressRoute 경로에서 알리고 뉴욕 사무실에 대한 온-프레미스 접두사 177.2.0.2/31을 미국 동부 ExpressRoute 회로에서 알리는 것입니다. 결과적으로, Microsoft에서 각 사무실에 연결하는 경로는 한 가지뿐입니다. 최적화된 모호성과 라우팅이 없습니다. 이 디자인에서는 장애 조치 전략에 대한 생각이 필요합니다. ExpressRoute를 통한 Microsoft의 경로가 중단된 경우 Exchange Online에서 온-프레미스 서버에 계속 연결할 수 있는지 확인해야 합니다. 

두 번째 방법은 어떤 접두사가 어떤 사무실에 근접한지 힌트를 제공하는 것 외에도 두 ExpressRoute 회로에서 두 접두사를 계속해서 알리는 것입니다. BGP AS Path 앞에 추가를 지원하므로 라우팅에 영향을 주는 접두사에 대한 AS Path를 구성할 수 있습니다. 이 예제에서는 미국 동부 172.2.0.0/31에 대한 AS PATH를 연장할 수 있으므로 이 접두사에 대해 전송되는 트래픽에는 미국 서부의 ExpressRoute 회로를 선호하게 됩니다(네트워크에서 이 접두사에 대한 경로가 서부에서 더 짧다고 생각하므로). 마찬가지로 미국 동부에서 ExpressRoute 회로를 선호하도록 미국 서부에서 172.2.0.2/31에 대한 AS PATH를 연장할 수 있습니다. 두 사무소 모두에 대해 라우팅이 최적화됩니다. 이 디자인에서 한 ExpressRoute 회로가 중단되면 Exchange Online에서 다른 ExpressRoute 회로 및 WAN을 통해 계속 연결할 수 있습니다. 

> [!IMPORTANT]
> Microsoft 피어링에서 수신한 접두사에 대한 AS PATH에서 개인 AS 번호를 제거합니다. Microsoft 피어링을 위한 라우팅에 영향을 주는 AS PATH에 공용 AS 번호를 추가해야 합니다.
> 
> 

![ExpressRoute 사례 2 솔루션 - AS PATH 접두사 사용](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> 여기에 지정된 예제는 Microsoft 및 공용 피어링에 대한 것으로 개인 피어링에 대해 동일한 기능을 지원하지 않습니다. 또한 앞에 추가된 AS 경로는 기본 경로 및 보조 경로의 선택에 영향을 주는 하나의 단일 ExpressRoute 회로 내에서 작동합니다.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>가상 네트워크 간에 최적이 아닌 라우팅
ExpressRoute의 경우 ExpressRoute 회로에 연결하여 Virtual Network("VNet"이라고도 함) 간에 통신을 사용할 수 있습니다. 여러 ExpressRoute 회로에 연결한 경우 VNet 간에 최적이 아닌 라우팅이 발생할 수 있습니다. 예제를 살펴보겠습니다. 두 개의 ExpressRoute 회로가 하나는 미국 서부에, 하나는 미국 동부에 있습니다. 각 지역에는 두 개의 VNet이 있습니다. 웹 서버가 하나의 VNet에 배포되고 애플리케이션 서버가 다른 VNet에 배포됩니다. 중복의 경우 각 지역에 있는 두 개의 VNet을 로컬 ExpressRoute 회로 및 원격 ExpressRoute 회로 모두에 연결합니다. 아래에서 볼 수 있듯이 각 VNet에서 다른 VNet에 대한 두 개의 경로가 있습니다. VNet은 ExpressRoute 회로가 로컬인지 아니면 원격인지를 알 수 없습니다. 따라서 ECMP(Equal-Cost-Multi-Path) 라우팅을 수행하여 VNet 간 트래픽의 부하를 분산하는 경우 일부 트래픽 흐름은 긴 경로를 사용하고 원격 ExpressRoute 회로에 라우팅됩니다.

![ExpressRoute 사례 3 - 가상 네트워크 간에 최적이 아닌 라우팅](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>솔루션: 로컬 연결에 높은 가중치 할당
해결책은 간단합니다. VNet 및 회로가 있는 위치를 알고 있으므로 각 VNet이 선호하는 경로를 알려줄 수 있습니다. 구체적으로 예를 들어 원격 연결보다 로컬 연결에 더 높은 가중치를 할당합니다(구성 예제는 [여기](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection) 참조). VNet이 여러 연결에서 다른 VNet의 접두사를 수신하는 경우 가중치가 가장 높은 연결을 선호하여 해당 접두사에 대상으로 지정된 트래픽을 보냅니다.

![ExpressRoute 사례 3 솔루션 - 로컬 연결에 높은 가중치 할당](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> 또한 여러 ExpressRoute 회로 설정한 경우 위의 두 번째 시나리오에서 설명한 기술인 AS PATH 접두사를 적용하는 대신 연결의 가중치를 구성하여 VNet에서 온-프레미스 네트워크로의 라우팅에 영향을 줄 수 있습니다. 각 접두사의 경우 트래픽을 전송하는 방법을 결정할 때 AS Path 길이 앞에 있는 연결 가중치를 항상 살펴봅니다.
>
>
