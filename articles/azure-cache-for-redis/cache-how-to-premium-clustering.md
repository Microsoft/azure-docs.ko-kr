---
title: 프리미엄 Azure Cache for Redis에 대한 Redis 클러스터링을 구성하는 방법 | Microsoft Docs
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 대한 Redis 클러스터링을 만들고 관리하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541389"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>프리미엄 Azure Cache for Redis에 대한 Redis 클러스터링을 구성하는 방법
Azure Cache for Redis에는 클러스터링, 지속성, 가상 네트워크 지원과 같은 프리미엄 계층 기능을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제안이 있습니다. 이 문서에서는 프리미엄 Azure Cache for Redis에서 클러스터링을 구성하는 방법에 대해 설명합니다.

다른 프리미엄 캐시 기능에 대한 자세한 내용은 [Azure Cache for Redis 프리미엄 계층 소개](cache-premium-tier-intro.md)를 참조하세요.

## <a name="what-is-redis-cluster"></a>Redis 클러스터란?
Azure Cache for Redis는 [Redis에서 구현된](https://redis.io/topics/cluster-tutorial) Redis 클러스터를 제공합니다. Redis 클러스터를 사용하면 다음과 같은 이점을 얻을 수 있습니다. 

* 여러 노드 간에 자동으로 데이터 세트를 분할하는 기능. 
* 노드의 하위 집합에서 오류가 발생하거나 나머지 클러스터와 통신할 수 없더라도 작업을 계속하는 기능. 
* 처리량 증가: 분할을 늘릴수록 처리량이 선형으로 늘어납니다. 
* 메모리 크기 증가: 분할을 늘릴수록 메모리가 선형으로 늘어납니다.  

클러스터링은 클러스터형 캐시에 사용할 수 있는 연결 수를 늘리지 않습니다. 프리미엄 캐시의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)를 참조하세요.

Azure에서 Redis 클러스터는 주/복제본 모델로 제공됩니다. 이 경우 각 분할에는 Azure Cache for Redis 서비스에서 관리하는 복제가 있는 주/복제 쌍이 있습니다. 

## <a name="clustering"></a>클러스터링
클러스터링은 캐시를 만드는 중에 **새 Azure Cache for Redis** 블레이드에서 사용하도록 설정합니다. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

클러스터링은 **Redis 클러스터** 블레이드에 구성됩니다.

![클러스터링][redis-cache-clustering]

클러스터에서 최대 10개의 분할된 데이터베이스를 사용할 수 있습니다. **사용**을 클릭하고 슬라이더를 밀거나 **분할 개수**에 1에서 10 사이의 숫자를 입력하고 **확인**을 클릭합니다.

각각의 분할된 데이터베이스는 Azure에서 관리하는 주/복제본 캐시 쌍이며, 캐시의 총 크기는 분할된 데이텁베이스 수에 가격 책정 계층에서 선택한 캐시 크기를 곱하여 산출합니다. 

![클러스터링][redis-cache-clustering-selected]

일단 캐시가 생성되면 이 캐시에 연결하여 클러스터되지 않은 캐시처럼 사용할 수 있으며, Redis에서 캐시 분할 데이터베이스 전체에 데이터를 배포합니다. 진단이 [사용](cache-how-to-monitor.md#enable-cache-diagnostics)으로 설정되면 메트릭이 각 분할별로 개별적으로 캡처되며 Azure Cache for Redis 블레이드에 [표시](cache-how-to-monitor.md)됩니다. 

> [!NOTE]
> 
> 클러스터링을 구성할 때 클라이언트 애플리케이션에 필요한 몇 가지 사소한 차이점이 있습니다. 자세한 내용은 [클러스터링을 사용하려면 클라이언트 애플리케이션을 변경해야 합니까?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

StackExchange.Redis 클라이언트를 통해 클러스터링으로 작업하는 샘플 코드의 경우 [Hello World](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 샘플의 [clustering.cs](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 부분을 참조하세요.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>실행 중인 프리미엄 캐시의 클러스터 크기 변경
클러스터링을 사용하도록 설정된 실행 중인 프리미엄 캐시에서 클러스터 크기를 변경하려면 **리소스 메뉴**에서 **Redis 클러스터 크기**를 클릭합니다.

> [!NOTE]
> Azure Cache for Redis 프리미엄 계층은 일반 공급되고 있지만, Redis 클러스터 크기 기능은 현재 미리 보기로 있습니다.
> 
> 

![Redis 클러스터 크기][redis-cache-redis-cluster-size]

클러스터 크기를 변경하려면 슬라이더를 사용하거나 **분할된 데이터베이스 수** 텍스트 상자에 1에서 10 사이의 수를 입력하고 **확인**을 클릭하여 저장합니다.

클러스터 크기를 늘리면 최대 처리량 및 캐시 크기가 증가합니다. 클러스터 크기를 늘리더라도 클라이언트가 사용할 수 있는 최대 연결 수는 증가하지는 않습니다.

> [!NOTE]
> 클러스터를 확장하면 비용이 많이 드는 [MIGRATE](https://redis.io/commands/migrate) 명령이 실행되므로 영향을 최소화하도록 사용량이 많지 않은 시간 동안에 이 작업을 실행하는 것이 좋습니다. 마이그레이션 프로세스 중에 서버 부하에 스파이크가 나타납니다. 클러스터 확장은 장기 실행 프로세스이며, 소요 시간은 키 수 및 해당 키와 관련된 값의 크기에 따라 달라집니다.
> 
> 

## <a name="clustering-faq"></a>클러스터링 FAQ
Azure Cache for Redis 클러스터링에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

* [클라이언트 애플리케이션에 변경 사항이 필요하여 클러스터링을 사용해야 합니까?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [클러스터에서 키를 분산하는 방법](#how-are-keys-distributed-in-a-cluster)
* [만들 수 있는 최대 캐시 크기는?](#what-is-the-largest-cache-size-i-can-create)
* [모든 Redis 클라이언트가 클러스터링을 지원하나요?](#do-all-redis-clients-support-clustering)
* [클러스터링을 사용할 때 캐시에 어떻게 연결하나요?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [내 케시의 분할된 데이터베이스에 직접 연결할 수 있나요?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [이전에 만든된 캐시에 대해 클러스터링을 구성할 수 있나요?](#can-i-configure-clustering-for-a-previously-created-cache)
* [기본 또는 표준 캐시에 클러스터링을 구성할 수 있나요?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Redis ASP.NET 세션 상태 및 출력 캐싱 공급자와 함께 클러스터링을 사용할 수 있나요?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [StackExchange.Redis를 사용하고 클러스터링을 수행할 때 MOVE 예외가 발생합니다. 어떻게 해야 하나요?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>클라이언트 애플리케이션에 변경 사항이 필요하여 클러스터링을 사용해야 합니까?
* 클러스터링 사용하는 경우 데이터베이스 0만을 사용할 수 있습니다. 클라이언트 애플리케이션이 여러 데이터베이스를 사용하고 0이 아닌 데이터베이스에 읽기 또는 쓰기를 시도하는 경우 다음과 같은 예외가 발생합니다. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  자세한 내용은 [Redis 클러스터 사양 - 구현된 하위 집합](https://redis.io/topics/cluster-spec#implemented-subset)을 참조하세요.
* [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)를 사용하는 경우 1.0.481 이상을 사용해야 합니다. 클러스터링을 사용하지 않는 캐시에 연결할 때와 동일한 [엔드포인트, 포트 및 키](cache-configure.md#properties) 를 사용하여 캐시에 연결합니다. 유일한 차이점은 데이터베이스 0에 모든 읽기 및 쓰기를 수행해야 한다는 점입니다.
  
  * 다른 클라이언트는 다른 요구 사항이 있을 수 있습니다.  [모든 Redis 클라이언트가 클러스터링을 지원하나요?](#do-all-redis-clients-support-clustering)
* 애플리케이션이 단일 명령으로 배치되는 다중 키 작업을 사용하면 동일한 분할에 모든 키가 위치해야 합니다. 동일한 분할된 데이터베이스에서 키를 찾으려면 [클러스터에서 키를 분산하는 방법](#how-are-keys-distributed-in-a-cluster)을 참조하세요.
* Redis ASP.NET 세션 상태 제공자를 사용하는 경우 2.0.1 이상을 사용해야 합니다.  [Redis ASP.NET 세션 상태 및 출력 캐싱 공급자와 함께 클러스터링을 사용할 수 있나요?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>클러스터에서 키를 분산하는 방법
키스페이스는 Redis [키 배포 모델](https://redis.io/topics/cluster-spec#keys-distribution-model) 설명서에 따라 16,384개의 슬롯으로 분할됩니다. 각 키는 이러한 슬롯 중 하나에 해시되고 할당되며 클러스터의 노드에 분산됩니다. 키의 어느 부분이 해시되는지 구성하여 여러 키가 해시 태그를 사용하여 동일한 분할에 위치하도록 합니다.

* 해시 태그가 있는 키 - 키의 모든 부분이 `{` 및 `}`로 묶인 경우 키의 해당 부분에만 키의 해시 슬롯을 결정하는 용도로 해시됩니다. 예를 들어 다음 3개의 키는 동일한 분할에 위치합니다. 이름의 `key` 부분이 해시되기 때문에 `{key}1`, `{key}2` 및 `{key}3`입니다. 키 해시 태그 사양의 전체 목록은 [키 해시 태그](https://redis.io/topics/cluster-spec#keys-hash-tags)를 참조하세요.
* 해시 태그 없는 키 - 전체 키 이름은 해시하는 데 사용됩니다. 그러면 캐시의 분할에 통계적으로 균일하게 배포됩니다.

최상의 성능 및 처리량의 경우 키를 고르게 분산하는 것이 좋습니다. 해시 태그로 키를 사용하는 경우 키가 균등하게 분산되도록 하는 것은 애플리케이션이 담당합니다.

자세한 내용은 [키 배포 모델](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis 클러스터 데이터 분할](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) 및 [키 해시 태그](https://redis.io/topics/cluster-spec#keys-hash-tags)를 참조하세요.

StackExchange.Redis 클라이언트를 통해 동일한 분할된 데이터베이스에서 키를 클러스터링하고 찾아서 작업하는 샘플 코드의 경우 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 샘플의 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 부분을 참조하세요.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>만들 수 있는 최대 캐시 크기는?
가장 큰 프리미엄 캐시 크기는 53GB입니다. 최대 10개의 분할된 데이터베이스를 만들 수 있으므로 530GB의 최대 크기를 제공합니다. 더 큰 크기가 필요한 경우 [추가 요청](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)이 가능합니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.

### <a name="do-all-redis-clients-support-clustering"></a>모든 Redis 클라이언트가 클러스터링을 지원하나요?
현재 일부 클라이언트가 Redis 클러스터링을 지원합니다. 이를 지원하는 클라이언트는 StackExchange.Redis입니다. 다른 클라이언트에 대한 자세한 내용은 [Redis 클러스터 자습서](https://redis.io/topics/cluster-tutorial)의 [클러스터 작업](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) 섹션을 참조하세요. 

Redis 클러스터링 프로토콜에 따르면 각 클러스터는 클러스터링 모드에서 분할된 각 데이터베이스에 직접 연결해야 합니다. 클러스터링을 지원하지 않는 클라이언트를 사용하려고 하면 많은 [MOVED 리디렉션 예외](https://redis.io/topics/cluster-spec#moved-redirection)가 발생할 수 있습니다.

> [!NOTE]
> StackExchange.Redis를 클라이언트로 사용하는 경우 클러스터링이 제대로 작동할 수 있게 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 이상의 최신 버전을 사용합니다. move 예외에 문제가 있을 경우 자세한 내용은 [move 예외](#move-exceptions) 를 참조하세요.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>클러스터링을 사용할 때 캐시에 어떻게 연결하나요?
클러스터링을 사용하지 않는 캐시에 연결할 때와 동일한 [엔드포인트](cache-configure.md#properties), [포트](cache-configure.md#properties) 및 [키](cache-configure.md#access-keys)를 사용하여 캐시에 연결할 수 있습니다. Redis가 백엔드에서 클러스터링을 관리하므로 클라이언트에서의 관리가 필요하지 않습니다.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>내 케시의 분할된 데이터베이스에 직접 연결할 수 있나요?
클러스터링 프로토콜에 따르면 클라이언트는 올바른 분할된 데이터베이스 연결을 설정해야 합니다. 따라서 클라이언트는 이 작업을 올바르게 수행해야 합니다. 즉 각각의 분할된 데이터베이스는 캐시 인스턴스로 통칭되는 주/복제본 캐시로 구성됩니다. GitHub에서 Redis 리포지토리의 [불안정한](https://redis.io/download) 분기에서 redis-cli 유틸리티를 사용하여 이러한 캐시 인스턴스에 연결할 수 있습니다. 이 버전에는 `-c` 스위치로 시작한 경우 기본 지원을 구현합니다. 자세한 내용은 [Redis 클러스터 자습서](https://redis.io/topics/cluster-tutorial)([https://redis.io](https://redis.io))의 [클러스터 작업](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)을 참조하세요.

비 SSL은 다음 명령을 사용합니다.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

SSL에서는 `1300N`을 `1500N`으로 대체합니다.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>이전에 만든된 캐시에 대해 클러스터링을 구성할 수 있나요?
현재 캐시를 만들 때만 클러스터링을 사용하도록 설정할 수 있습니다. 캐시를 만든 후 분할된 클러스터 크기를 변경할 수 있지만 캐시를 만든 후 프리미엄 캐시에 클러스터링을 추가하거나 프리미엄 캐시에서 클러스터링을 제거할 수는 없습니다. 클러스터링이 사용하도록 설정되고 분할된 데이터베이스가 하나뿐인 프리미엄 캐시는 클러스터링이 없는 동일한 크기의 프리미엄 캐시와 다릅니다.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>기본 또는 표준 캐시에 클러스터링을 구성할 수 있나요?
클러스터링은 프리미엄 캐시에만 사용할 수 있습니다.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Redis ASP.NET 세션 상태 및 출력 캐싱 공급자와 함께 클러스터링을 사용할 수 있나요?
* **Redis 출력 캐시 공급자** - 변경이 필요하지 않습니다.
* **Redis 세션 상태 제공자** -클러스터링을 사용하기 위해 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 이상을 사용하지 않으면 예외가 발생합니다. 주요 변경 내용입니다. 자세한 내용은 [v2.0.0 주요 변경 세부 사항](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)을 참조하세요.

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>StackExchange.Redis를 사용하고 클러스터링을 수행할 때 MOVE 예외가 발생합니다. 어떻게 해야 하나요?
StackExchange.Redis를 사용하고 있으며 클러스터링을 사용할 때 `MOVE` 예외가 발생하는 경우 [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) 이상을 사용하고 있는지 확인합니다. StackExchange.Redis를 사용하도록 .NET 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.

## <a name="next-steps"></a>다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

* [Azure Cache for Redis 프리미엄 계층 소개](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







