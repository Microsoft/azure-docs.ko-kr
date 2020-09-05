---
title: Azure Cosmos DB의 소비 기반 서버를 사용 하지 않는 제품
description: Azure Cosmos DB의 소비 기반 서버를 사용 하지 않는 제품에 대해 자세히 알아보세요.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: ef681f861a14fbbf86e7e350441d05f84fc95f58
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757900"
---
# <a name="azure-cosmos-db-serverless-preview"></a>서버를 사용 하지 않는 Azure Cosmos DB (미리 보기)

> [!IMPORTANT]
> 서버를 사용 하지 않는 Azure Cosmos DB 현재 미리 보기 상태입니다. 이 미리 보기 버전은 Service Level Agreement(서비스 수준 약정) 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

서버를 사용 하지 않는 Azure Cosmos DB 데이터베이스 작업에서 사용한 요청 단위 및 데이터에서 사용 하는 저장소에 대해서만 요금이 부과 되는 소비 기반 방식으로 Azure Cosmos 계정을 사용할 수 있습니다. 서버를 사용 하지 않는 모드로 Azure Cosmos DB를 사용 하는 경우에는 최소한의 요금이 부과 되지 않습니다.

> [!IMPORTANT] 
> 서버 리스에 대 한 의견이 있나요? 의견을 듣고 싶습니다! 서버를 사용 하지 않는 Azure Cosmos DB 팀에 게 메시지를 끌어서 놓으면 [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 됩니다.

Azure Cosmos DB 사용 하는 경우 모든 데이터베이스 작업에는 [요청 단위로](request-units.md)표현 된 비용이 있습니다. 이 비용에 대해 부과 되는 방법은 사용 하는 Azure Cosmos 계정 유형에 따라 달라 집니다.

- [프로 비전 된 처리량](set-throughput.md) 모드에서는 데이터베이스 및 컨테이너에 프로 비전 되는 특정 처리량 (초당 요청 단위)으로 커밋해야 합니다. 그런 다음 데이터베이스 작업 비용은 매 초 마다 사용 가능한 요청 단위 수에서 공제 됩니다. 청구 기간이 끝나면 프로 비전 한 처리량의 양에 대 한 요금이 청구 됩니다.
- 서버를 사용 하지 않는 모드에서는 Azure Cosmos 계정에 컨테이너를 만들 때 처리량을 프로 비전 할 필요가 없습니다. 청구 기간이 종료 되 면 데이터베이스 작업에 사용 된 요청 단위 수에 대 한 요금이 청구 됩니다.

## <a name="use-cases"></a>사용 사례

Azure Cosmos DB 서버를 사용 하지 않는 최상의 시나리오는 다음과 같습니다.

- **광원 트래픽**: 이러한 상황에서 프로 비전 용량이 필요 하지 않으며 비용이 높을 수 있기 때문입니다.
- **중간 규모 간 안정성**: 서버 리스 컨테이너는 초당 최대 5000 요청 단위를 제공할 수 있기 때문입니다.
- **보통 성능**: 서버 리스 컨테이너에 [특정 성능 특성이](#performance) 있으므로

이러한 이유로 서버를 사용 하지 않는 Azure Cosmos DB 다음 유형의 작업에 대해 고려해 야 합니다.

- 개발
- 테스트
- 프로토타입 생성
- 개념 증명
- 낮은 트래픽이 있는 중요 하지 않은 응용 프로그램

사용 사례에 가장 적합 한 제품을 선택 하는 방법에 대 한 자세한 지침은 [프로 비전 된 처리량과 서버 리스 서버 간에 선택 하는 방법](throughput-serverless.md) 문서를 참조 하세요.

## <a name="using-serverless-resources"></a>서버 리스 리소스 사용

서버를 사용 하지 않는 새 Azure Cosmos 계정 유형입니다. 즉, 새 계정을 만들 때 **프로 비전 된 처리량** 과 **서버 리스 서버** 를 선택 해야 합니다. 서버를 사용 하지 않는 상태로 시작 하려면 서버를 사용 하지 않는 새 계정을 만들어야 합니다. Preview 릴리스 중에는 [Azure Portal를 사용 하](create-cosmosdb-resources-portal.md)여 새 서버를 사용 하지 않는 계정을 만들 수 있습니다. 현재 서버를 사용 하지 않는 모드로 기존 계정 마이그레이션은 지원 되지 않습니다.

> [!NOTE]
> 서버 리스는 현재 Azure Cosmos DB 코어 (SQL) API 에서만 지원 됩니다.

서버를 사용 하지 않는 계정에서 만든 컨테이너는 서버를 사용 하지 않는 컨테이너입니다. 서버를 사용 하지 않는 컨테이너는 프로 비전 된 처리량 모드로 만든 컨테이너와 동일한 기능을 제공 하므로 정확히 동일한 방법으로 데이터를 읽고 쓰고 쿼리 합니다. 그러나 서버 리스 계정 및 컨테이너에는 특정 특성도 있습니다.

> [!IMPORTANT]
> 이러한 제한 사항 중 일부는 서버 리스가 일반 공급 되는 경우 줄어들 또는 제거 될 수 있으며, **사용자 의견** 을 통해 결정 하는 데 도움이 됩니다. 서버를 사용 하지 않는 환경에 대해 설명 하 고이에 대해 자세히 알려주세요 [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- 서버를 사용 하지 않는 계정은 단일 Azure 지역 에서만 실행할 수 있습니다. 서버를 만든 후에는 서버를 사용 하지 않는 계정에 Azure 영역을 더 추가할 수 없습니다.
- 서버 리스 계정에서는 [Synapse Link 미리 보기 기능](synapse-link.md) 을 사용 하도록 설정할 수 없습니다.
- 서버를 사용 하지 않는 컨테이너에서는 프로 비전 처리량이 필요 하지 않으므로 다음 문을 적용할 수 있습니다.
    - 서버를 사용 하지 않는 컨테이너를 만들 때 처리량을 전달할 수 없으며이로 인해 오류가 반환 됩니다.
    - 서버를 사용 하지 않는 컨테이너에서는 처리량을 읽거나 업데이트할 수 없으며이로 인해 오류가 반환 됩니다.
    - 서버를 사용 하지 않는 계정에서 공유 처리량 데이터베이스를 만들 수 없으므로 오류가 반환 됩니다.
- 서버를 사용 하지 않는 컨테이너는 초당 5000 요청 단위의 최대 처리량을 볼 수 있습니다.
- 서버를 사용 하지 않는 컨테이너는 최대 50 GB의 데이터 및 인덱스를 저장할 수 있습니다.

## <a name="monitoring-your-consumption"></a>사용량 모니터링

이전에 프로 비전 된 처리량 모드에서 Azure Cosmos DB 사용 하는 경우 트래픽이 프로 비전 된 용량을 정당화 하지 않는 경우 서버를 사용 하지 않는 것이 더 비용 효율적 이라는 것을 알 수 있습니다. 데이터베이스에서 처리 한 요청 수를 기준으로 요금이 청구 되므로 비용은 예측할 수 없게 됩니다. 따라서 현재 소비를 파악 하는 것이 중요 합니다.

계정의 **메트릭** 창을 검색할 때 **개요** 탭에서 **사용한 Request unit** 이라는 차트를 찾을 수 있습니다. 이 차트에서는 계정이 사용한 요청 단위 수를 보여 줍니다.

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="사용 된 요청 단위를 보여 주는 차트" border="false":::

[여기](monitor-request-unit-usage.md)에 설명 된 대로 Azure Monitor를 사용 하는 경우 동일한 차트를 찾을 수 있습니다. Azure Monitor를 사용 하면 요청 단위 사용이 특정 임계값을 통과 한 경우 사용자에 게 알리는 데 사용할 수 있는 [경고](../azure-monitor/platform/alerts-metric-overview.md)를 설정할 수 있습니다.

## <a name="performance"></a><a id="performance"></a>성능

서버를 사용 하지 않는 리소스는 프로 비전 된 처리량 리소스가 제공 하는 것과 다른 특정 성능 특성을 제공 합니다.

- **가용성**: 서버를 사용 하지 않는 제품이 일반 공급 되 면 가용성 영역 (영역 중복성)이 사용 되지 않을 때 서버 리스 컨테이너의 가용성이 99.9%의 SERVICE LEVEL AGREEMENT(서비스 수준 약정) (SLA)에 포함 됩니다. 가용성 영역 사용 되는 경우 SLA는 99.99%입니다.
- **대기 시간**: 서버를 사용 하지 않는 제품이 일반 공급 되 면 서버 리스 컨테이너의 대기 시간에 대해 10 밀리초 미만의 SLO (서비스 수준 목표)가 적용 됩니다. 지점 읽기 작업은 ID 및 파티션 키 값을 기준으로 단일 항목을 인출 하는 것으로 구성 됩니다.
- **삼 트 안정성**: 서버를 사용 하지 않는 제품이 일반 공급 되 면 서버 리스 컨테이너의 향상 된 안정성은 95%의 SLO (서비스 수준 목표)에 포함 됩니다. 즉, 최대 삼 시간을 최소 95%의 시간 동안 달성할 수 있습니다.

> [!NOTE]
> Azure 미리 보기 Azure Cosmos DB 서버 리스 서버는 SLA (서비스 수준 계약)에서 제외 됩니다. 위에서 언급 한 성능 특징은 일반적으로 사용할 수 있는 경우이 제품이 제공 하는 내용에 대 한 미리 보기로 제공 됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용 하 여 서버를 사용 하지 않는 상태로 시작 하세요.

- [Azure Cosmos DB의 요청 단위](request-units.md)
- [프로비저닝된 처리량과 서버리스 중에서 선택](throughput-serverless.md)
- [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
