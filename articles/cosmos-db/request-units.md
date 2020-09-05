---
title: Azure Cosmos DB에서 처리량 및 성능 통화로 요청 단위
description: Azure Cosmos DB에서 요청 단위 요구 사항을 지정하고 예측하는 방법에 대한 자세한 정보
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604818"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB의 요청 단위

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업의 비용은 Azure Cosmos DB에 의해 정규화 되며 *요청 단위* (단기)로 표현 됩니다. RUs는 Azure Cosmos DB에서 지 원하는 데이터베이스 작업을 수행 하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화 하는 성능 통화로 간주할 수 있습니다.

1 KB 항목에 대 한 지점 읽기 (즉, ID 및 파티션 키 값을 기준으로 단일 항목 페치)의 비용은 1 개의 요청 단위 (또는 1, 000 개)입니다. RU를 사용하여 다른 모든 데이터베이스 작업에 비슷하게 비용이 할당됩니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리 인지에 상관 없이 비용은 항상 RUs로 측정 됩니다.

다음 이미지는 RU의 개략적인 개념을 보여 줍니다.

:::image type="content" source="./media/request-units/request-units.png" alt-text="데이터베이스 작업은 요청 단위를 사용함" border="false":::

용량을 관리하고 계획하려면 Azure Cosmos DB에서는 지정된 데이터 세트를 통해 지정된 데이터베이스 작업에 대한 RU 수가 결정적이 되도록 확인합니다. 응답 헤더를 검사하여 모든 데이터베이스 작업에서 사용하는 RU 수를 추적할 수 있습니다. 매우 요금 및 응용 프로그램의 처리량 요구 사항에 [영향을 주는 요소](request-units.md#request-unit-considerations) 를 이해 하면 응용 프로그램 비용을 효과적으로 실행할 수 있습니다.

사용 중인 Azure Cosmos 계정 형식에 따라 사용 된 RUs의 요금 청구 방법이 결정 됩니다.

- [프로 비전 된 처리량](set-throughput.md) 모드에서는 초당 초당 100 rus 단위로 응용 프로그램의 rus 수를 프로 비전 합니다. 응용 프로그램에 대해 프로 비전 된 처리량의 크기를 조정 하기 위해 언제 든 지 100 RUs의 증가 또는 감소를 통해 RUs 수를 늘리거나 줄일 수 있습니다. 프로그래밍 방식으로 또는 Azure Portal을 사용하여 변경할 수 있습니다. 프로 비전 한 초당 RUs의 양에 대 한 요금이 청구 됩니다. 두 가지 고유 단위로 처리량을 프로비전할 수 있습니다.
  - **컨테이너**: 자세한 내용은 [Azure Cosmos 컨테이너에서 처리량 프로 비전](how-to-provision-container-throughput.md)을 참조 하세요.
  - **데이터베이스**: 자세한 내용은 [Azure Cosmos 데이터베이스에서 처리량 프로 비전](how-to-provision-database-throughput.md)을 참조 하세요.
- [서버](serverless.md) 를 사용 하지 않는 모드에서는 Azure Cosmos 계정에 리소스를 만들 때 처리량을 프로 비전 할 필요가 없습니다. 청구 기간이 종료 되 면 데이터베이스 작업에서 사용한 요청 단위 양에 대 한 요금이 청구 됩니다.

## <a name="request-unit-considerations"></a>요청 단위에 대한 고려 사항

작업에서 사용 하는 RUs의 수를 예상 하는 동안 다음 요인을 고려 합니다.

* **항목 크기**: 항목 크기가 늘어나면 항목을 읽거나 쓰는 데 사용되는 RU 수도 증가합니다.

* **항목 인덱싱**: 기본적으로 각 항목은 자동으로 인덱싱됩니다. 컨테이너에서 일부 항목을 인덱싱하지 않도록 선택하면 사용되는 RU 수가 감소합니다.

* **항목 속성 수**: 모든 속성에 기본 인덱싱이 있다고 가정하면 항목 속성 수가 증가함에 따라 항목을 작성하는 데 사용되는 RU 수도 증가합니다.

* **인덱싱되는 속성 수**: 각 컨테이너의 인덱스 정책에 따라 기본적으로 인덱싱되는 속성이 결정됩니다. 쓰기 작업에 필요한 RU 사용량을 줄이려면 인덱싱된 속성 수를 제한합니다.

* **데이터 일관성**: 강력 하 고 제한 된 부실 일관성 수준에는 다른 완화 된 일관성 수준과 비교할 때 읽기 작업을 수행 하는 동안 두 배 더 많은 RUs가 사용 됩니다.

* **읽기 유형**: Point reads는 쿼리 보다 RUs의 비용을 크게 줄일 수 있습니다.

* **쿼리 패턴**: 쿼리의 복잡성은 작업에 사용되는 RU 수에 영향을 줍니다. 쿼리 작업 비용에 영향을 주는 요소는 다음과 같습니다. 
    
    - 쿼리 결과 수
    - 조건자 수
    - 조건자 특성
    - 사용자 정의 함수의 수
    - 원본 데이터 크기
    - 결과 집합 크기
    - 프로젝션 수

  Azure Cosmos DB는 동일한 데이터에 대한 동일한 쿼리에서 반복 실행 시 항상 동일한 수의 RU를 사용하도록 보장합니다.

* **스크립트 사용법**: 쿼리를 사용할 때와 마찬가지로 저장 프로시저와 트리거는 수행 되는 작업의 복잡성을 기준으로 RUs를 사용 합니다. 애플리케이션을 개발하는 과정에서 각 작업에 사용되는 RU 용량을 더 잘 파악할 수 있도록 [요청 요금 헤더](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)를 살펴보세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량을 프로비전](set-throughput.md)하는 방법을 자세히 알아봅니다.
* [Azure Cosmos DB에서 서버를](serverless.md)사용 하지 않는 방법에 대해 자세히 알아보세요.
* [논리 파티션](partition-data.md)에 대해 자세히 알아봅니다.
* [프로비전된 처리량 크기를 전역적으로 조정](scaling-throughput.md)하는 방법을 자세히 알아봅니다.
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* [작업에 대 한 요청 단위 요금을 찾는](find-request-unit-charge.md)방법에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로 비전 된 처리량 비용을 최적화](optimize-cost-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos DB에서 읽기 및 쓰기 비용을 최적화](optimize-cost-reads-writes.md)하는 방법을 알아봅니다.
* [Azure Cosmos DB에서 쿼리 비용을 최적화](optimize-cost-queries.md)하는 방법을 알아봅니다.
* [메트릭을 사용](use-metrics.md)하 여 처리량을 모니터링 하는 방법에 대해 알아봅니다.
