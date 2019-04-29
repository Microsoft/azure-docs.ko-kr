---
title: Azure Cache for Redis 샘플 | Microsoft Docs
description: Azure Cache for Redis를 사용하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
origin.date: 01/23/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829806"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis 샘플
이 항목에서는 캐시에 연결, 캐시에서 데이터 읽기 및 쓰기, ASP.NET Azure Cache for Redis 공급자 사용과 같은 시나리오를 다루는 Azure Cache for Redis 샘플 목록을 제공합니다. 일부 샘플은 다운로드 가능한 프로젝트이고, 일부 샘플은 단계별 지침을 제공하며 코드 조각을 포함하지만 다운로드 가능한 프로젝트에 연결되지 않습니다.

## <a name="hello-world-samples"></a>Hello world 샘플
이 섹션의 샘플에서는 다양한 언어와 Redis 클라이언트를 사용하여 Azure Cache for Redis 인스턴스에 연결하고 캐시에서 데이터를 읽고 쓰는 방법에 대한 기본 사항을 보여 줍니다.

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 샘플에서는 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET 클라이언트를 사용하여 다양한 캐시 작업을 수행하는 방법을 보여줍니다.

이 샘플에서는 다음 작업을 수행하는 방법을 보여줍니다.

- 다양한 연결 옵션 사용
- 동기 및 비동기 작업을 사용하여 캐시에서 개체 읽기 및 캐시에 개체 쓰기
- Redis MGET/MSET 명령을 사용하여 지정된 키 값 반환
- Redis 트랜잭션 작업 수행
- Redis 목록 및 정렬된 집합 작업
- JsonConvert serializer를 사용하여.NET 개체 저장
- Redis 집합을 사용하여 태그 지정 구현
- Redis 클러스터 작업

자세한 내용은 GitHub의 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 설명서를 참조하고, 추가 사용 시나리오는 [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) 단위 테스트를 참조하세요.

[Python에서 Azure Cache for Redis를 사용하는 방법](cache-python-get-started.md)에서 Python과 [redis-py](https://github.com/andymccurdy/redis-py) 클라이언트를 사용하여 Azure Cache for Redis를 시작하는 방법을 보여 줍니다.

[캐시의 .NET 개체 사용](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)에서 Azure Cache for Redis 인스턴스에서 읽고 쓸 수 있도록 .NET 개체를 직렬화하는 한 가지 방법을 보여 줍니다. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Azure Cache for Redis를 ASP.NET SignalR에 대한 규모 확장 백플레인으로 사용
[Azure Cache for Redis를 ASP.NET SignalR에 대한 규모 확장 백플레인으로 사용](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) 샘플에서 Azure Cache for Redis를 SignalR 백플레인으로 사용하는 방법을 보여 줍니다. 백플레인에 대한 자세한 내용은 [Redis를 사용한 SignalR 규모 확장](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)을 참조하세요.

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache for Redis 고객 쿼리 샘플
이 샘플은 캐시의 데이터 액세스와 영구적 저장소의 데이터 액세스 간 성능 비교를 보여 줍니다. 이 샘플에는 두 개의 프로젝트가 있습니다.

- [Azure Cache for Redis에서 데이터를 캐싱하여 성능을 향상시키는 방법 데모](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
- [데모를 위한 데이터베이스 및 캐시 시드](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET 세션 상태 및 출력 캐싱
[Azure Cache for Redis를 사용하여 ASP.NET SessionState 및 OutputCache 저장](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) 샘플에서 Azure Cache for Redis를 사용하여 Redis용 SessionState 및 OutputCache 공급자를 사용하는 ASP.NET 세션 및 출력 캐시를 저장하는 방법을 보여 줍니다.

## <a name="manage-azure-cache-for-redis-with-maml"></a>MAML에서 Azure Cache for Redis 관리
[Azure Management Libraries를 사용하여 Azure Cache for Redis 관리](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 샘플에서 Azure Management Libraries를 사용하여 캐시를 관리(만들기/업데이트/삭제)하는 방법을 보여 줍니다. 

## <a name="custom-monitoring-sample"></a>사용자 지정 모니터링 샘플
[Azure Cache for Redis 모니터링 데이터 액세스](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 샘플에서 Azure Portal 외부에서 Azure Cache for Redis에 대한 모니터링 데이터에 액세스하는 방법을 보여 줍니다.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>PHP 및 Redis를 사용하여 작성된 Twitter 스타일 클론
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) 샘플은 Redis Hello World입니다. Redis 및 PHP를 사용하여 작성된 최소 Twitter 스타일 소셜 네트워크 클론으로, [Predis](https://github.com/nrk/predis) 클라이언트를 사용합니다. 소스 코드는 매우 간단하며 동시에 서로 다른 Redis 데이터 구조를 보여주도록 설계되었습니다.

## <a name="bandwidth-monitor"></a>대역폭 모니터
[대역폭 모니터](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) 샘플을 사용하면 클라이언트에서 사용 되는 대역폭을 모니터링할 수 있습니다. 대역폭을 측정하려면 캐시 클라이언트 컴퓨터에서 샘플을 실행하고, 캐시를 호출하고, 대역폭 모니터 샘플에서 보고하는 대역폭을 관찰합니다.

<!-- Update_Description: update metedata properties -->

