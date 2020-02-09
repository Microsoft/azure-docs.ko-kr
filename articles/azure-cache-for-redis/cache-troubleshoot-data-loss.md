---
title: Redis 용 Azure Cache의 데이터 손실 문제 해결
description: 'Redis에 대한 Azure Cache의 데이터 손실 문제를 해결 하는 방법 (예: 키의 부분 손실, 키 만료 또는 키의 완전 한 손실)을 알아봅니다.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530904"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Redis 용 Azure Cache의 데이터 손실 문제 해결

이 문서에서는 Azure Cache for Redis에서 발생할 수 있는 실제 또는 인식 된 데이터 손실을 진단 하는 방법을 설명 합니다.

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="partial-loss-of-keys"></a>키의 부분 손실

Redis 용 Azure Cache는 메모리에 저장 된 키를 임의로 삭제 하지 않습니다. 그러나 만료 또는 제거 정책에 대한 응답 및 명시적인 키 삭제 명령에 대한 키를 제거 합니다. 프리미엄 또는 표준 Azure Cache for Redis 인스턴스에 대한 마스터 노드에 기록 된 키를 복제본에서 바로 사용할 수 없을 수도 있습니다. 데이터는 비동기 및 비 블로킹 방식으로 마스터에서 복제본으로 복제 됩니다.

키가 캐시에서 사라진 경우 다음과 같은 가능한 원인을 확인 합니다.

| 원인 | Description |
|---|---|
| [키 만료](#key-expiration) | 제한 시간 설정으로 인해 키가 제거 됩니다. |
| [키 제거](#key-eviction) | 메모리가 부족 하면 키가 제거 됩니다. |
| [키 삭제](#key-deletion) | 키는 명시적 delete 명령으로 제거 됩니다. |
| [비동기 복제](#async-replication) | 데이터 복제 지연으로 인해 복제본에서 키를 사용할 수 없습니다. |

### <a name="key-expiration"></a>키 만료

Redis에 대한 Azure Cache는 키에 제한 시간이 할당 되 고 해당 기간이 경과 하면 자동으로 키를 제거 합니다. 키 만료 Redis에 대한 자세한 내용은 [만료](https://redis.io/commands/expire) 명령 설명서를 참조 하세요. 제한 시간 값은 [set](https://redis.io/commands/set), [setex](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)및 기타 **\*저장** 명령을 사용 하 여 설정할 수도 있습니다.

만료 된 키 수에 대한 통계를 얻으려면 [INFO](https://redis.io/commands/info) 명령을 사용 합니다. `Stats` 섹션에는 만료 된 키의 총 수가 표시 됩니다. `Keyspace` 섹션에서는 시간 제한이 있는 키 수와 평균 시간 제한 값에 대한 자세한 정보를 제공 합니다.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

캐시에 대한 진단 메트릭을 확인 하 여 키가 누락 된 시간과 만료 된 키의 스파이크 사이에 상관 관계가 있는지 확인할 수도 있습니다. Keyspace 알림 또는 **모니터** 를 사용 하 여 이러한 유형의 문제를 디버깅 하는 방법에 대한 자세한 내용은 [Redis Keyspace 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 의 부록을 참조 하세요.

### <a name="key-eviction"></a>키 제거

Redis 용 Azure Cache에는 데이터를 저장 하기 위한 메모리 공간이 필요 합니다. 필요한 경우 사용 가능한 메모리를 확보 하기 위해 키를 제거 합니다. [INFO](https://redis.io/commands/info) 명령의 **used_memory** 또는 **used_memory_rss** 값이 구성 된 **Maxmemory** 설정에 해당 하는 경우 Redis에 대한 Azure cache는 [캐시 정책을](https://redis.io/topics/lru-cache)기반으로 메모리에서 키를 제거 하기 시작 합니다.

[INFO](https://redis.io/commands/info) 명령을 사용 하 여 제거 된 키 수를 모니터링할 수 있습니다.

```
# Stats

evicted_keys:13224
```

캐시에 대한 진단 메트릭을 확인 하 여 키가 누락 된 시간과 제거 된 키의 스파이크 사이에 상관 관계가 있는지 확인할 수도 있습니다. Keyspace 알림 또는 **모니터** 를 사용 하 여 이러한 유형의 문제를 디버깅 하는 방법에 대한 자세한 내용은 [Redis Keyspace 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) 의 부록을 참조 하세요.

### <a name="key-deletion"></a>키 삭제

Redis 클라이언트는 [DEL](https://redis.io/commands/del) 또는 [hdel](https://redis.io/commands/hdel) 명령을 실행 하 여 Redis 용 Azure Cache에서 키를 명시적으로 제거할 수 있습니다. [INFO](https://redis.io/commands/info) 명령을 사용 하 여 삭제 작업의 수를 추적할 수 있습니다. **DEL** 또는 **hdel** 명령이 호출 된 경우 `Commandstats` 섹션에 나열 됩니다.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>비동기 복제

Standard 또는 Premium 계층의 Redis 용 Azure Cache 인스턴스는 마스터 노드 및 하나 이상의 복제본으로 구성 됩니다. 백그라운드 프로세스를 사용 하 여 데이터를 마스터에서 복제본으로 비동기적으로 복사 합니다. [Redis.io](https://redis.io/topics/replication) 웹 사이트에서는 redis 데이터 복제가 일반적으로 작동 하는 방식을 설명 합니다. 클라이언트가 자주 Redis에 쓰는 시나리오의 경우이 복제가 즉시 수행 되도록 보장 하기 때문에 일부 데이터가 손실 될 수 있습니다. 예를 들어 클라이언트가 키를 기록한 *후* 마스터의 작동이 중단 되는 경우 백그라운드 프로세스에서 복제본으로 해당 키를 보낼 수 *있으려면* 복제본이 새 마스터로 전환 될 때 키가 손실 됩니다.

## <a name="major-or-complete-loss-of-keys"></a>키의 주 또는 전체 손실

캐시에서 대부분 또는 모든 키가 사라진 경우 다음과 같은 가능한 원인을 확인 하십시오.

| 원인 | Description |
|---|---|
| [키 플러시](#key-flushing) | 키가 수동으로 제거 되었습니다. |
| [잘못 된 데이터베이스 선택](#incorrect-database-selection) | Redis에 대한 Azure Cache가 기본이 아닌 데이터베이스를 사용 하도록 설정 되어 있습니다. |
| [Redis 인스턴스 오류](#redis-instance-failure) | Redis 서버를 사용할 수 없습니다. |

### <a name="key-flushing"></a>키 플러시

클라이언트는 Redis cache의 *모든* 데이터베이스에서 모든 키를 제거 하도록 [flushdb](https://redis.io/commands/flushdb) 명령을 호출 하 여 *단일* 데이터베이스의 모든 키를 [제거 하거나 모든](https://redis.io/commands/flushall) 키를 제거할 수 있습니다. 키가 플러시 되었는지 여부를 확인 하려면 [INFO](https://redis.io/commands/info) 명령을 사용 합니다. `Commandstats` 섹션에서는 **FLUSH** 명령이 호출 되었는지 여부를 보여 줍니다.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>잘못 된 데이터베이스 선택

Azure Cache for Redis는 기본적으로 **d b 0** 데이터베이스를 사용 합니다. 다른 데이터베이스 (예: **db1**)로 전환 하 고 키를 읽으려고 하는 경우 Redis에 대한 Azure Cache에서 해당 파일을 찾지 못합니다. 모든 데이터베이스는 논리적으로 분리 된 단위 이며 다른 데이터 집합을 보유 합니다. [SELECT](https://redis.io/commands/select) 명령을 사용 하 여 사용 가능한 다른 데이터베이스를 사용 하 고 각 데이터베이스에서 키를 찾습니다.

### <a name="redis-instance-failure"></a>Redis 인스턴스 오류

Redis는 메모리 내 데이터 저장소입니다. 데이터는 Redis 캐시를 호스트 하는 물리적 또는 가상 컴퓨터에 유지 됩니다. 기본 계층의 Redis 용 Azure Cache 인스턴스는 단일 VM (가상 컴퓨터) 에서만 실행 됩니다. 해당 VM이 다운 되 면 캐시에 저장 한 모든 데이터가 손실 됩니다. 

Standard 및 Premium 계층의 캐시는 복제 된 구성에서 두 개의 Vm을 사용 하 여 데이터 손실에 대해 훨씬 높은 복원 력을 제공 합니다. 이러한 캐시의 마스터 노드가 실패 하면 복제본 노드가 데이터를 자동으로 처리 하는 데 사용 됩니다. 이러한 Vm은 장애 및 업데이트를 위해 별도의 도메인에 위치 하 여 동시에 사용할 수 없게 될 가능성을 최소화 합니다. 그러나 주요 데이터 센터 중단이 발생 하는 경우에도 Vm은 계속 해 서 함께 작동 하지 않을 수 있습니다. 이러한 드문 경우에는 데이터가 손실 됩니다.

[Redis 데이터 지 속성](https://redis.io/topics/persistence) 및 [지역에서 복제](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) 를 사용 하 여 이러한 인프라 오류 로부터 데이터 보호를 개선 하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)
