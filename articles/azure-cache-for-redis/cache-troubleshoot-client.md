---
title: Redis 클라이언트 쪽 문제에 대한 Azure 캐시 문제 해결
description: Redis client memory 압력, 트래픽 버스트, 높은 CPU, 제한 된 대역폭, 큰 요청 또는 큰 응답 크기와 같은 Redis 용 Azure Cache를 사용 하 여 일반적인 클라이언트 쪽 문제를 해결 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: abb73f93116fae217f527e0a9faaf61e2b42ba6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433367"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Redis 클라이언트 쪽 문제에 대한 Azure 캐시 문제 해결

이 섹션에서는 응용 프로그램에서 사용 하는 Redis 클라이언트에 대한 조건으로 인해 발생 하는 문제를 해결 하는 방법을 설명 합니다.

- [Redis client의 메모리 부족](#memory-pressure-on-redis-client)
- [트래픽 버스트](#traffic-burst)
- [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
- [클라이언트 쪽 대역폭 제한](#client-side-bandwidth-limitation)
- [큰 요청 또는 응답 크기](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis client의 메모리 부족

클라이언트 컴퓨터의 메모리 부족으로 인해 캐시에서 응답 처리를 지연 시킬 수 있는 모든 종류의 성능 문제가 발생 합니다. 메모리가 부족 해지면 시스템에서 데이터를 디스크에 페이징 할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다.

클라이언트의 메모리 압력을 감지 하려면:

- 컴퓨터의 메모리 사용량을 모니터링 하 여 사용 가능한 메모리를 초과 하지 않는지 확인 합니다.
- 클라이언트의 `Page Faults/Sec` 성능 카운터를 모니터링 합니다. 정상적인 작업 중 대부분의 시스템에는 일부 페이지 폴트가 있습니다. 요청 시간 제한에 해당 하는 페이지 폴트의 급증은 메모리 부족을 나타낼 수 있습니다.

클라이언트의 높은 메모리 부족은 여러 가지 방법으로 완화할 수 있습니다.

- 클라이언트에서 메모리 소비를 줄이기 위해 메모리 사용 패턴을 자세히 알아보세요.
- 더 많은 메모리를 사용 하 여 클라이언트 VM을 더 큰 크기로 업그레이드 합니다.

## <a name="traffic-burst"></a>트래픽 버스트

나쁜 `ThreadPool` 설정과 결합된 트래픽 폭주는 Redis 서버에서 이미 보냈으나 클라이언트 쪽에서 아직 소비되지 않은 데이타의 처리 지연이 발생할 수 있습니다.

[예제 `ThreadPoolLogger`를 ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)사용 하 여 `ThreadPool` 통계가 시간이 지남에 따라 어떻게 변화 하는지 모니터링 합니다. 아래와 같이 StackExchange의 `TimeoutException` 메시지를 사용 하 여 자세히 조사할 수 있습니다.

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

위의 예외에는 몇 가지 흥미로운 문제가 있습니다.

- `IOCP` 섹션과 `WORKER` 섹션에 `Min` 값보다 큰 `Busy` 값이 있습니다. 이러한 차이는 `ThreadPool` 설정을 조정 해야 함을 의미 합니다.
- 또한 `in: 64221`도 볼 수 있습니다: 이 값은 64211 바이트를 클라이언트의 커널 소켓 계층에서 받았지만 응용 프로그램에서 읽지 않았음을 나타냅니다. 이러한 차이는 일반적으로 응용 프로그램 (예: Redis)이 서버에서 데이터를 전송 하는 것 처럼 네트워크에서 데이터를 읽지 않는다는 것을 의미 합니다.

버스트 시나리오에서 스레드 풀이 신속 하 게 확장 되도록 [`ThreadPool` 설정을 구성할](https://gist.github.com/JonCole/e65411214030f0d823cb) 수 있습니다.

## <a name="high-client-cpu-usage"></a>클라이언트의 높은 CPU 사용량

높은 클라이언트 CPU 사용량은 시스템이 요청 된 작업을 수행할 수 없음을 나타냅니다. 캐시가 응답을 신속 하 게 전송 하더라도 클라이언트는 적절 한 시간 내에 응답을 처리 하지 못할 수 있습니다.

Azure Portal 또는 컴퓨터의 성능 카운터를 통해 사용 가능한 메트릭을 사용 하 여 클라이언트의 시스템 전체 CPU 사용량을 모니터링 합니다. 단일 프로세스의 CPU 사용량이 낮지만 시스템 차원의 CPU가 높을 수 있기 때문에 *프로세스* CPU를 모니터링 하지 않도록 주의 해야 합니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다. 또한 CPU가 높으면 [트래픽 버스트](#traffic-burst) 섹션에 설명 된 대로 `TimeoutException` 오류 메시지에 높은 `in: XXX` 값이 발생할 수 있습니다.

> [!NOTE]
> StackExchange.Redis 1.1.603 이상은 `TimeoutException` 오류 메시지에 `local-cpu` 매트릭을 포함합니다. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
>

클라이언트의 높은 CPU 사용량을 완화 하려면:

- CPU 급증 원인을 조사 합니다.
- 더 많은 CPU 용량을 사용 하 여 더 큰 VM 크기로 클라이언트를 업그레이드 합니다.

## <a name="client-side-bandwidth-limitation"></a>클라이언트 쪽 대역폭 제한

클라이언트 컴퓨터의 아키텍처에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 클라이언트에서 네트워크 용량을 오버 로드 하 여 사용 가능한 대역폭을 초과 하면 서버에서 데이터를 전송 하는 속도 만큼 속도가 클라이언트 쪽에서 처리 되지 않습니다. 이 경우 시간이 초과될 수 있습니다.

[`BandwidthLogger`예제를 ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)사용 하 여 대역폭 사용량이 시간에 따라 어떻게 변경 되는 지 모니터링 합니다. 이 코드는 (Azure 웹 사이트와 같이) 권한이 제한된 일부 환경에서 성공적으로 실행되지 않을 수도 있습니다.

이를 완화 하려면 네트워크 대역폭 사용량을 줄이거나 클라이언트 VM 크기를 네트워크 용량이 더 많은 것으로 늘리십시오.

## <a name="large-request-or-response-size"></a>큰 요청 또는 응답 크기

큰 요청/응답으로 시간 초과가 발생할 수 있습니다. 예를 들어 클라이언트에 구성 된 제한 시간 값이 1 초 라고 가정 합니다. 애플리케이션이 동시에 2개의 키(예: 'A' 및 'B')를 요청합니다(동일한 실제 네트워크 연결 사용). 대부분의 클라이언트는 요청 "파이프라인"을 지원 합니다. 여기에서 요청 ' A '와 ' B '는 응답을 기다리지 않고 다른 요청으로 전송 됩니다. 서버는 응답을 동일한 순서로 전송합니다. ' A ' 응답이 크면 이후 요청에 대한 시간 제한의 대부분을 만들 수 있습니다.

다음 예제에서는 요청 ' A '와 ' B '가 서버에 신속 하 게 전송 됩니다. 서버에서 ' A ' 및 ' B ' 응답을 신속 하 게 보내기 시작 합니다. 데이터 전송 시간 때문에 ' B ' 응답은 서버가 신속 하 게 응답 하는 경우에도 ' A ' 응답 뒤를 기다려야 합니다.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

이 요청/응답은 측정하기 어렵습니다. 클라이언트 코드를 계측 하 여 많은 요청 및 응답을 추적할 수 있습니다.

긴 응답 크기에 대한 해결 방법은 다음과 같습니다.

1. 몇 개의 작은 값이 아닌 많은 수의 작은 값에 대해 응용 프로그램을 최적화 합니다.
    - 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다.
    - [Redis에 대한 이상적인 값 크기 범위는 게시물을 참조 하세요. 100 KB가 너무 큼](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 더 작은 값이 권장 되는 이유에 대한 자세한 내용은을 참조 하십시오.
1. 더 높은 대역폭 기능을 얻기 위해 VM의 크기를 늘립니다.
    - 클라이언트 또는 서버 VM에 더 많은 대역폭이 있으면 큰 응답의 데이터 전송 시간을 줄일 수 있습니다.
    - 두 컴퓨터의 현재 네트워크 사용량을 현재 VM 크기에 대한 제한과 비교해 보세요. 서버에만 또는 클라이언트에만 대역폭을 추가 하면 충분 하지 않을 수 있습니다.
1. 응용 프로그램에서 사용 하는 연결 개체 수를 늘립니다.
    - 라운드 로빈 방식을 사용 하 여 여러 연결 개체에 대한 요청을 수행할 수 있습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
