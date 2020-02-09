---
title: Redis 용 Azure 캐시에 대한 모범 사례
description: 다음 모범 사례를 수행 하 여 Redis에 대한 Azure Cache를 효과적으로 사용 하는 방법을 알아봅니다.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 71056fd04069b861b37a595b1a4f2a8bba4a01ef
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689970"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Redis 용 Azure 캐시에 대한 모범 사례 
이러한 모범 사례를 따르면 Azure Cache for Redis 인스턴스에 대한 성능 및 비용 효율적 사용을 최대화할 수 있습니다.

## <a name="configuration-and-concepts"></a>구성 및 개념
 * **프로덕션 시스템용 표준 또는 프리미엄 계층을 사용 합니다.**  기본 계층은 데이터 복제가 없고 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다.  C0 캐시는 공유 CPU 코어, 작은 메모리를 포함 하 고 "잡음이 있는 환경" 문제를 발생 시킬 수 있으므로 간단한 개발/테스트 시나리오를 위한 것입니다.

 * **Redis는 메모리 내 데이터 저장소입니다.**  [이 문서에서는](cache-troubleshoot-data-loss.md) 데이터 손실이 발생할 수 있는 몇 가지 시나리오에 대해 간략하게 설명 합니다.

 * [패치 및 장애 조치 (failover)로 인해](cache-failover.md) **연결 문제를 처리할 수 있도록 시스템을 개발** 합니다.

 * **[Maxmemory-예약 된 설정을](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 구성 하 여 메모리 부족 상태에서 시스템 응답성을 개선** 합니다.  쓰기 작업이 많은 워크 로드의 경우 또는 Redis에 더 큰 값 (100 KB 이상)을 저장 하는 경우에는 충분 한 예약 설정이 특히 중요 합니다. 캐시 크기의 10%로 시작 하 고, 쓰기 작업량이 많은 부하가 있는 경우이 백분율을 늘려야 합니다.

 * **Redis는 작은 값으로 가장 적합**하므로 큰 데이터를 여러 키로 분할 하는 것이 좋습니다.  [이 Redis 토론](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)에는 신중 하 게 고려해 야 하는 몇 가지 고려 사항이 나와 있습니다.  큰 값을 사용할 때 야기될 수 있는 문제 예에 대해서는 [이 문서](cache-troubleshoot-client.md#large-request-or-response-size) 를 읽어보세요.

 * **동일한 지역에서 캐시 인스턴스와 응용 프로그램을 찾습니다.**  다른 지역의 캐시에 연결하면 대기 시간이 크게 증가하고 안정성이 떨어질 수 있습니다.  Azure 외부에서 연결할 수 있지만 *특히 Redis를 캐시로 사용*하지 않는 것이 좋습니다.  Redis를 키/값 저장소로 사용 하는 경우에는 대기 시간이 중요 하지 않을 수 있습니다. 

 * **연결을 다시 사용 합니다.**  새 연결을 만들면 비용이 많이 들고 대기 시간이 증가 하므로 가능한 한 많은 연결이 재사용 됩니다. 새 연결을 만들도록 선택 하는 경우 릴리스 전에 이전 연결을 닫아야 합니다 (.NET 또는 Java와 같은 관리 되는 메모리 언어 에서도).

 * 더 높은 CPU 상태 에서도 연결할 수 있도록 시스템 시간을 제공 하 여 **최소 15 초 이상의 *연결 제한* 시간을 사용 하도록 클라이언트 라이브러리를 구성**합니다.  연결 시간 제한 값이 작으면 해당 시간 프레임에 연결이 설정 되는 것을 보장 하지 않습니다.  오류가 발생 한 경우 (높은 클라이언트 CPU, 높은 서버 CPU 등) 연결 시도가 실패 하는 짧은 연결 시간 제한 값이 발생 합니다. 이 동작으로 인해 잘못 된 상황이 발생 하는 경우가 많습니다.  이 작업을 수행 하는 대신 시스템에서 다시 연결을 시도 하는 프로세스를 강제로 다시 시작 하 여 *연결 > 오류 > 다시 시도* 루프가 발생할 수 있도록 하 여 문제를 더 짧은 시간 제한으로 악화. 일반적으로 연결 제한 시간은 15 초 이상으로 유지 하는 것이 좋습니다. 잠시 후에만 다시 시도 하는 것 보다 15 초 또는 20 초 후에 연결이 성공 하도록 하는 것이 좋습니다. 이러한 다시 시도 루프는 처음에 시스템이 더 오래 걸리는 경우 보다 오래 지속 될 수 있습니다.  
     > [!NOTE]
     > 이 지침은 *연결 시도* 와 관련 된 것 이며 GET 또는 SET complete와 같은 *작업* 을 기다리는 시간과 관련 되지 않습니다.
 
 * **비용이 많이 드는 작업을 방지** 합니다. [키](https://redis.io/commands/keys) 명령과 같은 일부 Redis 작업은 비용이 *매우* 많이 들고 사용 하지 않아야 합니다.  자세한 내용은 [장기 실행 명령](cache-troubleshoot-server.md#long-running-commands) 에 대한 몇 가지 고려 사항을 참조 하세요.

 * **Tls 암호화 사용** -Azure Cache for Redis는 기본적으로 TLS 암호화 통신이 필요 합니다.  TLS 버전 1.0, 1.1 및 1.2는 현재 지원 됩니다.  그러나 TLS 1.0 및 1.1은 업계 전체를 사용 하지 않는 경로에 있으므로 가능 하면 TLS 1.2를 사용 합니다.  클라이언트 라이브러리 또는 도구에서 TLS를 지원 하지 않는 경우 Azure Portal 또는 [관리 api](https://docs.microsoft.com/rest/api/redis/redis/update) [를 통해](cache-configure.md#access-ports) 암호화 되지 않은 연결을 사용 하도록 설정할 수 있습니다.  암호화 된 연결을 사용할 수 없는 경우에는 캐시와 클라이언트 응용 프로그램을 가상 네트워크에 배치 하는 것이 좋습니다.  가상 네트워크 캐시 시나리오에서 사용 되는 포트에 대한 자세한 내용은 다음 [표](cache-how-to-premium-vnet.md#outbound-port-requirements)를 참조 하세요.
 
## <a name="memory-management"></a>메모리 관리
Redis server 인스턴스 내의 메모리 사용량과 관련 된 몇 가지 사항이 있습니다.  몇 가지 예를 들면 다음과 같습니다.

 * **응용 프로그램에 대해 작동 하는 [제거 정책을](https://redis.io/topics/lru-cache) 선택 합니다.**  Azure Redis에 대한 기본 정책은 *휘발성-lru*입니다. 즉, TTL 값이 설정 된 키만 제거 될 수 있습니다.  키에 TTL 값이 없는 경우 시스템에서 키를 제거 하지 않습니다.  메모리가 부족할 때 시스템에서 키를 제거할 수 있도록 하려면 *allkeys-lru* 정책을 고려할 수 있습니다.

 * **키에 만료 값을 설정 합니다.**  만료는 메모리가 부족할 때까지 기다리지 않고 사전에 키를 제거 합니다.  메모리가 부족 하 여 제거가 시작 되 면 서버에 대한 추가 부하가 발생할 수 있습니다.  자세한 내용은 [만료](https://redis.io/commands/expire) 및 [EXPIREAT](https://redis.io/commands/expireat) 명령에 대한 설명서를 참조 하세요.
 
## <a name="client-library-specific-guidance"></a>클라이언트 라이브러리 관련 지침
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-어떤 클라이언트를 사용 해야 하나요?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [(Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 세션 상태 공급자](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>언제 다시 시도 해도 되나요?
아쉽게도 답은 없습니다.  각 응용 프로그램은 다시 시도할 수 있는 작업과 수행할 수 없는 작업을 결정 해야 합니다.  각 작업에는 서로 다른 요구 사항과 키 간 종속성이 있습니다.  다음은 고려할 수 있는 몇 가지 사항입니다.

 * Redis에서 실행 하도록 요청한 명령을 성공적으로 실행 한 경우에도 클라이언트 쪽 오류를 가져올 수 있습니다.  예:
     - 시간 제한은 클라이언트 쪽 개념입니다.  작업이 서버에 도달 하면 클라이언트가 대기 상태를 제공 하는 경우에도 서버에서 명령을 실행 합니다.  
     - 소켓 연결에서 오류가 발생 하는 경우 작업이 서버에서 실제로 실행 되었는지 여부를 알 수 없습니다.  예를 들어 서버에서 요청을 처리 한 후 클라이언트에서 응답을 받기 전에 연결 오류가 발생할 수 있습니다.
 *  실수로 동일한 작업을 두 번 실행 하는 경우 응용 프로그램이 어떻게 반응 하나요?  예를 들어 정수를 한 번이 아닌 두 번 증가 시키는 경우는 어떻게 되나요?  응용 프로그램이 여러 위치에서 동일한 키에 쓰고 있나요?  내 재시도 논리가 내 앱의 다른 부분에 의해 설정 된 값을 덮어쓰는 경우는 어떻게 되나요?

오류 조건에서 코드가 작동 하는 방식을 테스트 하려면 [다시 부팅 기능](cache-administration.md#reboot)을 사용 하는 것이 좋습니다. 다시 부팅 하면 연결 문제 응용 프로그램에 미치는 영향을 확인할 수 있습니다.

## <a name="performance-testing"></a>성능 테스트
 * 먼저 **`redis-benchmark.exe`를 사용 하 여** 사용자 고유의 성능 테스트를 작성 하기 전에 가능한 처리량/대기 시간에 대한 느낌을 얻으십시오.  Redis 벤치 마크 설명서는 [여기에서 찾을](https://redis.io/topics/benchmarks)수 있습니다.  Redis 벤치 마크는 SSL을 지원 하지 않으므로 테스트를 실행 하기 전에 [포털을 통해 비 ssl 포트를 사용 하도록 설정](cache-configure.md#access-ports) 해야 합니다.  [Redis-benchmark.exe의 windows 호환 버전은 여기에서 찾을 수 있습니다.](https://github.com/MSOpenTech/redis/releases)
 * 테스트에 사용 되는 클라이언트 VM은 Redis cache 인스턴스와 **동일한 지역에** 있어야 합니다.
 * 더 나은 하드웨어를 제공 하 고 최상의 결과를 제공 하므로 클라이언트에 **DV2 VM 시리즈를 사용 하는 것이 좋습니다** .
 * 사용 하는 클라이언트 VM에는 테스트 중인 캐시와*최소한 많은 계산 및 대역폭이* 있는지 확인 합니다. 
 * Windows를 사용 하는 경우 클라이언트 컴퓨터에서 **VRSS를 사용 하도록 설정** 합니다.  [자세한 내용을 보려면 여기를 참조하세요](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Powershell 스크립트 예:
     >PowerShell-Set-executionpolicy Set-netadapterrss-Name (Get-netadapter)을 사용 합니다. 이름의 
     
 * **프리미엄 계층 Redis 인스턴스를 사용 하는 것이 좋습니다**.  이러한 캐시 크기는 CPU 및 네트워크 모두에 대해 더 나은 하드웨어에서 실행 되므로 네트워크 대기 시간 및 처리량이 향상 됩니다.
 
     > [!NOTE]
     > 여기에서 관찰 된 성능 결과가 [여기에 게시](cache-faq.md#azure-cache-for-redis-performance) 됩니다.   또한 SSL/TLS는 약간의 오버 헤드를 추가 하 여 전송 암호화를 사용 하는 경우 다른 대기 시간 및/또는 처리량을 얻을 수 있습니다.
 
### <a name="redis-benchmark-examples"></a>Redis 벤치 마크 예제
**사전 테스트 설정**: 아래 나열 된 대기 시간 및 처리량 테스트 명령에 필요한 데이터를 사용 하 여 캐시 인스턴스를 준비 합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**대기 시간 테스트**: 1k 페이로드를 사용 하 여 GET 요청을 테스트 합니다.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**처리량을 테스트 하려면:** 1k 페이로드를 포함 하는 파이프라인 GET 요청
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
