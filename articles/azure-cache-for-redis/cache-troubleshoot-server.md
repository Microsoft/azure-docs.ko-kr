---
title: Redis 서버 쪽 문제에 대한 Azure 캐시 문제 해결
description: 메모리 부족, 높은 CPU, 장기 실행 명령 또는 대역폭 제한과 같은 Redis 용 Azure Cache를 사용 하 여 일반적인 서버 쪽 문제를 해결 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433346"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Redis 서버 쪽 문제에 대한 Azure 캐시 문제 해결

이 섹션에서는 Redis에 대한 Azure 캐시 또는이를 호스팅하는 가상 컴퓨터에 대한 조건으로 인해 발생 하는 문제를 해결 하는 방법을 설명 합니다.

- [Redis 서버의 메모리 부족](#memory-pressure-on-redis-server)
- [높은 CPU 사용량 또는 서버 부하](#high-cpu-usage-or-server-load)
- [장기 실행 명령](#long-running-commands)
- [서버 쪽 대역폭 제한](#server-side-bandwidth-limitation)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="memory-pressure-on-redis-server"></a>Redis 서버의 메모리 부족

서버 쪽의 메모리 부족은 요청 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리가 부족 해지면 시스템에서 데이터를 디스크에 페이징 할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다. 이 메모리 부족의 몇 가지 가능한 원인은 다음과 같습니다.

- 캐시는 최대 용량에 가까운 데이터로 채워집니다.
- Redis가 높은 메모리 조각화를 표시 합니다. 이러한 조각화는 Redis가 작은 개체에 최적화 되어 있으므로 큰 개체를 저장 하 여 발생 하는 경우가 가장 많습니다.

Redis는이 문제를 식별 하는 데 도움이 될 수 있는 [INFO](https://redis.io/commands/info) 명령을 통해 "used_memory" 및 "used_memory_rss"의 두 가지 통계를 노출 합니다. 포털을 사용 하 여 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 수 있습니다.

메모리 사용을 정상 상태로 유지 하기 위해 수행할 수 있는 몇 가지 변경 사항이 있습니다.

- [메모리 정책을 구성](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 하고 키에 만료 시간을 설정합니다. 조각화가 있는 경우이 정책은 충분 하지 않을 수 있습니다.
- [maxmemory-reserved 값을 구성합니다](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) .
- 크게 캐시된 개체를 더 작은 관련 개체로 분할합니다.
- 사용 중인 메모리와 같은 메트릭에 대한 [경고를 만들어](cache-how-to-monitor.md#alerts) 잠재적 영향에 대해 조기에 알려 줍니다.
- 메모리 용량이 더 큰 캐시 크기로 [확장](cache-how-to-scale.md) 합니다.

## <a name="high-cpu-usage-or-server-load"></a>높은 CPU 사용량 또는 서버 부하

서버 부하 또는 CPU 사용률이 높으면 서버가 요청을 적시에 처리할 수 없음을 의미 합니다. 서버 응답 속도가 느리고 요청 속도를 유지할 수 없습니다.

CPU 또는 서버 부하와 같은 [메트릭을 모니터링](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 합니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다.

높은 서버 부하를 완화 하기 위해 수행할 수 있는 몇 가지 변경 사항은 다음과 같습니다.

- 낮은 메모리 압력으로 인해 아래에 명시 된 [장기 실행 명령](#long-running-commands) 또는 페이지 오류와 같은 CPU 급증 원인을 조사 하십시오.
- CPU 또는 서버 부하와 같은 메트릭에 대한 [경고를 만들어](cache-how-to-monitor.md#alerts) 잠재적 영향을 조기에 알립니다.
- 더 많은 CPU 용량을 포함 하는 더 큰 캐시 크기로 [확장](cache-how-to-scale.md) 합니다.

## <a name="long-running-commands"></a>장기 실행 명령

일부 Redis 명령은 다른 명령을 실행 하는 데 비용이 많이 듭니다. [Redis commands 설명서](https://redis.io/commands) 는 각 명령의 시간 복잡성을 보여 줍니다. Redis 명령 처리는 단일 스레드 이므로 실행 시간을 사용 하는 명령을 실행 하면 그 다음에 오는 다른 모든 항목은 차단 됩니다. Redis 서버에 발급 하는 명령을 검토 하 여 성능 영향을 이해 해야 합니다. 예를 들어 [키](https://redis.io/commands/keys) 명령은 O (N) 작업 이라고 알지 못해도 자주 사용 됩니다. [검색](https://redis.io/commands/scan) 을 사용 하 여 CPU 스파이크를 줄이는 키를 방지할 수 있습니다.

[SLOWLOG](https://redis.io/commands/slowlog) 명령을 사용 하 여 서버에 대해 실행 되는 비용이 많이 드는 명령을 측정할 수 있습니다.

## <a name="server-side-bandwidth-limitation"></a>서버 쪽 대역폭 제한

다른 캐시 크기의 네트워크 대역폭 용량은 다릅니다. 서버가 사용 가능한 대역폭을 초과 하면 데이터가 클라이언트에 게 신속 하 게 전송 되지 않습니다. 서버에서 클라이언트에 데이터를 충분히 빠르게 푸시할 수 없어 클라이언트 요청이 시간 초과 될 수 있습니다.

"Cache Read" 및 "Cache Write" 메트릭을 사용 하 여 사용 되는 서버 쪽 대역폭의 양을 확인할 수 있습니다. [이러한 메트릭은](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 포털에서 볼 수 있습니다.

네트워크 대역폭 사용량이 최대 용량에 근접 한 경우를 줄이려면 다음을 수행 합니다.

- 네트워크 수요를 줄이기 위해 클라이언트 호출 동작을 변경 합니다.
- 잠재적 영향에 대해 조기에 알리도록 캐시 읽기 또는 캐시 쓰기와 같은 메트릭에 대한 [경고를 만듭니다](cache-how-to-monitor.md#alerts) .
- 더 많은 네트워크 대역폭 용량을 사용 하 여 더 큰 캐시 크기로 [확장](cache-how-to-scale.md) 합니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 클라이언트 쪽 문제 해결](cache-troubleshoot-client.md)
- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)
