---
title: 'Azure Express 경로: 모니터링, 메트릭 및 경고'
description: 이 페이지에서는 ExpressRoute 모니터링에 대한 정보를 제공합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436907"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 모니터링, 메트릭 및 경고

이 문서는 Azure Monitor를 사용하여 ExpressRoute 모니터링, 메트릭 및 경고를 이해하는 데 도움이 됩니다. Azure Monitor에서는 Azure 전체의 모든 메트릭, 경고, 진단 로그를 확인할 수 있습니다.
 
>[!NOTE]
>**클래식 메트릭**을 사용하면 좋지 않습니다.
>

## <a name="expressroute-metrics"></a>Express 경로 메트릭

**메트릭을**보려면 *Azure Monitor* 페이지로 이동 하 여 *메트릭*을 클릭 합니다. **Express** 경로 메트릭을 보려면 리소스 유형 *express 경로 회로*를 기준으로 필터링 합니다. **Global Reach** 메트릭을 보려면 리소스 유형 *express 경로 회로* 를 기준으로 필터링 하 고 Global Reach 사용 하도록 설정 된 express 경로 회로 리소스를 선택 합니다. Express 경로 **직접** 메트릭을 보려면 *express 경로 포트*를 기준으로 리소스 종류를 필터링 합니다. 

메트릭을 선택 하면 기본 집계가 적용 됩니다. 필요에 따라 분할을 적용할 수 있습니다 .이 경우 다른 차원의 메트릭이 표시 됩니다.

### <a name="available-metrics"></a>사용 가능한 메트릭
|**메트릭**|**범주**|**개 차원**|**기능**|
| --- | --- | --- | --- |
|ARP 가용성|가용성|<ui><li>피어 (기본/보조 Express 경로 라우터)</ui></li><ui><li> 피어 링 유형 (개인/공용/Microsoft)</ui></li>|Express Route|
|Bgp 가용성|가용성|<ui><li> 피어 (기본/보조 Express 경로 라우터)</ui></li><ui><li> 피어 링 유형</ui></li>|Express Route|
|BitsInPerSecond|교통|<ui><li> 피어 링 유형 (Express 경로)</ui></li><ui><li>Link (Express 경로 직접)</ui></li>| <li> Express Route</li><li>ExpressRoute Direct|
|BitsOutPerSecond|교통| <ui><li>피어 링 유형 (Express 경로)</ui></li><ui><li> 링크 (Express 경로 직접) | <ui><li>Express 경로<ui><li>Express 경로 직접</ui></li> |
|GlobalReachBitsInPerSecond|교통|<ui><li>피어 링 회로 Skey (서비스 키)</ui></li>|글로벌 접근|
|GlobalReachBitsOutPerSecond|교통|<ui><li>피어 링 회로 Skey (서비스 키)</ui></li>|글로벌 접근|
|AdminState|실제 연결|링크|ExpressRoute Direct|
|LineProtocol|실제 연결|링크|ExpressRoute Direct|
|RxLightLevel|실제 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
|TxLightLevel|실제 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* 및 *GlobalGlobalReachBitsOutPerSecond* 를 사용 하는 것은 하나 이상의 Global Reach 연결이 설정 된 경우에만 표시 됩니다.
>

## <a name="circuits-metrics"></a>회로 메트릭

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>모든 피어 링에서의 비트 및 외부 메트릭

지정 된 Express 경로 회로의 모든 피어 링에서 메트릭을 볼 수 있습니다.

![회로 메트릭](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>비트 and Out-피어 링 당 메트릭

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

![피어링당 메트릭](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 가용성-피어로 분할  

피어 링 및 피어 (기본 및 보조 Express 경로 라우터)에서 BGP의 실시간 가용성을 확인할 수 있습니다. 이 대시보드는 개인 피어 링에 대한 기본 BGP 세션과 개인 피어 링에 대한 두 번째 BGP 세션을 보여 줍니다. 

![피어 당 BGP 가용성](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 가용성-피어 링으로 분할  

피어 링 및 피어 (기본 및 보조 Express 경로 라우터)에서 [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) 의 실시간 가용성을 확인할 수 있습니다. 이 대시보드는 두 피어에서 개인 피어 링 ARP 세션을 보여 주지만 피어 링에서 Microsoft 피어 링을 완료 합니다. 두 피어에서 기본 집계 (평균)를 활용 했습니다.  

![피어 당 ARP 가용성](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Express 경로 직접 메트릭

### <a name="admin-state---split-by-link"></a>상태 관리-링크로 분할
Express 경로 직접 포트 쌍의 각 링크에 대한 관리자 상태를 볼 수 있습니다.

![er 직접 관리 상태](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>초당 비트 수-링크로 분할
Express 경로 직접 포트 쌍의 두 링크에서 초당 비트 수를 볼 수 있습니다. 

![초당 er direct 비트 수](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>초 당 비트 아웃-링크로 분할
Express 경로 직접 포트 쌍의 두 링크에서 초당 비트를 볼 수도 있습니다. 

![er direct 비트/초](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>줄 프로토콜-링크로 분할
Express 경로 직접 포트 쌍의 각 링크에서 줄 프로토콜을 볼 수 있습니다.

![er direct line 프로토콜](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx 라이트 수준-링크로 분할
각 포트에 대해 Rx 라이트 수준 (Express 경로 다이렉트 포트가 **수신**하는 광원 수준)을 볼 수 있습니다. 정상 Rx light 수준은 일반적으로-10에서 0 dBm 사이입니다.

![er direct line Rx Light 수준](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 라이트 수준-링크로 분할
각 포트에 대해 Tx light 수준 (Express 경로 직접 포트가 **전송**하는 광원 수준)을 볼 수 있습니다. 정상적인 Tx light 수준은 일반적으로-10에서 0 dBm 사이의 범위에 속합니다.

![er direct line Rx Light 수준](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 게이트웨이 연결(비트/초)

![게이트웨이 연결](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성하려면 **Azure Monitor**로 이동하여 **경고**를 클릭합니다.

   ![경고](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. **+대상 선택**을 클릭하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   ![대상]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. 경고 세부 정보를 정의합니다.

   ![작업 그룹](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. 작업 그룹을 정의하고 추가합니다.

   ![작업 그룹 추가](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

 ![대상](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건**에서 신호 유형으로 **활동 로그**를 선택하고 신호를 선택할 수 있습니다.

  ![다른 대상](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
  * [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
  * [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
