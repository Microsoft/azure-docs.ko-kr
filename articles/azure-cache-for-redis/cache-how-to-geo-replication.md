---
title: Azure Cache for Redis에 대한 지역 복제를 구성하는 방법 | Microsoft Docs
description: 지리적 지역 간에 Azure Cache for Redis 인스턴스를 복제하는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552354"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 지역 복제를 구성하는 방법

지역 복제는 두 개의 프리미엄 계층 Azure Cache for Redis 인스턴스를 연결하는 메커니즘을 제공합니다. 하나의 캐시는 주 연결 된 캐시 및 보조 연결 된 캐시와 기타으로 선택 됩니다. 보조 연결된 캐시는 읽기 전용이 되고 주 캐시에 쓴 데이터는 보조 연결된 캐시에 복제됩니다. 이 기능은 Azure 지역 간에 캐시를 복제하는 데 사용할 수 있습니다. 이 문서에서는 프리미엄 계층 Azure Cache for Redis 인스턴스의 지역 복제를 구성하는 방법에 대한 지침을 제공합니다.

## <a name="geo-replication-prerequisites"></a>지역에서 복제 필수 조건

두 캐시 간에 지역에서 복제를 구성하려면 다음 필수 조건을 충족해야 합니다.

- 두 캐시 [프리미엄 계층](cache-premium-tier-intro.md) 캐시 합니다.
- 두 캐시 모두 동일한 Azure 구독에 있습니다.
- 보조 연결 된 캐시는 동일한 캐시 크기 또는 주 연결 된 캐시 보다 큰 캐시 크기로입니다.
- 두 캐시 모두 만들어집니다 및 실행 중 상태에서입니다.

지역에서 복제를 사용 하 여 일부 기능은 지원 되지 않습니다.

- 지역에서 복제를 사용 하 여 지 속성 지원 되지 않습니다.
- 두 캐시 모두 클러스터링을 사용 하 고 분할 된 데이터베이스 수가 같아야 하는 경우 클러스터링은 지원 됩니다.
- 동일한 VNET의 캐시를 사용할 수 있습니다.
- 다른 Vnet의 캐시에에서 주의 사용 하 여 지원 됩니다. 참조 [VNET에서 내 캐시를 사용 하 여 지역에서 복제를 사용할 수 있습니까?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) 자세한 내용은 합니다.

지역에서 복제를 구성한 후에는 연결된 캐시 쌍에 다음과 같은 제한이 적용됩니다.

- 보조 연결된 캐시는 읽기 전용입니다. 이 캐시에서 읽을 수는 있지만 데이터를 쓸 수 없습니다. 
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 하지만 지리적 복제 이후인 경우 보조 연결 된 캐시에 복제 된 데이터는 유지 제거.
- 먼저 [확장](cache-how-to-scale.md) 캐시 연결 되는 동안 두 캐시 합니다.
- 먼저 [분할 된 데이터베이스 수를 변경](cache-how-to-premium-clustering.md) 캐시에 클러스터링을 사용 하는 경우.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- 할 수 있습니다 [내보내기](cache-how-to-import-export-data.md#export) 하거나 캐시에서 합니다.
- 먼저 [가져오기](cache-how-to-import-export-data.md#import) 보조 연결 된 캐시로 합니다.
- 연결 된 캐시 또는 캐시를 연결 해제 될 때까지 포함 된 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 캐시 서로 다른 지역에 있는 경우 네트워크 송신 비용이 지역 간에 이동 되는 데이터에 적용 합니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 기본 및 보조 연결 된 캐시 간의 자동 장애 조치가 발생 하지 않습니다. 자세한 정보 및 클라이언트 응용 프로그램을 장애 조치 하는 방법에 대 한 정보를 참조 하세요. [보조 연결 된 캐시로 장애 조치 작동 방법?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>지역에서 복제 링크 추가

1. 지역에서 복제에 대 한 두 캐시를 함께 연결 하려면 클릭 펀치 **지역에서 복제** 캐시의 리소스 메뉴에서 캐시를 연결 된 기본 하려면. 그런 다음 **캐시 복제 링크 추가** 에서 합니다 **지역에서 복제** 블레이드입니다.

    ![링크 추가](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 원하는 보조 캐시의 이름을 클릭 합니다 **호환 가능한 캐시** 목록입니다. 목록에 표시 되지 않으면 보조 캐시에 있는지 확인 합니다 [지역에서 복제 필수 조건](#geo-replication-prerequisites) 충족 되는 보조 캐시에 대 한 합니다. 지역으로 캐시를 필터링 하려면 해당 캐시만 표시할 지도 영역을 클릭 합니다 **호환 가능한 캐시** 목록입니다.

    ![지역에서 복제 호환되는 캐시](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    또한 연결 프로세스를 시작 하거나 상황에 맞는 메뉴를 사용 하 여 보조 캐시에 대 한 세부 정보를 볼 수 있습니다.

    ![지역에서 복제 상황에 맞는 메뉴](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **링크**를 클릭하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.

    ![캐시 연결](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **지역에서 복제** 블레이드에서 복제 프로세스의 진행률을 볼 수 있습니다.

    ![연결 상태](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    주 캐시와 보조 캐시에 대한 **개요** 블레이드에서 연결 상태를 볼 수도 있습니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    복제 프로세스가 완료되면 **링크 상태**가 **성공**으로 바뀝니다.

    ![캐시 상태](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    연결 프로세스 중 주 연결 된 캐시를 계속 사용할 수 있습니다. 연결 프로세스가 완료 될 때까지 보조 연결 된 캐시를 사용할 수 없습니다.

## <a name="remove-a-geo-replication-link"></a>지역에서 복제 링크 제거

1. 두 캐시 간 링크를 제거하고 지역에서 복제를 중지하려면 **지역에서 복제** 블레이드에서 **Unlink caches**(캐시 연결 해제)를 클릭합니다.
    
    ![캐시 연결 해제](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역에서 복제 링크를 제거해도 주 연결된 캐시에서 복제된 데이터는 보조 캐시에 유지됩니다.
>
>

## <a name="geo-replication-faq"></a>지역에서 복제 FAQ

- [표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [둘 이상의 캐시를 함께 연결할 수 있나요?](#can-i-link-more-than-two-caches-together)
- [서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [크기가 다른 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-with-different-sizes)
- [클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET의 캐시에 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redis 지역에서 복제의 복제 일정이란?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [지역에서 복제에 걸리는 기간은 얼마나 되나요?](#how-long-does-geo-replication-replication-take)
- [복제 복구 지점이 보장되나요?](#is-the-replication-recovery-point-guaranteed)
- [PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?

아니요, 지역에서 복제는 프리미엄 계층 캐시에만 사용할 수 있습니다.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?

- 에 연결 하는 경우 연결 프로세스를 완료 하는 동안 주 연결 된 캐시를 계속 사용할 수 있습니다.
- 에 연결 하는 경우 보조 연결 된 캐시 연결 프로세스가 완료 될 때까지 사용할 수 없습니다.
- 이 연결을 해제 하는 경우 연결 해제 프로세스가 완료 되는 동안 두 캐시 모두 계속 사용할 수 있습니다.

### <a name="can-i-link-more-than-two-caches-together"></a>둘 이상의 캐시를 함께 연결할 수 있나요?

아니요, 두 캐시를 함께 연결할만 있습니다.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?

아니요, 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.

### <a name="can-i-link-two-caches-with-different-sizes"></a>크기가 다른 두 캐시를 연결할 수 있나요?

예, 보조 연결된 캐시가 주 연결된 캐시보다 큰 경우 연결할 수 있습니다.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?

예, 두 캐시의 분할된 데이터베이스 수가 동일한 경우 사용할 수 있습니다.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET의 캐시에 지역에서 복제를 사용할 수 있나요?

예, Vnet에서 캐시의 지역 간 복제 방법으로 사용할 수 있습니다.

- 동일한 VNET에 있는 캐시 간의 지역에서 복제가 지원됩니다.
- 다른 Vnet에 있는 캐시 간의 지역에서 복제도 지원 됩니다.
  - Vnet은 동일한 지역에 있는 경우 연결할 수 있습니다 사용 하 여 [VNET 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 또는 [VPN Gateway VNET 대 VNET 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)합니다.
  - Vnet은 서로 다른 지역의 경우 지역에서 복제 VNET을 사용 하 여 피어 링 기본 내부 부하 분산 장치를 사용 하 여 제약 조건으로 인해 지원 되지 않습니다. VNET 피어 링 제약 조건에 대 한 자세한 내용은 참조 하세요. [가상 네트워크 피어 링-요구 사항 및 제약 조건](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)합니다. 권장 되는 방법은 VPN Gateway VNET 대 VNET 연결을 사용 하는 것입니다.

사용 하 여 [이 Azure 템플릿](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), VPN Gateway VNET 대 VNET 연결을 사용 하 여 연결 된 VNET에 두 개의 지리적으로 복제 된 캐시를 신속 하 게 배포할 수 있습니다.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis 지역에서 복제의 복제 일정이란?

복제 연속 및 비동기로 이며 특정 일정에 따라 발생 하지 않습니다. 주 데이터베이스에 수행 된 모든 쓰기 즉시 및 비동기적으로 보조 데이터베이스에서 복제 됩니다.

### <a name="how-long-does-geo-replication-replication-take"></a>지역에서 복제에 걸리는 기간은 얼마나 되나요?

복제는 증분, 비동기 및 연속 및 지역에 걸쳐 소요 된 시간 대기 시간에서 큰 차이가 없습니다. 특정 상황에서는 보조 캐시는 주 데이터베이스에서 데이터의 전체 동기화를 위해 필요할 수 있습니다. 복제에이 경우의 다양 한 요소에 종속 됩니다: 주 캐시, 사용 가능한 네트워크 대역폭 및 지역 간 대기 시간에 로드 합니다. 전체 53 GB 지리적 복제 쌍에 대 한 복제 시간 사이일 수 5 ~ 10 분을 발견 했습니다.

### <a name="is-the-replication-recovery-point-guaranteed"></a>복제 복구 지점이 보장되나요?

지리적 복제 모드에서 캐시의 경우 지 속성을 사용 하는 사용할 수 없습니다. 지리적 복제 쌍을 연결 하는 고객이 시작한 장애 조치와 같은 없는 경우 보조 연결 된 캐시는 해당 시점까지 동기화 된 데이터를 유지 합니다. 이러한 상황에서 복구 지점이 보장 됩니다.

에서 복구 지점을 가져올 [내보내기](cache-how-to-import-export-data.md#export) 하거나 캐시에서 합니다. 나중에 할 수 있습니다 [가져오기](cache-how-to-import-export-data.md#import) 주 연결 된 캐시에 있습니다.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?

예, Azure portal, PowerShell 또는 Azure CLI를 사용 하 여 지역에서 복제를 관리할 수 있습니다. 자세한 내용은 참조는 [PowerShell 문서](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) 또는 [Azure CLI docs](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)합니다.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?

지역에서 복제를 사용하면 주 연결된 캐시의 데이터가 보조 연결된 캐시에 복제됩니다. 동일한 지역에 두 개의 연결 된 캐시 된 경우에 데이터 전송에 대 한 무료. 두 연결 된 캐시가 다른 지역에 있는 경우 데이터 전송 요금이 두 지역에 걸쳐 이동 하는 데이터의 네트워크 송신 비용. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?

지역에서 복제 링크를 제거할 때까지 연결 하는 동안 지리적으로 복제 된 캐시 및 해당 리소스 그룹을 삭제할 수 없습니다. 연결된 캐시 중 하나 또는 둘 다를 포함하는 리소스 그룹을 삭제하려고 하면, 리소스 그룹의 다른 리소스는 삭제되지만 해당 리소스 그룹은 `deleting` 상태로 유지되고 이 리소스 그룹의 연결된 캐시는 모두 `running` 상태로 남아 있습니다. 리소스 그룹 및 그 연결된 된 캐시를 완전히 삭제 하려면 캐시 연결 해제는에 설명 된 대로 [지역에서 복제 링크 제거](#remove-a-geo-replication-link)합니다.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?

일반적으로 액세스 하는 응용 프로그램과 동일한 Azure 지역에 캐시는 것이 좋습니다. 별도 기본 및 대체 (fallback) 영역을 사용 하 여 응용 프로그램에 대 한 것이 좋습니다에 기본 및 보조 캐시가 동일한 지역에 존재 합니다. 쌍을 이루는 지역에 대한 자세한 내용은 [모범 사례 - Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?

지역에서 복제 된 캐시에 대 한 Azure 지역에서 자동 장애 조치가 지원 되지 않습니다. 재해 복구 시나리오에서는 고객에 게 표시 해야 전체 응용 프로그램 스택을 조정 된 방식으로 해당 백업 지역에 있습니다. 개별 응용 프로그램 구성 요소를 결정할 수 있도록 해당 백업을 자체적으로 전환 하는 경우 성능에 부정적인 영향 수 있습니다. Redis의 주요 이점 중 하나 있다는 것을 매우 지연율이 낮은 저장소입니다. 고객의 기본 응용 프로그램 캐시와 다른 지역에 있으면 추가 왕복 시간 성능에 눈에 띄는 영향을 미칠 있습니다. 이러한 이유로 않도록 했습니다 자동으로 장애 조치 일시적인 가용성 문제로 인해 합니다.

고객이 시작한 장애 조치를 시작 하려면 먼저 캐시 연결 해제 합니다. (이전의 연결 된) 보조 캐시의 연결 끝점을 사용 하 여 Redis 클라이언트를 변경 합니다. 두 캐시가 연결 되 면 보조 캐시 일반 읽기-쓰기 캐시를 다시 되며 Redis 클라이언트에서 직접 요청을 수락 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis 프리미엄 계층](cache-premium-tier-intro.md)에 대해 자세히 알아봅니다.
