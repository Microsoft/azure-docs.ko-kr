---
title: 'Azure 백 엔드 연결 기능의 상호 운용성: 테스트 설정 | Microsoft Docs'
description: 이 문서에서는 ExpressRoute, 사이트 간 VPN 및 Azure의 가상 네트워크 피어링 간의 상호 운용성을 분석하는 데 사용할 수 있는 테스트 설정에 대해 설명합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811116"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Azure 백 엔드 연결 기능의 상호 운용성: 테스트 설정

이 문서에서는 Azure 네트워킹 서비스가 제어 평면 수준 및 데이터 평면 수준에서 상호 운용하는 방법을 분석하는 데 사용할 수 있는 테스트 설정에 대해 설명합니다. Azure 네트워킹 구성 요소를 간단하게 살펴보겠습니다.

-   **Azure ExpressRoute**: Azure ExpressRoute 개인 피어링을 사용하여 온-프레미스 네트워크의 사설 IP 공간을 Azure Virtual Network 배포에 직접 연결할 수 있습니다. ExpressRoute를 사용하여 더 높은 대역폭과 개인 연결을 달성할 수 있습니다. 많은 ExpressRoute 에코 파트너에서 SLA가 포함된 ExpressRoute 연결을 제공합니다. ExpressRoute 및 ExpressRoute 구성 방법에 대한 자세한 내용은 [ExpressRoute 소개][ExpressRoute]를 참조하세요.
-   **사이트 간 VPN**: Azure VPN Gateway를 사이트 간 VPN으로 사용하여 인터넷 또는 ExpressRoute를 통해 온-프레미스 네트워크를 Azure에 안전하게 연결할 수 있습니다. Azure에 연결하는 사이트 간 VPN을 구성하는 방법은 [VPN Gateway 구성][VPN]을 참조하세요.
-   **VNet 피어링**: VNet(가상 네트워크) 피어링을 사용하여 Azure Virtual Network의 VNet 간에 연결을 설정할 수 있습니다. VNet 피어링에 대한 자세한 내용은 [VNet 피어링 자습서][VNet]를 참조하세요.

## <a name="test-setup"></a>테스트 설정

다음 그림은 테스트 설정을 보여줍니다.

[![1]][1]

테스트 설정의 중심부는 Azure 지역 1의 허브 VNet입니다. 허브 VNet은 다음과 같은 방법으로 여러 네트워크에 연결됩니다.

-   허브 VNet은 VNet 피어링을 사용하여 스포크 VNet에 연결됩니다. 스포크 VNet은 허브 VNet의 두 게이트웨이에 원격으로 액세스할 수 있습니다.
-   허브 VNet은 사이트 간 VPN을 사용하여 분기 VNet에 연결됩니다. 연결은 eBGP를 사용하여 경로를 교환합니다.
-   허브 VNet은 ExpressRoute 개인 피어링을 기본 경로로 사용하여 온-프레미스 위치 1 네트워크에 연결됩니다. 사이트 간 VPN 연결을 백업 경로로 사용합니다. 이 문서의 나머지 부분에서는 이 ExpressRoute 회로를 ExpressRoute 1이라고 부릅니다. 기본적으로 ExpressRoute 회로는 고가용성을 위해 중복 연결을 제공합니다. ExpressRoute 1에서, 보조 MSEE(Microsoft Enterprise Edge Router)와 마주하는 보조 CE(고객 에지) 라우터의 하위 인터페이스가 비활성화되어 있습니다. 위의 그림에서 이중 화살표 위의 빨간색 선은 비활성화된 CE 라우터 하위 인터페이스를 나타냅니다.
-   허브 VNet은 또 다른 ExpressRoute 개인 피어링을 사용하여 온-프레미스 위치 2 네트워크에 연결됩니다. 이 문서의 나머지 부분에서는 이 보조 ExpressRoute 회로를 ExpressRoute 2라고 부릅니다.
-   또한 ExpressRoute 1은 허브 VNet과 온-프레미스 위치 1을 모두 Azure 지역 2의 원격 VNet에 연결합니다.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

ExpressRoute Microsoft 피어링을 사용하여 온-프레미스 네트워크와 Azure VNet 간에 개인적으로 데이터를 교환하도록 사이트 간 VPN을 구성할 수 있습니다. 이 구성을 사용하면 비밀성, 신뢰성 및 무결성을 유지하며 데이터를 교환할 수 있습니다. 데이터 교환은 재생 방지이기도 합니다. ExpressRoute Microsoft 피어링을 사용하여 터널 모드에서 사이트 간 IPsec VPN을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 사용하는 사이트 간 VPN 구성에 대한 기본 제한은 처리량입니다. IPsec 터널을 통한 처리량은 VPN Gateway 용량에 의해 제한됩니다. VPN Gateway 처리량은 ExpressRoute 처리량보다 낮습니다. 이 시나리오에서 매우 안전한 트래픽의 경우 IPsec 터널 및 모든 다른 트래픽의 경우 개인 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN

ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 테스트 설정에서 설명한 것처럼, Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 만드는 데 사이트 간 VPN을 사용할 수 있습니다. ExpressRoute 및 사이트 간 VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 ExpressRoute를 우선 순위로 지정합니다. ExpressRoute와 사이트 간 VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 사이트 간 VPN 연결을 사용하기 전에 ExpressRoute 연결을 사용하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN의 공존 연결을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [Azure에서 허브-스포크 네트워크 토폴로지 구현][Hub-n-Spoke]을 참조하세요.

지역 내의 VNet 피어링에서 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

서로 다른 지역에 있는 분기 VNet 및 온-프레미스 네트워크를 허브 VNet을 통해 서로 통신하도록 할 수 있습니다. 이 구성에 대한 네이티브 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안은 허브의 라우팅에 대해 NVA(네트워크 가상 어플라이언스)를 사용하는 것입니다.

자세한 내용은 [VPN Gateway란?][VPN] 및 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

테스트 토폴로지용 [구성 세부 정보][Configuration]에 대해 알아봅니다.

테스트 설정의 [제어 평면 분석][Control-Analysis] 및 토폴로지에서 다른 VNet 또는 VLAN의 보기에 대해 알아봅니다.

테스트 설정의 [제어 평면 분석][Data-Analysis] 및 Azure 네트워크 모니터링 기능 보기에 대해 알아봅니다.

[ExpressRoute FAQ][ExR-FAQ]를 참조하여 다음 사항을 알아보세요.
-   ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 개수를 알아봅니다.
-   ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 개수를 알아봅니다.
-   ExpressRoute의 다른 크기 조정 제한 사항에 대해 알아봅니다.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "테스트 토폴로지 다이어그램"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


