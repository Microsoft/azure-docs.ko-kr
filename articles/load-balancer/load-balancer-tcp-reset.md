---
title: Azure에서 유휴 시 Load Balancer TCP 재설정
titleSuffix: Azure Load Balancer
description: 이 문서에서는 유휴 시간 제한에 양방향 TCP RST 패킷을 사용 하는 Azure Load Balancer에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: eac7dc3b7188131685ef630c0dc01d248e1d6a6a
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134790"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>유휴 상태에서 TCP를 다시 설정 하는 Load Balancer

[표준 Load Balancer](load-balancer-standard-overview.md)를 지정된 규칙에 대해 TCP Reset on Idle을 사용하도록 설정하여 시나리오용으로 예측 가능성이 더 높은 애플리케이션 동작을 만들 수 있습니다. Load Balancer의 기본 동작은 흐름의 유휴 시간이 초과되면 흐름을 자동으로 끊는 것입니다.  이 기능을 사용하도록 설정하면 Load Balancer가 유휴 시간 초과 시 양방향 TCP Reset(TCP RST 패킷)을 전송합니다.  이 패킷은 연결 시간이 초과되어 더 이상 연결을 사용할 수 없음을 애플리케이션 엔드포인트에 알립니다.  엔드포인트는 필요한 경우 새 연결을 즉시 설정할 수 있습니다.

![Load Balancer TCP 재설정](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
이 기본 동작을 변경하고 인바운드 NAT 규칙, 부하 분산 규칙 및 [아웃바운드 규칙](https://aka.ms/lboutboundrules)에서 유휴 시간 초과 시 TCP 재설정 보내기를 사용하도록 설정합니다.  규칙에서 사용하도록 설정하면 Load Balancer는 모든 일치 흐름의 유휴 시간 초과 시 클라이언트 및 서버 엔드포인트 둘 다에 양방향 TCP 재설정(TCP RST 패킷)을 보냅니다.

TCP RST 패킷을 수신하는 엔드포인트는 즉시 해당 소켓을 닫습니다. 그러면 연결 해제가 발생했으며 동일한 TCP 연결의 이후 통신은 실패할 것이라는 알림이 곧바로 엔드포인트에 제공됩니다.  애플리케이션은 소켓이 닫히면 연결을 제거할 수 있으며 필요에 따라 TCP 연결 시간이 초과될 때까지 기다리지 않고 연결을 다시 설정할 수 있습니다.

이렇게 하면 많은 시나리오에서 흐름의 유휴 시간 초과를 새로 고치기 위해 TCP(또는 애플리케이션 계층) 연결 유지를 보내는 것을 줄일 수 있습니다. 

유휴 기간이 구성에서 허용하는 기간을 초과하거나 TCP 재설정을 사용하도록 설정하면 애플리케이션이 원치 않는 동작을 보여 주는 경우 계속 TCP 연결 유지(또는 애플리케이션 계층 연결 유지)를 사용하여 TCP 연결의 활동성을 모니터링해야 할 수도 있습니다.  또한 연결이 경로의 특정 위치, 특히 애플리케이션 계층 연결 유지에 프록시 설정된 경우에도 연결 유지가 유용할 수 있습니다.  

종단 간 시나리오 전체를 신중하게 검토하여 TCP 재설정을 사용하도록 설정하고 유휴 시간 초과를 조정하는 것이 유리한지, 애플리케이션이 원하는 대로 동작하게 하려면 추가 조치가 필요한지 여부를 결정합니다.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>유휴 시간 초과 시 TCP 재설정 사용

API 버전 2018-07-01을 사용하면 개별 규칙을 기반으로 유휴 시간 초과 시 양방향 TCP 재설정 보내기를 사용하도록 설정할 수 있습니다.

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> 지역 가용성

모든 지역에서 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

- Tcp RST 설정 된 상태에서 TCP 연결 중에만 전송 됩니다.

## <a name="next-steps"></a>다음 단계

- [표준 Load Balancer](load-balancer-standard-overview.md)에 대해 알아봅니다.
- [아웃바운드 규칙](load-balancer-outbound-rules-overview.md)에 대해 알아봅니다.
