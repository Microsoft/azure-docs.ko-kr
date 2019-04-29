---
title: 프리미엄 Azure Cache for Redis에 대한 데이터 지속성을 구성하는 방법
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 대한 데이터 지속성을 구성하고 관리하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543503"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>프리미엄 Azure Cache for Redis에 대한 데이터 지속성을 구성하는 방법
Azure Cache for Redis에는 클러스터링, 지속성, 가상 네트워크 지원과 같은 프리미엄 계층 기능을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제안이 있습니다. 이 문서에서는 프리미엄 Azure Cache for Redis에서 지속성을 구성하는 방법에 대해 설명합니다.

다른 프리미엄 캐시 기능에 대한 자세한 내용은 [Azure Cache for Redis 프리미엄 계층 소개](cache-premium-tier-intro.md)를 참조하세요.

## <a name="what-is-data-persistence"></a>데이터 지속성이란?
[Redis 지속성](https://redis.io/topics/persistence)을 사용하면 Redis에 저장된 데이터를 유지할 수 있습니다. 또한 스냅숏을 만들고, 하드웨어 오류 시 로드할 수 있게 데이터를 백업할 수 있습니다. 기본 또는 표준 계층보다 훨씬 큰 이러한 혜택은 모든 데이터가 메모리에 저장되기 때문에 가능하며 캐시 노드 다운 시 데이터 손실 가능성이 있습니다. 

Azure Cache for Redis에서 Redis 지속성을 제공하는 데 사용하는 모델은 다음과 같습니다.

* **RDB 지속성** - RDB(Redis 데이터베이스) 지속성이 구성되면 Azure Cache for Redis에서 구성 가능한 백업 빈도에 따라 Azure Cache for Redis 스냅숏을 Redis 이진 형식으로 디스크에 유지합니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 가장 최근의 스냅숏을 사용하여 캐시를 재구성합니다. RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* **AOF 지속성** - AOF(Append only file) 지속성이 구성되면 Azure Cache for Redis에서 모든 쓰기 작업을 Azure Storage 계정에 초당 1회 이상 저장되는 로그에 저장합니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 저장된 쓰기 작업을 사용하여 캐시를 재구성합니다. AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

지속성은 캐시를 만드는 중에 **새 Azure Cache for Redis** 블레이드에서 구성하거나 기존 프리미엄 캐시에 대한 **리소스 메뉴**에서 구성합니다.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

프리미엄 가격 책정 계층을 선택한 다음 **Redis 지속성**을 클릭합니다.

![Redis 지속성][redis-cache-persistence]

다음 섹션의 단계에서는 새 프리미엄 캐시에서 Redis 지속성을 구성하는 방법에 대해 설명합니다. Redis 지속성이 구성되어 있는 경우 **만들기** 를 클릭하여 Redis 지속성을 사용하는 새 프리미엄 캐시를 만듭니다.

## <a name="enable-redis-persistence"></a>Redis 지속성 사용

Redis 지속성은 **Redis 데이터 지속성** 블레이드에서 **RDB** 또는 **AOF** 지속성을 선택하여 사용하도록 설정합니다. 새 캐시의 경우 위의 섹션에서 설명한 대로 캐시 만들기 프로세스 중 이 블레이드에 액세스합니다. 기존 캐시의 경우 캐시의 **리소스 메뉴** 블레이드에서 **Redis 데이터 지속성** 블레이드에 액세스합니다.

![Redis 설정][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB 지속성 구성

RDB 지속성을 사용하도록 설정하려면 **RDB**를 클릭합니다. 이전에 사용하도록 설정한 프리미엄 캐시에서 Redis 지속성을 사용하지 않도록 설정하려면 **사용 안 함**을 클릭합니다.

![Redis RDB 지속성][redis-cache-rdb-persistence]

백업 간격을 구성하려면 드롭다운 목록에서 **Backup 빈도** 를 선택합니다. **15분**, **30분**, **60분**, **6시간**, **12시간** 및 **24시간** 중에서 선택할 수 있습니다. 이 간격은 이전 백업 작업이 성공적으로 완료되어 새 백업이 시작된 시점부터 계산됩니다.

**Storage 계정**을 클릭하여 사용할 Storage 계정을 선택하고 **스토리지 키** 드롭다운 목록에서 사용할 **기본 키** 또는 **보조 키**를 선택합니다. 캐시와 동일한 영역에 있는 Storage 계정을 선택해야 하며 높은 처리량을 가진 **Premium Storage** 계정을 사용하는 것이 좋습니다. 

> [!IMPORTANT]
> 지속성 계정에 대한 저장소 키가 다시 생성된 경우에는 **저장소 키** 드롭다운에서 원하는 키를 다시 구성해야 합니다.
> 
> 

**확인** 을 클릭하여 지속성 구성을 저장합니다.

백업 빈도 간격이 경과되면 다음 백업(새 캐시의 경우 첫 번째 백업)이 시작됩니다.

## <a name="configure-aof-persistence"></a>AOF 지속성 구성

AOF 지속성을 사용하도록 설정하려면 **AOF**를 클릭합니다. 이전에 사용하도록 설정한 프리미엄 캐시에서 AOF 지속성을 사용하지 않도록 설정하려면 **사용 안 함**을 클릭합니다.

![Redis AOF 지속성][redis-cache-aof-persistence]

AOF 지속성을 구성하려면 **첫 Storage 계정**을 지정합니다. 이 Storage 계정은 캐시와 동일한 영역에 있어야 하며 높은 처리량을 가진 **Premium Storage** 계정을 사용하는 것이 좋습니다. 경우에 따라 **두 번째 Storage 계정**이라는 추가 Storage 계정을 구성할 수 있습니다. 두 번째 저장소 계정이 구성된 경우 복제본 캐시에 대한 쓰기는 이 두 번째 저장소 계정에 기록됩니다. 구성된 각 Storage 계정에 대해 **스토리지 키** 드롭다운에서 사용할 **기본 키** 또는 **보조 키**를 선택합니다. 

> [!IMPORTANT]
> 지속성 계정에 대한 저장소 키가 다시 생성된 경우에는 **저장소 키** 드롭다운에서 원하는 키를 다시 구성해야 합니다.
> 
> 

AOF 지속성을 사용하도록 설정하면 캐시에 대한 쓰기 작업이 지정된 저장소 계정(두 번째 저장소 계정을 구성한 경우 해당 계정도 포함)에 저장됩니다. 주 캐시와 복제본 캐시 둘 다에서 심각한 오류가 발생하면 저장된 AOF 로그가 캐시를 다시 작성하는 데 사용됩니다.

## <a name="persistence-faq"></a>지속성 FAQ
Azure Cache for Redis 지속성에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

* [이전에 만든 캐시에서 지속성을 사용할 수 있나요?](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF 및 RDB 지속성을 동시에 사용할 수 있나요?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [어떤 지속성 모델을 선택해야 하나요?](#which-persistence-model-should-i-choose)
* [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB 지속성
* [캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 지속성
* [두 번째 저장소 계정은 언제 사용해야 하나요?](#when-should-i-use-a-second-storage-account)
* [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [두 번째 저장소 계정은 어떻게 제거할 수 있나요?](#how-can-i-remove-the-second-storage-account)
* [다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [저장소에서 AOF 데이터는 어떤 방식으로 구성되나요?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>이전에 만든 캐시에서 지속성을 사용할 수 있나요?
예, Redis 지속성은 캐시를 만들 때와 기존 프리미엄 캐시에서 모두 구성할 수 있습니다.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF 및 RDB 지속성을 동시에 사용할 수 있나요?

아니요. RDB 또는 AOF 중 하나만 사용하도록 설정할 수 있습니다.

### <a name="which-persistence-model-should-i-choose"></a>어떤 지속성 모델을 선택해야 하나요?

AOF 지속성은 모든 쓰기를 로그에 저장하므로, 구성된 백업 간격을 기준으로 백업을 저장하여 성능에 최소 영향을 미치는 RDB 지속성과 비교할 때 처리량에 영향을 미칩니다. 기본 목표가 데이터 손실을 최소화하는 것이며 캐시에 대한 처리량 감소를 처리할 수 있는 경우에는 AOF 지속성을 선택합니다. 캐시에 대해 최적의 처리량을 유지하려고 하면서도 데이터 복구 메커니즘을 계속 유지하려는 경우에는 RDB 지속성을 선택합니다.

* RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

AOF 지속성을 사용하는 경우 성능에 대한 자세한 내용은 [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)를 참조하세요.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?

RDB 및 AOF 지속성:

* 더 큰 크기로 조정했다면 영향은 없습니다.
* 더 작은 크기를 조정했고 새 크기에 대한 [데이터베이스 제한](cache-configure.md#databases)보다 사용자 지정 [데이터베이스](cache-configure.md#databases) 설정이 더 크다면, 그러한 데이터베이스에 저장된 데이터는 복원되지 않습니다. 자세한 내용은 [사용자 지정 데이터베이스 설정이 크기 조정 동안 영향을 받나요?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* 더 작은 크기로 조정 했고 마지막 백업의 모든 데이터를 저장하기에 충분한 공간이 더 작은 크기에 없는 경우, 일반적으로 [allkeys-lru](https://redis.io/topics/lru-cache) 제거 정책을 사용하여 복원 프로세스 중에 키가 제거됩니다.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?
그렇습니다. **Redis 데이터 지속성** 블레이드에서 RDB 지속성에 대한 백업 빈도를 변경할 수 있습니다. 자세한 내용은 Redis 지속성 구성을 참조하세요.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?
RDB 지속성 백업 간격의 주기는 이전 백업 프로세스가 성공적으로 완료되어야 시작됩니다. 백업 간격이 60분이고 백업 프로세스를 성공적으로 완료하는 데 15분이 걸린다면 다음 백업은 이전 백업 시작 시점에서 75분까지 시작되지 않습니다.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?
가장 최근 백업을 제외한 모든 RDB 지속성 백업은 자동으로 삭제됩니다. 즉시 삭제되지 않을 수 있으나 오래된 백업을 무한정 유지하지는 않습니다.


### <a name="when-should-i-use-a-second-storage-account"></a>두 번째 저장소 계정은 언제 사용해야 하나요?

AOF 지속성이 캐시에 대해 예상된 설정 작업보다 더 높다고 판단되면 AOF 지속성에 대해 두 번째 저장소 계정을 사용해야 합니다.  보조 저장소 계정을 설정하면 캐시가 저장소 대역폭 제한에 도달하지 않도록 하는 데 도움이 됩니다.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?

AOF 지속성은 캐시가 최대 부하보다 낮을 때(CPU 및 서버 부하 둘 다에서 90% 미만) 처리량에 15%-20% 정도 영향을 미칩니다. 캐시가 이러한 한도 내에 있을 때 대기 시간 문제는 없습니다. 그러나 캐시는 AOF가 사용하도록 설정되면 더 빨리 이러한 한도에 도달합니다.

### <a name="how-can-i-remove-the-second-storage-account"></a>두 번째 저장소 계정은 어떻게 제거할 수 있나요?

두 번째 저장소 계정을 첫 번째 저장소 계정과 동일하게 설정하여 AOF 지속성 보조 저장소 계정을 제거할 수 있습니다. 자세한 내용은 [AOF 지속성 구성](#configure-aof-persistence)을 참조하세요.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?

AOF 파일이 충분히 커지면 다시 쓰기가 자동으로 큐에 대기됩니다. 다시 쓰기를 수행하면 현재 데이터 집합을 만드는 데 필요한 작업의 최소 집합을 사용하여 AOF 파일 크기가 다시 조정됩니다. 다시 쓰기 동안, 성능 제한에 더 빠르게 도달하며 큰 데이터 세트를 처리할 때 특히 더 그렇습니다. AOF 파일이 더 커지면 다시 쓰기는 덜 자주 발생하지만 일단 발생하면 많은 시간이 소요됩니다.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?

크기 조정이 완료된 후에 파일이 다시 로드되므로 크기 조정 시 AOF 파일이 아주 커질 경우 크기 조정 작업이 예상한 것보다 더 오래 걸립니다.

크기 조정에 대한 자세한 내용은 [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)를 참조하세요.

### <a name="how-is-my-aof-data-organized-in-storage"></a>저장소에서 AOF 데이터는 어떤 방식으로 구성되나요?

AOF 파일에 저장된 데이터는 저장소에 데이터를 저장하는 성능을 향상시키기 위해 노드당 여러 페이지 Blob으로 구분됩니다. 다음 표에는 각 가격 책정 계층에 사용되는 페이지 Blob 수가 표시됩니다.

| 프리미엄 계층 | Blob |
|--------------|-------|
| P1           | 분할당 4    |
| P2           | 분할당 8    |
| P3           | 분할당 16   |
| P4           | 분할당 20   |

클러스터링을 사용하도록 설정하면 캐시의 각 분할은 이전 표에 표시된 것처럼 자체 페이지 Blob 집합을 갖습니다. 예를 들어 분할이 3개 있는 P2 캐시는 24개 페이지 Blob에 해당 AOF 파일을 분산합니다(분할당 8 Blob, 3개 분할).

다시 쓰기 후 2개의 AOF 파일 집합이 저장소에 존재합니다. 다시 쓰기는 백그라운드에서 발생하며 첫 번째 파일 집합에 추가되지만, 다시 쓰기 동안 캐시로 전송된 설정 작업은 두 번째 집합에 추가됩니다. 오류가 발생한 경우 백업은 다시 쓰기 동안 일시적으로 저장되지만 다시 쓰기가 끝난 후에 즉시 삭제됩니다.


## <a name="next-steps"></a>다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

* [Azure Cache for Redis 프리미엄 계층 소개](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
