---
title: Azure Cache for Redis 관리 Faq
description: Redis 용 Azure Cache를 관리 하는 데 도움이 되는 일반적인 질문에 대 한 답변을 알아보세요.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 5c5c7a5adae9891f764f714d1700c6024376de02
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205386"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Azure Cache for Redis 관리 Faq
이 문서에서는 Redis 용 Azure Cache를 관리 하는 방법에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.

## <a name="common-questions-and-answers"></a>일반적인 질문 및 답변
이 섹션에서는 다음 Faq를 다룹니다.

* [언제 비 TLS/SSL 포트를 사용하여 Redis에 연결할 수 있도록 해야 하나요?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [프로덕션 모범 사례에는 어떤 것이 있나요?](#what-are-some-production-best-practices)
* [일반적인 Redis 명령을 사용할 때 고려해야 하는 몇 가지 사항은 무엇인가요?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [ThreadPool 증가에 대한 중요한 세부 정보](#important-details-about-threadpool-growth)
* [StackExchange.Redis를 사용하는 경우 클라이언트에서 더 많은 처리량을 가져오는 서버 GC를 사용하도록 설정](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [성능 고려 사항 및 연결](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>언제 비 TLS/SSL 포트를 사용하여 Redis에 연결할 수 있도록 해야 하나요?
Redis 서버는 기본적으로 TLS를 지원하지 않지만 Azure Cache for Redis는 이를 지원합니다. Azure Cache for Redis에 연결하고 클라이언트에서 StackExchange.Redis와 같은 TLS를 지원하는 경우 TLS를 사용해야 합니다.

>[!NOTE]
>TLS가 아닌 포트는 기본적으로 새 Azure Cache for Redis 인스턴스에 사용되지 않습니다. 클라이언트에서 TLS를 지원하지 않는 경우 [Azure Cache for Redis에서 캐시 구성](cache-configure.md) 문서에 있는 [액세스 포트](cache-configure.md#access-ports) 섹션의 지침에 따라 TLS가 아닌 포트를 사용하도록 설정해야 합니다.
>
>

`redis-cli`와 같은 Redis 도구는 TLS 포트에서 작동하지 않지만, [Redis용 ASP.NET 세션 상태 제공자 미리 보기 릴리스 발표](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) 블로그 게시물의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하면 TLS 포트에 도구를 안전하게 연결할 수 있습니다.

Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](cache-development-faq.md#how-can-i-run-redis-commands) 섹션을 참조하세요.

### <a name="what-are-some-production-best-practices"></a>프로덕션 모범 사례에는 어떤 것이 있나요?
* [StackExchange.Redis 모범 사례](#stackexchangeredis-best-practices)
* [구성 및 개념](#configuration-and-concepts)
* [성능 테스트](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis 모범 사례
* `AbortConnect` 를 false로 설정하고 ConnectionMultiplexer가 자동으로 다시 연결되도록 합니다. [자세한 내용을 보려면 여기를 참조하세요](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* ConnectionMultiplexer를 다시 사용합니다. 요청마다 새로 만들지 마세요. [여기에 표시된](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)`Lazy<ConnectionMultiplexer>` 패턴을 사용하는 것이 좋습니다.
* Redis는 더 작은 값에서 가장 잘 작동하므로 더 큰 데이터를 여러 개의 키로 분할하는 것을 고려합니다. [이 Redis 토론](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)에서 100KB는 큰 것으로 간주합니다. 큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) 를 읽어보세요.
* 시간 초과를 방지하도록 [ThreadPool 설정](#important-details-about-threadpool-growth) 을 구성합니다.
* 기본 connectTimeout인 5초 이상을 사용합니다. 이렇게 하면 네트워크 문제 이벤트가 발생할 경우 StackExchange.Redis에서 연결을 다시 설정할 충분한 시간을 얻게 됩니다.
* 실행 중인 다른 작업을 관련된 성능 비용을 고려해야 합니다. 예를 들어 `KEYS` 명령은 O(n) 작업이므로 피해야 합니다. [redis.io 사이트](https://redis.io/commands/) 에는 지원되는 각 작업에 대한 시간 복잡도와 관련된 세부 정보가 제공됩니다. 각 작업에 대한 복잡성을 확인하려면 각 명령을 클릭합니다.

#### <a name="configuration-and-concepts"></a>구성 및 개념
* 프로덕션 시스템에 대해 표준 또는 프리미엄 계층을 사용합니다. 기본 계층은 데이터 복제 및 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다. C0 캐시는 일반적으로 간단한 개발/테스트 시나리오에 사용됩니다.
* Redis는 **메모리 내** 데이터 저장소입니다. 데이터 손실이 발생할 수 있는 시나리오에 대해서는 [이 문서](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 를 읽어보세요.
* [패치 및 장애 조치(failover)로 인한](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)연결 문제를 처리할 수 있도록 시스템을 개발하세요.

#### <a name="performance-testing"></a>성능 테스트
* 자체 성능 테스트를 작성하기 전에 먼저 `redis-benchmark.exe`를 사용하여 가능한 처리량을 확인하세요. `redis-benchmark`에서는 TLS가 지원되지 않으므로 테스트를 실행하기 전에 [Azure Portal을 통해 비 TLS 포트를 사용하도록 설정](cache-configure.md#access-ports)해야 합니다. 예를 들어 [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* 테스트에 사용되는 클라이언트 VM은 Azure Cache for Redis 인스턴스와 동일한 지역에 있어야 합니다.
* Dv2 VM 시리즈는 하드웨어 성능이 더 우수하고 최상의 결과를 제공하므로 클라이언트에 이 시리즈를 사용하는 것이 좋습니다.
* 선택하는 클라이언트 VM에는 적어도 테스트 중인 캐시만큼의 계산 및 대역폭 성능이 필요합니다.
* Windows에서 작업하는 경우 클라이언트 컴퓨터에서 VRSS를 사용하도록 설정합니다. [자세한 내용을 보려면 여기를 참조하세요](https://technet.microsoft.com/library/dn383582.aspx).
* 프리미엄 계층 Redis 인스턴스는 CPU와 네트워크 모두 더 나은 하드웨어에서 실행되므로 더 우수한 네트워크 대기 시간 및 처리량을 제공합니다.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>일반적인 Redis 명령을 사용할 때 고려해야 하는 몇 가지 사항은 무엇인가요?

* 완료하는 데 시간이 오래 걸리는 특정 Redis 명령은 해당 명령의 영향을 완전히 알고 있는 경우에만 사용해야 합니다. 예를 들어 [KEYS](https://redis.io/commands/keys) 명령은 프로덕션에서 실행하지 않아야 합니다. 키 수에 따라 반환되는 데 시간이 오래 걸릴 수 있습니다. Redis는 단일 스레드 서버이며 한 번에 하나씩 명령을 처리합니다. KEYS 후에 실행된 다른 명령이 있는 경우 Redis가 KEYS 명령을 처리할 때까지 처리되지 않습니다. [redis.io 사이트](https://redis.io/commands/) 에는 지원되는 각 작업에 대한 시간 복잡도와 관련된 세부 정보가 제공됩니다. 각 작업에 대한 복잡성을 확인하려면 각 명령을 클릭합니다.
* 키 크기 - 작은 키/값을 사용해야 하나요, 아니면 큰 키/값을 사용해야 하나요? 이는 시나리오에 따라 달라집니다. 시나리오에서 큰 키가 필요한 경우 ConnectionTimeout 및 다시 시도 값을 조정한 다음 다시 시도 논리를 조정할 수 있습니다. Redis 서버 관점에서는 값이 작을수록 성능이 향상됩니다.
* 이러한 고려 사항이 Redis에서 큰 값을 저장할 수 없다는 의미는 아닙니다. 다음과 같은 고려 사항에 주의해야 합니다. 대기 시간이 더 길어집니다. 큰 데이터 집합 1개와 작은 데이터 집합 1개가 있는 경우, 이전의 [StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) 섹션에 설명된 대로 각각 다른 시간 제한 및 재시도 값으로 구성된 여러 개의 ConnectionMultiplexer 인스턴스를 사용할 수 있습니다.

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?
* [캐시 진단을 사용](cache-how-to-monitor.md#enable-cache-diagnostics)하도록 설정하면 캐시의 상태를 [모니터링](cache-how-to-monitor.md)할 수 있습니다. Azure 포털에서 메트릭을 볼 수 있으며 선택한 도구를 사용하여 메트릭을 [다운로드 및 검토](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 할 수도 있습니다.
* redis-benchmark.exe를 사용하여 Redis 서버를 부하 테스트할 수 있습니다.
* 부하 테스트 클라이언트와 Azure Cache for Redis가 동일한 지역에 있는지 확인합니다.
* redis-cli.exe를 사용하고 INFO 명령을 통해 캐시를 모니터링합니다.
* 부하로 인해 높은 메모리 조각화가 발생하는 경우 더 큰 캐시 크기로 확장해야 합니다.
* Redis 도구 다운로드에 대한 지침은 [어떻게 Redis 명령을 실행할 수 있나요?](cache-development-faq.md#how-can-i-run-redis-commands) 섹션을 참조하세요.

다음 명령은 redis-benchmark.exe를 사용하는 예를 보여줍니다. 정확한 결과를 위해 캐시와 같은 지역에 있는 VM에서 이 명령을 실행합니다.

* 1K 페이로드를 사용하여 파이프라인 SET 요청 테스트

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 1K 페이로드를 사용하여 파이프라인 GET 요청 테스트

>[!NOTE]
> 먼저 위에 표시된 SET 테스트를 실행하여 캐시를 채웁니다.
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>ThreadPool 증가에 대한 중요한 세부 정보
CLR ThreadPool에는 두 가지 유형의 스레드 - "작업자" 및 "I/O 완료 포트"(IOCP) 스레드가 있습니다.

* 작업자 스레드는 `Task.Run(…)` 또는 `ThreadPool.QueueUserWorkItem(…)` 메서드를 처리하는 등의 작업에 사용됩니다. 이러한 스레드는 작업을 백그라운드 스레드에서 수행해야 할 경우에 CLR에서 다양한 구성 요소에서 사용됩니다.
* IOCP 스레드는 비동기 IO가 발생하는 경우(예: 네트워크에서 읽을 때) 사용됩니다.

스레드 풀은 스레드의 각 형식에 대한 "최소" 설정에 도달할 때까지 새 작업자 스레드 또는 주문형 I/O 완료 스레드(제한 없이)를 제공합니다. 기본적으로 최소 스레드 수는 시스템에서 프로세서의 수로 설정됩니다.

기존(사용 중) 스레드의 수가 스레드의 "최소" 수에 도달하면 ThreadPool은 500밀리초당 하나의 스레드에 새 스레드를 삽입하는 비율을 제한합니다. 일반적으로 시스템에서는 IOCP 스레드가 필요한 작업이 폭주하면 해당 작업을 신속하게 처리합니다. 그러나 작업 폭주가 구성된 "최소" 설정보다 큰 경우 ThreadPool은 두 가지 중 하나가 발생하기를 기다리므로 일부 작업 처리에 약간의 지연이 있을 수 있습니다.

* 기존 스레드는 작업을 처리할 수 있는 상태가 됩니다.
* 500ms에 대해 기존 스레드를 사용할 수 없으므로 새 스레드가 만들어집니다.

기본적으로 사용 중인 스레드의 수가 최소 스레드보다 큰 경우 네트워크 트래픽이 애플리케이션에서 처리되기 전에 500ms 지연을 지불할 가능성이 있음을 의미합니다. 또한 기존 스레드가 15초보다 오랫동안 유휴 상태로 유지되는 경우(기억에 기반) 정리되고 증가 및 감소의 주기를 반복할 수 있다는 것에 유의해야 합니다.

StackExchange.Redis(빌드 1.0.450 이상)의 예제 오류 메시지를 살펴보는 경우 ThreadPool 통계를 인쇄하는 것을 볼 수 있습니다(아래 IOCP 및 작업자 세부 정보 참조).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

이전 예에서 IOCP 스레드의 경우 사용 중인 6개의 스레드가 있고 시스템이 최소 4개의 스레드를 허용하도록 구성되어 있는 것을 확인할 수 있습니다. 이 경우 클라이언트는 6 > 4로 인해 두 500ms 지연을 볼 수 있습니다.

IOCP 또는 작업자 스레드의 증가에 제한이 있는 경우 StackExchange.Redis는 시간 제한에 도달할 수 있습니다.

#### <a name="recommendation"></a>권장

주어진 이 정보로 고객은 IOCP 및 작업자 스레드에 대해 기본값보다 큰 값으로 최소 구성 값을 설정하는 것이 좋습니다. 하나의 애플리케이션에 대한 올바른 값은 다른 애플리케이션에 대해 너무 높거나 낮을 수 있으므로 이 값에 대해 모든 지침에 맞는 하나의 크기를 제공할 수 없습니다. 이 설정은 복잡한 애플리케이션의 다른 부분의 성능에 영향을 미칠 수 있으므로 각 고객은 특정 요구에 맞게 이 설정을 미세 조정해야 합니다. 좋은 시작 지점은 200 또는 300이며 필요에 따라 테스트 및 조정합니다.

이 설정을 구성하는 방법

* `global.asax.cs`에서 [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) 메서드를 사용하여 프로그래밍 방식으로 이 설정을 변경하는 것이 좋습니다. 다음은 그 예입니다.

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > 이 메서드로 지정된 값은 전역 설정으로, 전체 AppDomain에 영향을 미칩니다. 예를 들어 4개의 코어 컴퓨터가 있으며 런타임 중에 *minWorkerThreads* 및 *minIOThreads*를 CPU당 50으로 설정하려는 경우 **ThreadPool.SetMinThreads(200, 200)** 을 사용하면 됩니다.

* 일반적으로 `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`에 있는, `Machine.config`의 `<processModel>` 구성 요소 아래 [*minIoThreads* 또는 *minWorkerThreads* 구성 설정](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx)을 사용하여 최소 스레드 설정을 지정할 수도 있습니다. **이러한 방식으로 최소 스레드 수를 설정하는 것은 시스템 차원의 설정이므로 일반적으로 권장되지 않습니다.**

  > [!NOTE]
  > 이 구성 요소에 지정된 값은 *코어당* 설정입니다. 예를 들어 4코어 컴퓨터가 있고 *minIOThreads* 설정을 런타임 시 200으로 지정하려는 경우 `<processModel minIoThreads="50"/>`를 사용합니다.
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>StackExchange.Redis를 사용하는 경우 클라이언트에서 더 많은 처리량을 가져오는 서버 GC를 사용하도록 설정
서버 GC를 사용하면 클라이언트를 최적화하고 StackExchange.Redis를 사용하는 경우 더 나은 성능 및 처리량을 제공할 수 있습니다. 서버 GC 및 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [서버 GC를 사용하도록 설정하려면](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [가비지 수집 기본 사항](/dotnet/standard/garbage-collection/fundamentals)
* [가비지 수집 및 성능](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>연결에 대한 성능 고려 사항

가격 책정 계층마다 클라이언트 연결, 메모리 및 대역폭에 대한 제한이 다릅니다. 각 캐시 크기는 특정 횟수의 연결*까지* 허용하지만 Redis에 대한 각 연결에는 오버헤드가 연결되어 있습니다. 이러한 오버헤드의 예로 TLS/SSL 암호화의 결과인 CPU 및 메모리 사용량이 있습니다. 특정 캐시 크기에 대한 최대 연결 제한은 부하가 적은 캐시를 가정합니다. 연결 오버헤드의 부하 *그리고* 클라이언트 작업의 부하가 시스템의 용량을 초과하면 현재 캐시 크기에 대한 연결 제한을 초과하지 않은 경우에도 캐시에 용량 문제가 발생할 수 있습니다.

각 계층의 다양한 연결 제한에 대한 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. 연결 및 기타 기본 구성에 대한 정보는 [기본 Redis 서버 구성](cache-configure.md#default-redis-server-configuration)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Redis faq 용 다른 Azure Cache](cache-faq.md)에 대해 알아봅니다.
