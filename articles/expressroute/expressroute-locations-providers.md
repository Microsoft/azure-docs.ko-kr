---
title: '위치 및 연결 공급자: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 위치에 따라 정렬됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: 6eaa2c038c75243c2b2ad4361c173fe527ebf3b4
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212323"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서의 표에서는 Express 경로 지리적 범위 및 위치, Express 경로 연결 공급자 및 SIs (Express 경로 시스템 통합 업체)에 대 한 정보를 제공 합니다.

> [!Note]
> Azure 지역 및 Express 경로 위치는 서로 다른 두 가지 개념 이며, Azure 하이브리드 네트워킹 연결을 탐색 하는 데 두 가지 간의 차이점을 이해 하는 것이 중요 합니다. 
>
>

## <a name="azure-regions"></a>Azure 지역
Azure 지역은 Azure 계산, 네트워킹 및 저장소 리소스가 있는 글로벌 데이터 센터입니다. Azure 리소스를 만들 때 고객은 리소스 위치를 선택 해야 합니다. 리소스 위치는 리소스를 만들 Azure 데이터 센터 (또는 가용성 영역)를 결정 합니다.

## <a name="expressroute-locations"></a>Express 경로 위치
Express 경로 위치 (예를 들어 피어 링 위치 또는 위치 면)는 MSEE (Microsoft Enterprise edge) 장치가 있는 공동 배치 기능입니다. Express 경로 위치는 Microsoft 네트워크에 대 한 진입점으로, 전 세계적으로 분산 되어 있어 전 세계의 Microsoft 네트워크에 연결할 수 있는 기회를 고객에 게 제공 합니다. 이러한 위치는 Express 경로 파트너와 Express 경로 다이렉트 고객이 Microsoft 네트워크에 대 한 교차 연결을 발급 하는 위치입니다. 일반적으로 Express 경로 위치는 Azure 지역과 일치 하지 않아도 됩니다. 예를 들어, 고객은 *시애틀* 피어 링 위치에 *미국 동부*의 리소스 위치로 express 경로 회로를 만들 수 있습니다.

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 

## <a name="locations"></a>지정 학적 지역 내에서 Express 경로 위치로 Azure 지역
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **오스트레일리아 정부** | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 프랑스 중부, 프랑스 남부, 독일 북부, 독일 중서부, 북부 유럽, 노르웨이 동부, 노르웨이 서 부, 스위스 북부, 스위스 서부, 영국 서부, 영국 남부, 유럽 서부 |암스테르담, Amsterdam2, 코펜하겐, 더블린, 프랑크푸르트, Geneva, 런던, London2, 마르세유에, Milan, 뮌헨, Newport (Wales), Oslo, 파리, Stavanger, 스톡홀름, 취리히, 뮌헨 |
| **북아메리카** | 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 달라스, 덴버, Las Vegas, 로스앤젤레스, 마이애미, 뉴욕, San Antonio, 시애틀, 실리콘 계곡, 실리콘 Valley2, 워싱턴 DC, 워싱턴 DC2, 몬트리올, 퀘벡 City, |
| **아시아** | 동아시아, 동남 아시아 | 방콕, 홍콩, 홍콩 Kong2, 자카르타, 콸라룸푸르, 싱가포르, 싱가포르 2, 타이베이 |
| **인도** | 인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 일본 서부, 일본 동부 |오사카, 도쿄 |
| **오세아니아** | 오스트레일리아 남동부, 오스트레일리아 동부 |오클랜드, 멜버른, 퍼스, 시드니, Sydney2 | 
| **대한민국** | 한국 중부, 한국 남부 |부산, 서울|
| **아랍에미리트** | 아랍에미리트 중부, 아랍에미리트 북부 | Dubai, Dubai2 |
| **남아프리카 공화국** | 남아프리카 공화국 서 부, 남아프리카 공화국 북부 |케이프타운, 요하네스버그 |
| **남아메리카** | 브라질 남부 |상파울루 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>국가별 클라우드의 Azure 지역 및 지정 학적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 각 위치에 대한 연결 위치 및 서비스 공급자를 보여 줍니다. 서비스를 제공할 수 있는 서비스 공급자 및 위치를 보려면 [서비스 공급자별 위치](expressroute-locations.md)를 참조하세요.

* **로컬 Azure 지역은** 각 피어 링 위치에서 [express](expressroute-faqs.md) 경로를 사용 하 여 액세스할 수 있습니다. **n/a** 는 해당 피어 링 위치에서 Express 경로 로컬을 사용할 수 없음을 나타냅니다.

* **영역은** [가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 나타냅니다.


### <a name="global-commercial-azure"></a>글로벌 상용 Azure
| **위치** | **주소** | **영역** | **로컬 Azure 지역** | **ER Direct** | **서비스 공급자** |
| --- | --- | --- | --- | --- | --- |
| **암스테르담** | [서-ix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 서유럽 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, Colt, 의사 Inix, euNetworks, GÉANT, InterCloud, Interx이온, KPN, IX Reach, 수준 3 통신, Megaport, NTT 통신, 주황색, Tata 통신 Zayo |
| **암스테르담2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 서유럽 | 10G, 100G | CenturyLink Cloud Connect, Colt, UNCIX, euNetworks, Interxion, 주황, Vodafone |
| **애틀랜타** | [서-ix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 해당 없음 | 해당 없음 | Equinix, Megaport |
| **오클랜드** | [Vocus Group NZ 올버니](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 해당 없음 | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **방콕** | [AIS](http://business.ais.co.th/solution/microsoft-azure.html?category=cloud) | 2 | 해당 없음 | 10G | AIS |
| **부산** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 한국 남부 | 해당 없음 | LG CNS |
| **캔버라** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 | 10G, 100G | CDC |
| **캔버라2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 2| 10G, 100G | CDC |
| **케이프타운** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 남아프리카 서부 | 10G | BCX, 인터넷 솔루션-클라우드 연결, 액체 텔레콤, Teraco |
| **첸나이** | Tata Communications | 2 | 인도 남부 | 10G | Global CloudXchange(GCX), SIFY, Tata Communications |
| **첸나이2** | Airtel | 2 | 인도 남부 | 해당 없음 | Airtel |
| **시카고** | [서-ix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 미국 중북부 | 10G, 100G | Aryaka Networks, & T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite,, PacketFabric, PCCW Global 제한, 스 프린트, Telia 캐리어, Verizon, Zayo |
| **코펜하겐** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 해당 없음 | 10G | Interxion |
| **댈러스** | [서-ix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 해당 없음 | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **덴버** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 미국 중서부 | 해당 없음 | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 아랍에미리트 북부 | 해당 없음 | Etisalat 아랍에미리트 |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | 아랍에미리트 북부 | 해당 없음 | du datamena, Megaport, 주황, Orixcom |
| **더블린** | [서-ix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 북유럽 | 10G, 100G | Colt, eir, Megaport, euNetworks, Interxion, |
| **프랑크푸르트** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 독일 중서부 | 10G, 100G | Colt, DE-DE x, GEANT, Interxion, Megaport, 주황, Telia 캐리어 |
| **Geneva** | [서-ix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 스위스 서부 | 10G, 100G | Equinix, Megaport |
| **홍콩** | [서-ix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 동아시아 | 해당 없음 | Aryaka Networks, 영국령 텔레콤, CenturyLink 클라우드 연결, 최고 통신, 중국 통신 글로벌, 고 대이, Megaport, NTT 통신, 주황색, PCCW Global 제한, Tata 통신, Telia 캐리어, Verizon |
| **홍콩 Kong2** | [메가 비트](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 해당 없음 | 10G | |
| **지역** | 서 용 kom 인도네시아 | 4 | 해당 없음 | 10G | |
| **요하네스버그** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 남아프리카 북부 | 10G | 영국령 텔레콤, 인터넷 솔루션-클라우드 연결, 액체 텔레콤, 주황, Teraco |
| **콸라룸푸르** | [시간 dotCom Menara 목표](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | 해당 없음 | 해당 없음 | TIME dotCom |
| **라스베이거스** | [LV 전환](https://www.switch.com/las-vegas) | 1 | 해당 없음 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **런던** | [서-ix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 영국 남부 | 10G, 100G | & T NetBond, 영국령 텔레콤, Colt, 고 지이, euNetworks, InterCloud, 인터넷 솔루션-클라우드 연결, Interx이온, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, 주황, PCCW Global 제한, Tata 통신, Telehouse-KDDI, 통신 사업자, Verizon, Vodafone, Zayo |
| **런던2** | [Telehouse 북부 2](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 영국 남부 | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Inix, Megaport, Telehouse |
| **로스앤젤레스** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 해당 없음 | 해당 없음 | CoreSite, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **마르세유** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 프랑스 남부 | 해당 없음 | 비 CIX, GEANT, Interxion, 재규어 네트워크 |
| **멜버른** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 오스트레일리아 남동부 | 10G, 100G | AARNet, Devoli,, Megaport, NEXTDC, Optus, Telstra Corporation, TPG 텔레콤 |
| **마이애미** | [서-ix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 해당 없음 | 10G | C3ntro, Megaport, Neutrona Networks |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | 해당 없음 | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 해당 없음 | 10G, 100G | 종 캐나다, Cologix, Megaport, Telus, Zayo |
| **뭄바이** | Tata Communications | 2 | 인도 서부 | 해당 없음 | GCX (Global Gcx), Jio, Sify, Tata 통신, Verizon |
| **뭄바이2** | Airtel | 2 | 인도 서부 | 해당 없음 | Airtel, Sify, Vodafone Idea |
| **뮌헨** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | 해당 없음 | 10G, 100G | |
| **뉴욕** | [서-ix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | 해당 없음 | 해당 없음 | CenturyLink Cloud Connect, Colt, Coresite, 서 면 Inix, InterCloud, Megaport, Packet, Zayo |
| **뉴포트(웨일스)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 영국 서부 | 해당 없음 | 영국령 텔레콤, Colt, 수준 3 통신, 차세대 데이터 |
| **오사카** | [서-ix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 일본 서부 | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 노르웨이 동부 | 10G, 100G | Megaport, 통신 사업자 |
| **파리** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | 프랑스 중부 | 해당 없음 | CenturyLink Cloud Connect, Colt,와 서는 Inix, Intercloud, Interx이온, 주황색, Telia 캐리어, Zayo |
| **퍼스** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 해당 없음 | 10G | Megaport, NextDC |
| **퀘벡 시티** | [유리한](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 캐나다 동부 | 해당 없음 | Bell Canada, Megaport |
| **샌안토니오** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 미국 중남부 | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **상파울루** | [서-ix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 브라질 남부 | 해당 없음 | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **시애틀** | [서-ix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 미국 서부 2 | 10G, 100G | Aryaka Networks, Megaport, Telus, Zayo |
| **서울** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 한국 중부 | 10G, 100G | KINX, KT, LG CN, Sejong 텔레콤 |
| **실리콘밸리** | [서-ix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 미국 서부 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Colt, Comcast, Coresite, 의사 Inix, InterCloud, Internet2, IX Reach, 패킷, PacketFabric, 수준 3 통신, Megaport, 주황, 스 프린트, Tata 통신, Telia 캐리어, Verizon, Zayo |
| **실리콘 Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 미국 서부 | 10G, 100G | Colt, Coresite | 
| **싱가포르** | [서-ix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 동남아시아 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, 중국 모바일 국제 통신, 엡실론 글로벌 통신, Inix, InterCloud, Level 3 Communications, Megaport, NTT Communications, 주황, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [전역 스위치 타이 Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 동남아시아 | 10G, 100G | 중국 Unicom Global, Colt, 엡실론 글로벌 통신, Megaport, SingTel |
| **Stavanger** | [녹색 산지 DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 노르웨이 서 부 | 10G, 100G | |
| **스톡홀름** | [서-ix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 해당 없음 | 10G | 서-ix, Telia 캐리어 |
| **시드니** | [서-ix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 오스트레일리아 동부 | 10G, 100G | AARNet, & T NetBond, 영국령 텔레콤, Devoli, Kordia, Megaport, NEXTDC, NTT Communications, Optus, 주황, Spark NZ, Telstra Corporation, TPG 텔레콤, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 오스트레일리아 동부 | 10G, 100G | NextDC |
| **타이베이** | Chief Telecom | 2 | 해당 없음 | 10G | 최고 통신, FarEasTone |
| **도쿄** | [서-ix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 일본 동부 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Colt, Equinix, 인터넷 이니셔티브 일본 Inc.-IIJ, Megaport, NTT 통신, NTT 동부, 주황, 소프트 뱅크, Verizon |
| **토론토** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 캐나다 중부 | 10G, 100G | & T NetBond, 종 캐나다, CenturyLink Cloud Connect, Cologix, Megaport, Telus, Verizon, Zayo |
| **워싱턴 DC** | [서-ix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Cologix, Internet2, InterCloud, IX Reach, 수준 3 통신, Megaport, Neutrona Networks, NTT 통신, 주황색, PacketFabric, SES , 스 프린트, Tata 통신, Telia 캐리어, Verizon, Zayo |
| **워싱턴 DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **취리히** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 스위스 북부 | 해당 없음 | Intercloud, Interx이온, Megaport, Swisscom |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environments"></a>국가별 클라우드 환경

Azure 국가별 클라우드는 서로 격리 되며 전 세계 상용 Azure에서 격리 됩니다. 한 Azure 클라우드의 Express 경로는 다른 azure 지역에 연결할 수 없습니다.

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **위치** | **주소** | **로컬 Azure 지역**| **ER Direct** | **서비스 공급자** |
| --- | --- | --- | --- | --- |
| **시카고** | [서-ix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 해당 없음 | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **댈러스** | [서-ix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 해당 없음 | 10G, 100G | Equinix, Megaport, Verizon |
| **뉴욕** | [서-ix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 해당 없음 | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 애리조나 | 해당 없음 | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **샌안토니오** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 텍사스 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **실리콘밸리** | [서-ix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 해당 없음 | 10G, 100G | & T에서, 수준 3 통신, Verizon |
| **시애틀** | [서-ix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 해당 없음 | 해당 없음 | Equinix, Megaport |
| **워싱턴 DC** | [서-ix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 동부, US Gov 버지니아 | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>중국
| **위치** | **서비스 공급자** |
| --- | --- |
| **베이징** |China Telecom |
| **베이징2** | 중국 통신, 중국 Unicom, GDS |
| **상하이** |China Telecom |
| **상하이2** | 중국 통신, GDS |

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
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>위성 연산자를 통한 연결
원격이 고 파이버 연결이 없거나 다른 연결 옵션을 탐색 하려는 경우 다음 위성 연산자를 확인할 수 있습니다. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>추가 서비스 공급자를 통한 연결
| **위치** | **Exchange** | **연결 공급자** |
| --- | --- | --- |
| **암스테르담** | 수준 3 통신, Interxion | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Bridge 국제, Kalaam 텔레콤 바레인 Bics, MainOne, Nianet, POST 통신 룩셈부르크, Proximus, TDC Erhverv, 텔레콤 Italia 반짝, Telekom Deutschland GmbH, Telia |
| **애틀랜타** | Equinix| Ccs (crown 성
| **케이프타운** | Teraco | MTN |
| **시카고** | Equinix| Ccs (crown 성, 스펙트럼 Enterprise, Windstream |
| **댈러스** | Equinix, Megaport | Axtel, C3ntro 텔레콤, Cox Business, Ccs (crown 성, Data Foundry, 스펙트럼 Enterprise, and Stelco |
| **프랑크푸르트** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **함부르크** | Equinix | 중국 |
| **홍콩 특별 행정구** | Equinix | Chief, Macroview Telecom |
| **요하네스버그** | Teraco | MTN |
| **런던** | BICS, 서-Ix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **로스앤젤레스** | Equinix |Ccs (crown 성, 스펙트럼 Enterprise, and Stelco |
| **마드리드** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Aptum 기술, Iwmentor, Zirro |
| **뉴욕** |Equinix, Megaport | Altice Business, Ccs (crown 성, 스펙트럼 Enterprise, Webair |
| **파리** | Equinix | Proximus |
| **퀘벡 시티** | Megaport | Fibrenoire |
| **상파울루** | Equinix | Venha Pra Nuvem |
| **시애틀** |Equinix | Alaska Communications |
| **실리콘밸리** |Coresite, | Cox Business, 스펙트럼 Enterprise, Windstream, X2nsat i n c. |
| **싱가포르** |Equinix |1CLOUDSTAR, BICS, CMC 통신, 엡실론 통신 제한, LGA 텔레콤, UIH (미국 정보 고속도로) |
| **슬라우** | Equinix | HSO|
| **시드니** | Megaport | Macquarie Telecom Group|
| **도쿄** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **토론토** | Equinix, Megaport | 락 게이트 기술 Inc., Beanfield Metroconnect, Aptum 기술, IVedha Inc., Iwmentor, Thinktel, Zirro|
| **워싱턴 DC** |Equinix | Altice Business, BICS, Cox Business, Ccs (crown 성, Gtt Communications Inc., 엡실론 통신 제한, Masergy, Windstream |

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
