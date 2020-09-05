---
title: Azure Cache for Redis 시간 제한 문제 해결
description: Redis 서버 패치 및 StackExchange Redis timeout 예외와 같이 Azure Cache for Redis의 일반적인 시간 제한 문제를 해결 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213457"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Azure Cache for Redis 시간 제한 문제 해결

이 섹션에서는 Redis 용 Azure 캐시에 연결할 때 발생 하는 시간 제한 문제를 해결 하는 방법을 설명 합니다.

- [Redis 서버 패치](#redis-server-patching)
- [StackExchange.Redis 시간 제한 예외 조사](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="redis-server-patching"></a>Redis 서버 패치

Redis 용 Azure Cache는 서버 소프트웨어를 제공 하는 관리 되는 서비스 기능의 일부로 정기적으로 업데이트 합니다. 이 [패치](cache-failover.md) 작업은 주로 장면 뒤에 발생 합니다. Redis 서버 노드를 패치 하는 동안 장애 조치 (failover) 중에 이러한 노드에 연결 된 Redis 클라이언트는 이러한 노드 간에 연결이 전환 될 때 임시 시간 초과가 발생할 수 있습니다. 응용 프로그램에 어떤 부작용이 발생할 수 있는지에 대 한 자세한 내용 및 패치 이벤트의 처리를 개선 하는 방법에 대 한 자세한 내용은 [장애 조치 (failover)가 클라이언트 응용 프로그램에 미치는 영향](cache-failover.md#how-does-a-failover-affect-my-client-application) 을 참조 하세요.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 시간 제한 예외 조사

Redis는 `synctimeout` 기본 값이 5000 밀리초 인 동기 작업에 대해 이라는 구성 설정을 사용 합니다. 이 시간 내에 동기 호출이 완료 되지 않으면 Redis 클라이언트에서 다음 예제와 비슷한 시간 제한 오류를 throw 합니다.

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

이 오류 메시지에는 문제의 원인과 가능한 해결로 이끌 메트릭이 들어 있습니다. 다음 테이블에는 오류 메시지 메트릭에 대한 세부 정보가 있습니다.

| 오류 메시지 메트릭 | 세부 정보 |
| --- | --- |
| inst |마지막 시간 조각에서: 0 명령이 발급되었습니다 |
| mgr |소켓 관리자가 작업을 수행 하는 `socket.select` 중입니다. 즉, 어떤 작업을 수행 해야 하는 소켓을 나타내도록 OS에 요청 합니다. 판독기는 수행할 작업이 없다고 생각 하기 때문에 네트워크에서 적극적으로 읽지 않습니다. |
| queue |진행 중인 작업이 총 73건 있습니다. |
| qu |진행 중인 작업 중 6 개는 보내지 않은 큐에 있으며 아직 아웃 바운드 네트워크에 기록 되지 않았습니다. |
| qs |67 진행 중인 작업이 서버에 전송 되었지만 아직 응답을 사용할 수 없습니다. 응답은 `Not yet sent by the server` 또는 `sent by the server but not yet processed by the client.`일 수 있습니다. |
| qc |진행 중인 작업 중 0 개는 회신을 표시 하지만 완료 루프에서 대기 하 고 있으므로 아직 완료로 표시 되지 않았습니다. |
| wr |활성 기록기가 있습니다 (6 개의 보내지 않은 요청이 무시 되지 않음). 바이트/activewriters |
| in |활성 판독기가 없으며 NIC 바이트/activereaders에서 판독하는 데 사용 가능한 바이트는 0입니다. |

다음 단계를 사용 하 여 가능한 근본 원인을 조사할 수 있습니다.

1. Redis client를 사용 하는 경우 다음 패턴을 사용 하 여 연결 하는 것이 가장 좋습니다.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    자세한 내용은 [StackExchange.Redis를 사용하여 캐시에 연결](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)을 참조하세요.

1. 서버와 클라이언트 응용 프로그램이 Azure의 동일한 지역에 있는지 확인 합니다. 예를 들어 캐시가 미국 동부에 있지만 클라이언트는 미국 서 부에 있고 요청이 일정 시간 내에 완료 되지 않은 경우 `synctimeout` 또는 로컬 개발 컴퓨터에서 디버그할 때 시간 초과가 발생할 수 있습니다. 

    동일한 Azure 지역에 캐시와 클라이언트를 두도록 하는 것이 좋습니다. 지역 간 호출을 포함 하는 시나리오가 있는 경우 `synctimeout` `synctimeout` 연결 문자열에 속성을 포함 하 여 간격을 기본 5000 밀리초 간격 보다 높은 값으로 설정 해야 합니다. 다음 예제에서는 `synctimeout` 2000 밀리초의 Redis에 대해 Azure Cache에서 제공 하는 Redis에 대 한 연결 문자열의 조각을 보여 줍니다.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
1. 요청이 서버 또는 클라이언트의 대역폭 제한에 의해 제한 되는 경우 완료 하는 데 시간이 오래 걸리고 시간 초과가 발생할 수 있습니다. 서버의 네트워크 대역폭으로 인해 시간 초과가 발생 하는지 확인 하려면 [서버 쪽 대역폭 제한](cache-troubleshoot-server.md#server-side-bandwidth-limitation)을 참조 하세요. 클라이언트 네트워크 대역폭으로 인해 시간 초과가 발생 하는지 확인 하려면 [클라이언트 쪽 대역폭 제한](cache-troubleshoot-client.md#client-side-bandwidth-limitation)을 참조 하세요.
1. CPU가 서버 또는 클라이언트에 바인딩됩니까?

   - 클라이언트에서 CPU에 의해 바인딩되는 지 확인 합니다. CPU가 높으면 요청이 간격 내에 처리 되지 않으며 `synctimeout` 요청이 시간 초과 될 수 있습니다. 더 큰 클라이언트 크기로 이동 하거나 부하를 분산 하면이 문제를 쉽게 제어할 수 있습니다.
   - CPU [캐시 성능 메트릭을](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)모니터링 하 여 서버에 cpu가 바인딩되어 있는지 확인 합니다. Redis가 CPU 바인딩될 때 들어오는 요청은 이러한 요청이 시간 초과 될 수 있습니다. 이 문제를 해결 하기 위해 부하를 프리미엄 캐시에서 여러 분할 분산 하거나 더 큰 크기나 가격 책정 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [서버 쪽 대역폭 제한](cache-troubleshoot-server.md#server-side-bandwidth-limitation)을 참조 하세요.
1. 서버에서 처리하는데 시간이 오래 걸리는 명령이 있습니까? Redis 서버에서 처리 하는 데 시간이 오래 걸리는 장기 실행 명령은 시간 초과가 발생할 수 있습니다. 장기 실행 명령에 대 한 자세한 내용은 [장기 실행 명령](cache-troubleshoot-server.md#long-running-commands)을 참조 하세요. Redis-cli 클라이언트 또는 [Redis 콘솔](cache-configure.md#redis-console)을 사용 하 여 Redis 용 Azure Cache 인스턴스에 연결할 수 있습니다. 그런 다음 [SLOWLOG](https://redis.io/commands/slowlog) 명령을 실행 하 여 예상 보다 느린 요청이 있는지 확인 합니다. Redis 서버와 StackExchange.Redis는 작은 수의 큰 요청보다 많은 수의 작은 요청을 위해 최적화되어 있습니다. 데이터를 더 작은 청크로 분할한다면 다음을 향상시킵니다.

    Redis 및 stunnel를 사용 하 여 캐시의 TLS/SSL 끝점에 연결 하는 방법에 대 한 자세한 내용은 [ASP.NET Session State Provider For Redis Preview 릴리스 발표](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)블로그 게시물을 참조 하세요.
1. 높은 Redis 서버 부하로 시간 초과가 발생할 수 있습니다. `Redis Server Load` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링함으로써 서버 부하를 모니터링할 수 있습니다. 100(최대 값)이란 서버 부하는 redis 서버가 요청을 처리하느라 유휴 시간이 전혀 없이 사용 중이었음을 나타냅니다. 특정 요청이 서버 기능의 전부를 차지하는지 확인하려면 이전 단락에서 설명한 대로 SlowLog 명령을 실행합니다. 자세한 내용은 높은 CPU 사용량/서버 로드를 참조하세요.
1. 클라이언트 쪽에서 네트워크 문제를 일으킬 만한 이벤트가 있었습니까? 일반적인 이벤트에는 클라이언트 인스턴스 수를 확장 또는 축소 하거나 새 버전의 클라이언트를 배포 하거나 자동 크기 조정을 사용 하도록 설정 하는 작업이 포함 됩니다. 이 테스트에서는 자동 크기 조정 또는 확장/축소로 인해 아웃 바운드 네트워크 연결이 몇 초 동안 손실 될 수 있음을 알게 되었습니다. StackExchange.Redis 코드는 이러한 이벤트에 복원력이 있어 다시 연결합니다. 다시 연결 하는 동안 큐에 있는 모든 요청은 시간 초과 될 수 있습니다.
1. 시간이 초과 된 캐시에 대 한 몇 개의 작은 요청 앞에 대량 요청이 있나요? `qs`오류 메시지의 매개 변수는 클라이언트에서 서버로 전송 된 요청 수를 알려 주지만 응답을 처리 하지 않았습니다. StackExchange.Redis는 하나의 TCP 연결을 사용하고 한 번에 하나의 응답만 읽을 수 있기 때문에 이 값은 계속 증가할 수 있습니다. 첫 번째 작업이 시간 초과 되더라도 서버에 전송 되는 데이터를 더 이상 중지 하지 않습니다. 다른 요청은 대량 요청이 완료 될 때까지 차단 되 고 시간 초과가 발생할 수 있습니다. 하나의 솔루션은 워크로드를 감당할 수 있게 캐시를 충분히 크게 하고 큰 값을 작은 청크로 분할하여 시간 초과의 가능성을 최소화하는 것입니다. 또 다른 가능한 솔루션은 클라이언트에서 `ConnectionMultiplexer` 개체 풀을 사용하고, 새 요청을 보낼 때 부하가 최소인 `ConnectionMultiplexer`을 선택합니다, 여러 연결 개체에서 로드 하는 경우에는 한 번의 시간 제한으로 인해 다른 요청이 시간 초과 되지 않도록 해야 합니다.
1. 를 사용 하 `RedisSessionStateProvider` 는 경우 다시 시도 시간 제한을 올바르게 설정 했는지 확인 합니다. `retryTimeoutInMilliseconds`는 `operationTimeoutInMilliseconds`보다 높아야 합니다. 그렇지 않으면 재시도가 발생하지 않습니다. 다음 예제에서 `retryTimeoutInMilliseconds` 가 3000으로 설정됩니다. 자세한 내용은 [Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md) 및 [세션 상태 제공자 및 출력 캐시 공급자의 구성 매개 변수를 사용하는 방법](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)을 참조하세요.

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. 및을 [모니터링](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) 하 여 Azure Cache for Redis 서버에서 메모리 사용량을 확인 `Used Memory RSS` `Used Memory` 합니다. 제거 정책이 구현되었다면, `Used_Memory` 가 캐시 크기에 도달할 때 Redis는 제거 키를 작동합니다. 이상적으로 `Used Memory RSS`는 `Used memory`보다 약간만 높아야 합니다. 큰 차이는 메모리 조각화 (내부 또는 외부)가 있음을 의미 합니다. `Used Memory RSS`가 `Used Memory`보다 작다면 운영 체제가 캐시 메모리의 일부를 스왑했음을 의미합니다. 이 스왑이 발생하는 경우 일부 상당한 대기 시간을 예상할 수 있습니다. Redis는 할당이 메모리 페이지에 매핑되는 방식을 제어할 수 없기 때문에 `Used Memory RSS` 메모리 사용량이 급증 하는 경우가 많습니다. Redis 서버에서 메모리를 해제 하는 경우 할당자는 메모리를 사용 하지만 시스템에 메모리를 다시 제공 하거나 제공 하지 않을 수 있습니다. 운영 체제가 보고하는 `Used Memory` 값과 메모리 소비량 사이에 차이가 있을 수 있습니다. 메모리가 사용 되 고 Redis에 의해 해제 되었지만 시스템에 다시 제공 되지 않았을 수 있습니다. 메모리 문제를 완화 하기 위해 다음 단계를 수행할 수 있습니다.

   - 시스템의 메모리 제한에 대해 실행 되지 않도록 캐시를 더 큰 크기로 업그레이드 합니다.
   - 키에 만료 시간을 설정하여 이전 값이 사전에 제거되게 합니다.
   - `used_memory_rss` 캐시 메트릭을 모니터링합니다. 이 값이 캐시 크기에 가까워지면 성능 문제가 표시 되기 시작할 가능성이 높습니다. 프리미엄 캐시를 사용 하거나 더 큰 캐시 크기로 업그레이드 하는 경우 여러 분할에 데이터를 배포 합니다.

   자세한 내용은 [Redis 서버에서의 메모리 부족](cache-troubleshoot-server.md#memory-pressure-on-redis-server)을 참조 하세요.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 클라이언트 쪽 문제 해결](cache-troubleshoot-client.md)
- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
