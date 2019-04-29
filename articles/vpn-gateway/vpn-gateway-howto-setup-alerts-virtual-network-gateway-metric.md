---
title: Azure VPN Gateway 메트릭에 대 한 경고를 설정 하는 방법
description: VPN Gateway 메트릭에 대해 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769468"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway 메트릭에 대 한 경고 설정

이 문서에서는 VPN Gateway 메트릭에 대해 경고를 설정 합니다. Azure monitor는 Azure 리소스에 대 한 경고를 설정 하는 기능을 제공 합니다. "VPN" 형식의 가상 네트워크 게이트웨이에 대 한 경고를 설정할 수 있습니다.


|**메트릭**   | **단위** | **세분성** | **설명** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 바이트/초  | 5분| 게이트웨이에서 사이트 간 연결을 모두의 결합 된 대역폭 사용률을 평균입니다.     |
|**P2SBandwidth**| 바이트/초  | 1분  | 결합 된 대역폭 사용률 게이트웨이에서 모든 지점-사이트 간 연결의 평균입니다.    |
|**P2SConnectionCount**| 카운트  | 1분  | 게이트웨이에서 수의 P2S 연결 합니다.   |
|**TunnelAverageBandwidth** | 바이트/초    | 5분  | 게이트웨이에서 터널의 평균 대역폭 사용률입니다. |
|**TunnelEgressBytes** | 바이트 | 5분 | 게이트웨이에서 터널에서 나가는 트래픽을 합니다.   |
|**TunnelEgressPackets** | 카운트 | 5분 | 게이트웨이에서 터널에서 나가는 패킷의 수입니다.   |
|**TunnelEgressPacketDropTSMismatch** | 카운트 | 5분 | 나가는 패킷 수입니다. TS 불일치를 야기 하는 터널에서 삭제 합니다. |
|**TunnelIngressBytes** | 바이트 | 5분 | 게이트웨이에서 터널에서 들어오는 트래픽을 합니다.   |
|**TunnelIngressPackets** | 카운트 | 5분 | 게이트웨이에서 터널에서 들어오는 패킷의 수입니다.   |
|**TunnelIngressPacketDropTSMismatch** | 카운트 | 5분 | TS 불일치를 야기 하는 터널에서 삭제 하는 들어오는 패킷의 수입니다. |


## <a name="setup"></a>포털을 사용 하 여 메트릭을 기반으로 하는 Azure Monitor 경고 설정

아래 예제 단계 경고에 대 한 게이트웨이 만듭니다. <br>

**메트릭:** 터널의 평균 대역폭 <br>
**조건:** 대역폭 > 10 바이트/초 <br>
**Window:** 5분 <br>
**경고 작업:** Email <br>



1. 가상 네트워크 게이트웨이 리소스를 이동 및 모니터링 탭에서 "경고"를 선택 후 새 경고 규칙을 만들 하거나 기존 경고 규칙 편집.

![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "만들기")

2. 리소스와 VPN gateway를 선택 합니다.

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. 메트릭 경고에 대 한 구성 선택 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "선택")
4. 신호 논리를 구성 합니다. 신호 논리 구성 요소 세 가지가 있습니다.

    a. 차원: 메트릭 차원에 있는 경우에 해당 차원의 데이터 경고에만 계산 되도록 특정 차원 값 선택할 수 있습니다. 이 선택적입니다.<br>
    b. 조건: 작업 메트릭 값을 평가 하는입니다.<br>
    다. 시간: 메트릭 데이터의 세분성 및 경고를 평가 하는 기간을 지정 합니다.<br>

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 구성된 된 규칙을 보려면 "경고 규칙 관리"를 클릭 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "선택")

## <a name="next-steps"></a>다음 단계

터널 진단 로그에 경고를 구성 하려면 [VPN 게이트웨이 진단 로그에 경고 설정 방법](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)합니다.
