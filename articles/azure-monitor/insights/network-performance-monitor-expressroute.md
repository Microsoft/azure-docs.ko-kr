---
title: Azure Log Analytics의 네트워크 성능 모니터 솔루션 | Microsoft Docs
description: 네트워크 성능 모니터의 ExpressRoute 모니터 기능을 사용하여 Azure ExpressRoute를 통한 지사와 Azure 간의 종단 간 연결 및 성능을 모니터링할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: abshamsft
ms.openlocfilehash: d0819b57307fc037b3be6ab04ed9ec6c8720a618
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110143"
---
# <a name="expressroute-monitor"></a>ExpressRoute 모니터

[네트워크 성능 모니터](network-performance-monitor.md)의 Azure ExpressRoute 모니터 기능을 사용하여 Azure ExpressRoute를 통한 지사와 Azure 간의 종단 간 연결 및 성능을 모니터링할 수 있습니다. 주요 이점은 다음과 같습니다. 

- 구독에 연결된 ExpressRoute 회로의 자동 검색
- ExpressRoute에 대한 회로, 피어링 및 Azure Virtual Network 수준에서 대역폭 사용률, 손실 및 대기 시간 추적
- ExpressRoute 회로의 네트워크 토폴로지 검색

![ExpressRoute 모니터](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>구성 
네트워크 성능 모니터에 대한 구성을 열려면 [네트워크 성능 모니터 솔루션](network-performance-monitor.md)을 열고 **구성**을 선택합니다.

### <a name="configure-network-security-group-rules"></a>네트워크 보안 그룹 규칙 구성 
네트워크 성능 모니터를 통한 모니터링에 사용되는 Azure에 있는 서버의 경우 가상 트랜잭션을 위해 네트워크 성능 모니터에서 사용하는 포트에 TCP 트래픽을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 구성합니다. 기본 포트는 8084입니다. 이 구성을 사용하면 Azure VM에 설치된 Log Analytics 에이전트가 온-프레미스 모니터링 에이전트와 통신할 수 있습니다. 

NSG에 대한 자세한 내용은  [네트워크 보안 그룹](../../virtual-network/manage-network-security-group.md)을 참조하세요. 

>[!NOTE]
> 이 단계를 계속하기 전에 온-프레미스 서버 에이전트와 Azure 서버 에이전트를 설치하고 EnableRules.ps1 PowerShell 스크립트를 실행합니다. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute 피어링 연결 검색 
 
1. **ExpressRoute 피어링** 보기를 선택합니다.
2. **지금 검색** 단추를 선택하여 이 Azure Log Analytics 작업 영역에 연결된 Azure 구독의 가상 네트워크에 연결되어 있는 모든 ExpressRoute 개인 피어링을 검색합니다.

    >[!NOTE]
    > 이 솔루션은 현재, ExpressRoute 개인 피어링만 검색합니다. 

    >[!NOTE]
    > 이 Log Analytics 작업 영역에 연결된 구독 관련 가상 네트워크에 연결되어 있는 개인 피어링만 검색됩니다. ExpressRoute가 이 작업 영역에 연결된 구독 외부의 가상 네트워크에 연결된 경우 해당 구독에 Log Analytics 작업 영역을 만듭니다. 그런 다음, 네트워크 성능 모니터를 사용하여 해당 피어링을 모니터링합니다. 

    ![ExpressRoute 모니터 구성](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   검색이 완료된 후에 검색된 개인 피어링 연결이 표에 나열됩니다. 이러한 피어링에 대한 모니터링은 처음에는 사용할 수 없는 상태입니다. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>ExpressRoute 피어링 연결 모니터링 사용 

1. 모니터링하려는 개인 피어링 연결을 선택합니다.
2. 오른쪽 창에서 **이 피어링 모니터링** 확인란을 선택합니다. 
3. 이 연결에 대한 상태 이벤트를 만들려는 경우 **이 피어링에 상태 모니터링 사용**을 선택합니다. 
4. 모니터링 조건을 선택합니다. 임계값을 입력하여 상태 이벤트 생성에 대한 사용자 지정 임계값을 설정할 수 있습니다. 조건 값이 피어링 연결의 선택된 임계값을 초과할 경우 상태 이벤트가 생성됩니다. 
5. **에이전트 추가**를 선택하여 이 피어링 연결을 모니터링하는 데 사용할 모니터링 에이전트를 선택합니다. 에이전트를 연결의 양쪽 끝에 추가했는지 확인합니다. 이 피어링에 연결된 가상 네트워크에 하나 이상의 에이전트가 필요합니다. 이 피어링에 연결된 하나 이상의 온-프레미스 에이전트가 필요합니다. 
6. **저장**을 선택하여 구성을 저장합니다. 

   ![ExpressRoute 모니터링 구성](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


규칙을 사용하도록 설정하고 값 및 에이전트를 선택한 후에 값을 채우고 **ExpressRoute 모니터링** 타일을 표시하는 데 30-60분을 기다립니다. 모니터링 타일이 표시되면 이제 ExpressRoute 회로 및 연결 리소스가 네트워크 성능 모니터에서 모니터링됩니다. 

>[!NOTE]
> 이 기능은 새로운 쿼리 언어로 업그레이드된 작업 영역에서 안정적으로 작동합니다.

## <a name="walkthrough"></a>연습 

네트워크 성능 모니터 대시보드는 ExpressRoute 회로 및 피어링 연결의 상태를 개략적으로 보여줍니다. 

![네트워크 성능 모니터 대시보드](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>회로 목록 

모니터링되는 모든 ExpressRoute 회로 목록을 보려면 ExpressRoute 회로 타일을 선택합니다. 회로를 선택하고 해당 상태, 패킷 손실, 대역폭 사용률 및 대기 시간에 대한 추세 차트를 볼 수 있습니다. 차트는 대화형으로 작동합니다. 차트를 그리기 위한 사용자 지정 기간을 선택할 수 있습니다. 차트의 영역 위로 마우스를 끌어 확대하고 세분화된 데이터 요소를 확인합니다. 

![ExpressRoute 회로 목록](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>손실, 대기 시간 및 처리량 추세 

대역폭 사용률, 대기 시간, 손실 차트는 대화형으로 작동합니다. 마우스 컨트롤을 사용하여 이러한 차트의 섹션을 확대할 수 있습니다. 또한 다른 간격에서 대역폭, 대기 시간 및 손실 데이터를 확인할 수 있습니다. **동작** 단추의 아래 왼쪽 위에서  **날짜/시간**을 선택합니다. 

![ExpressRoute 대기 시간](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>피어링 목록 

개인 피어링을 통한 가상 네트워크의 모든 연결 목록을 불러오려면 대시보드의 **개인 피어링** 타일을 선택합니다. 여기서 가상 네트워크 연결을 선택하고 해당 상태, 패킷 손실, 대역폭 사용률 및 대기 시간에 대한 추세 차트를 볼 수 있습니다. 

![ExpressRoute 피어링](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>회로 토폴로지 

회로 토폴로지를 보려면 **토폴로지** 타일을 선택합니다. 이 작업을 통해 선택된 회로 또는 피어링의 토폴로지 보기로 이동됩니다. 토폴로지 다이어그램은 네트워크에 있는 각 세그먼트에 대한 대기 시간을 제공하며 각 계층 3 홉은 다이어그램의 노드로 표시됩니다. 홉을 선택하면 홉에 대한 자세한 내용이 표시됩니다. 온-프레미스 홉을 포함하도록 표시 유형의 수준을 증가시키려면 **필터** 아래의 슬라이더 막대를 이동합니다. 슬라이더 막대를 왼쪽 또는 오른쪽으로 이동하면 토폴로지 그래프의 홉 수가 증가하거나 감소합니다. 각 세그먼트 간의 대기 시간이 표시되므로 네트워크에서 대기 시간이 긴 세그먼트를 더 빠르게 구분할 수 있습니다.

![ExpressRoute 토폴로지](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>회로의 자세한 토폴로지 보기 

이 보기에서는 가상 네트워크 연결을 표시합니다. 

![ExpressRoute 가상 네트워크 연결](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>진단 

네트워크 성능 모니터를 사용하여 여러 회로 연결 문제를 진단할 수 있습니다. 확인할 수 있는 문제 중 일부는 다음과 같습니다.

알림 코드를 확인하고 **LogAnalytics**를 통해 알림 코드에 대한 경고를 설정할 수 있습니다. **NPM 진단** 페이지에서 트리거된 모든 진단 메시지에 대한 설명을 확인할 수 있습니다.

| 알림 코드(로그) | 설명 |
| --- | --- |
| 5501 | ExpressRoute 회로의 보조 연결을 통해 트래버스할 수 없음 |
| 5502 | ExpressRoute 회로의 기본 연결을 통해 트래버스할 수 없음 |
| 5503 | 작업 영역에 연결된 구독에 대한 회로를 찾을 수 없음 | 
| 5508 | 트래픽이 경로에 대한 모든 회로를 통과하는지 확인할 수 없음 |
| 5510 | 트래픽이 의도된 회로를 통과하지 않음 | 
| 5511 | 트래픽이 의도된 가상 네트워크를 통과하지 않음 | 

**회로가 작동 중단됨.** 네트워크 성능 모니터는 온-프레미스 리소스와 Azure 가상 네트워크 간 연결이 끊어지는 즉시 알림을 표시합니다. 이 알림을 통해 사용자 에스컬레이션이 수신되기 전에 사전 조치를 취하여 작동 중단 시간을 줄일 수 있습니다.

![ExpressRoute 회로 중단](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**트래픽이 의도한 회로를 통해 전송되지 않음.** 네트워크 성능 모니터에서는 트래픽이 의도된 ExpressRoute 회로를 통해 흐르지 않는 경우 알림을 표시합니다. 회로가 중단되고 트래픽이 백업 경로를 통해 흐르는 경우 이러한 문제가 발생할 수 있습니다. 라우팅 문제가 있는 경우에도 발생할 수 있습니다. 이 정보는 사전 예방적으로 라우팅 정책의 모든 구성 문제를 관리하고 가장 최적의 안전한 경로가 사용되도록 하는 데 도움이 됩니다. 

 

**트래픽이 기본 회로를 통해 전송되지 않음.** 네트워크 성능 모니터는 트래픽이 보조 ExpressRoute 회로를 통해 전송되는 경우 알림을 표시합니다. 이 경우에 연결 문제가 발생하지 않더라도 기본 회로의 문제를 사전에 해결하는 것이 좋습니다. 

 
![ExpressRoute 트래픽 흐름](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**최대 사용량으로 인한 성능 저하.** 대역폭 사용량 추세와 대기 시간 간의 상관 관계를 파악하여 Azure 워크로드 성능 저하가 대역폭 사용량 최대 증가로 인한 것인지 여부를 식별할 수 있습니다. 그런 다음, 작업을 적절하게 수행할 수 있습니다.

![ExpressRoute 대역폭 사용률](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>다음 단계
자세한 네트워크 성능 데이터 레코드를 보려면 [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 수행합니다.
