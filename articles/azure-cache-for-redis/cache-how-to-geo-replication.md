---
title: Redis 용 Azure 캐시에 대한 지역에서 복제를 설정 하는 방법 | Microsoft Docs
description: 지리적 지역 간에 Azure Cache for Redis 인스턴스를 복제하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129421"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Redis 용 Azure 캐시에 대한 지역에서 복제를 설정 하는 방법

지역 복제는 두 개의 프리미엄 계층 Azure Cache for Redis 인스턴스를 연결하는 메커니즘을 제공합니다. 캐시 하나는 기본 연결 된 캐시로, 다른 하나는 보조 연결 된 캐시로 선택 됩니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다. 이 문서에서는 Redis 인스턴스에 대한 프리미엄 계층 Azure 캐시에 대한 지역에서 복제를 구성 하는 지침을 제공 합니다.

## <a name="geo-replication-prerequisites"></a>지역에서 복제 필수 조건

두 캐시 간에 지역에서 복제를 구성 하려면 다음 필수 구성 요소를 충족 해야 합니다.

- 두 캐시는 모두 [프리미엄 계층](cache-premium-tier-intro.md) 캐시입니다.
- 두 캐시는 동일한 Azure 구독에 있습니다.
- 보조 연결 된 캐시는 캐시 크기가 같거나 기본 연결 된 캐시 보다 큰 캐시 크기입니다.
- 두 캐시가 생성 되 고 실행 중 상태가 됩니다.

일부 기능은 지역에서 복제에서 지원 되지 않습니다.

- 지 속성은 지역에서 복제를 지원 하지 않습니다.
- 두 캐시에 모두 클러스터링이 사용 하도록 설정 되어 있고 분할 수가 같은 경우 클러스터링이 지원 됩니다.
- 동일한 VNET의 캐시가 지원 됩니다.
- 다른 Vnet의 캐시는 주의 하 여 지원 됩니다. 자세한 내용은 [VNET에서 내 캐시로 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) 를 참조 하세요.

지역에서 복제가 구성 된 후에는 연결 된 캐시 쌍에 다음과 같은 제한 사항이 적용 됩니다.

- 보조 연결된 캐시는 읽기 전용입니다. 이 캐시에서 읽을 수는 있지만 데이터를 쓸 수 없습니다. 
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 그러나 나중에 지역에서 복제를 제거 하는 경우 복제 된 데이터는 보조 연결 된 캐시에 남아 있습니다.
- 캐시를 연결 하는 동안에는 캐시를 [확장할](cache-how-to-scale.md) 수 없습니다.
- 캐시에서 클러스터링을 사용 하도록 설정한 경우에는 [분할 수를 변경할](cache-how-to-premium-clustering.md) 수 없습니다.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- 두 캐시에서 [내보낼](cache-how-to-import-export-data.md#export) 수 있습니다.
- 보조 연결 된 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 없습니다.
- 캐시의 연결을 끊을 때까지 연결 된 캐시 또는 해당 캐시를 포함 하는 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 캐시가 서로 다른 지역에 있는 경우 네트워크 송신 비용이 지역 간에 이동 되는 데이터에 적용 됩니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 자동 장애 조치 (failover)는 기본 및 보조 연결 된 캐시 사이에서 발생 하지 않습니다. 클라이언트 응용 프로그램을 장애 조치 (failover) 하는 방법에 대한 자세한 내용 및 정보는 [보조 연결 된 캐시로](#how-does-failing-over-to-the-secondary-linked-cache-work) 의 장애 조치 (failover) 작동 방식을 참조 하세요.

## <a name="add-a-geo-replication-link"></a>지역에서 복제 링크 추가

1. 지역에서 복제를 위해 두 캐시를 함께 연결 하려면 주 연결 된 캐시로 사용할 캐시의 리소스 메뉴에서 **지역에서 복제** 를 클릭 합니다. 그런 다음 **지역에서 복제** 블레이드에서 **캐시 복제 추가 링크** 를 클릭 합니다.

    ![링크 추가](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. **호환 되는 캐시** 목록에서 원하는 보조 캐시의 이름을 클릭 합니다. 보조 캐시가 목록에 표시 되지 않으면 보조 캐시에 대한 [지역에서 복제 필수 구성 요소가](#geo-replication-prerequisites) 충족 되는지 확인 합니다. 지역별로 캐시를 필터링 하려면 맵의 지역을 클릭 하 여 **호환 되는 캐시** 목록에 해당 캐시만 표시 합니다.

    ![지역에서 복제 호환되는 캐시](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    상황에 맞는 메뉴를 사용 하 여 연결 프로세스를 시작 하거나 보조 캐시에 대한 세부 정보를 볼 수도 있습니다.

    ![지역에서 복제 상황에 맞는 메뉴](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **링크**를 클릭하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.

    ![캐시 연결](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **지역에서 복제** 블레이드에서 복제 프로세스의 진행률을 볼 수 있습니다.

    ![연결 상태](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    주 캐시와 보조 캐시에 대한 **개요** 블레이드에서 연결 상태를 볼 수도 있습니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    복제 프로세스가 완료되면 **링크 상태**가 **성공**으로 바뀝니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    연결 프로세스 중에는 기본 연결 된 캐시를 계속 사용할 수 있습니다. 연결 프로세스가 완료 될 때까지 보조 연결 된 캐시를 사용할 수 없습니다.

## <a name="remove-a-geo-replication-link"></a>지역에서 복제 링크 제거

1. 두 캐시 간의 링크를 제거 하 고 지역에서 복제를 중지 하려면 **지역에서 복제** 블레이드에서 **캐시 연결 해제** 를 클릭 합니다.
    
    ![캐시 연결 해제](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역에서 복제 링크를 제거 하면 기본 연결 된 캐시에서 복제 된 데이터는 보조 캐시에 남아 있습니다.
>
>

## <a name="geo-replication-faq"></a>지역에서 복제 FAQ

- [표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [둘 이상의 캐시를 함께 연결할 수 있나요?](#can-i-link-more-than-two-caches-together)
- [서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [크기가 다른 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-with-different-sizes)
- [클러스터링이 설정 된 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET에서 내 캐시에 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redis 지역에서 복제의 복제 일정이란?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [지역에서 복제에 걸리는 기간은 얼마나 되나요?](#how-long-does-geo-replication-replication-take)
- [복제 복구 지점이 보장되나요?](#is-the-replication-recovery-point-guaranteed)
- [PowerShell 또는 Azure CLI를 사용 하 여 지역에서 복제를 관리할 수 있나요?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?

아니요, 지역에서 복제는 프리미엄 계층 캐시에만 사용할 수 있습니다.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?

- 연결할 때 연결 프로세스가 완료 되는 동안 기본 연결 된 캐시는 계속 사용할 수 있습니다.
- 연결할 때 연결 프로세스가 완료 될 때까지 보조 연결 된 캐시를 사용할 수 없습니다.
- 연결을 해제 하는 경우 연결 끊기 프로세스가 완료 되는 동안 두 캐시를 모두 계속 사용할 수 있습니다.

### <a name="can-i-link-more-than-two-caches-together"></a>둘 이상의 캐시를 함께 연결할 수 있나요?

아니요, 두 캐시를 함께 연결할 수 있습니다.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?

아니요, 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.

### <a name="can-i-link-two-caches-with-different-sizes"></a>크기가 다른 두 캐시를 연결할 수 있나요?

예, 보조 연결된 캐시가 주 연결된 캐시보다 큰 경우 연결할 수 있습니다.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>클러스터링이 설정 된 지역에서 복제를 사용할 수 있나요?

예, 두 캐시의 분할된 데이터베이스 수가 동일한 경우 사용할 수 있습니다.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET에서 내 캐시에 지역에서 복제를 사용할 수 있나요?

예, Vnet의 캐시에 대한 지역에서 복제가 지원 됩니다.

- 동일한 VNET에 있는 캐시 간의 지역에서 복제가 지원됩니다.
- 서로 다른 Vnet 캐시 간의 지역에서 복제도 지원 됩니다.
  - Vnet이 동일한 지역에 있는 경우 [vnet 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 또는 [VPN Gateway vnet 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)을 사용 하 여 연결할 수 있습니다.
  - Vnet 서로 다른 지역에 있는 경우 기본 내부 부하 분산 장치가 포함 된 제약 조건 때문에 VNET 피어 링을 사용 하는 지역에서 복제가 지원 되지 않습니다. VNET 피어 링 제약 조건에 대한 자세한 내용은 [Virtual Network-피어 링-요구 사항 및 제약 조건](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)을 참조 하세요. VPN Gateway VNET 간 연결을 사용 하는 것이 좋습니다.

[이 Azure 템플릿을](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)사용 하 여 VPN Gateway vnet 간 연결로 연결 된 vnet에 두 개의 지역에서 복제 된 캐시를 신속 하 게 배포할 수 있습니다.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis 지역에서 복제의 복제 일정이란?

복제는 지속적이 고 비동기식 이며 특정 일정에서 발생 하지 않습니다. 주 복제본에 대해 수행 되는 모든 쓰기는 즉각적으로 보조 데이터베이스에서 비동기적으로 복제 됩니다.

### <a name="how-long-does-geo-replication-replication-take"></a>지역에서 복제에 걸리는 기간은 얼마나 되나요?

복제는 증분, 비동기 및 연속 이며 소요 되는 시간은 지역 간 대기 시간과 크게 다르지 않습니다. 특정 상황에서는 보조 캐시가 주 데이터베이스의 데이터 전체 동기화를 수행 하는 데 필요할 수 있습니다. 이 경우의 복제 시간은 기본 캐시에 로드, 사용 가능한 네트워크 대역폭 및 지역 간 대기 시간 등의 요소 수에 따라 달라 집니다. 전체 53 지역 복제 쌍에 대한 복제 시간은 5 분에서 10 분 사이에 있을 수 있습니다.

### <a name="is-the-replication-recovery-point-guaranteed"></a>복제 복구 지점이 보장되나요?

지역에서 복제 된 모드의 캐시의 경우 지 속성을 사용할 수 없습니다. 지리적으로 복제 된 쌍의 연결을 해제 하는 경우 (예: 고객이 시작한 장애 조치 (failover)) 보조 연결 된 캐시는 동기화 된 데이터를 해당 시점까지 유지 합니다. 이러한 경우 복구 지점은 보장 되지 않습니다.

복구 지점을 얻으려면 두 캐시에서 [내보냅니다](cache-how-to-import-export-data.md#export) . 나중에 기본 연결 된 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 있습니다.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell 또는 Azure CLI를 사용 하 여 지역에서 복제를 관리할 수 있나요?

예, Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 지역에서 복제를 관리할 수 있습니다. 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) 또는 [Azure CLI 문서](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)를 참조 하세요.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?

지역에서 복제를 사용 하는 경우 기본 연결 된 캐시의 데이터는 보조 연결 된 캐시에 복제 됩니다. 두 연결 된 캐시가 동일한 지역에 있는 경우 데이터 전송에 대한 요금이 부과 되지 않습니다. 두 연결 된 캐시가 서로 다른 지역에 있는 경우 데이터 전송 요금은 어느 지역에서 든 이동 하는 데이터의 네트워크 송신 비용입니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?

지역에서 복제 된 캐시와 해당 리소스 그룹은 지역에서 복제 링크를 제거할 때까지 연결 된 상태에서 삭제할 수 없습니다. 연결된 캐시 중 하나 또는 둘 다를 포함하는 리소스 그룹을 삭제하려고 하면, 리소스 그룹의 다른 리소스는 삭제되지만 해당 리소스 그룹은 `deleting` 상태로 유지되고 이 리소스 그룹의 연결된 캐시는 모두 `running` 상태로 남아 있습니다. 리소스 그룹 및 그 안에 연결 된 캐시를 완전히 삭제 하려면 [지역에서 복제 링크 제거](#remove-a-geo-replication-link)의 설명에 따라 캐시 연결을 해제 합니다.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?

일반적으로 캐시가 액세스 하는 응용 프로그램과 동일한 Azure 지역에 있는 것이 좋습니다. 별도의 기본 및 대체 지역이 있는 응용 프로그램의 경우 기본 및 보조 캐시가 동일한 지역에 존재 하는 것이 좋습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [모범 사례 - Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?

Azure 지역 간 자동 장애 조치 (failover)는 지역에서 복제 된 캐시에 대해 지원 되지 않습니다. 재해 복구 시나리오에서 고객은 전체 응용 프로그램 스택을 백업 지역에서 조정 된 방식으로 가져와야 합니다. 개별 응용 프로그램 구성 요소가 자신의 백업으로 전환할 시기를 결정 하도록 허용 하면 성능에 부정적인 영향을 줄 수 있습니다. Redis의 주요 이점 중 하나는 대기 시간이 짧은 저장소 임을 나타내는 것입니다. 고객의 주 응용 프로그램이 캐시와 다른 지역에 있는 경우 추가 된 라운드트립 시간이 성능에 크게 영향을 줄 수 있습니다. 이러한 이유로 일시적인 가용성 문제로 인해 자동으로 장애 조치 (failover) 하지 않습니다.

고객이 시작한 장애 조치 (failover)를 시작 하려면 먼저 캐시의 연결을 해제 합니다. 그런 다음 Redis 클라이언트를 변경 하 여 (이전에 연결 된) 보조 캐시의 연결 엔드포인트을 사용 합니다. 두 캐시의 연결이 끊어지면 보조 캐시는 일반적인 읽기-쓰기 캐시가 다시 되며 Redis 클라이언트에서 직접 요청을 받습니다.

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis 프리미엄 계층](cache-premium-tier-intro.md)에 대해 자세히 알아봅니다.
