---
title: Azure Monitor에서 네트워크 모니터링에 대 한 로그 | Microsoft Docs
description: 클라우드, 온-프레미스 및 하이브리드 환경에 걸친 네트워크를 관리하는, NPM을 비롯한 네트워크 모니터링 솔루션의 개요입니다.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 42fb5d69a1f32d669ad5191e342b3f2f880b8c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564820"
---
# <a name="network-monitoring-solutions"></a>네트워크 모니터링 솔루션 

Azure는 네트워킹 자산을 모니터링하는 솔루션 호스트를 제공합니다. Azure에는 네트워크 연결, ExpressRoute 회로 상태를 모니터링하고 클라우드의 네트워크 트래픽을 분석하는 솔루션 및 유틸리티가 있습니다.

## <a name="network-performance-monitor-npm"></a>NPM(네트워크 성능 모니터)

NPM(네트워크 성능 모니터)은 애플리케이션에 대한 네트워크 연결인 네트워크의 상태를 모니터링하도록 맞춰진, 네트워크 성능에 대한 정보를 제공하는 기능 모음입니다. NPM은 클라우드를 기반으로 하며, 다음의 연결을 모니터링하는 하이브리드 네트워크 모니터링 솔루션을 제공합니다.
 
* 클라우드 배포 및 온-프레미스 위치
* 여러 데이터 센터 및 지사
* 중요 업무용 다중 계층 애플리케이션/마이크로 서비스
* 사용자 위치 및 웹 기반 애플리케이션(HTTP/HTTPS) 

성능 모니터, ExpressRoute 모니터 및 서비스 연결 모니터는 NPM 내의 모니터링 기능이며 아래에 설명되어 있습니다.

## <a name="performance-monitor"></a>성능 모니터링

성능 모니터는 NPM의 일부이며 클라우드, 하이브리드 및 온-프레미스 환경에 대한 네트워크 모니터링입니다. 원격 분기와 지사, 상점 위치, 데이터 센터 및 클라우드 전반의 네트워크 연결을 모니터링할 수 있습니다. 사용자가 불만을 제기하기 전에 네트워크 문제를 감지할 수 있습니다. 주요 이점은 다음과 같습니다.

* 다양한 서브넷에서 손실 및 대기 시간 모니터링, 경고 설정
* 네트워크의 모든 경로(중복 경로 포함) 모니터링
* 복제하기 어려운 일시적 및 특정 시점의 네트워크 문제 해결
* 성능 저하를 야기한 네트워크의 특정 세그먼트 확인
* SNMP 없이 네트워크의 상태 모니터링

![NPM 토폴로지 맵](./media/network-monitoring-overview/npm-topology-map.png) 

자세한 내용은 다음 문서를 참조하세요.

* [Azure Monitor 로그에는 네트워크 성능 모니터 솔루션 구성](../azure-monitor/insights/network-performance-monitor.md) 
* [사용 사례](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  제품 업데이트: [2017 년 2 월](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017 년 8 월](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute 모니터

ExpressRoute를 위한 NPM은 Azure 개인 피어링 및 Microsoft 피어링 연결에 대한 종합적인 ExpressRoute 모니터링을 제공합니다. ExpressRoute를 통해 지사와 Azure 간의 E2E 연결 및 성능을 모니터링할 수 있습니다. 주요 기능은 다음과 같습니다.

* 구독에 연결된 ER 회로의 자동 검색
* 온-프레미스에서 클라우드 애플리케이션까지 네트워크 토폴로지 검색
* 용량 계획, 대역폭 사용률 분석
* 기본 및 보조 경로에서 모니터링 및 경고
* ExpressRoute를 통해 Office 365, Dynamics 365 등의 Azure 서비스에 대한 연결 모니터링
* VNet에 대한 연결에서 성능 저하 감지

![전체 지역의 트래픽을 보여주는 지역 지도](./media/network-monitoring-overview/expressroute-topology-map.png) 

자세한 내용은 다음 문서를 참조하세요.

* [ExpressRoute에 대한 네트워크 성능 모니터 구성](../expressroute/how-to-npm.md)
* [블로그 게시물](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>서비스 연결 모니터

서비스 연결 모니터링을 사용하면 이제 애플리케이션의 연결을 테스트하고, 온-프레미스, 통신 회사 네트워크 및 클라우드/개인 데이터 센터에서 성능 병목 상태를 검색할 수 있습니다.

* 애플리케이션에 대한 종단 간 네트워크 연결 모니터링
* 애플리케이션 전달을 네트워크 성능과 연결, 사용자와 애플리케이션 간의 경로에 따라 성능 저하의 정확한 위치를 감지
* 전 세계 여러 사용자 위치에서 애플리케이션 연결 테스트
* 비즈니스 및 SaaS 애플리케이션에 대한 네트워크 대기 시간 및 패킷 손실 파악
* 잘못된 애플리케이션 성능을 일으킬 수 있는 네트워크상 핫 스폿 확인
* Microsoft Office 365, Dynamics 365, Skype for Business 및 기타 Microsoft 서비스를 위한 기본 테스트를 사용하여 Office 365 애플리케이션에 대한 연결 가능성 모니터링

자세한 내용은 다음 문서를 참조하세요.

* [서비스 엔드포인트를 모니터링하기 위한 네트워크 성능 모니터 구성](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [블로그 게시물](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>트래픽 분석
트래픽 분석은 클라우드 네트워크에서 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. NSG 흐름 로그는 다음에 대한 자세한 정보를 제공하도록 분석됩니다.

* Azure와 인터넷, 공용 클라우드 지역, VNET 및 서브넷 사이의 네트워크를 통한 트래픽 흐름
* 스니퍼나 전용 흐름 수집기가 필요 없는 네트워크상의 애플리케이션 및 프로토콜
* 상위 토커, 대화량이 많은 애플리케이션, 클라우드의 VM 대화, 트래픽 핫스폿
* VNET, 중요한 비즈니스 서비스와 애플리케이션 간의 상호 관계 트래픽의 원본 및 대상
* 보안 – 악성 트래픽, 인터넷에 열려 있는 포트, 인터넷 액세스를 시도하는 애플리케이션 또는 VM…
* 용량 사용률 - VPN 게이트웨이 및 기타 서비스의 사용률 추세를 모니터링하여 과도한 프로비전이나 미달 사용 문제를 해결하는 데 도움이 됩니다.

트래픽 분석은 조직의 네트워크 활동을 감사하고, 애플리케이션 및 데이터를 보호하고, 워크로드 성능을 최적화하고, 준수 상태를 유지하는 데 도움이 되는 실행 가능한 정보를 제공합니다.

![전체 지역의 트래픽을 보여주는 지역 지도](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

관련 링크:
* [블로그 게시물](https://aka.ms/trafficanalytics), [설명서](https://aka.ms/trafficanalyticsdocs), [FAQ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS 분석
DNS 관리자 용으로 구축된 이 솔루션은 DNS 로그를 수집, 분석 및 상호 연관시켜서 보안, 운영 및 성능 관련 자세한 정보를 제공합니다.  일부 기능은 다음과 같습니다.

* 악성 도메인을 확인하려는 클라이언트 식별
* 부실 리소스 레코드 식별
* 자주 쿼리되는 도메인 이름 및 Talkative DNS 클라이언트에 대한 가시성
* DNS 서버의 요청 부하에 대한 가시성
* 동적 DNS 등록 오류에 대한 모니터링

![DNS 분석 대시보드](./media/network-monitoring-overview/dns-analytics-overview.png) 

관련 링크:
* [블로그 게시물](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [설명서](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>기타

* [새로운 가격 책정](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
