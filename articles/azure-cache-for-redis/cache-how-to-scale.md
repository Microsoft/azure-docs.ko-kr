---
title: Azure Cache for Redis 크기를 조정하는 방법
description: Azure Portal 및 Azure PowerShell, Azure CLI 등의 도구를 사용 하 여 Redis 인스턴스에 대 한 Azure 캐시 크기를 조정 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/11/2017
ms.openlocfilehash: e780ef0b82240ac6771059f8bd239b90395135d9
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213340"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Azure Cache for Redis 크기를 조정하는 방법
Azure Cache for Redis에는 캐시 크기와 기능을 유연하게 선택할 수 있는 다양한 캐시 제안이 있습니다. 캐시를 만든 후 애플리케이션 요구 사항이 변경되면 캐시의 크기 및 가격 책정 계층의 크기를 조정할 수 있습니다. 이 문서에서는 Azure Portal과 Azure PowerShell 및 Azure CLI와 같은 도구를 사용하여 캐시 크기를 조정하는 방법을 보여 줍니다.

## <a name="when-to-scale"></a>크기를 조정하는 경우
Azure Cache for Redis의 [모니터링](cache-how-to-monitor.md) 기능을 사용하여 캐시의 상태 및 성능을 모니터링하고 캐시 크기를 조정해야 하는 경우를 결정할 수 있습니다. 

다음 메트릭을 모니터링하면 크기를 조정해야 하는지 결정하는데 도움이 될 수 있습니다.

* Redis 서버 부하
* 메모리 사용량
* 네트워크 대역폭
* CPU 사용량

캐시가 더 이상 애플리케이션 요구 사항을 충족시키지 못한다고 판단되면 애플리케이션에 적합하도록 더 크거나 더 작은 캐시 가격 책정 계층으로 규모를 변경할 수 있습니다. 사용할 캐시 가격 책정 계층을 결정 하는 방법에 대 한 자세한 내용은 [올바른 계층 선택](cache-overview.md#choosing-the-right-tier)을 참조 하세요.

## <a name="scale-a-cache"></a>캐시 크기 조정
캐시 크기를 조정하려면 [Azure Portal](cache-configure.md#configure-azure-cache-for-redis-settings)에서 [캐시를 찾은](https://portal.azure.com) 다음 **리소스 메뉴**에서 **크기 조정**을 클릭합니다.

![확장](./media/cache-how-to-scale/redis-cache-scale-menu.png)

**가격 책정 계층 선택** 블레이드에서 원하는 가격 책정 계층을 선택하고 **선택**을 클릭합니다.

![가격 책정 계층][redis-cache-pricing-tier-blade]


다른 가격 책정 계층으로 크기를 조정할 수 있지만 다음과 같은 제한 사항이 있습니다.

* 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
  * **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
  * **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
* **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
* **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장합니다.
* 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.
 
캐시의 크기를 새 가격 책정 계층으로 조정하는 동안에는 **Azure Cache for Redis** 블레이드에 **크기 조정 중** 상태가 표시됩니다.

![확장][redis-cache-scaling]

크기 조정이 완료되면 상태가 **Scaling(크기 조정 중)** 에서 **실행 중**으로 변경됩니다.

## <a name="how-to-automate-a-scaling-operation"></a>크기 조정 작업을 자동화하는 방법
Azure Portal에서 캐시 인스턴스의 크기를 조정할 뿐만 아니라 PowerShell cmdlet, Azure CLI를 사용하거나 MAML(Microsoft Azure Management Libraries)을 사용하여 크기를 조정할 수 있습니다. 

* [PowerShell을 사용하여 크기 조정](#scale-using-powershell)
* [Azure CLI를 사용한 크기 조정](#scale-using-azure-cli)
* [MAML을 사용하여 크기 조정](#scale-using-maml)

### <a name="scale-using-powershell"></a>PowerShell을 사용하여 크기 조정

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) `Size` , `Sku` 또는 속성이 수정 될 때 AzRedisCache cmdlet을 사용 하 여 PowerShell을 통해 Redis 인스턴스에 대 한 Azure 캐시의 크기를 조정할 수 있습니다 `ShardCount` . 다음 예제에서는 `myCache` 라는 캐시를 2.5GB 캐시로 크기를 조정하는 방법을 보여 줍니다. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

PowerShell로 크기를 조정 하는 방법에 대 한 자세한 내용은 [powershell을 사용 하 여 Azure Cache For Redis 크기 조정을](cache-how-to-manage-redis-cache-powershell.md#scale)참조 하세요.

### <a name="scale-using-azure-cli"></a>Azure CLI를 사용한 크기 조정
Azure CLI를 사용하여 Azure Cache for Redis 인스턴스의 크기를 조정하려면 `azure rediscache set` 명령을 호출하고 원하는 크기 조정 작업에 따라 새 크기, SKU, 또는 클러스터 크기가 포함된 원하는 구성 변경 내용을 전달합니다.

Azure CLI를 사용하여 크기를 조정하는 방법에 대한 자세한 내용은 [기존 Azure Cache for Redis에 대한 설정 변경](cache-manage-cli.md#scale)을 참조하세요.

### <a name="scale-using-maml"></a>MAML을 사용하여 크기 조정
[MAML(Microsoft Azure Management Libraries)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)을 사용하여 Azure Cache for Redis 인스턴스의 크기를 조정하려면 `IRedisOperations.CreateOrUpdate` 메서드를 호출하고 `RedisProperties.SKU.Capacity`에 대한 새 크기를 전달합니다.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

자세한 내용은 [MAML을 사용하여 Azure Cache for Redis 관리](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 샘플을 참조하세요.

## <a name="scaling-faq"></a>크기 조정 FAQ
Azure Cache for Redis 크기 조정에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

* [프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?](#can-i-scale-to-from-or-within-a-premium-cache)
* [크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [크기 조정은 어떻게 수행되나요?](#how-does-scaling-work)
* [크기를 조정하는 동안 캐시의 데이터가 손실되나요?](#will-i-lose-data-from-my-cache-during-scaling)
* [사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?](#is-my-custom-databases-setting-affected-during-scaling)
* [크기를 조정하는 동안 내 캐시를 사용할 수 있나요?](#will-my-cache-be-available-during-scaling)
* 지역 복제를 구성하여 내 캐시의 크기를 조정하거나 클러스터의 분할된 데이터베이스를 변경할 수 없는 이유는 무엇인가요?
* [지원되지 않는 작업](#operations-that-are-not-supported)
* [크기 조정은 시간이 얼마나 걸리나요?](#how-long-does-scaling-take)
* [크기 조정이 완료되었는지 어떻게 알 수 있나요?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>프리미엄 캐시로 확장하거나, 이 캐시를 축소하거나 이 캐시 내에서 크기를 조정할 수 있나요?
* **프리미엄** 캐시에서 **기본** 또는 **표준** 가격 책정 계층으로 축소할 수 없습니다.
* 하나의 **프리미엄** 캐시 가격 책정 계층에서 다른 프리미엄 캐시 가격 책정 계층으로 크기를 조정할 수 있습니다.
* **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준**에서 **프리미엄**으로 확장합니다.
* **프리미엄** 캐시를 만들 때 클러스터링을 사용하도록 설정했으면 [클러스터 크기를 변경](cache-how-to-premium-clustering.md#cluster-size)할 수 있습니다. 클러스터를 사용하지 않고 캐시를 만든 경우 나중에 클러스터링를 구성할 수 있습니다.
  
  자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?
아니요, 캐시 이름 및 키는 크기 조정 작업을 수행하는 동안 변경되지 않습니다.

### <a name="how-does-scaling-work"></a>크기 조정은 어떻게 수행되나요?
* **기본** 캐시 크기를 다른 크기로 조정하는 경우 캐시가 종료되고 새 크기를 사용하여 새 캐시를 프로비전합니다. 이 시간 동안에는 캐시를 사용할 수 없으며 캐시의 모든 데이터가 손실됩니다.
* **기본** 캐시를 **표준** 캐시로 확장하는 경우 복제본 캐시가 프로비전되며 데이터가 주 캐시에서 복제본 캐시로 복사됩니다. 크기를 조정하는 동안 캐시를 계속 사용할 수 있습니다.
* **표준** 캐시가 다른 크기나 **프리미엄** 캐시로 확장 되는 경우 복제본 중 하나가 종료 되 고 새 크기 및 전송 되는 데이터에 다시 프로 비전 된 후 다른 복제본이 다시 프로 비전 되기 전에 장애 조치 (failover)를 수행 하 여 캐시 노드 중 하나가 실패 하는 동안 발생 하는 프로세스와 유사 합니다.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>크기를 조정하는 동안 캐시의 데이터가 손실되나요?
* **기본** 캐시 크기를 새 크기로 조정하는 경우 모든 데이터가 손실되고 크기 조정 작업을 수행하는 동안 캐시를 사용할 수 없습니다.
* **기본** 캐시를 **표준** 캐시로 확장하는 경우 캐시의 데이터가 일반적으로 유지됩니다.
* **표준** 캐시가 더 큰 크기나 계층으로 확장되거나, **프리미엄** 캐시가 더 크게 확장되는 경우에는 일반적으로 모든 데이터가 유지됩니다. **표준** 또는 **프리미엄** 캐시를 더 작게 축소하는 경우, 새로 조정된 크기 대비 캐시에 있는 데이터의 양에 따라 데이터가 손실될 수 있습니다. 크기를 축소하는 경우 데이터가 손실되면 [allkeys-lru](https://redis.io/topics/lru-cache) 제거 정책을 사용하여 키를 제거합니다. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>사용자 지정 데이터베이스 설정이 크기 조정 하는 동안에 영향을 받나요?
캐시 생성 중에 `databases` 설정에 대한 사용자 지정 값을 구성한 경우 일부 가격 책정 계층에 서로 다른 [데이터베이스 제한](cache-configure.md#databases)이 있습니다. 이 시나리오를 확장할 때 몇 가지 고려 사항이 있습니다.

* 현재 계층보다 낮은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때:
  * 모든 가격 책정 계층에 대해 기본값이 16개인 `databases`을 사용하는 경우 데이터 손실은 전혀 없습니다.
  * 크기 조정하는 계층에 대한 제한내에 포함되는 `databases`의 사용자 지정 수를 사용하는 경우, 이 `databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.
  * 새 계층의 제한을 초과하는 `databases`의 사용자 지정 수를 사용하는 경우, `databases` 설정은 새 계층의 제한까지 낮춰지고 제거된 데이터베이스의 모든 데이터는 손실됩니다.
* 현재 계층보다 같거나 높은 `databases` 제한을 가진 가격 책정 계층으로 크기를 조정할 때 `databases` 설정은 유지되고 데이터 손실은 전혀 없습니다.

표준 및 프리미엄 캐시의 가용성에 대한 SLA는 99.9%이나 데이터 손실에 대한 SLA는 없습니다.

### <a name="will-my-cache-be-available-during-scaling"></a>크기를 조정하는 동안 내 캐시를 사용할 수 있나요?
* **표준** 및 **프리미엄** 캐시는 크기 조정 작업을 수행하는 동안 사용할 수 있습니다. 그러나 표준 및 프리미엄 캐시 크기를 조정하는 동안 및 기본 에서 표준 캐시로 확장하는 동안 연결 블립이 발생할 수 있습니다. 이러한 연결 블립은 작을 것으로 예상되며 redis 클라이언트는 연결을 즉시 다시 설정할 수 있습니다.
* 작업을 다른 크기로 확장하는 동안 **기본** 캐시는 오프라인 상태입니다. **기본**에서 **표준**으로 확장하는 동안 기본 캐시를 그대로 사용할 수 있지만 작은 연결 블립이 발생할 수도 있습니다. 연결 블립이 발생하는 경우 redis 클라이언트가 해당 연결을 즉시 다시 설정할 수 있습니다.


### <a name="scaling-limitations-with-geo-replication"></a>지역 복제를 사용하여 제한 사항 크기 조정

두 개의 캐시 간에 지역 복제 링크를 추가하면 더 이상 크기 조정 작업을 시작하거나 클러스터에서 분할된 데이터베이스의 수를 변경할 수 없습니다. 캐시의 연결을 해제하여 이러한 명령을 실행해야 합니다. 자세한 내용은 [지역 복제 구성](cache-how-to-geo-replication.md)을 참조하세요.


### <a name="operations-that-are-not-supported"></a>지원되지 않는 작업
* 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
  * **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
  * **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
* **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
* **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본**에서 **표준**으로 확장한 다음, 후속 작업을 통해 **표준**에서 **프리미엄**으로 확장합니다.
* 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.

크기 조정 작업이 실패하면 서비스는 작업을 되돌리려고 하며 캐시는 원래 크기로 되돌아갑니다.


### <a name="how-long-does-scaling-take"></a>크기 조정은 시간이 얼마나 걸리나요?
크기 조정 시간은 캐시에 있는 데이터의 양에 따라 달라 지 며 완료 하는 데 시간이 더 오래 걸립니다. 크기 조정에는 약 20 분이 걸립니다. 클러스터형 캐시의 경우 분할은 분할 당 약 20 분이 걸립니다.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>크기 조정이 완료되었는지 어떻게 알 수 있나요?
Azure Portal에서 진행 중인 크기 조정 작업을 볼 수 있습니다. 크기 조정이 완료되면 캐시 상태가 **실행 중**으로 변경됩니다.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
