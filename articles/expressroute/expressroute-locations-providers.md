---
title: '위치 및 연결 공급 기업: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 위치에 따라 정렬됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: cherylmc
ms.openlocfilehash: 30d41729aaf5301d28186c87ce3684c2fcd5d6f5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927666"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서에는 ExpressRoute 지리적 범위 및 위치, ExpressRoute 연결 공급자 및 ExpressRoute SI(시스템 통합 업체)에 대한 정보를 제공하는 표가 있습니다.

> [!Note]
> Azure 지역 및 ExpressRoute 위치는 서로 다른 두 가지 개념이며, 두 가지 간의 차이점을 이해하는 것은 Azure 하이브리드 네트워킹 연결을 탐색하는 데 중요합니다. 
>
>

## <a name="azure-regions"></a>Azure 지역
Azure 지역은 Azure 컴퓨팅, 네트워킹 및 스토리지 리소스가 있는 글로벌 데이터 센터입니다. Azure 리소스를 만들 때 고객은 리소스 위치를 선택해야 합니다. 리소스 위치는 리소스를 만들 Azure 데이터 센터(또는 가용성 영역)를 결정합니다.

## <a name="expressroute-locations"></a>ExpressRoute 위치
ExpressRoute 위치(예: 피어링 위치 또는 Meet-Me 위치)는 MSEE(Microsoft Enterprise 에지) 디바이스가 있는 공동 배치 시설입니다. ExpressRoute 위치는 Microsoft 네트워크에 대한 진입점으로, 전역적으로 분산되어 있어 전 세계의 Microsoft 네트워크에 연결할 수 있는 기회를 고객에게 제공합니다. 이러한 위치는 ExpressRoute 파트너와 ExpressRoute Direct 고객이 Microsoft 네트워크에 대한 교차 연결이 발생하는 위치입니다. 일반적으로 ExpressRoute 위치는 Azure 지역과 일치하지 않아도 됩니다. 예를 들어, 고객은 *시애틀* 피어링 위치에서 *미국 동부* 리소스 위치로 ExpressRoute 회로를 만들 수 있습니다.

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>지역 내 ExpressRoute 위치에 대한 Azure 지역
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **오스트레일리아 정부** | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 프랑스 중부, 프랑스 남부, 독일 북부, 독일 중서부, 북유럽, 노르웨이 동부, 노르웨이 서부, 스위스 북부, 스위스 서부, 영국 서부, 영국 남부, 서유럽 |암스테르담, 암스테르담 2, 베를린, 코펜하겐, 더블린, 프랑크푸르트, 제네바, 런던, 런던 2, 마르세이유, 밀라노, 뮌헨, 뉴포트(웨일스), 오슬로, 파리, 스타방에르, 스톡홀름, 취리히 |
| **북아메리카** | 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 달라스, 덴버, Las Vegas, 로스앤젤레스, 시야가 Angeles2, 마이애미, Minneapolis, 몬트리올, 뉴욕, Phoenix, 퀘벡 City, Queretaro (멕시코), 퀸시, San Antonio, 시애틀, 실리콘 유역, 실리콘 Valley2,, 밴쿠버, 워싱턴 DC, 워싱턴 DC2 |
| **아시아** | 동아시아, 동남 아시아 | 방콕, 홍콩, 홍콩2, 자카르타, 콸라룸푸르, 싱가포르, 싱가포르2, 타이베이 |
| **인도** | 인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 일본 서부, 일본 동부 |오사카, 도쿄, 도쿄2 |
| **오세아니아** | 오스트레일리아 남동부, 오스트레일리아 동부 |오클랜드, 멜버른, 퍼스, 시드니, 시드니2 | 
| **대한민국** | 한국 중부, 한국 남부 |부산, 서울|
| **아랍에미리트** | 아랍에미리트 중부, 아랍에미리트 북부 | 두바이, 두바이2 |
| **남아프리카 공화국** | 남아프리카 공화국 서부, 남아프리카 공화국 북부 |케이프타운, 요하네스버그 |
| **남아메리카** | 브라질 남부 |상파울루 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>국내 클라우드에 대한 Azure 지역 및 지리적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아, US DoD 중부, US DoD 동부  |애틀랜타, 시카고, 댈러스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 각 위치에 대한 연결 위치 및 서비스 공급자를 보여 줍니다. 서비스를 제공할 수 있는 서비스 공급자 및 위치를 보려면 [서비스 공급자별 위치](expressroute-locations.md)를 참조하세요.

* **로컬 Azure 지역**은 각 피어링 위치에서 액세스할 수 있는 [ExpressRoute 로컬](expressroute-faqs.md)입니다. **n/a**는 해당 피어링 위치에서 ExpressRoute 로컬을 사용할 수 없음을 나타냅니다.

* **영역**은 [가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 참조합니다.


### <a name="global-commercial-azure"></a>글로벌 상업용 Azure
| **위치** | **주소** | **영역** | **로컬 Azure 지역** | **ER Direct** | **서비스 공급자** |
| --- | --- | --- | --- | --- | --- |
| **암스테르담** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 서유럽 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **암스테르담2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 서유럽 | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-DE x, euNetworks, GÉANT, Interxion, NOS, 주황, Vodafone |
| **애틀랜타** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 해당 없음 | 10G, 100G | Equinix, Megaport |
| **오클랜드** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 해당 없음 | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **방콕** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | 해당 없음 | 10G | AIS, UIH |
| **베를린** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | 독일 북부 | 10G | NTT 글로벌 데이터 센터 EMEA|
| **부산** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | 한국 남부 | 해당 없음 | LG CNS |
| **캔버라** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 | 10G, 100G | CDC |
| **캔버라2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 2| 10G, 100G | CDC |
| **케이프타운** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 남아프리카 공화국 서부 | 10G | BCX, Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **첸나이** | Tata Communications | 2 | 인도 남부 | 10G | Global CloudXchange(GCX), SIFY, Tata Communications |
| **첸나이2** | Airtel | 2 | 인도 남부 | 10G | Airtel |
| **시카고** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 미국 중북부 | 10G, 100G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **코펜하겐** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 해당 없음 | 10G | Interxion |
| **댈러스** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 해당 없음 | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **덴버** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 미국 중서부 | 해당 없음 | CoreSite, Megaport, Zayo |
| **두바이** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 아랍에미리트 북부 | 해당 없음 | Etisalat UAE |
| **두바이2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | 아랍에미리트 북부 | 해당 없음 | DE-DE x, du datamena, Megaport, 주황, Orixcom |
| **더블린** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 북유럽 | 10G, 100G | Colt, eir, 서는 Inix, GEANT, euNetworks, Interxion, Megaport |
| **프랑크푸르트** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 독일 중서부 | 10G, 100G | &T NetBond, CenturyLink Cloud Connect, Colt, UNCIX, Inix, euNetworks, GEANT, Interxion, Megaport, 주황, Telia 캐리어 |
| **제네바** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 스위스 서부 | 10G, 100G | Equinix, Megaport |
| **홍콩** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 동아시아 | 10G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **홍콩2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 동아시아 | 10G | 중국 모바일 국제, 중국 통신 글로벌, PCCW Global 제한, SingTel |
| **자카르타** | Telkom Indonesia | 4 | 해당 없음 | 10G | |
| **요하네스버그** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 남아프리카 북부 | 10G | BCX, British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **콸라룸푸르** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | 해당 없음 | 해당 없음 | TIME dotCom |
| **라스베이거스** | [Switch LV](https://www.switch.com/las-vegas) | 1 | 해당 없음 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **런던** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 영국 남부 | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **런던2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 영국 남부 | 10G, 100G | CenturyLink Cloud Connect, Colt, GTT, IX Reach, EQUINIX, Megaport, Telehouse |
| **로스앤젤레스** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 해당 없음 | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **시야가 Angeles2** | [서-ix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | 해당 없음 | 10G, 100G | Equinix |
| **마르세유** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 프랑스 남부 | 해당 없음 | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **멜버른** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 오스트레일리아 남동부 | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **마이애미** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 해당 없음 | 10G, 100G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **밀라노** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | 해당 없음 | 10G | Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | 해당 없음 | 10G, 100G | Cologix |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 해당 없음 | 10G, 100G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **뭄바이** | Tata Communications | 2 | 인도 서부 | 10G | Gcx x, GCX (Global), Jio, Sify, Tata 통신, Verizon |
| **뭄바이2** | Airtel | 2 | 인도 서부 | 10G | Airtel, Sify, Vodafone Idea |
| **뮌헨** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | 해당 없음 | 10G | DE-CIX |
| **뉴욕** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | 해당 없음 | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, \CIX, Megaport, Packet, Zayo |
| **뉴포트(웨일스)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 영국 서부 | 해당 없음 | British Telecom, Colt, Level 3 Communications, Next Generation Data |
| **오사카** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 일본 서부 | 10G, 100G | 도쿄, Colt, 고 간, 인터넷 이니셔티브 일본 Inc..-IIJ, Megaport, NTT Communications, NTT SmartConnect, 소프트 은행 |
| **오슬로** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 노르웨이 동부 | 10G, 100G | 전역 Connect, Megaport, 통신 회사 |
| **파리** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | 프랑스 중부 | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **퍼스** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 해당 없음 | 10G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | 해당 없음 | 10G | |
| **퀘벡 시티** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 캐나다 동부 | 해당 없음 | Bell Canada, Megaport |
| **Queretaro (멕시코)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | 해당 없음 | 10G | Transtelco|
| **퀸시** | [Sabey Datacenter-빌드](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | 미국 서부 2 | 10G, 100G | | 
| **샌안토니오** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 미국 중남부 | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **상파울루** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 브라질 남부 | 해당 없음 | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **시애틀** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 미국 서부 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **서울** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | 한국 중부 | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **실리콘밸리** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 미국 서부 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **실리콘밸리2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 미국 서부 | 10G, 100G | Colt, Coresite | 
| **싱가포르** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 동남아시아 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 동남아시아 | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 노르웨이 서부 | 10G, 100G |전역 연결, Megaport |
| **스톡홀름** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 해당 없음 | 10G | Equinix, Telia Carrier |
| **시드니** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 오스트레일리아 동부 | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **시드니2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 오스트레일리아 동부 | 10G, 100G | Megaport, NextDC |
| **타이베이** | Chief Telecom | 2 | 해당 없음 | 10G | Chief Telecom, Chunghwa Telecom, FarEasTone |
| **도쿄** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 일본 동부 | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **도쿄2** | [AT TOKYO](https://www.attokyo.com/) | 2 | 일본 동부 | 10G, 100G | AT TOKYO |
| **토론토** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 캐나다 중부 | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **밴쿠버** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | 해당 없음 | 10G | Cologix |
| **워싱턴 DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **워싱턴 DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **취리히** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 스위스 북부 | 10G, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environments"></a>국가별 클라우드 환경

Azure 국가별 클라우드는 서로 격리되며 전 세계 상용 Azure에서 격리됩니다. 한 Azure 클라우드의 ExpressRoute는 다른 Azure 지역에 연결할 수 없습니다.

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **위치** | **주소** | **로컬 Azure 지역**| **ER Direct** | **서비스 공급자** |
| --- | --- | --- | --- | --- |
| **애틀랜타** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | 해당 없음 | 10G, 100G | Equinix |
| **시카고** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 해당 없음 | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **댈러스** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 해당 없음 | 10G, 100G | Equinix, Megaport, Verizon |
| **뉴욕** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 해당 없음 | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 애리조나 | 해당 없음 | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **샌안토니오** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 텍사스 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **실리콘밸리** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 해당 없음 | 10G, 100G | AT&T, Equinix, Level 3 Communications, Verizon |
| **시애틀** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 해당 없음 | 해당 없음 | Equinix, Megaport |
| **워싱턴 DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov 버지니아 | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>중국
| **위치** | **서비스 공급자** |
| --- | --- |
| **베이징** |China Telecom |
| **베이징2** | China Telecom, China Unicom, GDS |
| **상하이** |China Telecom |
| **상하이2** | China Telecom, China Unicom, GDS |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>독일
| **위치** | **서비스 공급자** |
| --- | --- |
| **베를린** |e-shelter, Megaport+, T-Systems |
| **프랑크푸르트** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Exchange 공급자를 통한 연결
연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>위성 운영자를 통한 연결
원격으로 파이버 연결이 없거나 다른 연결 옵션을 탐색하려는 경우 다음 위성 운영자를 확인할 수 있습니다. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>추가 서비스 공급자를 통한 연결
| **위치** | **Exchange** | **연결 공급자** |
| --- | --- | --- |
| **암스테르담** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **애틀랜타** | Equinix| Crown Castle
| **케이프타운** | Teraco | MTN |
| **시카고** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **댈러스** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **프랑크푸르트** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **함부르크** | Equinix | 중국 |
| **홍콩 특별행정구** | Equinix | Chief, Macroview Telecom |
| **요하네스버그** | Teraco | MTN |
| **런던** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **로스앤젤레스** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **마드리드** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **뉴욕** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **파리** | Equinix | Proximus |
| **퀘벡 시티** | Megaport | Fibrenoire |
| **상파울루** | Equinix | Venha Pra Nuvem |
| **시애틀** |Equinix | Alaska Communications |
| **실리콘밸리** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **싱가포르** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **슬라우** | Equinix | HSO|
| **시드니** | Megaport | Macquarie Telecom Group|
| **도쿄** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **토론토** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **워싱턴 DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 프라이빗 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **Continent** | **시스템 통합 업체** |
| --- | --- |
| **아시아** |Avanade Inc., OneAs1a |
| **오스트레일리아** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **유럽** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **북아메리카** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **남아메리카** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
