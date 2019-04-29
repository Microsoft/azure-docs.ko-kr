---
title: Azure Cache for Redis를 구성하는 방법 | Microsoft Docs
description: Azure Cache for Redis에 대한 기본 Redis 구성을 이해하고 Azure Cache for Redis 인스턴스를 구성하는 방법을 알아봅니다.
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 6bf42406c97ccb67251a14a7a963d3da2e01dbb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554696"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Azure Cache for Redis를 구성하는 방법
이 항목에서는 Azure Cache for Redis 인스턴스에 사용할 수 있는 구성에 대해 설명합니다. 또한 Azure Cache for Redis 인스턴스에 대한 기본 Redis 서버 구성에 대해서도 설명합니다.

> [!NOTE]
> 프리미엄 캐시 기능을 구성하고 사용하는 방법에 대한 자세한 내용은 [지속성을 구성하는 방법](cache-how-to-premium-persistence.md), [클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md) 및 [Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Azure Cache for Redis 설정 구성
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis 설정은 **리소스 메뉴**를 사용하여 **Azure Cache for Redis** 블레이드에 표시되고 구성됩니다.

![Azure Cache for Redis 설정](./media/cache-configure/redis-cache-settings.png)

**리소스 메뉴**를 사용하여 다음 설정을 살펴보고 구성할 수 있습니다.

* [개요](#overview)
* [활동 로그](#activity-log)
* [액세스 제어(IAM)](#access-control-iam)
* [태그](#tags)
* [문제 진단 및 해결](#diagnose-and-solve-problems)
* [설정](#settings)
    * [액세스 키](#access-keys)
    * [고급 설정](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
    * [규모](#scale)
    * [Redis 클러스터 크기](#cluster-size)
    * [Redis 데이터 지속성](#redis-data-persistence)
    * [업데이트 예약](#schedule-updates)
    * [지역에서 복제](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [방화벽](#firewall)
    * [Properties](#properties)
    * [잠금](#locks)
    * [Automation 스크립트](#automation-script)
* 관리
    * [데이터 가져오기](#importexport)
    * [데이터 내보내기](#importexport)
    * [Reboot](#reboot)
* [모니터링](#monitoring)
    * [Redis 메트릭](#redis-metrics)
    * [경고 규칙](#alert-rules)
    * [진단](#diagnostics)
* 설정 지원 및 문제 해결
    * [리소스 상태](#resource-health)
    * [새 지원 요청](#new-support-request)


## <a name="overview"></a>개요

**개요**에서는 이름, 포트, 가격 책정 계층 및 선택한 캐시 메트릭과 같은 캐시에 대한 기본 정보를 제공합니다.

### <a name="activity-log"></a>활동 로그

캐시에 대해 수행된 작업을 보려면 **활동 로그** 를 클릭합니다. 또한 다른 리소스를 포함하도록 이 뷰를 확장하려면 필터링을 사용하면 됩니다. 감사 로그 작업에 대한 자세한 내용은 [Resource Manager를 사용하는 감사 작업](../azure-resource-manager/resource-group-audit.md)을 참조하세요. Azure Cache for Redis 이벤트를 모니터링하는 방법에 대한 자세한 내용은 [작업 및 경고](cache-how-to-monitor.md#operations-and-alerts)를 참조하세요.

### <a name="access-control-iam"></a>액세스 제어(IAM)

**액세스 제어(IAM)** 섹션에서는 Azure Portal의 RBAC(역할 기반 액세스 제어)에 대한 지원을 제공합니다. 이 구성은 조직이 액세스 관리 요구 사항을 간편하고 정확하게 충족하도록 도와줍니다. 자세한 내용은 [Azure 포털의 역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

### <a name="tags"></a>태그들

**태그** 섹션은 리소스 구성에 도움이 됩니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/resource-group-using-tags.md)을 참조하세요.


### <a name="diagnose-and-solve-problems"></a>문제 진단 및 해결

일반적인 문제 및 이러한 문제를 해결하기 위한 전략을 확인하려면 **문제 진단 및 해결** 을 클릭합니다.



## <a name="settings"></a>설정
**설정** 섹션을 사용하여 캐시에 대한 다음 설정에 액세스하고 해당 설정을 구성할 수 있습니다.

* [액세스 키](#access-keys)
* [고급 설정](#advanced-settings)
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
* [규모](#scale)
* [Redis 클러스터 크기](#cluster-size)
* [Redis 데이터 지속성](#redis-data-persistence)
* [업데이트 예약](#schedule-updates)
* [지역에서 복제](#geo-replication)
* [Virtual Network](#virtual-network)
* [방화벽](#firewall)
* [Properties](#properties)
* [잠금](#locks)
* [Automation 스크립트](#automation-script)



### <a name="access-keys"></a>액세스 키
**선택키** 를 클릭하여 캐시에 대한 선택키를 보거나 다시 생성할 수 있습니다. 이러한 키는 캐시에 연결하는 클라이언트에서 사용합니다.

![Azure Cache for Redis 액세스 키](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>고급 설정
다음 설정은 **고급 설정** 블레이드에 구성됩니다.

* [액세스 포트](#access-ports)
* [메모리 정책](#memory-policies)
* [Keyspace 알림(고급 설정)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>액세스 포트
비 SSL 액세스는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 SSL 포트를 사용하도록 설정하려면 **고급 설정 블레이드**의 **SSL을 통해서만 액세스 허용**에서 **아니요**를 클릭한 다음 **저장**을 클릭합니다.

> [!NOTE]
> Azure Cache for Redis에 대한 SSL 액세스는 기본적으로 TLS 1.0을 지원합니다. 최소 TLS 버전은 **고급 설정** 블레이드에서 **최소 TLS 버전** 드롭다운을 사용하여 원하는 경우 TLS 1.2까지 높일 수 있습니다. 그런 다음, **저장**을 클릭합니다.

![Azure Cache for Redis 액세스 포트](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>메모리 정책
**고급 설정** 블레이드의 **Maxmemory 정책**, **maxmemory-reserved** 및 **maxfragmentationmemory-reserved** 설정은 캐시에 대한 메모리 정책을 구성합니다.

![Azure Cache for Redis Maxmemory 정책](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory 정책**은 캐시에 대한 제거 정책을 구성하고, 다음 제거 정책 중에서 선택할 수 있도록 합니다.

* `volatile-lru` - 기본 제거 정책입니다.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory` 정책에 대한 자세한 내용은 [제거 정책](https://redis.io/topics/lru-cache#eviction-policies)을 참조하세요.

**maxmemory-reserved** 설정은 장애 조치(Failover) 중 복제와 같은 비캐시 작업을 위해 예약되는 메모리의 양을 MB 단위로 구성합니다. 이 값을 설정하면 부하가 달라져도 Redis 서버 환경이 더 일관되도록 할 수 있습니다. 이 값은 쓰기 작업이 많은 워크로드에서 더 높게 설정되어야 합니다. 이러한 작업을 위해 메모리가 예약된 경우 캐시된 데이터의 저장에는 사용할 수 없습니다.

**maxfragmentationmemory-reserved** 설정은 메모리 조각화를 고려하여 예약된 메모리 양을 MB 단위로 구성합니다. 이 값을 설정하면 캐시가 가득 찼거나 거의 가득 찼고 조각화 비율이 높을 때 더욱 일관된 Redis 서버 환경을 갖출 수 있습니다. 이러한 작업을 위해 메모리가 예약된 경우 캐시된 데이터의 저장에는 사용할 수 없습니다.

새 메모리 예약 값(**maxmemory-reserved** 또는 **maxfragmentationmemory-reserved**)을 선택할 때 고려해야 할 사항 중 하나는 이러한 변경이 이미 많은 양의 데이터로 실행 중인 캐시에 미칠 수 있는 영향력입니다. 예를 들어 49GB의 데이터가 있는 53GB 캐시가 있는 경우 예약 값을 8GB로 변경하면 시스템에 사용 가능한 최대 메모리가 45GB로 줄어듭니다. 현재 `used_memory` 또는 `used_memory_rss` 값이 새 제한인 45GB보다 높으면 시스템에서 `used_memory`과 `used_memory_rss` 모두가 45GB 미만이 될 때까지 데이터를 제거해야 합니다. 제거는 서버 부하 및 메모리 조각화를 증가시킬 수 있습니다. `used_memory` 및 `used_memory_rss`와 같은 캐시 메트릭에 대한 자세한 내용은 [사용 가능한 메트릭 및 보고 간격](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 참조하세요.

> [!IMPORTANT]
> **maxmemory-reserved** 및 **maxfragmentationmemory-reserved** 설정은 Standard 및 Premium 캐시에만 사용할 수 있습니다.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace 알림(고급 설정)
Redis keyspace 알림은 **고급 설정** 블레이드에서 구성됩니다. Keyspace 알림을 사용하면 특정 이벤트가 발생할 때 클라이언트에서 알림을 받을 수 있습니다.

![Azure Cache for Redis 고급 설정](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace 알림과 **notify-keyspace-events** 설정은 표준 및 프리미엄 캐시에만 사용할 수 있습니다.
> 
> 

자세한 내용은 [Redis Keyspace 알림](https://redis.io/topics/notifications)을 참조하세요. 샘플 코드는 [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 샘플의 [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) 파일을 참조하세요.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis Advisor
**Azure Cache for Redis Advisor** 블레이드에는 캐시에 대한 추천 사항이 표시됩니다. 정상적으로 작동하는 중에는 추천이 표시되지 않습니다. 

![권장 사항](./media/cache-configure/redis-cache-no-recommendations.png)

캐시 작업 중에 높은 메모리 사용량, 네트워크 대역폭, 또는 서버 부하와 같은 조건이 발생하면 **Azure Cache for Redis** 블레이드에 경고가 표시됩니다.

![권장 사항](./media/cache-configure/redis-cache-recommendations-alert.png)

자세한 내용은 **추천** 블레이드에서 확인할 수 있습니다.

![권장 사항](./media/cache-configure/redis-cache-recommendations.png)

이러한 메트릭은 **Azure Cache for Redis** 블레이드의 [모니터링 차트](cache-how-to-monitor.md#monitoring-charts) 및 [사용 현황 차트](cache-how-to-monitor.md#usage-charts) 섹션에서 모니터링할 수 있습니다.

가격 책정 계층마다 클라이언트 연결, 메모리 및 대역폭에 대한 제한이 다릅니다. 캐시가 오랫동안 이러한 메트릭의 최대 용량에 근접하면 추천이 생성됩니다. **추천** 도구에서 검토하는 메트릭 및 제한에 대한 자세한 내용은 다음 테이블을 참조하세요.

| Azure Cache for Redis 메트릭 | 자세한 정보 |
| --- | --- |
| 네트워크 대역폭 사용량 |[캐시 성능 - 사용 가능한 대역폭](cache-faq.md#cache-performance) |
| 연결된 클라이언트 |[기본 Redis 서버 구성 - maxclients](#maxclients) |
| 서버 부하 |[사용 현황 차트 - Redis 서버 부하](cache-how-to-monitor.md#usage-charts) |
| 메모리 사용량 |[캐시 성능 - 크기](cache-faq.md#cache-performance) |

캐시를 업그레이드하려면 **지금 업그레이드**를 클릭하여 [가격 책정 계층](#scale)을 변경하고 캐시 크기를 조정하세요. 가격 책정 계층을 선택하는 방법에 대한 자세한 내용은 [사용해야 하는 Azure Cache for Redis 제안 및 크기는 어떻게 되나요?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)를 참조하세요.


### <a name="scale"></a>확장
**확장**을 클릭하여 캐시에 대한 가격 책정 계층을 보거나 변경합니다. 크기를 조정하는 방법에 대한 자세한 내용은 [Azure Cache for Redis 크기를 조정하는 방법](cache-how-to-scale.md)을 참조하세요.

![Azure Cache for Redis 가격 책정 계층](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis 클러스터 크기
**(미리 보기) Redis 클러스터 크기** 를 클릭하여 클러스터링을 사용하도록 설정되어 있는 실행 중인 프리미엄 캐시에 대한 클러스터 크기를 변경합니다.

> [!NOTE]
> Azure Cache for Redis 프리미엄 계층은 일반 공급되고 있지만, Redis 클러스터 크기 기능은 현재 미리 보기로 있습니다.
> 
> 

![Redis 클러스터 크기](./media/cache-configure/redis-cache-redis-cluster-size.png)

클러스터 크기를 변경하려면 슬라이더를 사용하거나 **분할된 데이터베이스 수** 텍스트 상자에 1에서 10 사이의 수를 입력하고 **확인**을 클릭하여 저장합니다.

> [!IMPORTANT]
> Redis 클러스터링은 프리미엄 캐시에만 사용할 수 있습니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.
> 
> 


### <a name="redis-data-persistence"></a>Redis 데이터 지속성
**Redis 데이터 지속성** 을 클릭하여 프리미엄 캐시에 대해 데이터 지속성 구성 및 사용 여부를 설정합니다. Azure Cache for Redis는 [RDB 지속성](cache-how-to-premium-persistence.md#configure-rdb-persistence) 또는 [AOF 지속성](cache-how-to-premium-persistence.md#configure-aof-persistence)을 사용하여 Redis 지속성을 제공합니다.

자세한 내용은 [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.


> [!IMPORTANT]
> Redis 데이터 지속성은 프리미엄 캐시에만 사용할 수 있습니다.
> 
> 

### <a name="schedule-updates"></a>업데이트를 예약
**업데이트 예약** 블레이드에서는 캐시의 Redis 서버 업데이트에 대한 유지 관리 기간을 지정할 수 있습니다. 

> [!IMPORTANT]
> 유지 관리 기간은 Redis 서버 업데이트에만 적용되며 Azure 업데이트나 캐시를 호스트하는 VM의 운영 체제에 대한 업데이트에는 적용되지 않습니다.
> 
> 

![업데이트를 예약](./media/cache-configure/redis-schedule-updates.png)

유지 관리 기간을 지정하려면 원하는 요일을 선택하고 각 요일의 유지 관리 기간 시작 시간을 지정한 후 **확인**을 클릭합니다. 유지 관리 기간은 UTC 단위입니다.

> [!IMPORTANT]
> **업데이트 예약** 기능은 프리미엄 계층 캐시에만 제공됩니다. 자세한 내용과 지침은 [Azure Cache for Redis 관리 - 업데이트 예약](cache-administration.md#schedule-updates)을 참조하세요.
> 
> 

### <a name="geo-replication"></a>지역에서 복제

**지역 복제** 블레이드에서 두 개의 프리미엄 계층 Azure Cache for Redis 인스턴스를 연결하는 메커니즘을 제공합니다. 한 캐시는 주 연결된 캐시로 지정하고 다른 캐시는 보조 연결된 캐시로 지정합니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다.

> [!IMPORTANT]
> **지역에서 복제**는 프리미엄 계층 캐시에서만 사용할 수 있습니다. 자세한 내용과 지침은 [Azure Cache for Redis에 대한 지역 복제를 구성하는 방법](cache-how-to-geo-replication.md)을 참조하세요.
> 
> 

### <a name="virtual-network"></a>Virtual Network
**Virtual Network** 섹션에서 캐시의 가상 네트워크 설정을 구성할 수 있습니다. VNET 지원을 통해 프리미엄 캐시를 만들고 설정을 업데이트하는 방법에 대한 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.

> [!IMPORTANT]
> 가상 네트워크 설정은 캐시를 만드는 동안 VNET 지원을 통해 구성된 프리미엄 캐시에만 제공됩니다. 
> 
> 

### <a name="firewall"></a>방화벽

방화벽 규칙 구성은 모든 Azure Cache for Redis 계층에서 사용할 수 있습니다.

**방화벽**을 클릭하여 Cache에 대한 방화벽 규칙을 보고 구성합니다.

![방화벽](./media/cache-configure/redis-firewall-rules.png)

시작 및 끝 IP 주소 범위를 사용하여 방화벽 규칙을 지정할 수 있습니다. 방화벽 규칙이 구성되면 지정된 IP 주소 범위의 클라이언트 연결만 캐시에 연결할 수 있습니다. 방화벽 규칙이 저장되면 잠시 지연되었다가 규칙이 적용됩니다. 이러한 지연 시간은 일반적으로 1분 미만입니다.

> [!IMPORTANT]
> 방화벽 규칙이 구성된 경우에도 Azure Cache for Redis 모니터링 시스템의 연결은 항상 허용됩니다.
> 
> 

### <a name="properties"></a>properties
**속성** 을 클릭하여 캐시 엔드포인트 및 포트를 포함하여 캐시에 대한 정보를 볼 수 있습니다.

![Azure Cache for Redis 속성](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>잠금
**잠금** 섹션에서는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 방지할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](../azure-resource-manager/resource-group-lock-resources.md)를 참조하세요.

### <a name="automation-script"></a>Automation 스크립트

미래 배포를 위해 배포된 리소스의 템플릿을 빌드하고 내보내려면 **Automation 스크립트**를 클릭합니다. 템플릿 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 참조하세요.

## <a name="administration-settings"></a>관리 설정
**관리** 섹션의 설정을 사용하여 캐시에 대해 다음과 같은 관리 작업을 수행할 수 있습니다. 

![관리](./media/cache-configure/redis-cache-administration.png)

* [데이터 가져오기](#importexport)
* [데이터 내보내기](#importexport)
* [Reboot](#reboot)


### <a name="importexport"></a>Import/Export
가져오기/내보내기는 Azure Cache for Redis 데이터 관리 작업입니다. 즉 프리미엄 캐시에서 Azure Storage 계정의 페이지 Blob으로 Azure Cache for Redis 데이터베이스(RDB) 스냅숏을 가져오고 내보내는 방식으로 캐시에서 데이터를 가져오고 내보낼 수 있습니다. 가져오기/내보내기를 사용하면 다양한 Azure Cache for Redis 인스턴스 간에 마이그레이션하거나 데이터를 사용하기 전에 캐시에 채울 수 있습니다.

가져오기는 Linux, Windows 또는 Amazon Web Services 및 기타 클라우드 공급자에서 실행되는 Redis를 비롯한 환경이나 클라우드에서 실행되는 Redis 서버로부터 Redis 호환 RDB 파일을 가져오는 데 사용됩니다. 데이터 가져오기는 미리 채워진 데이터로 캐시를 만드는 손쉬운 방법입니다. 가져오기 프로세스 중에는 Azure Cache for Redis에서 RDB 파일을 Azure Storage에서 메모리로 로드한 다음, 키를 캐시에 삽입합니다.

내보내기를 사용하면 Azure Cache for Redis에 저장된 데이터를 Redis 호환 RDB 파일로 내보낼 수 있습니다. 이 기능을 사용하여 데이터를 Azure Cache for Redis 인스턴스에서 다른 인스턴스 또는 다른 Redis 서버로 이동할 수 있습니다. 내보내기 프로세스 중에는 임시 파일이 Azure Cache for Redis 서버 인스턴스를 호스팅하는 VM에 만들어지고, 지정된 스토리지 계정에 업로드됩니다. 성공 또는 실패 상태로 내보내기 작업이 완료되면, 임시 파일은 삭제됩니다.

> [!IMPORTANT]
> Import/Export는 프리미엄 계층 캐시에만 제공됩니다. 자세한 내용과 지침은 [Azure Cache for Redis에서 데이터 가져오기 및 내보내기](cache-how-to-import-export-data.md)를 참조하세요.
> 
> 

### <a name="reboot"></a>Reboot
**다시 부팅** 블레이드에서는 캐시 노드를 다시 부팅할 수 있습니다. 이 다시 부팅 기능을 사용하면 캐시 노드에 오류가 발생하는 경우 애플리케이션의 복원력을 테스트할 수 있습니다.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

클러스터링이 설정된 프리미엄 캐시를 사용하는 경우 재부팅할 캐시 분할을 선택할 수 있습니다.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

하나 이상의 캐시 노드를 다시 부팅하려면 원하는 노드를 선택하고 **다시 부팅**을 클릭합니다. 클러스터링이 설정된 프리미엄 캐시를 사용하는 경우 다시 부팅할 분할을 선택하고 **다시 부팅**을 클릭합니다. 몇 분 후 선택된 노드가 재부팅되고, 다시 몇 분 후에 온라인 상태가 됩니다.

> [!IMPORTANT]
> 이제 모든 가격 책정 계층에서 다시 부팅을 사용할 수 있습니다. 자세한 내용과 지침은 [Azure Cache for Redis 관리 - 다시 부팅](cache-administration.md#reboot)을 참조하세요.
> 
> 


## <a name="monitoring"></a>모니터링

**모니터링** 섹션에서 Azure Cache for Redis에 대한 진단 및 모니터링을 구성할 수 있습니다. Azure Cache for Redis를 모니터링하고 진단하는 방법에 대한 자세한 내용은 [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)을 참조하세요.

![진단](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis 메트릭](#redis-metrics)
* [경고 규칙](#alert-rules)
* [진단](#diagnostics)

### <a name="redis-metrics"></a>Redis 메트릭
캐시에 대한 [메트릭을 보려면](cache-how-to-monitor.md#view-cache-metrics) **Redis 메트릭**을 클릭합니다.

### <a name="alert-rules"></a>경고 규칙

**경고 규칙**을 클릭하여 Azure Cache for Redis 메트릭을 기반으로 하는 경고를 구성합니다. 자세한 내용은 [경고](cache-how-to-monitor.md#alerts)를 참조하세요.

### <a name="diagnostics"></a>진단

기본적으로 Azure Monitor의 캐시 메트릭은 [30일 동안 저장](../azure-monitor/platform/data-platform-metrics.md)되었다가 삭제됩니다. 30일 이후에도 캐시 메트릭을 유지하려면 **진단**을 클릭하여 캐시 진단을 저장하는 데 사용되는 [저장소 계정을 구성](cache-how-to-monitor.md#export-cache-metrics)합니다.

>[!NOTE]
>캐시 메트릭을 저장소에 보관 하는 것 외에도 할 수도 있습니다 [이벤트 허브로 스트림 하거나 Azure Monitor 로그를 보낼](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)합니다.
>
>

## <a name="support--troubleshooting-settings"></a>설정 지원 및 문제 해결
**설정 지원 + 문제 해결** 섹션의 설정은 캐시로 문제를 해결하는 옵션을 제공합니다.

![설정 지원 + 문제 해결](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [리소스 상태](#resource-health)
* [새 지원 요청](#new-support-request)

### <a name="resource-health"></a>리소스 상태
**리소스 상태** 기능은 리소스를 감시하고 예상대로 실행되는지를 알려줍니다. Azure 리소스 상태 관리 서비스에 대한 자세한 내용은 [Azure 리소스 상태 개요](../resource-health/resource-health-overview.md)를 참조하세요.

> [!NOTE]
> 리소스 상태는 현재 가상 네트워크에서 호스팅되는 Azure Cache for Redis 인스턴스의 상태를 보고할 수 없습니다. 자세한 내용은 [VNET에서 캐시를 호스팅하는 경우 모든 캐시 기능이 작동하나요?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>새 지원 요청
캐시에 대한 지원 요청을 열려면 **새 지원 요청** 을 클릭합니다.





## <a name="default-redis-server-configuration"></a>기본 Redis 서버 구성
새 Azure Cache for Redis 인스턴스는 다음과 같은 기본 Redis 구성 값으로 구성됩니다.

> [!NOTE]
> 이 섹션의 설정은 `StackExchange.Redis.IServer.ConfigSet` 메서드를 사용하여 변경할 수 없습니다. 이 메서드를 이 섹션의 명령 중 하나와 함께 호출하면 다음 예제와 유사한 예외가 발생됩니다.  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> **max-memory-policy**와 같이 구성 가능한 모든 값은 Azure Portal 또는 명령줄 관리 도구(예: Azure CLI 또는 PowerShell)를 통해 구성할 수 있습니다.
> 
> 

| 설정 | 기본값 | 설명 |
| --- | --- | --- |
| `databases` |16 |데이터베이스의 기본 수는 16이지만 가격 책정 계층에 따라 다른 숫자를 구성할 수 있습니다.<sup>1</sup> 기본 데이터베이스는 DB 0입니다. `connection.GetDatabase(dbid)`을 사용하여 연결 단위로 다른 데이터베이스를 선택할 수 있습니다. 여기서 `dbid`는 `0`에서 `databases - 1` 사이의 숫자입니다. |
| `maxclients` |가격 책정 계층에 따라 달라집니다.<sup>2</sup> |이 값은 동시에 연결이 허용되는 클라이언트의 최대 수입니다. 제한에 도달하면 Redis는 'max number of clients reached' 오류를 반환하고 모든 새 연결을 닫습니다. |
| `maxmemory-policy` |`volatile-lru` |`maxmemory` 정책은 최대 메모리(캐시를 만들 때 선택한 캐시의 크기)에 도달했을 때 Redis가 어떤 것을 제거할지 선택하는 방법에 대한 설정입니다. Azure Cache for Redis를 사용하면 기본 설정은 `volatile-lru`이며, 이 경우 LRU 알고리즘을 사용하여 만료 기간이 설정되어 있는 키를 제거합니다. 이 설정은 Azure 포털에서 구성할 수 있습니다. 자세한 내용은 [메모리 정책](#memory-policies)을 참조하세요. |
| `maxmemory-samples` |3 |메모리를 절약하기 위해 LRU 및 최소 TTL 알고리즘은 정밀한 알고리즘이 아닌 대략적인 알고리즘입니다. 기본적으로 Redis는 세 개의 키를 확인하고 가장 오래 전에 사용된 키를 선택합니다. |
| `lua-time-limit` |5,000 |밀리초 단위의 Lua 스크립트 최대 실행 시간입니다. 최대 실행 시간에 도달하면 Redis는 허용된 시간 이후에도 실행 중인 스크립트를 기록하고 쿼리에 오류로 응답하기 시작합니다. |
| `lua-event-limit` |500 |스크립트 이벤트 큐의 최대 크기 |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |클라이언트 출력 버퍼 제한은 어떤 이유로 서버에서 데이터를 읽는 속도가 충분히 빠르지 않은 클라이언트의 연결을 강제로 끊는 데 사용할 수 있습니다. 속도가 느린 일반적인 이유는 게시/구독 클라이언트가 게시자의 생성 속도만큼 빠르게 메시지를 소화하지 못하기 때문입니다. 자세한 내용은 [https://redis.io/topics/clients](https://redis.io/topics/clients)을 참조하세요. |

<a name="databases"></a>
<sup>1</sup>`databases`에 대한 제한은 Azure Cache for Redis 가격 책정 계층마다 다르며 캐시를 만들 때 설정할 수 있습니다. 캐시를 만드는 동안 `databases` 설정이 지정되지 않았다면 기본값은 16입니다.

* 기본 및 표준 캐시
  * C0(250MB) 캐시 - 최대 16개의 데이타베이스
  * C1(1GB) 캐시 - 최대 16개의 데이타베이스
  * C2(2.5GB) 캐시 - 최대 16개의 데이타베이스
  * C3(6GB) 캐시 - 최대 16개의 데이타베이스
  * C4(13GB) 캐시 - 최대 32개의 데이타베이스
  * C5(26GB) 캐시 - 최대 48개의 데이타베이스
  * C6(53GB) 캐시 - 최대 64개의 데이타베이스
* 프리미엄 캐시
  * P1(6GB-60GB)-최대 16개의 데이터베이스
  * P2(13GB-130GB)-최대 32개의 데이터베이스
  * P3(26GB-260GB)-최대 48개의 데이터베이스
  * P4(53GB-530GB)-최대 64개의 데이터베이스
  * Redis 클러스터를 사용할 수 있는 모든 프리미엄 캐시 - Redis 클러스터는 0 데이터베이스의 사용만을 지원하므로 Redis 클러스터를 사용할 수 있는 모든 프리미엄 캐시에 대한 `databases` 제한은 사실상 1이며 [Select](https://redis.io/commands/select) 명령은 허용되지 않습니다. 자세한 내용은 [클러스터링을 사용하려면 클라이언트 애플리케이션을 변경해야 합니까?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

데이터베이스에 대한 자세한 내용은 [Redis 데이터베이스란?](cache-faq.md#what-are-redis-databases)을 참조하세요.

> [!NOTE]
>  `databases` 설정은 캐시를 만드는 동안에만 PowerShell, CLI, 또는 다른 관리 클라이언트를 사용하여 구성할 수 있습니다. PowerShell을 사용하여 캐시를 만드는 동안 `databases`를 구성하는 예제는 [New-AzRedisCache](cache-howto-manage-redis-cache-powershell.md#databases)를 참조하세요.
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients`는 Azure Cache for Redis 가격 책정 계층마다 다릅니다.

* 기본 및 표준 캐시
  * C0(250MB) 캐시 - 최대 256개 연결
  * C1(1GB) 캐시 - 최대 1,000개 연결
  * C2(2.5GB) 캐시 - 최대 2,000개 연결
  * C3(6GB) 캐시 - 최대 5,000개 연결
  * C4(13GB) 캐시 - 최대 10,000개 연결
  * C5(26GB) 캐시 - 최대 15,000개 연결
  * C6(53GB) 캐시 - 최대 20,000개 연결
* 프리미엄 캐시
  * P1(6GB - 60GB) - 최대 7,500개 연결
  * P2(13GB - 130GB) - 최대 15,000개 연결
  * P3(26GB - 260GB) - 최대 30,000개 연결
  * P4(53GB - 530GB) - 최대 40,000개 연결

> [!NOTE]
> 각 캐시 크기는 특정 횟수의 연결*까지* 허용하지만 Redis에 대한 각 연결에는 오버헤드가 연결되어 있습니다. 이러한 오버헤드의 예로 TLS/SSL 암호화의 결과인 CPU 및 메모리 사용량이 있습니다. 특정 캐시 크기에 대한 최대 연결 제한은 부하가 적은 캐시를 가정합니다. 연결 오버헤드의 부하 *그리고* 클라이언트 작업의 부하가 시스템의 용량을 초과하면 현재 캐시 크기에 대한 연결 제한을 초과하지 않은 경우에도 캐시에 용량 문제가 발생할 수 있습니다.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Azure Cache for Redis에서 지원되지 않는 Redis 명령
> [!IMPORTANT]
> Azure Cache for Redis 인스턴스에 대한 구성과 관리는 Microsoft에서 구성하므로 다음 명령은 비활성화됩니다. 이러한 명령을 호출하려고 하면 `"(error) ERR unknown command"`와 유사한 오류 메시지가 표시됩니다.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * 저장
> * SHUTDOWN
> * SLAVEOF
> * 클러스터 - 클러스트 쓰기 명령은 비활성화되지만, 읽기 전용 클러스터 명령은 허용됩니다.
> 
> 

Redis 명령에 대한 자세한 내용은 [https://redis.io/commands](https://redis.io/commands)를 참조하세요.

## <a name="redis-console"></a>Redis 콘솔
Azure Portal에서 모든 캐시에 제공되는 **Redis 콘솔**을 사용하여 Azure Cache for Redis 인스턴스에 명령을 안전하게 실행할 수 있습니다.

> [!IMPORTANT]
> - Redis 콘솔이 [VNET](cache-how-to-premium-vnet.md)에서 작동하지 않습니다. 캐시가 VNET의 일부인 경우 VNET의 클라이언트만 캐시에 액세스할 수 있습니다. Redis 콘솔은 VNET 외부에 있는 로컬 브라우저에서 실행되기 때문에 캐시에 연결할 수 없습니다.
> - Redis 명령 중 일부는 Azure Cache for Redis에서 지원되지 않습니다. Azure Cache for Redis에서 사용할 수 없는 Redis 명령 목록은 이전 [Azure Cache for Redis에서 지원되지 않는 Redis 명령](#redis-commands-not-supported-in-azure-cache-for-redis) 섹션을 참조하세요. Redis 명령에 대한 자세한 내용은 [https://redis.io/commands](https://redis.io/commands)를 참조하세요.
> 
> 

Redis 콘솔에 액세스하려면 **Azure Cache for Redis** 블레이드에서 **콘솔**을 클릭합니다.

![Redis 콘솔](./media/cache-configure/redis-console-menu.png)

캐시 인스턴스에 대해 명령을 실행하려면 원하는 명령을 콘솔에 입력합니다.

![Redis 콘솔](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>프리미엄 클러스터형 캐시에서 Redis 콘솔 사용

프리미엄 클러스터형 캐시에서 Redis 콘솔을 사용하는 경우 캐시의 단일 분할된 데이터베이스에 대해 명령을 실행할 수 있습니다. 특정 분할된 데이터베이스에 대해 명령을 실행하려면 분할된 데이터베이스 선택에서 원하는 분할된 데이터베이스를 클릭하여 연결합니다.

![Redis 콘솔](./media/cache-configure/redis-console-premium-cluster.png)

연결된 분할된 데이터베이스가 아닌 다른 분할된 데이터베이스에 저장된 키에 액세스하려고 하면 다음과 같은 오류 메시지가 표시됩니다.

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

이전 예제에서 분할된 데이터베이스 1은 선택된 분할된 데이터베이스이지만 `myKey`는 오류 메시지의 `(shard 0)` 부분에 표시된 대로 분할된 데이터베이스 0에 있습니다. 이 예제에서 `myKey`에 액세스하려면 분할된 데이터베이스 선택을 사용하여 분할된 데이터베이스 0을 선택한 다음 원하는 명령을 실행합니다.


## <a name="move-your-cache-to-a-new-subscription"></a>캐시를 새 구독으로 이동
**이동**을 클릭하여 캐시를 새 구독으로 이동할 수 있습니다.

![Azure Cache for Redis 이동](./media/cache-configure/redis-cache-move.png)

한 리소스 그룹에서 다른 리소스 그룹으로, 그리고 한 구독에서 다른 구독으로 리소스를 이동하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* Redis 명령을 사용하는 방법은 [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)를 참조하세요.

