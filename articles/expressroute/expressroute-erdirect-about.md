---
title: Azure Express 경로 직접 정보
description: 사용 가능한 Sku 및 기술 요구 사항과 같이 Express 경로 다이렉트에 등록 하는 데 필요한 정보 및 Azure Express 경로 다이렉트의 주요 기능에 대해 알아봅니다.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f0faa4992b025783e4e5448dcf04fcfa958ba5ed
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192352"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct 정보

ExpressRoute Direct는 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. Express 경로 다이렉트는 규모에 맞게 활성/활성 연결을 지 원하는 이중 100 Gbps 또는 10gbps 연결을 제공 합니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* 스토리지 및 Cosmos DB와 같은 서비스로 대규모 데이터 수집
* 은행, 정부, 소매업체 등 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

## <a name="onboard-to-expressroute-direct"></a>Express 경로 직접에 등록

Express 경로 다이렉트를 사용 하기 전에 먼저 구독을 등록 해야 합니다. 등록하려면 다음 세부 사항을 포함하여 구독 ID로 <ExpressRouteDirect@microsoft.com>에 이메일을 전송합니다.

* **ExpressRoute Direct**를 사용하여 수행하려는 시나리오
* 위치 기본 설정 - 모든 위치의 전체 목록은 [파트너 및 피어링 위치](expressroute-locations-providers.md) 참조
* 구현을 위한 타임라인
* 기타 궁금한 점이 있는 경우

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>서비스 공급자 및 ExpressRoute Direct를 사용하는 ExpressRoute

| **서비스 공급자를 사용하는 ExpressRoute** | **Express 경로 직접** | 
| --- | --- |
| 서비스 공급자를 활용하여 기존 인프라로 빠른 온보딩 및 연결 활성화 | 모든 계층의 100 Gbps/10 Gbps 인프라 및 전체 관리 필요
| 이더넷 및 MPLS를 포함하는 수백 개의 공급자와 통합 | 규제 산업 및 대규모 데이터 수집에 대한 직접/전용 용량 |
| 50Mbps - 10Gbps의 회로 SKU | 고객은 100 Gbps Express 경로에서 다음과 같은 회로 Sku의 조합을 선택할 수 있습니다. <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> 고객은 10gbps Express 경로 다이렉트에서 다음 회로 Sku의 조합을 선택할 수 있습니다.<ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>
| 단일 테넌트에 최적화됨 | 여러 사업부 및 여러 작업 환경에서 단일 테 넌 트에 대해 최적화 됨

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 회로

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. Express 경로를 사용 하 여 Microsoft Azure 및 Office 365와 같은 Microsoft 클라우드 서비스에 대 한 연결을 설정할 수 있습니다.

각 피어링 위치는 Microsoft의 글로벌 네트워크에 대한 액세스 권한을 가지며 기본적으로 지리적 영역에 있는 모든 지역에 액세스할 수 있으며 프리미엄 회로를 사용하여 전 세계 모든 지역에 액세스할 수 있습니다.  

대부분의 시나리오에서 기능은 작동하기 위해 ExpressRoute 서비스 공급자를 활용하는 회로와 동일합니다. 추가 세분성 및 ExpressRoute Direct를 사용하여 제공되는 새로운 기능을 지원하기 위해 ExpressRoute Direct 회로에 존재하는 특정 키 기능이 있습니다.

## <a name="circuit-skus"></a>회로 SKU

ExpressRoute Direct는 Azure Storage 및 기타 빅 데이터 서비스에 대규모 데이터 수집 시나리오를 지원합니다. 100 Gbps Express 경로에 대 한 Express 경로 회로는 이제 **40 gbps** 및 **100 Gbps** 회로 sku도 지원 합니다. 실제 포트 쌍은 **100 또는 10Gbps** 전용 이며 여러 가상 회로를 가질 수 있습니다. 회로 크기:

| **100 Gbps Express 경로 직접** | **10gbps Express 경로 직접** | 
| --- | --- |
| **구독 된 대역폭**: 200 Gbps | **구독 된 대역폭**: 20gbps |
| <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> | <ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>

## <a name="technical-requirements"></a>기술적인 요구 사항

* Microsoft Enterprise Edge Router (MSEE) 인터페이스:
    * 라우터 쌍 간에 이중 10 또는 100 기가 비트 이더넷 포트
    * 단일 모드 LR 파이버 연결
    * IPv4 및 IPv6
    * IP MTU 1500 바이트

* 스위치/라우터 계층 2/계층 3 연결:
    * 1 802.1 Q (Dot1Q) 태그 또는 2 개의 Tag 802.1 Q (QinQ) 태그 캡슐화를 지원 해야 합니다.
    * Ethertype = 0x8100
    * Microsoft에서 지정한 VLAN ID에 따라 외부 VLAN 태그 (STAG)를 추가 해야 합니다 ( *QinQ에만 해당* ).
    * 포트 및 장치당 여러 BGP 세션 (Vlan)을 지원 해야 합니다.
    * IPv4 및 IPv6 연결 I p v 6 *의 경우 하위 인터페이스가 추가로 생성 되지 않습니다. IPv6 주소가 기존 하위 인터페이스에 추가*됩니다. 
    * 선택 사항: [BFD (양방향 전달 검색)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) 지원: express 경로 회로의 모든 개인 피어 링에 대해 기본적으로 구성 됩니다.

## <a name="vlan-tagging"></a>VLAN 태그 지정

ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 태그 지정을 지원합니다.

* **QinQ VLAN 태그 지정**은 ExpressRoute 회로 기준당 격리된 라우팅 도메인을 허용합니다. Azure는 회로 생성 시 동적으로 S 태그를 할당하고 이는 변경할 수 없습니다. 회로의 각 피어링(프라이빗 및 Microsoft)은 VLAN으로 고유한 C 태그를 활용합니다. C 태그는 ExpressRoute Direct 포트의 회로에서 고유할 필요가 없습니다.

* **Dot1Q VLAN 태그 지정**은 각 ExpressRoute Direct 포트 쌍 기준당 단일 태그가 지정된 VLAN을 허용합니다. 피어링에 사용되는 C 태그는 ExpressRoute Direct 포트 쌍의 모든 회로 및 피어링에서 고유해야 합니다.

## <a name="workflow"></a>워크플로

[![워크플로](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct는 Microsoft 글로벌 네트워크에 대한 활성/활성 중복 연결과 동일한 엔터프라이즈급 SLA를 제공합니다. ExpressRoute 인프라는 중복되며, Microsoft 글로벌 네트워크로 연결은 중복되고 다양하며 고객 요구 사항에 적절하게 크기를 조정합니다. 

## <a name="next-steps"></a>다음 단계

[ExpressRoute Direct 구성](expressroute-howto-erdirect.md)
