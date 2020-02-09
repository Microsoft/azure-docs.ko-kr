---
title: Azure Cosmos DB의 요청 단위 및 처리량
description: Azure Cosmos DB에서 요청 단위 요구 사항을 지정하고 예측하는 방법에 대한 자세한 정보
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383103"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB의 요청 단위

Azure Cosmos DB를 사용하면 프로비전하는 처리량 및 시간 단위로 사용하는 스토리지의 요금을 지불합니다. Azure Cosmos 데이터베이스에 대한 충분 한 시스템 리소스를 항상 사용할 수 있도록 처리량을 프로 비전 해야 합니다. [Azure Cosmos DB sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)를 충족 하거나 초과 하는 데 충분 한 리소스가 필요 합니다.

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다. 

모든 데이터베이스 작업의 비용은 Azure Cosmos DB에 의해 정규화 되며 *요청 단위* (단기)로 표현 됩니다. 초당 RU를 처리량 통화로 간주할 수 있습니다. 초당 RU는 속도 기반 통화로, Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리 등의 시스템 리소스를 추상화합니다. 

1kb 항목을 읽는 비용은 1 개의 요청 단위 (또는 1, 000 개)입니다. 1gb의 각 데이터를 저장 하려면 최소 10 r u/초를 입력 해야 합니다. 다른 모든 데이터베이스 작업에는 또한 RUs를 사용 하 여 비용이 할당 됩니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다.

다음 그림은 RUs의 개략적인 개념을 보여 줍니다.

![데이터베이스 작업은 요청 단위를 사용함](./media/request-units/request-units.png)

용량을 관리하고 계획하려면 Azure Cosmos DB에서는 지정된 데이터 세트를 통해 지정된 데이터베이스 작업에 대한 RU 수가 결정적이 되도록 확인합니다. 응답 헤더를 검사 하 여 모든 데이터베이스 작업에서 사용 되는 RUs의 수를 추적할 수 있습니다. 매우 요금 및 응용 프로그램의 처리량 요구 사항에 [영향을 주는 요소](request-units.md#request-unit-considerations) 를 이해 하면 응용 프로그램 비용을 효과적으로 실행할 수 있습니다.

초당 100 RU 증분으로, 애플리케이션에 대한 RU 수를 프로비전합니다. 애플리케이션에 대해 프로비전되는 처리량 크기를 조정하기 위해 언제든지 RU 수를 늘리거나 줄일 수 있습니다. 100 RUs의 증가 또는 감소를 확장할 수 있습니다. 프로그래밍 방식으로 또는 Azure Portal을 사용하여 변경할 수 있습니다. 시간 단위로 요금이 청구 됩니다.

두 가지 고유 단위로 처리량을 프로비전할 수 있습니다. 

* **컨테이너**: 자세한 내용은 [Azure Cosmos 컨테이너에서 처리량 프로 비전](how-to-provision-container-throughput.md)을 참조 하세요.
* **데이터베이스**: 자세한 내용은 [Azure Cosmos 데이터베이스에서 처리량 프로 비전](how-to-provision-database-throughput.md)을 참조 하세요.

## <a name="request-unit-considerations"></a>요청 단위 고려 사항

프로비전할 초당 RU 수를 예상하는 동안 다음 요소를 고려하세요.

* **항목 크기**: 항목의 크기가 늘어나면 항목을 읽거나 쓰는 데 사용 된 RUs의 수도 늘어납니다.

* **항목 인덱싱**: 기본적으로 각 항목이 자동으로 인덱싱됩니다. 컨테이너에서 일부 항목을 인덱싱하지 않도록 선택하면 더 적은 RU가 사용됩니다.

* **항목 속성 수**: 기본 인덱싱이 모든 속성에 있다고 가정 하면 항목 속성 수가 증가할수록 항목을 쓰는 데 사용 된 RUs 수가 늘어납니다.

* **인덱싱된 속성**: 각 컨테이너의 인덱스 정책에 따라 기본적으로 인덱싱되는 속성이 결정 됩니다. 쓰기 작업에 대한 RU 사용을 줄이려면 인덱싱되는 속성 수를 제한합니다.

* **데이터 일관성**: 강력 하 고 제한 된 부실 일관성 수준에는 다른 완화 된 일관성 수준과 비교할 때 읽기 작업을 수행 하는 동안 두 배 더 많은 RUs가 사용 됩니다.

* **쿼리 패턴**: 쿼리의 복잡성은 작업에 사용 된 RUs의 수에 영향을 줍니다. 쿼리 작업 비용에 영향을 주는 요소는 다음과 같습니다. 
    
    - 쿼리 결과의 수입니다.
    - 조건자 수
    - 조건자의 특성
    - 사용자 정의 함수의 수
    - 원본 데이터의 크기입니다.
    - 결과 집합의 크기
    - 프로젝션

  Azure Cosmos DB는 동일한 데이터의 동일한 쿼리가 항상 반복 실행에서 동일한 RU 수를 사용하도록 보장합니다.

* **스크립트 사용법**: 쿼리를 사용할 때와 마찬가지로 저장 프로시저와 트리거는 수행 되는 작업의 복잡성을 기준으로 RUs를 사용 합니다. 응용 프로그램을 개발할 때 [요청 요금 헤더](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) 를 검사 하 여 각 작업이 사용 하는 수 용량을 더 잘 파악 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량을 프로비전](set-throughput.md)하는 방법을 자세히 알아봅니다.
* [논리 파티션](partition-data.md)에 대해 자세히 알아봅니다.
* [프로비전된 처리량 크기를 전역적으로 조정](scaling-throughput.md)하는 방법을 자세히 알아봅니다.
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* [작업에 대한 요청 단위 요금을 찾는](find-request-unit-charge.md)방법에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로 비전 된 처리량 비용을 최적화](optimize-cost-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos DB에서 읽기 및 쓰기 비용을 최적화](optimize-cost-reads-writes.md)하는 방법을 알아봅니다.
* [Azure Cosmos DB에서 쿼리 비용을 최적화](optimize-cost-queries.md)하는 방법을 알아봅니다.
* [메트릭을 사용](use-metrics.md)하 여 처리량을 모니터링 하는 방법에 대해 알아봅니다.
