---
title: 'Azure Express 경로: 구성 BFD'
description: 이 문서는 ExpressRoute 회로의 프라이빗 피어링을 통해 BFD(Bidirectional Forwarding Detection)를 구성하는 방법에 대한 지침을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080790"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute를 통해 BFD 구성

Express 경로는 개인 및 Microsoft 피어 링을 통해 BFD (양방향 전달 검색)를 지원 합니다. Express 경로를 통해 BFD를 사용 하도록 설정 하면 MSEE (Microsoft Enterprise edge) 장치와 Express 경로 회로 (CE/PE)를 종료 하는 라우터에 대한 오류 감지를 신속 하 게 연결할 수 있습니다. Customer Edge 라우팅 디바이스 또는 Partner Edge 라우팅 디바이스를 통해 ExpressRoute를 종료할 수 있습니다(관리형 Layer 3 연결 서비스에 동의한 경우). 이 문서에서는 BFD의 필요성과 ExpressRoute를 통해 BFD를 활성화하는 방법을 안내합니다.

## <a name="need-for-bfd"></a>BFD의 필요성

다음 다이어그램에서는 ExpressRoute 회로를 통해 BFD를 활성화할 경우의 장점을 보여줍니다. [ ![1]][1]

Layer 2 연결 또는 관리형 Layer 3 연결을 사용하여 ExpressRoute 회로를 활성화할 수 있습니다. 두 경우 모두, ExpressRoute 연결 경로에 하나 이상의 Layer-2 디바이스가 있는 경우 경로의 연결 오류를 감지하는 역할은 상위 BGP에서 수행합니다.

MSEE 디바이스에서 BGP keepalive 및 hold-time은 일반적으로 각각 60초와 180초로 구성됩니다. 따라서 연결 오류 후에 연결 오류를 감지하고 대체 연결로 트래픽을 전환하는 데 최대 3분이 걸립니다.

Customer Edge 피어링 디바이스에서 하위 BGP keepalive 및 hold-time을 구성하여 BGP 타이머를 제어할 수 있습니다. 두 피어링 디바이스 간에 BGP 타이머가 일치하지 않으면 피어 간의 BGP 세션에서 하위 타이머 값을 사용합니다. BGP는 최소 3초까지 설정할 수 있고, hold-time은 10초 단위로 설정할 수 있습니다. 그러나 프로토콜은 프로세스를 많이 사용 하므로 BGP 타이머를 적극적으로 설정 하는 것이 더 좋습니다.

이 시나리오에서는 BFD가 도움이 될 수 있습니다. BFD는 초 단위 미만의 시간 간격으로 오버헤드가 낮은 연결 오류 감지를 지원합니다. 


## <a name="enabling-bfd"></a>BFD 사용

BFD는 기본적으로 MSEE에서 새로 생성된 모든 ExpressRoute 프라이빗 피어링 인터페이스에 구성됩니다. 따라서 BFD를 사용 하도록 설정 하려면 CEs/Pe (기본 및 보조 장치 모두)에서 BFD를 구성 하기만 하면 됩니다. BFD 구성은 2 단계 프로세스입니다. 인터페이스에서 BFD를 구성한 다음 BGP 세션에 연결 해야 합니다.

예제 CE/PE (Cisco IOS XE 사용) 구성은 다음과 같습니다. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>기존의 프라이빗 피어링에 BFD를 활성화하려면 피어링을 다시 설정해야 합니다. [Express 경로 다시 설정][ResetPeering] 을 참조 하세요.
>

## <a name="bfd-timer-negotiation"></a>BFD 타이머 협상

BFD 피어 간의 전송 속도는 둘 중 더 느린 쪽을 따릅니다. MSEE BFD 전송/수신 간격 300밀리초로 설정됩니다. 특정 시나리오에서 간격은 750밀리초의 높은 값으로 설정될 수 있습니다. 더 높은 값을 구성하면 이러한 간격을 강제로 늘릴 수 있지만 간격을 단축할 수는 없습니다.

>[!NOTE]
>지역 중복 Express 경로 회로를 구성 했거나 사이트 간 IPSec VPN 연결을 백업으로 사용 하는 경우 BFD를 사용 하도록 설정 하면 Express 경로 연결 오류로 인해 보다 빠르게 장애 조치를 수행할 수 있습니다. 
>

## <a name="next-steps"></a>다음 단계

자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [Express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "연결 오류 추론 시간을 가속화하는 BFD"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






