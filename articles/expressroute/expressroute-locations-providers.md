---
title: '위치 및 연결 공급 기업: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 위치에 따라 정렬됩니다.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2019
ms.author: pareshmu
ms.openlocfilehash: 95b06800b29d021290776025f9693eec8a43e74a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129471"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서의 표에서는 ExpressRoute 연결 공급자, ExpressRoute 지역 범위, ExpressRoute를 통해 지원되는 Microsoft 클라우드 서비스 및 ExpressRoute SI(시스템 통합업체)에 대한 정보를 제공합니다.

## <a name="partners"></a>ExpressRoute 연결 공급자
ExpressRoute는 모든 Azure 지역 및 위치에서 지원됩니다. 다음 맵에 Azure 지역 및 ExpressRoute 위치 목록을 제공합니다. ExpressRoute 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.

![위치 맵][0]

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>지역 내 ExpressRoute 위치에 대한 Azure 지역
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **영역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- | --- |
| **오스트레일리아 정부** | 1 | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 1 |프랑스 중부, 프랑스 남부, 북유럽, 유럽 서부, 영국 서부, 영국 남부 |암스테르담, Amsterdam2, 더블린, 런던, London2, 마르세유에, 뉴포트 (웨일스), 파리, 취리히 |
| **북아메리카** | 1 |미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 댈러스, 덴버, 라스베이거스, 로스앤젤레스, 마이애미, 뉴욕, 샌안토니오, 시애틀, 실리콘밸리, 실리콘 Valley2, 워싱턴 DC, 워싱턴 DC2, 몬트리올, 퀘벡 시티, 토론토 |
| **아시아** | 2 |동아시아, 동남 아시아 |홍콩 특별 행정구, 콸라룸푸르, 싱가포르, 싱가포르 2, 타이베이 |
| **오스트레일리아** | 2 |오스트레일리아 남동부, 오스트레일리아 동부 |멜버른, 퍼스, 시드니 | 
| **인도** | 2 |인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 2 |일본 서부, 일본 동부 |오사카, 도쿄 |
| **대한민국** | 2 |한국 중부, 한국 남부 |부산, 서울|
| **UAE** | 2 | UAE Central, UAE North | 두바이 |
| **남아프리카 공화국** | 3 |남아프리카 공화국 서 부, 남아프리카 공화국 북부 |케이프타운, 요하네스버그 |
| **남미** | 3 |브라질 남부 |상파울루 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>국내 클라우드에 대한 지역 및 지리적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |미국 애리조나 주 정부, 미국 아이오와 주 정부, 미국 텍사스 주 정부, 미국 버지니아 주 정부, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="locations"></a>연결 공급자 위치

다음 테이블에서는 각 위치에 대한 연결 위치 및 서비스 공급자를 보여 줍니다. 서비스를 제공할 수 있는 서비스 공급자 및 위치를 보려면 [서비스 공급자별 위치](expressroute-locations.md#locations)를 참조하세요. 


### <a name="production-azure"></a>프로덕션 Azure
| **위치**: | **피어링 위치 소유자** | **서비스 공급자** |
| --- | --- | --- |
| **암스테르담** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **암스테르담2** | Interxion | DE-CIX Interxion |
| **애틀랜타** | Equinix | Equinix, Megaport |
| **부산** |LG CNS | LG CNS |
| **캔버라** | CDC | CDC |
| **캔버라2** | CDC | CDC |
| **케이프타운** | Teraco | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **첸나이** | Tata Communications | Global CloudXchange(GCX), SIFY, Tata Communications |
| **첸나이2** | Airtel | Airtel |
| **시카고** | Equinix | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **댈러스** | Equinix | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Verizon, Zayo|
| **덴버** | CoreSite | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat 아랍에미리트 | Etisalat 아랍에미리트 |
| **더블린** | Equinix | Colt, eir, Equinix, Interxion, Megaport |
| **Hong Kong SAR** | Equinix | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **요하네스버그** | Teraco | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **콸라룸푸르** | TIME dotCom | TIME dotCom |
| **라스베이거스** | Switch | CenturyLink Cloud Connect, Megaport |
| **런던** | Equinix | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **런던2** | Telehouse | IX 도달률, Equinix |
| **로스앤젤레스** | CoreSite | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **마르세유** |Interxion | Interxion |
| **멜버른** | NextDC | AARNet, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **마이애미** | Equinix | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | Bell Canada, Cologix, Telus, Zayo |
| **뭄바이** | Tata Communications | 의존성 Jio, global CloudXchange (GCX), Sify, Tata Communications |
| **뭄바이2** | Airtel | Airtel, Sify, Vodafone Idea |
| **뉴욕** | Equinix | Coresite, Equinix, InterCloud, Megaport, 패킷, Zayo 센추리 클라우드 연결 |
| **뉴포트(웨일스)** | Next Generation Data | British Telecom, Level 3 Communications, Next Generation Data |
| **오사카** | Equinix | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **파리** | Interxion | Colt, Equinix, Intercloud, Interxion, Orange, Zayo |
| **퍼스** | NextDC | Megaport, NextDC |
| **퀘벡 시티** | 4Degrees | Bell Canada, Megaport |
| **샌안토니오** | CyrusOne | CenturyLink Cloud Connect, Megaport |
| **상파울루** | Equinix | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **시애틀** | Equinix | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Zayo |
| **서울** | KINX | KINX, LG CNS, Sejong Telecom |
| **실리콘밸리** | Equinix | Aryaka 네트워크, AT & T NetBond, British Telecom 센추리 클라우드 연결, Comcast, Coresite, Equinix InterCloud, 패킷, PacketFabric, Level 3 Communications, Megaport, Orange, 스 프린트, Tata Communications, Verizon, Zayo IX 도달 |
| **실리콘 Valley2** | Coresite | Coresite | 
| **싱가포르** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | 글로벌 스위치 | Colt, Epsilon Global Communications, Megaport, SingTel |
| **시드니** | Equinix | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **타이베이** | Chief Telecom | Chief Telecom |
| **도쿄** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **토론토** | Cologix | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, Megaport, Telus, Zayo |
| **워싱턴 DC** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **워싱턴 DC2** | Coresite | Coresite | 
| **Zurich** | Interxion | Interxion |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environments"></a>국가별 클라우드 환경

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **위치**: | **서비스 공급자** |
| --- | --- |
| **시카고** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **댈러스** |Equinix, Megaport, Verizon |
| **뉴욕** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect |
| **샌안토니오** | Megaport |
| **실리콘밸리** | Equinix, Level 3 Communications, Verizon |
| **시애틀** | Equinix, Megaport |
| **워싱턴 DC** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>중국
| **위치**: | **서비스 공급자** |
| --- | --- |
| **베이징** |China Telecom |
| **베이징2** | GDS |
| **상하이** |China Telecom |
| **상하이2** | GDS |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>독일
| **위치** | **서비스 공급자** |
| --- | --- |
| **베를린** |e-shelter, Megaport+, T-Systems |
| **프랑크푸르트** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Exchange 공급자를 통한 연결
연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="c1partners"></a>추가 서비스 공급자를 통한 연결
| **위치**: | **Exchange** | **연결 공급자** |
| --- | --- | --- |
| **암스테르담** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **케이프타운** | Teraco | MTN |
| **시카고** | Equinix | Lightower, Windstream |
| **댈러스** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **프랑크푸르트** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **함부르크** | Equinix | 중국 |
| **Hong Kong SAR** | Equinix | Chief, Macroview Telecom |
| **요하네스버그** | Teraco | MTN |
| **런던** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **로스앤젤레스** | Equinix |Transtelco |
| **마드리드** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **뉴욕** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **파리** | Equinix | Proximus |
| **퀘벡 시티** | Megaport | Fibrenoire |
| **상파울루** | Equinix | Venha Pra Nuvem |
| **시애틀** |Equinix | Alaska Communications |
| **실리콘밸리** |Equinix | Cox Business, Windstream |
| **싱가포르** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **슬라우** | Equinix | HSO|
| **시드니** | Megaport | Macquarie Telecom Group|
| **도쿄** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **토론토** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **워싱턴 DC** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **대륙** | **시스템 통합 업체** |
| --- | --- |
| **아시아** |Avanade Inc., OneAs1a |
| **오스트레일리아** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **유럽** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **북아메리카** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **남미** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
