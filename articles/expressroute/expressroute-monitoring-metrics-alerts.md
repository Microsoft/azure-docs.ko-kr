---
title: 'Azure Express 경로: 모니터링, 메트릭 및 경고'
description: Azure의 모든 메트릭, 경고, 진단 로그에 대 한 한 가지 stop 상점을 Azure Monitor 사용 하 여 Azure Express 경로 모니터링, 메트릭 및 경고에 대해 알아봅니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: cherylmc
ms.openlocfilehash: 6f3bd5392457f05caa8704e7e1de5b4155b28794
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871350"
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
|ARP 가용성|가용성|<ui><li>피어 (기본/보조 Express 경로 라우터)</ui></li><ui><li> 피어 링 유형 (비공개/공용/Microsoft)</ui></li>|ExpressRoute|
|Bgp 가용성|가용성|<ui><li> 피어 (기본/보조 Express 경로 라우터)</ui></li><ui><li> 피어 링 유형</ui></li>|ExpressRoute|
|BitsInPerSecond|트래픽|<ui><li> 피어 링 유형 (Express 경로)</ui></li><ui><li>링크 (Express 경로 직접)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|트래픽| <ui><li>피어 링 유형 (Express 경로)</ui></li><ui><li> 링크 (Express 경로 직접) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|CPU 사용률|성능| <ui><li>인스턴스</ui></li>|Express 경로 Virtual Network 게이트웨이|
|초당 패킷 수|성능| <ui><li>인스턴스</ui></li>|Express 경로 Virtual Network 게이트웨이|
|GlobalReachBitsInPerSecond|트래픽|<ui><li>피어 링 회로 Skey (서비스 키)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|트래픽|<ui><li>피어 링 회로 Skey (서비스 키)</ui></li>|Global Reach|
|AdminState|실제 연결|링크|ExpressRoute Direct|
|LineProtocol|실제 연결|링크|ExpressRoute Direct|
|RxLightLevel|실제 연결|<ui><li>링크나</ui></li><ui><li>차선</ui></li>|ExpressRoute Direct|
|TxLightLevel|실제 연결|<ui><li>링크나</ui></li><ui><li>차선</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* 및 *GlobalGlobalReachBitsOutPerSecond* 를 사용 하는 것은 하나 이상의 Global Reach 연결이 설정 된 경우에만 표시 됩니다.
>

## <a name="circuits-metrics"></a>회로 메트릭

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>모든 피어 링에서의 비트 및 외부 메트릭

지정 된 Express 경로 회로의 모든 피어 링에서 메트릭을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="회로 메트릭":::

### <a name="bits-in-and-out---metrics-per-peering"></a>비트 and Out-피어 링 당 메트릭

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="피어 링 별 메트릭":::

### <a name="bgp-availability---split-by-peer"></a>BGP 가용성-피어로 분할  

피어 링 및 피어 (기본 및 보조 Express 경로 라우터)에서 BGP의 실시간 가용성을 확인할 수 있습니다. 이 대시보드는 개인 피어 링에 대 한 기본 BGP 세션과 개인 피어 링에 대 한 두 번째 BGP 세션을 보여 줍니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="피어 당 BGP 가용성":::

### <a name="arp-availability---split-by-peering"></a>ARP 가용성-피어 링으로 분할  

피어 링 및 피어 (기본 및 보조 Express 경로 라우터)에서 [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) 의 실시간 가용성을 확인할 수 있습니다. 이 대시보드는 두 피어에서 개인 피어 링 ARP 세션을 보여 주지만 피어 링에서 Microsoft 피어 링을 완료 합니다. 두 피어에서 기본 집계 (평균)를 활용 했습니다.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="피어 당 ARP 가용성":::

## <a name="expressroute-direct-metrics"></a>Express 경로 직접 메트릭

### <a name="admin-state---split-by-link"></a>상태 관리-링크로 분할

Express 경로 직접 포트 쌍의 각 링크에 대 한 관리자 상태를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER 직접 관리 상태":::

### <a name="bits-in-per-second---split-by-link"></a>초당 비트 수-링크로 분할

Express 경로 직접 포트 쌍의 두 링크에서 초당 비트 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="초당 ER Direct 비트 수":::

### <a name="bits-out-per-second---split-by-link"></a>초 당 비트 아웃-링크로 분할

Express 경로 직접 포트 쌍의 두 링크에서 초당 비트를 볼 수도 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 비트/초":::

### <a name="line-protocol---split-by-link"></a>줄 프로토콜-링크로 분할

Express 경로 직접 포트 쌍의 각 링크에서 줄 프로토콜을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct line 프로토콜":::

### <a name="rx-light-level---split-by-link"></a>Rx 라이트 수준-링크로 분할

각 포트에 대해 Rx 라이트 수준 (Express 경로 다이렉트 포트가 **수신**하는 광원 수준)을 볼 수 있습니다. 정상 Rx light 수준은 일반적으로-10에서 0 dBm 사이입니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light 수준":::

### <a name="tx-light-level---split-by-link"></a>Tx 라이트 수준-링크로 분할

각 포트에 대해 Tx light 수준 (Express 경로 직접 포트가 **전송**하는 광원 수준)을 볼 수 있습니다. 정상적인 Tx light 수준은 일반적으로-10에서 0 dBm 사이의 범위에 속합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light 수준":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Express 경로 Virtual Network 게이트웨이 메트릭

### <a name="cpu-utilization---split-instance"></a>CPU 사용률-분할 인스턴스

게이트웨이 인스턴스의 CPU 사용률을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 분할":::

### <a name="packets-per-second---split-by-instance"></a>초당 패킷-인스턴스당 분할

게이트웨이를 트래버스하는 초당 패킷 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="초당 패킷-분할":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 게이트웨이 연결(비트/초)

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="게이트웨이 연결":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성 하려면 **Azure Monitor**으로 이동한 다음 **경고**를 선택 합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="경고":::
2. **+대상 선택**을 클릭하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="대상":::
3. 경고 세부 정보를 정의합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="작업 그룹":::
4. 작업 그룹을 정의하고 추가합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="작업 그룹 추가":::

## <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="각 피어 링":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건**에서 신호 유형으로 **활동 로그**를 선택하고 신호를 선택할 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="활동 로그":::
  
## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
* [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
* [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
