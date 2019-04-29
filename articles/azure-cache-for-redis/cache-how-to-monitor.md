---
title: Azure Cache for Redis를 모니터링하는 방법 | Microsoft Docs
description: Azure Cache for Redis 인스턴스의 상태와 성능을 모니터링하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 32d0fb2ba17d322c0a273ebaf0a21d2b3ca0668f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830689"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Azure Cache for Redis를 모니터링하는 방법
Azure Cache for Redis에서는 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 사용하여 캐시 인스턴스를 모니터링하기 위한 몇 가지 옵션을 제공합니다. 메트릭을 보고, 메트릭 차트를 시작 보드에 고정하고, 모니터링 차트의 날짜 및 시간 범위를 사용자 지정하고, 차트에서 메트릭을 추가 및 제거하고, 특정 조건이 충족될 경우의 경고를 설정할 수 있습니다. 이러한 도구는 Azure Cache for Redis 인스턴스의 상태를 모니터링할 수 있게 해주며 캐싱 애플리케이션 관리에 도움이 됩니다.

Azure Cache for Redis 인스턴스의 메트릭은 Redis [INFO](https://redis.io/commands/info) 명령을 사용하여 분당 약 2번 수집되고 30일 동안 자동으로 저장되므로(다른 보존 정책을 구성하려는 경우 [캐시 메트릭 내보내기](#export-cache-metrics) 참조) 메트릭 차트에 표시하고 경고 규칙에 따라 평가할 수 있습니다. 각 캐시 메트릭에 사용되는 다양한 INFO 값에 대한 자세한 내용은 [사용 가능한 메트릭 및 보고 간격](#available-metrics-and-reporting-intervals)을 참조하세요.

<a name="view-cache-metrics"></a>

캐시 메트릭을 보려면 [Azure Portal](https://portal.azure.com)에서 캐시 인스턴스를 [찾아보세요](cache-configure.md#configure-azure-cache-for-redis-settings).  Azure Cache for Redis는 **개요** 블레이드 및 **Redis 메트릭** 블레이드에 몇 가지 기본 제공 차트를 제공합니다. 각 차트는 메트릭을 추가하거나 제거하고 보고 간격을 변경하여 사용자 지정할 수 있습니다.

![Redis 메트릭](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>미리 구성된 메트릭 차트 보기

**개요** 블레이드에는 미리 구성된 다음 모니터링 차트가 있습니다.

* [모니터링 차트](#monitoring-charts)
* [사용 현황 차트](#usage-charts)

### <a name="monitoring-charts"></a>모니터링 차트
**개요** 블레이드의 **모니터링** 섹션에는 **적중 및 누락**, **가져오기 및 설정** 및 **연결** 및 **총 명령** 차트가 있습니다.

![모니터링 차트](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>사용 현황 차트
**개요** 블레이드의 **사용 현황** 섹션에는 **Redis 서버 부하**, **메모리 사용량**, **네트워크 대역폭** 및 **CPU 사용량** 차트가 있으며 캐시 인스턴스의 **가격 책정 계층**도 표시됩니다.

![사용 현황 차트](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**가격 책정 계층** 은 캐시 가격 책정 계층을 표시하며 다른 가격 책정 계층으로 캐시 [크기를 조정](cache-how-to-scale.md) 하는 데 사용할 수 있습니다.

## <a name="view-metrics-with-azure-monitor"></a>Azure Monitor의 메트릭 보기
Azure Monitor를 사용하여 Redis 메트릭을 보고 사용자 지정 차트를 만들려면 **리소스 메뉴**에서 **메트릭**을 클릭하고 원하는 메트릭, 보고 간격, 차트 종류 등을 사용하여 차트를 사용자 지정합니다.

![Redis 메트릭](./media/cache-how-to-monitor/redis-cache-monitor.png)

Azure Monitor에서 메트릭을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>캐시 메트릭 내보내기
기본적으로 Azure Monitor의 캐시 메트릭은 [30일 동안 저장](../azure-monitor/platform/data-platform-metrics.md)된 후 삭제됩니다. 캐시 메트릭을 30일보다 더 오래 유지하려면 [저장소 계정을 지정](../azure-monitor/platform/archive-diagnostic-logs.md)하고 캐시 메트릭에 대한 **보존(일)** 정책을 지정할 수 있습니다. 

캐시 메트릭에 대한 저장소 계정을 구성하려면

1. **Azure Cache for Redis** 블레이드의 **리소스 메뉴**에서 **진단**을 클릭합니다.
2. **설정**을 클릭합니다.
3. **저장소 계정에 보관**을 선택합니다.
4. 캐시 메트릭을 저장할 저장소 계정을 선택합니다.
5. **1분** 확인란을 선택하고 **보존(일)** 정책을 지정합니다. 보존 정책 적용 및 데이터 무기한 보존을 원하지 않는 경우 **보존(일)** 을 **0**으로 설정합니다.
6. **저장**을 클릭합니다.

![Redis 진단](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>캐시 메트릭을 저장소에 보관 하는 것 외에도 할 수도 있습니다 [이벤트 허브로 스트림 하거나 Azure Monitor 로그를 보낼](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values)합니다.
>
>

메트릭에 액세스하려면 이 문서 앞부분에서 설명한 대로 Azure Portal에서 보고 [Azure Monitor 메트릭 REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)를 사용하여 액세스할 수도 있습니다.

> [!NOTE]
> 저장소 계정을 변경하는 경우 이전에 구성된 저장소 계정의 데이터는 계속 다운로드할 수는 있으나 Azure 포털에 표시되지는 않습니다.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>사용 가능한 메트릭 및 보고 간격
캐시 메트릭은 **지난 시간**, **오늘**, **지난 주** 및 **사용자 지정**을 포함한 몇 가지 보고 간격을 사용하여 보고됩니다. 각 메트릭 차트의 **메트릭** 블레이드에는 차트의 각 메트릭에 대한 평균값, 최소값 및 최대값이 표시되고 일부 메트릭의 경우 보고 간격에 대한 총계가 표시됩니다. 

각 메트릭은 두 가지 버전을 포함합니다. 하나의 메트릭은 전체 캐시 및 [클러스터링](cache-how-to-premium-clustering.md)을 사용하는 캐시에 대한 성능을 측정합니다. 이름에 `(Shard 0-9)`를 포함하는 메트릭의 차기 버전은 캐시에서 단일 분할에 대한 성능을 측정합니다. 예를 들어 캐시에 4개의 분할이 있는 경우 `Cache Hits`은 전체 캐시에 대한 총 적중 수이고 `Cache Hits (Shard 3)`는 캐시의 해당 분할에 대한 적중입니다.

> [!NOTE]
> 활성 클라이언트 애플리케이션이 연결되어 있지 않아서 캐시가 유휴 상태인 경우에도 연결된 클라이언트, 메모리 사용, 수행 중인 작업 등 일부 캐시 활동이 나타날 수 있습니다. Azure Cache for Redis 인스턴스 작업 중에는 이러한 활동이 일반적으로 나타납니다.
> 
> 

| 메트릭 | 설명 |
| --- | --- |
| 캐시 적중 |지정한 보고 간격 동안 성공한 키 조회 수입니다. 이 값은 Redis [INFO](https://redis.io/commands/info) 명령에서 `keyspace_hits`에 매핑됩니다. |
| 캐시 대기 시간(미리 보기) | 캐시의 대기 시간은 캐시의 노드 간 대기 시간에 따라 계산됩니다. 이 메트릭은 마이크로초 단위로 측정되고, "평균", "최소" 및 "최대"라는 3차원이 있습니다. 해당 차원은 각각 지정된 보고 간격 동안 캐시의 평균, 최소 및 최대 대기 시간을 나타냅니다. |
| 캐시 누락 |지정한 보고 간격 동안 실패한 키 조회 수입니다. 이 값은 Redis INFO 명령에서 `keyspace_misses` 에 매핑됩니다. 캐시 누락이 반드시 캐시에 문제가 있음을 의미하는 것은 아닙니다. 예를 들어 캐시 배제 프로그래밍 패턴을 사용하는 경우 애플리케이션은 먼저 캐시에서 항목을 찾습니다. 항목이 캐시에 없으면(캐시 누락) 데이터베이스에서 항목을 검색하고 다음 검색을 위해 캐시에 항목을 추가합니다. 캐시 누락은 캐시 배제 프로그래밍 패턴의 일반적인 동작입니다. 캐시 누락 수가 예상보다 높은 경우 캐시를 채우고 캐시에서 읽는 애플리케이션 논리를 검사합니다. 메모리 압력 때문에 캐시에서 항목이 제거되고 있는 경우 캐시 누락이 있을 수 있으며 메모리 압력을 모니터링하는 데 더 유용한 메트릭은 `Used Memory` 또는 `Evicted Keys`입니다. |
| 캐시 읽기 |지정한 보고 간격 동안 캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양입니다. 이 값은 캐시를 호스트하는 가상 컴퓨터를 지원하는 네트워크 인터페이스 카드에서 가져오며 Redis에 특정한 값이 아닙니다. **이 값은 캐시에서 사용되는 네트워크 대역폭에 해당합니다. 서버 쪽 네트워크 대역폭 제한에 대한 경고를 설정하려면 `Cache Read` 카운터를 사용하여 경고를 생성합니다. 다양한 캐시 가격 책정 계층 및 크기에 대해 관찰된 대역폭 제한은 [이 테이블](cache-faq.md#cache-performance)을 참조하세요.** |
| 캐시 쓰기 |지정한 보고 간격 동안 캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양입니다. 이 값은 캐시를 호스트하는 가상 머신을 지원하는 네트워크 인터페이스 카드에서 가져오며 Redis에 특정한 값이 아닙니다. 이 값은 클라이언트에서 캐시로 전송되는 데이터의 네트워크 대역폭에 해당됩니다. |
| 연결된 클라이언트 |지정한 보고 간격 동안 캐시에 설정된 클라이언트 연결 수입니다. 이 값은 Redis INFO 명령에서 `connected_clients` 에 매핑됩니다. [연결 제한](cache-configure.md#default-redis-server-configuration) 에 도달하면 캐시에 대한 후속 연결 시도가 실패합니다. 활성 클라이언트 애플리케이션이 없는 경우에도 내부 프로세스 및 연결 때문에 연결된 클라이언트 인스턴스가 여전히 몇 개 있을 수 있습니다. |
| CPU |지정한 보고 간격 동안의 Azure Cache for Redis 서버 CPU 사용률(%)입니다. 이 값은 운영 체제 `\Processor(_Total)\% Processor Time` 성능 카운터에 매핑됩니다. |
| 오류 | 지정된 보고 간격 동안 캐시에서 특정 오류 및 성능 문제가 발생할 수 있습니다. 이 메트릭에는 여러 오류 형식을 나타내는 8차원이 있지만 나중에 더 추가될 수 있습니다. 이제 표시된 오류 형식은 다음과 같습니다. <br/><ul><li>**장애 조치** – 캐시가 장애 조치하는 경우(슬레이브가 마스터로 승격)</li><li>**충돌** – 캐시가 노드 중 하나에서 예기치 않게 충돌하는 경우</li><li>**데이터 손실** - 캐시에 데이터 손실이 발생하는 경우</li><li>**UnresponsiveClients** – 클라이언트가 서버에서 데이터를 충분히 빠르게 읽고 있지 않은 경우</li><li>**AOF** – AOF 지속성과 관련된 문제가 발생하는 경우</li><li>**RDB** – RDB 지속성과 관련된 문제가 발생하는 경우</li><li>**가져오기** – 가져오기 RDB와 관련된 문제가 발생하는 경우</li><li>**내보내기** – 내보내기 RDB와 관련된 문제가 발생하는 경우</li></ul> |
| 제거된 키 |지정한 보고 간격 동안 `maxmemory` 제한 때문에 캐시에서 제거된 항목의 수입니다. 이 값은 Redis INFO 명령에서 `evicted_keys` 에 매핑됩니다. |
| 만료된 키 |지정한 보고 간격 동안 캐시에서 만료된 항목의 수입니다. 이 값은 Redis INFO 명령에서 `expired_keys` 에 매핑됩니다.|
| 가져오기 |지정한 보고 간격 동안 캐시에서 수행된 가져오기 작업의 수입니다. 이 값은 모든 Redis INFO 명령 `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` 및 `cmdstat_getrange` 값의 합계이며 보고 간격 동안의 캐시 적중 및 누락 합계에 해당합니다. |
| 초당 작업 | 지정한 보고 간격 동안 캐시 서버에서 초당 처리한 총 명령 수입니다.  이 값은 Redis INFO 명령의 "instantaneous_ops_per_sec"에 매핑됩니다. |
| Redis 서버 부하 |Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 이 카운터가 100이 되면 Redis 서버가 성능 한계에 도달하여 CPU가 더 빨리 작업을 처리할 수 없습니다. Redis 서버 부하가 높으면 클라이언트에 시간 제한 예외가 표시됩니다. 이 경우 강화나 여러 캐시로의 데이터 분할을 고려해야 합니다. |
| 설정 |지정한 보고 간격 동안 캐시에 수행된 설정 작업의 수입니다. 이 값은 모든 Redis INFO 명령 `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` 및 `cmdstat_setnx` 값의 합계입니다. |
| 전체 키  | 이전 보고 기간 동안 캐시에 있는 최대 키 수입니다. 이 값은 Redis INFO 명령에서 `keyspace` 에 매핑됩니다. 기본 메트릭 시스템의 제한으로 인해 클러스터링이 사용되도록 설정된 캐시에서 전체 키는 보고 간격 동안 최대 키 수를 가진 분할된 데이터베이스의 최대 키 수를 반환합니다.  |
| 총 작업 |지정한 보고 간격 동안 캐시 서버에서 처리한 총 명령 수입니다. 이 값은 Redis INFO 명령에서 `total_commands_processed` 에 매핑됩니다. Azure Cache for Redis가 pub/sub에만 사용되는 경우 `Cache Hits`, `Cache Misses`, `Gets` 또는 `Sets`에 대한 메트릭은 없으나 pub/sub 작업의 캐시 사용량을 반영하는 `Total Operations` 메트릭은 있습니다. |
| 사용된 메모리 |지정한 보고 간격 동안 캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB)입니다. 이 값은 Redis INFO 명령에서 `used_memory` 에 매핑됩니다. 메타데이터 또는 조각화를 포함하지 않습니다. |
| 사용된 메모리 비율 | 지정된 보고 간격 동안 사용되는 총 메모리의 %입니다.  백분율을 계산하기 위해 Redis INFO 명령의 "used_memory" 값을 참조합니다. |
| 사용된 메모리 RSS |조각화 및 메타데이터를 포함하여 지정한 보고 간격 동안 사용된 캐시 메모리의 양(MB)입니다. 이 값은 Redis INFO 명령에서 `used_memory_rss` 에 매핑됩니다. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>경고
메트릭 및 활동 로그를 기반으로 경고를 수신하도록 구성할 수 있습니다. Azure Monitor를 사용하여 트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 전자 메일 알림 보내기
* 웹후크 호출
* Azure 논리 앱 호출

캐시에 대한 경고 규칙을 구성하려면 **리소스 메뉴**에서 **경고 규칙**을 클릭합니다.

![모니터링](./media/cache-how-to-monitor/redis-cache-monitoring.png)

경고 구성 및 사용에 대한 자세한 내용은 [경고 개요](../monitoring-and-diagnostics/insights-alerts-portal.md)를 참조하세요.

## <a name="activity-logs"></a>활동 로그
활동 로그는 Azure Cache for Redis 인스턴스에서 수행된 작업에 대한 정보를 제공합니다. 이전에는 이러한 로그를 "감사 로그" 또는 "작업 로그"라고도 했습니다. 활동 로그를 통해 Azure Cache for Redis 인스턴스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 "무엇을, 누가, 언제"를 판단할 수 있습니다. 

> [!NOTE]
> 활동 로그에는 읽기(GET) 작업은 포함되지 않습니다.
>
>

캐시에 대한 활동 로그를 보려면 **리소스 메뉴**에서 **활동 로그**를 클릭합니다.

활동 로그에 대한 자세한 내용은 [Azure 활동 로그 개요](../azure-monitor/platform/activity-logs-overview.md)를 참조하세요.











