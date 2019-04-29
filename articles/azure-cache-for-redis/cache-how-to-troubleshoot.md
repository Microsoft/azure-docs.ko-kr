---
title: Azure Cache for Redis 문제를 해결하는 방법 | Microsoft Docs
description: Azure Cache for Redis와 관련된 일반적인 문제를 해결하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830010"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Azure Cache for Redis 문제를 해결하는 방법

이 문서는 서로 다른 범주의 Redis 인스턴스에 대 한 Azure 캐시를 사용 하 여 연결할 때 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다.

- [클라이언트 쪽 문제 해결](#client-side-troubleshooting) 식별 하 고 캐시에 연결 하 여 응용 프로그램에서 문제를 해결 하는 데 도움이 됩니다.
- [서버 쪽 문제 해결](#server-side-troubleshooting) 식별 하 고 Redis 측면에 대 한 Azure 캐시에서 발생 하는 문제를 해결 하는 데 도움이 됩니다.
- [데이터 손실 해결](#data-loss-troubleshooting) 식별 하 고 키 필요 하지만 캐시에서 찾을 수 없습니다는 있는 인시던트를 해결 하는 데 도움이 됩니다.
- [StackExchange.Redis 시간 초과 예외](#stackexchangeredis-timeout-exceptions) StackExchange.Redis 라이브러리를 사용 하 여 문제를 해결 한 특정 지침을 제공 합니다.

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>
>

## <a name="client-side-troubleshooting"></a>클라이언트 쪽 문제 해결

이 섹션에서는 클라이언트 애플리케이션에 부가된 조건 때문에 발생하는 문제 해결에 대해 알아봅니다.

- [클라이언트 쪽의 메모리 부족](#memory-pressure-on-the-client)
- [트래픽 폭주](#burst-of-traffic)
- [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
- [클라이언트 쪽 대역폭 초과](#client-side-bandwidth-exceeded)
- [큰 요청/응답 크기](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>클라이언트 쪽의 메모리 부족

클라이언트 컴퓨터의 메모리 압력 모든 종류의 캐시에서 응답의 처리를 지연 시킬 수 있는 성능 문제로 이어집니다. 메모리가 부족 해지면 시스템 디스크에 데이터 페이지 수입니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다.

클라이언트의 메모리 부족 검색할:

- 사용 가능한 메모리를 초과 하지 않는지 확인 하는 컴퓨터에서 메모리 사용량을 모니터링 합니다.
- 클라이언트의 모니터링 `Page Faults/Sec` 성능 카운터입니다. 정상적인 작업 중 대부분의 시스템에는 약간의 페이지 폴트가 있습니다. 요청 시간 제한을 사용 하 여 해당 페이지 폴트 급증 메모리 압력을 나타낼 수 있습니다.

클라이언트에서 높은 메모리가 중에 여러 가지 방법으로 완화할 수 있습니다.

- 클라이언트에서 메모리 소비를 줄이기 위해 메모리 사용 패턴을 자세히 살펴봅니다.
- 더 많은 메모리를 사용 하 여 더 큰 크기로 VM 클라이언트를 업그레이드 합니다.

### <a name="burst-of-traffic"></a>트래픽 폭주

나쁜 `ThreadPool` 설정과 결합된 트래픽 폭주는 Redis 서버에서 이미 보냈으나 클라이언트 쪽에서 아직 소비되지 않은 데이타의 처리 지연이 발생할 수 있습니다.

모니터링 하는 방법에 `ThreadPool` 통계를 사용 하 여 바뀔 [예로 `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)합니다. 사용할 수 있습니다 `TimeoutException` 자세히 조사 하려면 아래와 같은 메시지 StackExchange.Redis에서:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

이전 예외를 가지는 몇 가지 흥미로운 문제가 있습니다.

- `IOCP` 섹션과 `WORKER` 섹션에 `Min` 값보다 큰 `Busy` 값이 있습니다. 이러한 차이로 인해 프로그램 `ThreadPool` 설정이 조정이 필요로 합니다.
- 또한 `in: 64221`도 볼 수 있습니다: 이 값은 64211 바이트 클라이언트의 커널 소켓 계층에서 받았지만 응용 프로그램에서 읽지를 나타냅니다. 이 차이 일반적으로 응용 프로그램 (예: StackExchange.Redis) 서버에서 보내는 만큼를 신속 하 게 데이터를 네트워크에서 읽지 못하고 있음을 의미 합니다.

할 수 있습니다 [구성 하 `ThreadPool` 설정을](https://gist.github.com/JonCole/e65411214030f0d823cb) 버스트 시나리오 스레드 풀에서 신속 하 게 조정 되도록 합니다.

### <a name="high-client-cpu-usage"></a>클라이언트의 높은 CPU 사용량

클라이언트의 높은 CPU 사용량 시스템이 수행 하 라는 메시지가 표시 되는 작업을 유지할 수 없는 것을 나타냅니다. 캐시 응답을 신속 하 게 전송 하더라도 클라이언트는 적시에 응답을 처리 하는 데 실패할 수 있습니다.

Azure portal에서 또는 컴퓨터에서 성능 카운터를 통해 사용할 수 있는 메트릭을 사용 하 여 클라이언트의 시스템 CPU 사용량을 모니터링 합니다. 모니터링 하지 않도록 주의 하십시오 *프로세스* CPU 있으므로 단일 프로세스는 낮은 CPU 사용량을 가질 수 있지만 시스템 CPU 커질 수 있습니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다. 높은 CPU 높은 발생할 수 있습니다 `in: XXX` 에 값 `TimeoutException` 오류 메시지에 설명 된 대로 합니다 [트래픽 폭주](#burst-of-traffic) 섹션입니다.

> [!NOTE]
> StackExchange.Redis 1.1.603 이상은 `TimeoutException` 오류 메시지에 `local-cpu` 매트릭을 포함합니다. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
>
>

클라이언트의 높은 CPU 사용량을 완화 합니다.

- CPU 스파이크의 원인이 무엇 인지 조사 합니다.
- 더 많은 CPU 용량을 사용 하 여 더 큰 VM 크기로 클라이언트를 업그레이드 합니다.

### <a name="client-side-bandwidth-exceeded"></a>클라이언트 쪽 대역폭 초과

클라이언트 컴퓨터의 아키텍처에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 클라이언트 네트워크 용량을 오버 로드 하 여 사용 가능한 대역폭을 초과 하면, 다음 데이터 처리 되지 않습니다 클라이언트 쪽 서버에서 보내는 만큼 신속 하 게 합니다. 이 경우 시간이 초과될 수 있습니다.

대역폭 사용량 사용 하 여 시간에 따른 변화를 모니터링 [예로 `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)합니다. 이 코드는 (Azure 웹 사이트와 같이) 권한이 제한된 일부 환경에서 성공적으로 실행되지 않을 수도 있습니다.

를 완화 하기 위해 네트워크 대역폭 소비를 늘리거나 줄일 클라이언트 VM 크기를 하나 더 많은 네트워크 용량으로 합니다.

### <a name="large-requestresponse-size"></a>큰 요청/응답 크기

큰 요청/응답으로 시간 초과가 발생할 수 있습니다. 예를 들어 클라이언트에 구성 된 시간 제한 값은 1 초를 가정 합니다. 애플리케이션이 동시에 2개의 키(예: 'A' 및 'B')를 요청합니다(동일한 실제 네트워크 연결 사용). 두 요청 'A'와 'B'를 보내는 위치 하나씩 해당 응답을 기다리지 않고 요청 "파이프라인", 대부분의 클라이언트에서 지원 합니다. 서버는 응답을 동일한 순서로 전송합니다. 응답 'A'가 큰 경우에 이후 요청에 대 한 제한 시간 대부분 먹을 수 있습니다.

다음 예제에서는 요청 'A'와 'B' 서버에 신속 하 게 전송 됩니다. 서버 응답 'A'와 'B'를 신속 하 게 보내기 시작 합니다. 데이터 전송 시간 때문에 'B' 응답 'A' 뒤에 기다려야 하는 응답 시간이 초과 서버 신속 하 게 응답 하는 경우에 됩니다.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

이 요청/응답은 측정하기 어렵습니다. 큰 요청 및 응답을 추적 하도록 클라이언트 코드를 계측할 수 있습니다.

큰 응답 크기에 대 한 해결 방법은 다양 하지만 포함:

1. 많은 수의 몇 가지 큰 값 보다는 작은 값에 대 한 응용 프로그램을 최적화 합니다.
    - 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다.
    - 게시물을 참조 [redis 위한 이상적인 값 크기 범위는 무엇입니까? 100KB는 너무 큽니다? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 이유에 대 한 세부 정보에 대 한 값이 작을수록는 것이 좋습니다.
1. 더 높은 대역폭 기능을 가져오려면 VM의 크기 늘리기
    - 클라이언트 또는 서버 VM에서 더 많은 대역폭이 더 큰 응답에 대 한 데이터 전송 시간을 줄일 수 있습니다.
    - 현재 VM 크기를 제한 하려면 두 컴퓨터 모두에서 현재 네트워크 사용량을 비교 합니다. 서버의 또는 클라이언트에만 더 많은 대역폭이 충분 한 수 없습니다.
1. 응용 프로그램에서 사용 하는 연결 개체의 수를 늘립니다.
    - 다른 연결 개체를 통해 요청을 라운드 로빈 방식을 사용 합니다.

## <a name="server-side-troubleshooting"></a>서버 쪽 문제 해결

이 섹션에서는 캐시 서버에 부가된 조건 때문에 발생하는 문제 해결에 대해 알아봅니다.

- [서버 쪽의 메모리 부족](#memory-pressure-on-the-server)
- [높은 CPU 사용량 / 서버 부하](#high-cpu-usage--server-load)
- [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>서버 쪽의 메모리 부족

서버 쪽의 메모리 부족은 요청 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리가 부족 해지면 시스템 디스크에 데이터 페이지 수입니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다. 이 메모리 부족의 몇 가지 가능한 원인은 다음과 같습니다.

- 캐시의 최대 용량 데이터로 채워집니다.
- Redis가 높은 메모리 조각화를 일어나는 합니다. 이 조각화 Redis 작은 개체에 대 한 최적화는 큰 개체를 저장 하 여 가장 자주 발생 합니다.

Redis를 통해 두 개의 통계를 노출 합니다 [정보](https://redis.io/commands/info) 이 문제를 식별 하는 도움이 될 수 있는 명령: "used_memory" 및 "used_memory_rss"입니다. 할 수 있습니다 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 포털을 사용 합니다.

변경이 몇 가지 가능한 메모리 사용량을 정상 상태로 유지 하기 위해 할 수 있습니다.

- [메모리 정책을 구성](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 하고 키에 만료 시간을 설정합니다. 이 정책에 조각화가 있을 경우에 충분 하지 않을 수 있습니다.
- [maxmemory-reserved 값을 구성합니다](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) . 자세한 내용은 추가 참조 [메모리 예약에 대 한 고려 사항](#considerations-for-memory-reservations) 아래.
- 크게 캐시된 개체를 더 작은 관련 개체로 분할합니다.
- [경고를 만들](cache-how-to-monitor.md#alerts) 같은 메트릭에 대 한 잠재적인 영향에 대 한 초기 알릴 메모리를 사용 합니다.
- [확장](cache-how-to-scale.md) 더 많은 메모리 용량을 사용 하 여 캐시 크기를 늘립니다.

#### <a name="considerations-for-memory-reservations"></a>메모리 예약에 대 한 고려 사항

캐시 성능에 영향을 줄 수, 최대 메모리 예약와 같은 메모리 예약 값을 업데이트 합니다. 49GB의 데이터가 채워진 53 GB 캐시 있다고 가정 합니다. 시스템의 사용 가능한 최대 메모리가 45GB로 삭제 예약 값을 8GB로 변경 합니다. 하는 경우 _used_memory_ 하거나 _used_memory_rss_ 값을 45 GB 보다 높은 시스템 될 때까지 데이터를 제거할 수 있습니다 _used_memory_ 고 _used_memory_rss_ 45gb로 줄어듭니다 다음과 같습니다. 제거는 서버 부하 및 메모리 조각화를 증가시킬 수 있습니다.

### <a name="high-cpu-usage--server-load"></a>높은 CPU 사용량 / 서버 부하

높은 서버 부하 또는 CPU 사용량을 서버는 적시에 요청을 처리할 수 없습니다.을 의미 합니다. 느리게 응답 하 고 요청 속도 따라갈 수 없습니다 서버 수 있습니다.

[메트릭을 모니터링할](cache-how-to-monitor.md#view-metrics-with-azure-monitor) CPU 또는 서버 부하 등입니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다.

여러 변경이 방법으로 높은 서버 부하를 완화 하기 위해 할 수 있습니다.

- 실행과 같은 CPU 스파이크의 원인이 무엇 인지 조사 [부담이 큰 명령을](#expensive-commands) 또는 높은 메모리가 중으로 인해 오류가 발생 하는 페이지입니다.
- [경고를 만들](cache-how-to-monitor.md#alerts) 잠재적인 영향에 대 한 초기 알릴 서버나 CPU 부하와 같은 메트릭에 대 한 합니다.
- [확장](cache-how-to-scale.md) 더 많은 CPU 용량을 사용 하 여 캐시 크기를 늘립니다.

#### <a name="expensive-commands"></a>부담이 큰 명령

모든 Redis 명령이 동일 하 게 만들어지면-일부는 다른 항목 보다 실행 비용이 많이 듭니다. 합니다 [Redis 명령 설명서](https://redis.io/commands) 각 명령의 시간 복잡도 보여 줍니다. 해당 명령의 성능 영향을 이해 하려면 캐시에서 실행 중인 명령을 검토 하는 것이 좋습니다. 예를 들어 합니다 [키](https://redis.io/commands/keys) 명령은 o (n) 작업이 것을 알 필요 없이 자주 사용 됩니다. 사용 하 여 키를 방지할 수 있습니다 [스캔](https://redis.io/commands/scan) CPU를 줄이기 위해 급증 합니다.

사용 하 여 [SLOWLOG](https://redis.io/commands/slowlog) 명령, 서버에 대해 실행 되는 부담이 큰 명령을 측정할 수 있습니다.

### <a name="server-side-bandwidth-exceeded"></a>서버 쪽 대역폭 초과

다양 한 캐시 크기에는 다른 네트워크 대역폭 용량이 있습니다. 서버가 사용 가능한 대역폭을 초과 하는 경우 클라이언트에 게 신속 하 게 데이터를 보낼 수 없습니다. 서버 충분히 빠르게 데이터를 클라이언트에 푸시할 수 없습니다 때문에 클라이언트 요청 시간 초과 수 있습니다.

얼마나 많은 서버 쪽 대역폭 사용량을 확인 하려면 "캐시 읽기" 및 "캐시 쓰기" 메트릭을 사용할 수 있습니다. 할 수 있습니다 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor) 포털에서 합니다.

네트워크 대역폭 사용량 최대 용량에 가까운이 상황을 완화 합니다.

- 네트워크 요구를 줄이기 위해 클라이언트 호출 동작을 변경 합니다.
- [경고를 만들](cache-how-to-monitor.md#alerts) 캐시 읽기 또는 알림을 받으려면 조기에 잠재적인 영향을 캐시 쓰기와 같은 메트릭에 대 한 합니다.
- [확장](cache-how-to-scale.md) 더 많은 네트워크 대역폭 용량을 사용 하 여 캐시 크기를 늘립니다.

## <a name="data-loss-troubleshooting"></a>데이터 손실 해결

특정 데이터가 Azure Cache for Redis 인스턴스에 있을 것이라고 예상했지만 여기에 없는 것 같습니다.

가능한 원인 및 해결 방법은 [내 Redis 데이터에서 무엇이 변경되었나요?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 를 참조하세요.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 시간 제한 예외 조사

StackExchange.Redis 구성 명명 된 설정을 사용 하 여 `synctimeout` 1000ms 인 동기 작업 기본값에 대 한 합니다. 이 시간 동기적 호출이 완료 되지 않으면, StackExchange.Redis 클라이언트는 다음 예제와 비슷한 시간 초과 오류가 throw 됩니다.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

이 오류 메시지에는 문제의 원인과 가능한 해결로 이끌 메트릭이 들어 있습니다. 다음 테이블에는 오류 메시지 메트릭에 대한 세부 정보가 있습니다.

| 오류 메시지 메트릭 | 세부 정보 |
| --- | --- |
| inst |마지막 시간 조각에서 발급된 명령이 없습니다. |
| mgr |소켓 관리자가 수행 하는 `socket.select`, 즉 OS가 할 일이 있는 소켓을 요청 합니다. 해당 할 일이 생각 관련이 있기 때문에 네트워크에서 판독기 적극적으로 읽지 못하고 |
| 큐 |진행 중인 작업이 총 73건 있습니다. |
| qu |진행 중인 작업의 6 보내지 않은 큐에 및 아웃 바운드 네트워크에 아직 작성 하지 않은 |
| qs |진행 중인 작업의 67 건 서버로 보내 졌지만 사용할 아직 응답 합니다. 응답은 `Not yet sent by the server` 또는 `sent by the server but not yet processed by the client.`일 수 있습니다. |
| qc |0 또는 진행 중인 작업을 보았으나 완료 루프에서 대기 하느라 완료로 표시 아직 하지 않은 |
| wr |활성 기록기 (보내지 않은 요청 6 무시 되지 의미) 바이트/않았음을 |
| in |활성 판독기가 없으며 NIC 바이트/activereaders에서 판독하는 데 사용 가능한 바이트는 0입니다. |

### <a name="steps-to-investigate"></a>조사 단계

1. 모범 사례로 StackExchange.Redis 클리이언트를 사용할 때 연결 하기 위해 다음 패턴을 사용 하는 있는지를 확인 합니다.

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

1. 서버 및 클라이언트 응용 프로그램이 Azure에서 동일한 지역에 있는지 확인 합니다. 예를 들어, 있습니다 수 될 때 시 관 초과가 캐시는 미국 동부에 있지만 클라이언트는 미국 서 부 및 요청 내에 완료 하지는 `synctimeout` 간격 또는 사용자 수 될 때 시 관 초과가 로컬 개발 컴퓨터에서 디버깅 하는 경우. 

    동일한 Azure 지역에 캐시와 클라이언트를 두도록 하는 것이 좋습니다. 지역 간 호출을 포함하는 시나리오가 있다면, `synctimeout` 속성을 연결 문자열에 포함함으로써 `synctimeout` 간격을 기본값인 1000ms 간격보다 높은 값에 설정해야 합니다. 다음 예제에서는 StackExchange.Redis를 사용 하 여 Redis에 대 한 Azure 캐시에서 제공에 대 한 연결 문자열의 조각을 보여 줍니다.는 `synctimeout` 2000ms의 합니다.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
1. 하기 위해 오래 걸리는 요청, 서버 또는 클라이언트가 대역폭 제한에 의해 바인딩된 경우 완료 하 고 시간 제한이 발생할 수 있습니다. 시간 초과가 서버의 네트워크 대역폭 때문 인지, 참조 [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)합니다. 시간 초과가 클라이언트 네트워크 대역폭 때문 인지, 참조 [클라이언트 쪽 대역폭 초과](#client-side-bandwidth-exceeded)합니다.
1. CPU가 서버 또는 클라이언트에 바인딩됩니까?

   - 경우는 가져오는 바인딩한 cpu 클라이언트에서 확인 합니다. 높은 CPU 요청 내에서 처리 되지을 초래할는 `synctimeout` 간격 및 요청 시간 초과 원인입니다. 더 큰 클라이언트 크기로 이동하거나 부하를 분산한다면 이 문제를 제어하는데 도움이 됩니다.
   - 확인 표시 되는 경우 CPU를 모니터링 하 여 서버에 바인딩된 합니다 `CPU` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)합니다. Redis가 CPU 바인딩된 동안 들어오는 요청 시간이 초과 초래할 수 있습니다. 이 상태를 해결하기 위해 부하를 프리미엄 캐시에서 여러 분할된 데이터베이스로 분산하거나 더 큰 크기나 가격 책정 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [서버 쪽 대역폭 초과](#server-side-bandwidth-exceeded)를 참조합니다.
1. 서버에서 처리하는데 시간이 오래 걸리는 명령이 있습니까? Redis 서버에서 처리 하는 데 시간이 오래 걸리는 장기 실행 명령의 제한 시간에 발생할 수 있습니다. 장기 실행 명령에 대 한 자세한 내용은 참조 하세요. [부담이 큰 명령을](#expensive-commands)합니다. Redis cli 클라이언트를 사용 하 여 Redis 인스턴스에 대 한 Azure 캐시에 연결할 수 있습니다 또는 [Redis 콘솔](cache-configure.md#redis-console)합니다. 그런 다음 실행 합니다 [SLOWLOG](https://redis.io/commands/slowlog) 있는지 확인 하는 명령은 예상 보다 느리게 요청 합니다. Redis 서버와 StackExchange.Redis는 작은 수의 큰 요청보다 많은 수의 작은 요청을 위해 최적화되어 있습니다. 데이터를 더 작은 청크로 분할한다면 다음을 향상시킵니다.

    Redis cli 및 stunnel를 사용 하 여 캐시 SSL 끝점에 연결 하는 방법에 대 한 자세한 내용은 블로그 게시물을 참조 하세요 [미리 보기 릴리스 Redis 용 ASP.NET 세션 상태 제공자 발표](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)합니다.
1. 높은 Redis 서버 부하로 시간 초과가 발생할 수 있습니다. `Redis Server Load` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링함으로써 서버 부하를 모니터링할 수 있습니다. 100(최대 값)이란 서버 부하는 redis 서버가 요청을 처리하느라 유휴 시간이 전혀 없이 사용 중이었음을 나타냅니다. 특정 요청이 서버 기능의 전부를 차지하는지 확인하려면 이전 단락에서 설명한 대로 SlowLog 명령을 실행합니다. 자세한 내용은 높은 CPU 사용량/서버 로드를 참조하세요.
1. 클라이언트 쪽에서 네트워크 문제를 일으킬 만한 이벤트가 있었습니까? 일반 이벤트에 포함 됩니다: 클라이언트 인스턴스 수를 확장 하거나 축소, 클라이언트 또는 자동 크기 조정 설정의 새 버전을 배포 합니다. 이 테스트에서는 자동 크기 조정 또는 크기 조정 스핀 수 몇 초 정도 손실에 대 한 아웃 바운드 네트워크 연결 하면 발견 했습니다. StackExchange.Redis 코드는 이러한 이벤트에 복원력이 있어 다시 연결합니다. 다시 연결할 때 하는 동안 큐에 있는 모든 요청 시간이 초과 됩니다.
1. 큰 요청 시간 초과 된 캐시에 여러 작은 요청 앞에 있었던? 매개 변수 `qs` 오류 메시지 알려 몇 개의 요청 클라이언트에서 서버로 보냈지만 응답을 처리 하지 않은 합니다. StackExchange.Redis는 하나의 TCP 연결을 사용하고 한 번에 하나의 응답만 읽을 수 있기 때문에 이 값은 계속 증가할 수 있습니다. 첫 번째 작업이 시간 초과 하는 경우에 더 많은 데이터를 보내는 것 또는 서버에서 중지 하지는 않습니다. 다른 요청은 큰 요청이 완료 되 고 시간 초과 하면 때까지 차단 됩니다. 하나의 솔루션은 워크로드를 감당할 수 있게 캐시를 충분히 크게 하고 큰 값을 작은 청크로 분할하여 시간 초과의 가능성을 최소화하는 것입니다. 또 다른 가능한 솔루션은 클라이언트에서 `ConnectionMultiplexer` 개체 풀을 사용하고, 새 요청을 보낼 때 부하가 최소인 `ConnectionMultiplexer`을 선택합니다, 여러 연결 개체에서 로드 또한 시간 초과 대 한 다른 요청에서 단일 시간 초과 방지 해야 합니다.
1. 사용 중인 경우 `RedisSessionStateProvider`, 재시도 제한 시간을 올바르게 설정 했는지 확인 합니다. `retryTimeoutInMilliseconds`는 `operationTimeoutInMilliseconds`보다 높아야 합니다. 그렇지 않으면 재시도가 발생하지 않습니다. 다음 예제에서 `retryTimeoutInMilliseconds` 가 3000으로 설정됩니다. 자세한 내용은 [Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md) 및 [세션 상태 제공자 및 출력 캐시 공급자의 구성 매개 변수를 사용하는 방법](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)을 참조하세요.

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

1. `Used Memory RSS` 및 `Used Memory`를 [모니터링](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)하여 Azure Cache for Redis 서버의 메모리 사용량을 확인합니다. 제거 정책이 구현되었다면, `Used_Memory` 가 캐시 크기에 도달할 때 Redis는 제거 키를 작동합니다. 이상적으로 `Used Memory RSS`는 `Used memory`보다 약간만 높아야 합니다. 큰 차이 (내부 또는 외부) 메모리 조각화가 의미 합니다. `Used Memory RSS`가 `Used Memory`보다 작다면 운영 체제가 캐시 메모리의 일부를 스왑했음을 의미합니다. 이 스왑이 발생하는 경우 일부 상당한 대기 시간을 예상할 수 있습니다. Redis는 할당이 높은 메모리 페이지에 매핑되는 방식을 제어할 없기 때문 `Used Memory RSS` 메모리 사용량에 스파이크가의 결과인 경우가 많습니다. Redis 서버 메모리를 해제 하면 할당자는 메모리를 사용 하지만 있고 시스템에 메모리를 다시 제공 될 수 있습니다. 운영 체제가 보고하는 `Used Memory` 값과 메모리 소비량 사이에 차이가 있을 수 있습니다. 메모리 사용 및 Redis에서 시스템으로 돌려 지지 발표 될 수 있습니다. 메모리 문제를 완화 하려면 다음 단계를 수행할 수 있습니다.

   - 시스템의 메모리 제한에 대해 실행 되지 않도록 캐시 더 큰 크기로 업그레이드 합니다.
   - 키에 만료 시간을 설정하여 이전 값이 사전에 제거되게 합니다.
   - `used_memory_rss` 캐시 메트릭을 모니터링합니다. 이 값이 해당 캐시의 크기에 가까워지면 성능 문제를 표시 하려면 가능성이 있습니다. 프리미엄 캐시를 사용 하거나 더 큰 캐시 크기로 업그레이드 하는 경우 여러 분할 된 데이터베이스 간에 데이터를 배포 합니다.

   자세한 내용은 [서버 쪽의 메모리 부족](#memory-pressure-on-the-server)을 참조하세요.

## <a name="additional-information"></a>추가 정보

- [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
