---
title: Redis 개발용 Azure Cache Faq
description: Redis 용 Azure Cache를 개발 하는 데 도움이 되는 일반적인 질문에 대 한 답변을 알아보세요.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: ef85b6f9e4595e7b4ff367da415fad777de68679
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211303"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Redis 개발용 Azure Cache Faq

이 문서에서는 Redis 용 Azure Cache를 개발 하는 방법에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.

## <a name="common-questions-and-answers"></a>일반적인 질문 및 답변
이 섹션에서는 다음 Faq를 다룹니다.

* [Azure Cache for Redis를 시작하려면 어떻게 해야 하나요?](#how-can-i-get-started-with-azure-cache-for-redis)
* [StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?](#what-do-the-stackexchangeredis-configuration-options-do)
* [사용할 수 있는 Azure Cache for Redis 클라이언트는 어떻게 되나요?](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis에 대한 로컬 에뮬레이터가 있나요?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [어떻게 Redis 명령을 실행할 수 있나요?](#how-can-i-run-redis-commands)
* [Azure Cache for Redis에 MSDN 클래스 라이브러리 참조가 없는 이유는 무엇 인가요?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Azure Cache for Redis는 PHP 세션 캐시로 사용할 수 있나요?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redis 데이터베이스란?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Azure Cache for Redis를 시작하려면 어떻게 해야 하나요?
Azure Cache for Redis는 몇 가지 방법으로 시작할 수 있습니다.

* [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) 및 [Python](cache-python-get-started.md)에 대해 사용할 수 있는 자습서 중 하나를 확인해볼 수 있습니다.
* [Microsoft Azure Cache for Redis를 사용하여 고성능 앱을 빌드하는 방법](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)을 참조할 수 있습니다.
* Redis 사용 방법을 확인하려면 사용 중인 프로젝트의 개발 언어와 일치하는 클라이언트용 클라이언트 설명서를 확인해 보세요. Azure Cache for Redis에서 사용할 수 있는 많은 Redis 클라이언트가 있습니다. Redis 클라이언트 목록은 [https://redis.io/clients](https://redis.io/clients)에서 참조할 수 있습니다.

Azure 계정이 없는 경우 다음을 수행할 수 있습니다.

* [Azure 계정을 무료로 개설할 수 있습니다](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 됩니다. 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스 및 기능을 사용할 수 있습니다.
* [Visual Studio 구독자 혜택 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero) MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 구성 옵션은 어떤 기능을 수행하나요?
StackExchange.Redis에는 많은 옵션이 있습니다. 이 섹션에서는 몇 가지 일반적인 설정에 대해 설명합니다. StackExchange.Redis 옵션에 대한 자세한 내용은 [StackExchange.Redis 구성](https://stackexchange.github.io/StackExchange.Redis/Configuration)을 참조하세요.

| ConfigurationOptions | Description | 권장 |
| --- | --- | --- |
| AbortOnConnectFail |true로 설정하면 네트워크 오류가 발생한 후 연결이 다시 연결되지 않습니다. |false로 설정하여 StackExchange.Redis가 자동으로 다시 연결하도록 합니다. |
| ConnectRetry |초기 연결 중에 연결 시도를 반복할 횟수입니다. |지침은 다음 사항을 참조하세요. |
| ConnectTimeout |연결 작업의 시간 제한(ms)입니다. |지침은 다음 사항을 참조하세요. |

보통은 클라이언트의 기본값으로 충분합니다. 워크로드에 따라 옵션을 미세 조정할 수 있습니다.

* **재시도**
  * ConnectRetry 및 ConnectTimeout에 대한 일반적인 지침은 페일 패스트 및 다시 시도입니다. 이 지침은 클라이언트가 Redis 명령을 실행하고 응답을 수신하는 데 걸리는 평균 시간 및 워크로드에 따라 달라집니다.
  * 연결 상태를 확인하고 직접 다시 연결하는 대신 StackExchange.Redis가 자동으로 다시 연결하도록 합니다. **ConnectionMultiplexer.IsConnected 속성을 사용하지 마세요**.
  * 때로는 사용자가 다시 시도하면 사태가 더욱 심각해지고 결코 복구되지 않는 문제가 발생할 수도 있습니다. 이 경우 Microsoft Patterns & Practices 그룹이 게시한 [다시 시도 일반 지침](../best-practices-retry-general.md)의 설명에 따라 지수 백오프 다시 시도 알고리즘 사용을 고려해야 합니다.
  
* **시간 제한 값**
  * 작업을 고려하여 적절하게 값을 설정합니다. 큰 값을 저장하는 경우 시간 제한을 더 큰 값으로 설정합니다.
  * `AbortOnConnectFail`을 false로 설정하여 StackExchange.Redis가 다시 연결하도록 합니다.
  * 애플리케이션에 단일 ConnectionMultiplexer 인스턴스를 사용합니다. [ConnectionMultiplexer 클래스를 사용하여 캐시에 연결](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)에 표시된 대로, LazyConnection을 사용하여 Connection 속성에 의해 반환되는 단일 인스턴스를 만들 수 있습니다.
  * 진단 목적으로 `ConnectionMultiplexer.ClientName` 속성을 앱 인스턴스 고유 이름으로 설정합니다.
  * 사용자 지정 작업에 여러 개의 `ConnectionMultiplexer` 인스턴스를 사용합니다.
      * 애플리케이션에 다양한 부하가 있는 경우 이 모델을 따를 수 있습니다. 다음은 그 예입니다.
      * 큰 키를 처리하기 위한 멀티플렉서 1개가 있습니다.
      * 작은 키를 처리하기 위한 멀티플렉서 1개가 있습니다.
      * 사용하는 각 ConnectionMultiplexer의 연결 시간 제한 및 다시 시도 논리에 대해 다른 값을 설정할 수 있습니다.
      * 진단에 도움이 되도록 각 멀티플렉서의 `ClientName` 속성을 설정합니다.
      * 이 지침을 따르면 `ConnectionMultiplexer`당 대기 시간이 감소할 수 있습니다.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>사용할 수 있는 Azure Cache for Redis 클라이언트는 어떻게 되나요?
Redis의 장점 중 하나는 여러 가지 개발 언어를 지원하는 많은 클라이언트가 있다는 것입니다. 클라이언트의 현재 목록에 대해서는 [Redis 클라이언트](https://redis.io/clients)를 참조하세요. 여러 다른 언어 및 클라이언트를 포함하는 자습서에 대해서는 [Azure Cache for Redis 사용 방법](cache-dotnet-how-to-use-azure-redis-cache.md)과 목차에 포함된 관련 문서를 참조하세요.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 로컬 에뮬레이터가 있나요?
Azure Cache for Redis에 대한 로컬 에뮬레이터는 없지만 다음 예제와 같이 로컬 머신의 [Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/)에서 redis-server.exe의 MSOpenTech 버전을 실행하고 이에 연결하여 로컬 캐시 에뮬레이터와 비슷한 환경을 얻을 수 있습니다.

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

원하는 경우 선택적으로 온라인 Azure Cache for Redis의 [기본 캐시 설정](cache-configure.md#default-redis-server-configuration)과 더 가깝게 일치하도록 [redis.conf](https://redis.io/topics/config) 파일을 구성할 수 있습니다.

### <a name="how-can-i-run-redis-commands"></a>어떻게 Redis 명령을 실행할 수 있나요?
[Azure Cache for Redis에서 지원되지 않는 Redis 명령](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)에 나열된 명령을 제외하고는 [Redis 명령](https://redis.io/commands#)에 나열된 명령 중 하나를 사용할 수 있습니다. Redis 명령을 실행하는 여러 가지 옵션이 있습니다.

* 표준 또는 프리미엄 캐시를 사용하는 경우 [Redis 콘솔](cache-configure.md#redis-console)을 사용하여 Redis 명령을 실행할 수 있습니다. Redis 콘솔은 Azure Portal에서 Redis 명령을 안전하게 실행하는 방법을 제공합니다.
* Redis 명령줄 도구를 사용할 수도 있습니다. 이 도구를 사용하려면 다음 단계를 수행합니다.
* [Redis 명령줄 도구](https://github.com/MSOpenTech/redis/releases/)를 다운로드합니다.
* `redis-cli.exe`를 사용하여 캐시에 연결합니다. 다음 예제와 같이 -h 스위치를 사용하여 캐시 엔드포인트를 전달하고 -a를 사용하여 키를 전달합니다.
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis 명령줄 도구는 TLS 포트에서 작동하지 않지만, [Azure Cache for Redis에서 Redis 명령줄 도구를 사용하는 방법](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) 문서의 지침에 따라 `stunnel`과 같은 유틸리티를 사용하여 도구를 TLS 포트에 안전하게 연결할 수 있습니다.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Azure Cache for Redis에 MSDN 클래스 라이브러리 참조가 없는 이유는 무엇 인가요?
Redis에 대 한 캐시 Microsoft Azure는 인기 있는 오픈 소스 메모리 내 데이터 저장소 Redis를 기반으로 합니다. 여러 프로그래밍 언어를 위한 다양한 [Redis 클라이언트](https://redis.io/clients)에서 액세스할 수 있습니다. 각 클라이언트에는 [Redis 명령](https://redis.io/commands)을 사용하여 Azure Cache for Redis 인스턴스를 호출하는 자체 API가 있습니다.

클라이언트마다 다르기 때문에 MSDN에 하나의 중앙 집중식 클래스 참조는 없고, 각 클라이언트가 자체 참조 설명서를 유지 관리합니다. 참조 설명서 외에도 다양한 언어와 캐시 클라이언트를 사용하여 Azure Cache for Redis를 시작하는 방법을 보여 주는 몇 가지 자습서가 있습니다. 이 자습서에 액세스하려면 [Azure Cache for Redis 사용 방법](cache-dotnet-how-to-use-azure-redis-cache.md)과 목차에 포함된 관련 문서를 참조하세요.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Azure Cache for Redis는 PHP 세션 캐시로 사용할 수 있나요?
예, Azure Cache for Redis를 PHP 세션 캐시로 사용하려면 `session.save_path`에서 Azure Cache for Redis 인스턴스에 대한 연결 문자열을 지정합니다.

> [!IMPORTANT]
> Azure Cache for Redis를 PHP 세션 캐시로 사용하는 경우 다음 예제와 같이 캐시에 연결하는 데 사용되는 보안 키를 URL로 인코딩해야 합니다.
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> 키가 URL로 인코드되지 않으면 `Failed to parse session.save_path`와 비슷한 메시지와 함께 예외가 발생할 수 있습니다.
>

PhpRedis 클라이언트에서 Azure Cache for Redis를 PHP 세션 캐시로 사용하는 방법에 대한 자세한 내용은 [PHP 세션 처리기](https://github.com/phpredis/phpredis#php-session-handler)를 참조하세요.

### <a name="what-are-redis-databases"></a>Redis 데이터베이스란?

Redis 데이터베이스는 동일한 Redis 인스턴스 내에 있는 데이터를 논리적으로 분리한 단위입니다. 캐시 메모리는 모든 데이터베이스와 해당 데이터베이스에 저장된 키/값에 따라 지정된 데이터베이스의 실제 메모리 소비 간에 공유됩니다. 예를 들어 C6 캐시에는 53GB, P5에는 120GB의 메모리가 있습니다. 하나의 데이터베이스에 53GB/120GB를 모두 사용하거나 여러 데이터베이스 간에 분할할 수 있습니다. 

> [!NOTE]
> 클러스터링을 사용하도록 설정된 프리미엄 Azure Cache for Redis를 사용하는 경우 데이터베이스 0만 사용할 수 있습니다. 이 제한은 본질적인 Redis 제한이므로 Azure Cache for Redis에만 한정되지는 않습니다. 자세한 내용은 [클라이언트 응용 프로그램에서 클러스터링을 사용 하도록 변경 해야 하나요?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)를 참조 하세요.
> 
> 

## <a name="next-steps"></a>다음 단계

[Redis faq 용 다른 Azure Cache](cache-faq.md)에 대해 알아봅니다.
