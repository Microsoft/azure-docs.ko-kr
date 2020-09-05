---
title: Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s 모니터링
description: Azure Cosmos DB에서 작업의 정규화된 요청 단위 사용량을 모니터링하는 방법을 알아봅니다. 어떤 작업이 더 많은 요청 단위를 소비하는지를 Azure Cosmos DB 계정의 소유자가 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: e7005a3786bb2d538450b076c113e159c766d72e
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642081"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s를 모니터링하는 방법

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다.

**정규화** 된 사용 메트릭을 사용 하 여 파티션 키 범위가 트래픽에 대해 얼마나 포화 상태 인지 확인할 수 있습니다. Azure Cosmos DB는 모든 파티션 키 범위에서 처리량을 동일 하 게 분산 합니다. 이 메트릭은 파티션 키 범위에 대 한 최대 처리량 사용률의 초당 보기를 제공 합니다. 이 메트릭을 사용 하 여 지정 된 컨테이너에 대 한 파티션 키 범위에서 r u/초 사용량을 계산할 수 있습니다. 이 메트릭을 사용 하 여 Azure monitor의 모든 파티션 키 범위에 대 한 요청 단위 사용률의 높은 백분율이 표시 되는 경우 작업 요구 사항에 맞게 처리량을 늘려야 합니다. 

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>정규화된 RU/s가 높은 경우 필요한 항목 및 수행할 작업

정규화 된 r u/초 사용률이 지정 된 파티션 키 범위에 대해 100%에 도달 하 고 클라이언트가 여전히 해당 기간 동안 해당 기간 동안 1 초 동안 요청을 수행 하는 경우에는 비율 제한 오류를 수신 합니다. 클라이언트는 제안 된 대기 시간을 준수 하 고 요청을 다시 시도해 야 합니다. SDK를 사용 하면 적절 한 대기 시간으로 미리 구성 된 시간을 다시 시도 하 여 이러한 상황을 쉽게 처리할 수 있습니다.  정규화 된 r이 100%에 도달 했기 때문에 매우 비율 제한 오류가 표시 되는 것은 아닙니다. 정규화 된 작업은 모든 파티션 키 범위에 대 한 최대 사용량을 나타내는 단일 값 이므로 파티션 키 범위 하나를 사용 중일 수 있지만 다른 파티션 키 범위에서 문제 없이 요청을 처리할 수 있기 때문입니다. 예를 들어 파티션 키 범위에 대 한 모든 o s/s를 사용 하는 저장 프로시저와 같은 단일 작업을 수행 하면 정규화 된 r u/초에 짧은 스파이크가 발생 합니다. 이러한 경우 요청 속도가 낮거나 다른 파티션 키 범위의 다른 파티션에 대 한 요청을 수행 하는 경우 즉시 속도가 제한 되지 않습니다. 

Azure Monitor 메트릭은 **총 요청** 메트릭을 사용 하 여 SQL API에 대 한 상태 코드 별로 작업을 찾는 데 도움이 됩니다. 나중에 이러한 요청을 429 상태 코드로 필터링하고 **작업 유형**별로 분할할 수 있습니다.  

속도가 제한된 요청을 찾는 데 권장되는 방법은 진단 로그를 통해 이 정보를 구하는 것이 좋습니다.

여러 파티션 키 범위에서 100%의 표준화 된 연속 사용량이 나 100%에 근접 한 경우 처리량을 늘리는 것이 좋습니다. Azure monitor 메트릭 및 Azure monitor 진단 로그를 활용 하 여 더 많은 작업 및 피크 사용량을 확인할 수 있습니다.

요약 하자면 **정규화** 된 사용 메트릭은 사용 측면에서 더 웜 된 파티션 키 범위를 확인 하는 데 사용 됩니다. 따라서 파티션 키 범위에 대 한 처리량의 오차를 얻을 수 있습니다. 나중에 Azure Monitor 로그에서 **PartitionKeyRUConsumption** 로그를 확인하고 사용량 측면에서 어떤 논리 파티션 키가 핫(hot)한지에 대한 정보를 얻을 수 있습니다. 이는 파티션 키를 선택 하거나 응용 프로그램 논리의 변경을 가리킵니다. 속도 제한을 해결 하려면 데이터의 부하를 여러 파티션에 분산 하거나 필요에 따라 처리량을 늘리는 것이 좋습니다. 



## <a name="view-the-normalized-request-unit-consumption-metric"></a>정규화된 요청 단위 소비 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **모니터**를 선택하고 **메트릭**을 선택합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창":::

3. **메트릭** 창 > **리소스 선택**에서 필요한 **구독** 및 **리소스 그룹**을 선택합니다. **리소스 유형**으로 **Azure Cosmos DB 계정**을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용**을 선택합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="메트릭을 볼 Azure Cosmos 계정 선택":::

4. 다음에는 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 스토리지, 대기 시간, 가용성, Cassandra 등과 관련된 메트릭을 선택할 수 있습니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예에서는 **정규화된 RU 소비** 메트릭과 **Max**를 집계 값으로 선택하겠습니다.

   이러한 세부 정보 외에 메트릭의 **시간 범위**와 **시간 단위**를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Azure Portal에서 메트릭 선택":::

### <a name="filters-for-normalized-request-unit-consumption"></a>정규화된 요청 단위 소비에 대한 필터

특정 **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**, **Region**별로 표시되는 메트릭 및 차트를 필터링할 수도 있습니다. 메트릭을 필터링하려면 **필터 추가**를 선택하고 필요한 속성(예: **CollectionName** 및 관심 있는 값)을 선택합니다. 그러면 선택한 기간 동안 컨테이너에 소비된 정규화된 RU 소비 단위가 그래프에 표시됩니다.  

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수 있습니다.  

각 컨테이너에 대한 정규화된 요청 단위 소비 메트릭은 다음 이미지와 같이 표시됩니다.

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="정규화된 요청 단위 소비 메트릭에 필터 적용":::

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
