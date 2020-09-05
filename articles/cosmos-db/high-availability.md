---
title: Azure Cosmos DB의 고가용성
description: 이 문서에서는 Azure Cosmos DB에서 고가용성을 제공하는 방법을 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 8bae89e68e5a016dbdc10c763f1ea2daedece3c8
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605324"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB의 고가용성

Azure Cosmos DB Azure Cosmos 계정에 연결 된 모든 Azure 지역에서 데이터를 투명 하 게 복제 합니다. Azure Cosmos DB는 다음 이미지와 같이 데이터에 대 한 여러 가지 중복 계층을 활용 합니다.

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="물리적 분할" border="false":::

- Azure Cosmos 컨테이너 내의 데이터는 [수평 분할](partitioning-overview.md)됩니다.

- 파티션 집합은 여러 복제본 집합의 컬렉션입니다. 각 지역 내에서 모든 파티션은 모든 쓰기가 복제되는 복제본 세트로 보호되며, 대부분의 복제본에서 지속적으로 커밋됩니다. 복제본은 10 ~ 20개의 장애 도메인 간에 분산됩니다.

- 모든 지역에 걸쳐 있는 각 파티션이 복제됩니다. 각 지역에는 Azure Cosmos 컨테이너의 모든 데이터 파티션이 포함 되며 쓰기를 허용 하 고 읽기를 제공할 수 있습니다.  

Azure Cosmos 계정이 *n* 개의 azure 지역에 배포 되는 경우 모든 데이터의 최소 *n* x 4 복사본이 있습니다. 일반적으로 2 개 이상의 지역에 Azure Cosmos 계정이 있으면 응용 프로그램의 가용성이 향상 되 고 연결 된 지역에서 짧은 대기 시간을 제공 합니다. 

## <a name="slas-for-availability"></a>가용성 SLA

전역적으로 분산 된 데이터베이스인 Azure Cosmos DB는 처리량, 대기 시간 (99 번째 백분위 수), 일관성 및 고가용성을 포함 하는 포괄적인 Sla를 제공 합니다. 아래 표에서는 단일 및 다중 지역 계정에 대해 Azure Cosmos DB에서 제공 하는 고가용성을 보장 합니다. 고가용성을 위해 항상 여러 쓰기 영역 (다중 마스터가 라고도 함)을 갖도록 Azure Cosmos 계정을 구성 합니다.

|작업 유형  | 단일 지역 |다중 지역(단일 지역 쓰기)|다중 지역(다중 지역 쓰기) |
|---------|---------|---------|-------|
|쓰기    | 99.99    |99.99   |99.999|
|읽기     | 99.99    |99.999  |99.999|

> [!NOTE]
> 실제로 제한 된 부실, 세션, 일관 된 접두사 및 최종 일관성 모델에 대 한 실제 쓰기 가용성이 게시 된 Sla 보다 훨씬 더 높습니다. 모든 일관성 수준에 대한 실제 읽기 가용성은 게시된 SLA보다 훨씬 높습니다.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>지역 가동 중단 시 Azure Cosmos DB를 통한 고가용성

드물지만 지역 가동 중단이 발생 하는 경우에 Azure Cosmos DB 데이터베이스를 항상 항상 사용할 수 있도록 합니다. 다음 세부 정보는 Azure Cosmos 계정 구성에 따라 가동 중단 중에 Azure Cosmos DB 동작을 캡처합니다.

- Azure Cosmos DB를 사용 하는 경우 쓰기 작업이 클라이언트에 승인 되기 전에 쓰기 작업을 수락 하는 지역 내의 복제본 쿼럼에 의해 데이터가 지속적으로 커밋됩니다.

- 다중 쓰기 지역/다중 마스터를 사용 하 여 구성 된 다중 지역 계정은 쓰기 및 읽기 모두에서 항상 사용 가능 합니다. 지역별 장애 조치(failover)는 즉각적이며, 애플리케이션에서 변경할 필요가 없습니다.

- 단일 지역 계정은 지역 중단으로 인해 가용성이 손실될 수도 있습니다. 항상 고가용성을 보장 하기 위해 **두 개 이상의 지역** (하나 이상의 쓰기 지역)을 Azure Cosmos 계정으로 설정 하는 것이 좋습니다.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>단일 쓰기 지역이 있는 다중 지역 계정 (쓰기 영역 중단)

- Azure Cosmos 계정에 **자동 장애 조치 (failover)** 를 구성 하는 경우 azure Cosmos 계정은 쓰기 지역 가동 중단 중에 자동으로 보조 지역을 새 주 쓰기 지역으로 승격 합니다. 사용 하도록 설정 하면 지정한 지역 우선 순위에 따라 다른 지역으로 장애 조치 (failover)가 수행 됩니다.
- 이전에 영향을 받은 지역이 다시 온라인 상태가 되 면 해당 지역이 실패 했을 때 복제 되지 않은 모든 쓰기 데이터는 [충돌 피드](how-to-manage-conflicts.md#read-from-conflict-feed)를 통해 사용할 수 있게 됩니다. 응용 프로그램은 충돌 피드를 읽고, 응용 프로그램별 논리에 따라 충돌을 해결 하 고, 업데이트 된 데이터를 적절 하 게 Azure Cosmos 컨테이너에 다시 쓸 수 있습니다.
- 이전에 영향을 받는 쓰기 지역이 복구되고 나면, 자동으로 읽기 지역으로 사용할 수 있게 됩니다. 쓰기 지역으로 복구 된 지역으로 다시 전환할 수 있습니다. [PowerShell, Azure CLI 또는 Azure Portal](how-to-manage-database-account.md#manual-failover)를 사용 하 여 지역을 전환할 수 있습니다. 쓰기 지역을 전환 하 고 응용 프로그램의 가용성이 계속 유지 되기 전에는 **데이터 나 가용성 손실이 발생 하지** 않습니다.

> [!IMPORTANT]
> **자동 장애 조치 (failover)를 사용 하도록 설정**하려면 프로덕션 워크 로드에 사용 되는 Azure Cosmos 계정을 구성 하는 것이 좋습니다. 장애 조치 (failover) 중에 데이터 손실이 발생 하지 않도록 수동 장애 조치 (failover)를 수행 하려면 보조 및 기본 쓰기 지역 간의 연결이 필요 합니다. 주 지역을 사용할 수 없는 경우이 일관성 검사를 완료할 수 없으며 수동 장애 조치 (failover)가 실패 하 여 쓰기 가용성이 손실 됩니다.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>단일 쓰기 지역이 있는 다중 지역 계정 (읽기 영역 중단)

- 읽기 지역 가동 중단 중에는 세 개 이상의 읽기 지역에서 일관성 수준 또는 강력한 일관성을 사용 하는 Azure Cosmos 계정이 읽기 및 쓰기에 항상 사용 가능한 상태로 유지 됩니다.
- 읽기 & 쓰기 영역을 포함 하는 읽기 지역 두 개 이하로 강력한 일관성을 사용 하는 Azure Cosmos 계정은 읽기 지역 가동 중단 중에 쓰기 가용성을 상실 하지만 남은 지역에 대해서는 읽기 가용성을 유지 합니다.
- 영향을 받는 지역은 자동으로 연결이 끊어지고 오프 라인으로 표시 됩니다. [Azure Cosmos DB sdk](sql-api-sdk-dotnet.md) 는 기본 지역 목록에서 사용 가능한 다음 지역으로 읽기 호출을 리디렉션합니다.
- 기본 설정 지역 목록의 어느 지역도 사용할 수 없는 경우 호출은 현재 쓰기 지역으로 자동으로 대체됩니다.
- 읽기 지역 중단을 처리하기 위해 애플리케이션 코드를 변경할 필요가 없습니다. 영향을 받는 읽기 지역이 다시 온라인 상태가 되 면 현재 쓰기 지역과 자동으로 동기화 되 고 읽기 요청을 제공 하기 위해 다시 사용할 수 있게 됩니다.
- 후속 읽기는 애플리케이션 코드를 변경하지 않고도 복구된 지역으로 리디렉션됩니다. 이전에 실패 한 지역에 대 한 장애 조치 (failover)와 다시 가입 하기 중에는 Azure Cosmos DB에서 읽기 일관성을 유지 합니다.
- Azure 지역이 영구적으로 복구할 수 없는 때 드물게 발생 하는 이벤트 에서도, 다중 지역 Azure Cosmos 계정이 *강력한* 일관성으로 구성 된 경우 데이터가 손실 되지 않습니다. 제한 된 부실 일관성을 사용 하 여 구성 된 다중 지역 Azure Cosmos 계정에 대 한 영구 복구할 수 없는 쓰기 지역의 경우 잠재적 데이터 손실 기간은 K = 100000 업데이트 및 T = 5 분 인 부실 창 (*k* 또는 *t*)으로 제한 됩니다. 세션, 일관 된 접두사 및 최종 일관성 수준에 대 한 잠재적인 데이터 손실 기간은 최대 15 분으로 제한 됩니다. Azure Cosmos DB의 RTO 및 RPO 대상에 대 한 자세한 내용은 [일관성 수준 및 데이터](consistency-levels-tradeoffs.md#rto) 지 속성을 참조 하세요.

## <a name="availability-zone-support"></a>가용성 영역 지원

지역 간 복원 력 외에도 이제 Azure Cosmos 데이터베이스와 연결할 지역을 선택할 때 **영역 중복성** 을 사용 하도록 설정할 수 있습니다.

가용성 영역을 지 원하는 Azure Cosmos DB를 사용 하 여 복제본이 지정 된 지역 내의 여러 영역에 배치 되도록 하 여 영역 오류 발생 시 고가용성 및 복원 력을 제공 합니다. 이 구성에서는 대기 시간 및 기타 Sla를 변경 하지 않습니다. 단일 영역 장애가 발생 하는 경우 영역 중복성은 RPO = 0 및 RTO = 0 인 가용성에 대 한 전체 데이터 내구성을 제공 합니다.

영역 중복성은 [다중 지역 쓰기 기능에서 복제](how-to-multi-master.md) 에 대 한 *추가 기능* 입니다. 영역 중복성 만으로는 지역 복원 력을 달성할 수 없습니다. 예를 들어 지역에 걸친 지역 가동 중단 또는 짧은 대기 시간에 대 한 이벤트의 경우 영역 중복성 외에도 여러 쓰기 영역을 사용 하는 것이 좋습니다.

Azure Cosmos 계정에 대 한 다중 지역 쓰기를 구성 하는 경우 추가 비용 없이 영역 중복성을 옵트인 (opt in) 할 수 있습니다. 그렇지 않으면 영역 중복성 지원에 대 한 가격 책정과 관련 된 아래 참고를 참조 하세요. 지역을 제거 하 고 영역 중복성을 사용 하 여 다시 추가 하 여 Azure Cosmos 계정의 기존 지역에서 영역 중복성을 사용 하도록 설정할 수 있습니다.

이 기능은 *영국 남부, 동남 아시아, 미국 동부, 미국 동부 2, 미국 중부, 유럽 서부, 미국 서 부 2, 일본 동부, 유럽 북부, 프랑스 중부, 오스트레일리아 동부* 지역에서 사용할 수 있습니다.

> [!NOTE]
> 단일 지역 Azure Cosmos 계정에 대해 가용성 영역를 사용 하도록 설정 하면 계정에 추가 지역을 추가 하는 것과 동일한 요금이 부과 됩니다. 가격 책정에 대 한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 및 [Azure Cosmos DB 문서의 다중 지역 비용](optimize-cost-regions.md) 을 참조 하세요.

다음 표에는 다양 한 계정 구성의 고가용성 기능이 요약 되어 있습니다.

|KPI  |가용성 영역 없는 단일 영역 (AZ)  |가용성 영역 있는 단일 지역 (AZ)  |가용성 영역 (AZ, 2 regions)를 사용 하 여 다중 지역 작성-가장 권장 되는 설정 |
|---------|---------|---------|---------|
|쓰기 가용성 SLA | 99.99% | 99.99% | 99.999% |
|가용성 SLA 읽기  | 99.99% | 99.99% | 99.999% |
|가격 | 단일 지역 청구 요금 | 단일 지역 가용성 영역 요금 청구 요금 | 다중 지역 요금 청구 요금 |
|영역 오류-데이터 손실 | 데이터 손실 | 데이터 손실 없음 | 데이터 손실 없음 |
|영역 오류-가용성 | 가용성 손실 | 가용성 손실 없음 | 가용성 손실 없음 |
|읽기 대기 시간 | 지역 간 | 지역 간 | 낮음 |
|쓰기 대기 시간 | 지역 간 | 지역 간 | 낮음 |
|지역 가동 중단-데이터 손실 | 데이터 손실 |  데이터 손실 | 데이터 손실 <br/><br/> 여러 쓰기 지역과 둘 이상의 지역에서 제한 된 부실 일관성을 사용 하는 경우 데이터 손실은 계정에 구성 된 제한 된 부실 항목으로 제한 됩니다. <br /><br />여러 지역에서 강력한 일관성을 구성 하 여 지역 가동 중단 중에 데이터 손실을 방지할 수 있습니다. 이 옵션은 가용성 및 성능에 영향을 주는 장단점을 제공 합니다. 단일 지역 쓰기에 대해 구성 된 계정에만 구성할 수 있습니다. |
|지역 가동 중단-가용성 | 가용성 손실 | 가용성 손실 | 가용성 손실 없음 |
|처리량 | X r u/초 프로 비전 된 처리량 | X r u/초 프로 비전 된 처리량 | 프로 비전 된 처리량 2 배 <br/><br/> 이 구성 모드를 사용 하려면 두 개의 지역이 있기 때문에 가용성 영역를 사용 하는 단일 지역과 비교할 때 처리량의 두 배가 필요 합니다. |

> [!NOTE]
> 다중 지역 Azure Cosmos 계정에 대 한 가용성 영역 지원을 사용 하도록 설정 하려면 계정에 다중 지역 쓰기 쓰기가 활성화 되어 있어야 합니다.

새 또는 기존 Azure Cosmos 계정에 영역을 추가할 때 영역 중복성을 사용 하도록 설정할 수 있습니다. Azure Cosmos 계정에서 영역 중복성을 사용 하도록 설정 하려면 `isZoneRedundant` `true` 특정 위치에 대 한 플래그를로 설정 해야 합니다. 이 플래그는 위치 속성 내에서 설정할 수 있습니다. 예를 들어 다음 PowerShell 코드 조각은 "동남 아시아" 지역에 대해 영역 중복성을 사용 하도록 설정 합니다.

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

다음 명령은 "EastUS" 및 "WestUS2" 지역에 대해 영역 중복성을 사용 하도록 설정 하는 방법을 보여 줍니다.

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Azure Cosmos 계정을 만들 때 Azure Portal를 사용 하 여 가용성 영역를 사용 하도록 설정할 수 있습니다. 계정을 만들 때 **지역 중복**, **다중 지역 쓰기**를 사용 하도록 설정 하 고 가용성 영역 지원 되는 지역을 선택 해야 합니다.

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="Azure Portal를 사용 하 여 가용성 영역 사용"::: 

## <a name="building-highly-available-applications"></a>고가용성 애플리케이션 빌드

- 높은 쓰기 및 읽기 가용성을 보장 하려면 여러 쓰기 지역이 있는 두 개 이상의 지역에 걸쳐 Azure Cosmos 계정을 구성 합니다. 이 구성은 Sla에서 지원 되는 읽기 및 쓰기 둘 다에 대해 최고 가용성, 가장 낮은 대기 시간 및 최고 확장성을 제공 합니다. 자세한 내용은 [여러 쓰기 영역을 사용 하 여 Azure Cosmos 계정을 구성](tutorial-global-distribution-sql-api.md)하는 방법을 참조 하세요.

- 단일 쓰기 지역으로 구성 된 다중 지역 Azure Cosmos 계정의 경우 [Azure CLI 또는 Azure Portal를 사용 하 여 자동 장애 조치 (failover)를 사용 하도록 설정](how-to-manage-database-account.md#automatic-failover)합니다. 자동 장애 조치(failover)를 사용하도록 설정하면, Cosmos DB는 지역 재해가 있을 때마다 자동으로 사용자 계정을 장애 조치(failover)합니다.  

- Azure Cosmos 계정을 항상 사용할 수 있는 경우에도 응용 프로그램이 항상 사용 가능한 상태로 유지 되도록 올바르게 설계 되지 않았을 수 있습니다. 응용 프로그램 테스트 또는 DR (재해 복구) 드릴의 일부로 응용 프로그램의 종단 간 고가용성을 테스트 하려면 계정에 대해 자동 장애 조치 (failover)를 일시적으로 사용 하지 않도록 설정 하 고 [PowerShell, Azure CLI 또는 Azure Portal를 사용 하 여 수동 장애](how-to-manage-database-account.md#manual-failover)조치 (failover)를 호출한 다음 응용 프로그램의 장애 조치 (failover)를 모니터링 합니다. 완료 되 면 주 지역으로 장애 복구 (failback) 하 고 계정에 대 한 자동 장애 조치 (failover)를 복원할 수 있습니다.

- 전역적으로 분산 된 데이터베이스 환경 내에서는 지역 전체 중단이 발생 했을 때 일관성 수준 및 데이터 내 구성을 직접 관계가 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트가 발생한 후 애플리케이션이 완전히 복구되기까지 허용되는 최대 시간을 이해해야 합니다. 애플리케이션을 완전히 복구하는 데 필요한 시간을 RTO(복구 시간 목표)라고 합니다. 또한 중단 이벤트가 발생한 후 복구될 때 애플리케이션에서 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 기간도 이해해야 합니다. 손실될 수 있는 업데이트 기간을 RPO(복구 지점 목표)라고 합니다. Azure Cosmos DB의 RPO 및 RTO를 확인하려면 [일관성 수준 및 데이터 내구성](consistency-levels-tradeoffs.md#rto)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 읽을 수 있습니다.

- [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
- [전역적으로 프로비전된 처리량 크기 조정](scaling-throughput.md)
- [글로벌 배포 - 내부 살펴보기](global-dist-under-the-hood.md)
- [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
- [여러 쓰기 영역으로 Cosmos 계정을 구성 하는 방법](how-to-multi-master.md)
