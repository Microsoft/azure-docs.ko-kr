---
title: Azure에 온-프레미스 네트워크 연결 | Microsoft Docs
description: 에 대해 설명 하 고 Azure, Office 365 및 Dynamics CRM Online 같은 Microsoft 클라우드 서비스에 연결 하기 위한 사용 가능한 다른 메서드를 비교 합니다.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583539"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Azure에 온-프레미스 네트워크 연결
Microsoft는 몇 가지 유형의 클라우드 서비스를 제공합니다. 공용 인터넷을 통해 모든 서비스에 연결 하 고 수 하는 동안 인터넷을 통해 또는 Microsoft에 직접, 개인 연결을 통해 가상 사설망 (VPN) 터널을 사용 하 여 서비스에 연결할 수 있습니다. 이 문서에서는 연결 옵션 사용 하는 Microsoft 클라우드 서비스의 유형을 기반으로 사용자의 요구를 가장 잘 맞는 결정 합니다. 대부분의 조직에는 아래에 설명 된 연결 유형을 여러 개 활용 합니다.

## <a name="connecting-over-the-public-internet"></a>공용 인터넷을 통해 연결
이 연결 형식은 아래와 같이 인터넷을 통해 직접 Microsoft 클라우드 서비스에 대 한 액세스를 제공 합니다.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

이 연결은 일반적으로 Microsoft 클라우드 서비스에 연결 하는 데 첫 번째 형식입니다. 다음 표에서이 연결 유형에 따른 장점과 단점을 나열합니다.

| **이점** | **고려 사항** |
| --- | --- |
| 에 필요한 온-프레미스 네트워크를 수정 하지 않으면 모든 클라이언트 장치에 모든 IP 주소 및 인터넷에서 포트에 대 한 무제한 액세스 합니다. |트래픽이 HTTPS를 사용 하 여 암호화 종종 되어 있지만 공용 인터넷을 트래버스 하므로 전송 중에 다시 가로챌 수 있습니다 것입니다. |
| 공용 인터넷에 노출 하는 모든 Microsoft 클라우드 서비스에 연결할 수 있습니다. |예기치 않은 대기 시간 연결이 인터넷을 트래버스 하므로 합니다. |
| 기존 인터넷 연결을 사용합니다. | |
| 모든 연결 장치를 관리할이 필요 하지 않습니다. | |

기존 인터넷 연결을 사용 하므로이 연결에 연결 또는 대역폭 비용이 없습니다.

## <a name="connecting-with-a-point-to-site-connection"></a>지점-사이트 간 연결을 사용 하 여 연결
이 연결 형식은 아래와 같이 인터넷을 통해 보안 소켓 터널링 프로토콜 (SSTP) 터널을 통해 일부 Microsoft 클라우드 서비스에 대 한 액세스를 제공 합니다.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "지점 및 사이트 간 연결")

연결에 기존 인터넷 연결을 통해 했지만 Azure VPN Gateway를 사용 해야 합니다. 다음 표에서이 연결 유형에 따른 장점과 단점을 나열합니다.

| **이점** | **고려 사항** |
| --- | --- |
| 에 필요한 온-프레미스 네트워크를 수정 하지 않으면 모든 클라이언트 장치에 모든 IP 주소 및 인터넷에서 포트에 대 한 무제한 액세스 합니다. |IPSec를 사용 하 여 트래픽을 암호화 되어 있지만 공용 인터넷을 트래버스 하므로 전송 중에 다시 가로챌 수 있습니다 것입니다. |
| 기존 인터넷 연결을 사용합니다. |예기치 않은 대기 시간 연결이 인터넷을 트래버스 하므로 합니다. |
| 초당 200mb 게이트웨이 당 최대 처리량입니다. |온-프레미스 네트워크의 각 장치 및 각 장치에 연결 해야 하는 경우 각 게이트웨이 간에 별도 연결의 생성 및 관리 해야 합니다. |
| Azure Virtual Machines 및 Azure Cloud Services와 같은 Azure Virtual Network (VNet)에 연결할 수 있는 Azure 서비스에 연결할 사용할 수 있습니다. |Azure VPN Gateway의 최소 지속적인 관리가 필요합니다. |
| Microsoft Office 365 또는 Dynamics CRM Online에 연결할 사용할 수 없습니다. | |
| VNet에 연결할 수 없습니다. Azure 서비스에 연결을 사용할 수 없습니다. | |

에 대해 자세히 알아보세요 합니다 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) 서비스를 해당 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway), 및 아웃 바운드 데이터 전송 [가격 책정](https://azure.microsoft.com/pricing/details/data-transfers)합니다.

## <a name="connecting-with-a-site-to-site-connection"></a>사이트 간 연결을 사용 하 여 연결
이 연결 형식은 아래와 같이 인터넷을 통해 IPSec 터널을 통해 일부 Microsoft 클라우드 서비스에 대 한 액세스를 제공 합니다.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "사이트 간 연결")

연결에 기존 인터넷 연결을 통해 했지만 해당 연결 가격 책정 및 아웃 바운드 데이터 전송 가격 책정을 사용 하 여 Azure VPN Gateway를 사용 해야 합니다. 다음 표에서이 연결 유형에 따른 장점과 단점을 나열합니다.

| **이점** | **고려 사항** |
| --- | --- |
| 온-프레미스 네트워크의 모든 장치는 각 장치에 대해 개별 연결을 구성 하지 않아도 되므로 VNet에 연결 하는 Azure 서비스와 통신할 수 있습니다. |IPSec를 사용 하 여 트래픽을 암호화 되어 있지만 공용 인터넷을 트래버스 하므로 전송 중에 다시 가로챌 수 있습니다 것입니다. |
| 기존 인터넷 연결을 사용합니다. |예기치 않은 대기 시간 연결이 인터넷을 트래버스 하므로 합니다. |
| 가상 머신과 같은 VNet에 연결할 수 있는 Azure 서비스 및 클라우드 서비스에 연결할 수 있습니다. |구성 및 관리는 유효성이 검사 된 VPN 장치 * 해야 온-프레미스입니다. |
| 초당 200mb 게이트웨이 당 최대 처리량입니다. |Azure VPN Gateway의 최소 지속적인 관리가 필요합니다. |
| 검사 및 사용자 정의 경로 또는 (BGP (경계 게이트웨이 프로토콜)를 사용 하 여 로깅에 대 한 온-프레미스 네트워크를 통해 클라우드 가상 컴퓨터에서 시작 하는 아웃 바운드 트래픽을 강제로 수 * * 합니다. |Microsoft Office 365 또는 Dynamics CRM Online에 연결할 사용할 수 없습니다. |
| VNet에 연결할 수 없습니다. Azure 서비스에 연결을 사용할 수 없습니다. | |
| 다시 온-프레미스 장치에 대 한 연결을 시작 하는 서비스를 사용 하 고 보안 정책에 필요한 경우 온-프레미스 네트워크와 Azure 간에 방화벽을 할 수도 있습니다. | |

* *의 목록을 보려면 [VPN 장치의 유효성을 검사](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)합니다.
* * *를 사용 하 여 자세히 알아봅니다 [사용자 정의 경로](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) 또는 [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 를 온-프레미스 장치를 위해 Azure Vnet에서 라우팅 적용 합니다.

## <a name="connecting-with-a-dedicated-private-connection"></a>전용된 개인 연결을 사용 하 여 연결
아래와 같이이 연결 유형에 모든 Microsoft 클라우드 서비스에 대 한 액세스 전용된 개인 연결을 통해 인터넷을 트래버스 하지 않습니다는 Microsoft에 제공 합니다.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute 연결")

연결에 ExpressRoute 서비스와 연결 공급자에 연결을 사용을 해야합니다. 다음 표에서이 연결 유형에 따른 장점과 단점을 나열합니다.

| **이점** | **고려 사항** |
| --- | --- |
| 서비스 공급자를 통해 전용된 연결을 사용 하므로 트래픽이 공용 인터넷을 통해 전송 가로챌 수 없습니다. |온-프레미스 라우터 관리가 필요합니다. |
| ExpressRoute 회로 및 초당 2gb 각 게이트웨이 최대 처리량 당 10 g b/s 최대 대역폭입니다. |연결 공급자에 대 한 전용된 연결에 필요합니다. |
| 예측 가능한 대기 시간을 Microsoft에 인터넷을 트래버스 하지 않는 전용된 연결 이므로 합니다. |(회로 Vnet에 연결) 하는 경우 하나 이상의 Azure VPN Gateway의 지속적인 관리 작업이 최소화 해야 합니다. |
| 원하는 경우 트래픽을 암호화할 수 있습니다 하지만 암호화 된 통신에 필요 하지 않습니다. |다시 온-프레미스 장치에 대 한 연결을 시작 하는 클라우드 서비스를 사용 하는 경우에 온-프레미스 네트워크와 Azure 사이 방화벽을 할 수도 있습니다. |
| 몇 가지 예외 *를 사용 하 여 모든 Microsoft 클라우드 서비스에 직접 연결할 수 있습니다. |네트워크 주소 변환 (NAT) 서비스 VNet.* *에 연결할 수 없음에 대 한 Microsoft 데이터 센터를 입력 하는 온-프레미스 IP 주소 필요 |
| 아웃 바운드 트래픽을 검사 하 고 BGP를 사용 하 여 로깅에 대 한 온-프레미스 네트워크를 통해 클라우드 가상 컴퓨터에서 시작을 강제할 수 있습니다. | |

* * 보기를 [서비스 목록을](../expressroute/expressroute-faqs.md#supported-services) ExpressRoute를 사용 하 여 사용할 수 없습니다. Office 365에 연결할 Azure 구독을 승인 해야 합니다.  참조 된 [Office 365 용 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) 세부 정보에 대 한 문서.
* * *에 대해 자세히 알아보려면 ExpressRoute [NAT](../expressroute/expressroute-nat.md) 요구 사항입니다.

에 대해 자세히 알아보세요 [ExpressRoute](../expressroute/expressroute-introduction.md), 관련 [가격 책정](https://azure.microsoft.com/pricing/details/expressroute), 및 [연결 공급자](../expressroute/expressroute-locations.md#locations)합니다.

## <a name="additional-considerations"></a>추가 고려 사항
* 위의 옵션 중 몇 가지 제한이 다양 한 최대 VNet 연결, 게이트웨이 연결 및 기타 조건에 대 한 지 수 있습니다. Azure를 검토 하는 것이 좋습니다 [네트워킹 제한](../azure-subscription-service-limits.md#networking-limits) 연결 형식을 사용 하도록 선택 하면 영향을 어떤 것을 이해 합니다.
* ExpressRoute 게이트웨이를 동일한 VNet에 게이트웨이 사이트 간 VPN 연결에서 연결 하려는 경우 알아야 할 중요 한 제약 조건을 사용 하 여 먼저 합니다. 참조를 [구성할 ExpressRoute 및 사이트 간 공존 연결](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) 을 참조 하십시오.

## <a name="next-steps"></a>다음 단계
아래 리소스를이 문서에서 다루는 연결 형식을 구현 하는 방법에 설명 합니다.

* [지점 대 사이트간 연결 구현](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [사이트 간 연결 구현](guidance-hybrid-network-vpn.md)
* [전용된 개인 연결을 구현 합니다.](guidance-hybrid-network-expressroute.md)
* [고가용성에 대 한 사이트 간 연결을 사용 하 여 전용된 개인 연결을 구현 합니다.](guidance-hybrid-network-expressroute-vpn-failover.md)
