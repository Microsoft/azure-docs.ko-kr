---
title: Azure Front Door Service - 라우팅 아키텍처 | Microsoft Docs
description: 이 문서는 Front Door의 아키텍처에 대한 전체적인 관점을 이해하는 데 도움이 됩니다.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736288"
---
# <a name="routing-architecture-overview"></a>라우팅 아키텍처 개요

Azure Front Door Service가 클라이언트 요청을 수신하면 요청에 응답하거나(캐싱이 가능한 경우) 적절한 애플리케이션 백엔드로 전달(역방향 프록시로)합니다.

</br>Azure Front Door로 라우팅하는 경우는 물론 백엔드로 라우팅하는 경우 트래픽을 최적화할 기회가 있습니다.

## <a name = "anycast"></a>트래픽 라우팅(Anycast)을 위해 Front Door 환경 선택

Azure Front Door 환경으로 라우팅하면 DNS(Domain Name System)와 HTTP(Hypertext Transfer Protocol) 트래픽 모두에 대해 [Anycast](https://en.wikipedia.org/wiki/Anycast)가 활용되기 때문에 사용자 트래픽은 네트워크 토폴로지 측면에서 가장 가까운 환경(가장 적은 홉)으로 이동합니다. 이 아키텍처는 일반적으로 최종 사용자의 왕복 시간을 향상시킵니다(분할 TCP의 이점을 극대화 함) Front Door는 자체 환경을 기본 및 대체 "링(ring)"으로 구성합니다.  외부 링에는 사용자에게 더 가까운 환경이 있어서, 대기 시간을 단축합니다.  내부 링에는 문제가 발생할 경우 외부 링 환경에 대한 장애 조치(failover)를 처리할 수 있는 환경이 있습니다. 외부 링은 모든 트래픽에서 선호하는 대상이지만 내부 링은 외부 링의 트래픽 오버플로를 처리하는 데 필요합니다. VIP(가상 인터넷 프로토콜 주소)의 측면에서 프런트 엔드 호스트 또는 Front Door에서 제공하는 도메인에는 내부 링의 환경에서만 발표되는 대체(fallback) VIP뿐만 아니라 내부 및 외부 링의 환경에 의해 발표되는 기본 VIP가 할당됩니다. 

</br>이러한 전반적인 전략을 통해 최종 사용자의 요청이 항상 가장 가까운 Front Door 환경에 도달할 수 있으며, 선호하는 Front Door 환경의 상태가 비정상인 경우에도 트래픽이 다음으로 가장 가까운 환경으로 자동으로 이동할 수 있습니다.

## <a name = "splittcp"></a>Front Door 환경에 연결(분할 TCP)

[분할 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy)는 긴 왕복 시간을 초래하는 연결을 작은 조각으로 분리하여 대기 시간과 TCP 문제를 줄이는 기법입니다.  Front Door 환경을 최종 사용자에 가깝게 배치하고 Front Door 환경 내에서 TCP 연결을 종료함으로써 애플리케이션 백 엔드에 대한 RTT(왕복 시간)가 긴 하나의 TCP 연결이 두 개의 TCP 연결로 분할됩니다. 최종 사용자와 Front Door 환경 사이의 연결이 짧으면 세 번의 긴 왕복 대신 세 번의 짧은 왕복을 통해 연결이 설정되어 대기 시간이 단축됩니다.  Front Door 환경과 백 엔드 간의 긴 연결은 미리 설정되어 여러 최종 사용자 호출에 재사용될 수 있으며 TCP 연결 시간을 다시 절약합니다.  이 효과는 SSL/TLS(전송 계층 보안) 연결을 설정할 때 배가됩니다. 연결을 보호할 왕복이 더 많기 때문입니다.

## <a name="processing-request-to-match-a-routing-rule"></a>라우팅 규칙과 일치하도록 요청 처리
연결을 설정하고 SSL 핸드셰이크를 수행한 후 Front Door 환경에 요청이 도착하면 라우팅 규칙을 매칭하는 것이 첫 번째 단계입니다. 이러한 매칭은 기본적으로 Front Door의 모든 구성에서 어떤 특정 라우팅 규칙을 요청과 매칭할지 결정합니다. 자세한 내용은 Front Door에서 [경로 매칭](front-door-route-matching.md)이 수행되는 방법에 대해 읽어보세요.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>라우팅 규칙에 대해 백 엔드 풀에서 사용 가능한 백 엔드 식별
Front Door에 들어오는 요청을 기반으로 라우팅 규칙이 일치하는 항목이 있고 캐싱이 없는 경우 다음 단계는 일치하는 경로와 연결된 백 엔드 풀의 상태 프로브 상태를 끌어오는 것입니다. 자세한 내용은 Front Door에서 [상태 프로브](front-door-health-probes.md)를 사용하여 백엔드 상태를 모니터링하는 방법에 대해 읽어보세요.

## <a name="forwarding-the-request-to-your-application-backend"></a>애플리케이션 백엔드에 요청 전달
마지막으로 캐싱이 구성되어 있지 않다고 가정하면, 사용자 요청은 [Front Door 라우팅 방법](front-door-routing-methods.md) 구성을 기반으로 "최상의" 백 엔드로 전달됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
