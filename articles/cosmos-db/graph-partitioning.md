---
title: Azure Cosmos DB Gremlin API의 데이터 분할
description: Azure Cosmos DB에서 분할된 그래프를 사용하는 방법을 알아봅니다. 이 문서에서는 또한 분할된 그래프에 대한 요구 사항 및 모범 사례를 설명합니다.
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888416"
---
<!--Verify sucessfully-->
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할된 그래프 사용

Azure Cosmos DB에서 Gremlin API의 주요 기능 중 하나는 수평 확장을 통해 대규모 그래프를 처리하는 기능입니다. 수평 크기 조정은 [Azure Cosmos DB의 분할 기능](partition-data.md)을 통해 이루어집니다. 컨테이너는 스토리지 및 처리량 측면에서 독립적으로 확장할 수 있습니다. 그래프 데이터를 저장하기 위해 자동으로 크기를 조정할 수 있는 Azure Cosmos DB에서 컨테이너를 만들 수 있습니다. 데이터는 지정된 **파티션 키**를 기준으로 자동으로 균형이 조정됩니다.

이 문서에서 그래프 데이터베이스가 분할되는 방식에 대한 사양은 꼭짓점(또는 절점) 및 모서리를 활용해서 설명됩니다.

## <a name="requirements-for-partitioned-graph"></a>분할된 그래프에 대한 요구 사항

다음은 분할된 그래프 컨테이너를 만들 때 이해해야 하는 세부 사항입니다.

- 컨테이너 크기가 10GB 이상을 저장해야 하는 경우 또는 초당 10,000개 이상의 요청 단위를 할당하려는 경우 **분할이 필요합니다**.

- **꼭짓점 및 가장자리는 모두 JSON 문서로 저장됩니다**.

- **꼭짓점에는 파티션 키가 필요합니다**. 이 키는 해시 알고리즘을 통해 꼭짓점이 저장되는 파티션을 결정합니다. 이 파티션 키의 이름은 공백이나 특수 문자가 포함되지 않은 단일 단어 문자열입니다. 새 컨테이너를 만들 때 파티션 키가 정의되며 `/partitioning-key-name` 형식입니다.

- **모서리는 원본 꼭짓점과 함께 저장됩니다**. 즉, 각 꼭짓점의 경우 해당 파티션 키는 돌출된 모서리와 함께 저장되는 위치를 정의합니다. 이것은 그래프 쿼리에서 `out()` 카디널리티를 사용할 때 교차 분할 쿼리를 방지하기 위한 것입니다.

- **그래프 쿼리는 파티션 키를 지정해야 합니다**. Azure Cosmos DB에서 수평 분할을 최대한 활용하려면, 가능할 경우 단일 꼭짓점을 선택할 때마다 파티션 키를 지정해야 합니다. 다음은 분할된 그래프에서 하나 이상의 꼭짓점을 선택하기 위한 쿼리입니다.

    - `/id` 및 `/label`는 Gremlin API의 컨테이너에 대한 파티션 키로 지원되지 않습니다.

    - ID로 꼭짓점을 선택한 후 **`.has()` 단계를 사용하여 파티션 키 속성 지정** 

        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - **파티션 키 값 및 ID를 포함하는 튜플을 지정**하여 꼭짓점 선택 

        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - **파티션 키 값 및 ID 튜플 배열** 지정

        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - 꼭짓점 집합을 선택하고 **파티션 키 값 목록 지정** 

        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>분할된 그래프를 사용할 때의 모범 사례

무제한 컨테이너를 사용하여 분할된 그래프를 사용하는 경우 성능 및 확장성을 보장하려면 다음 지침을 따르세요.

- **꼭짓점을 쿼리할 때 항상 파티션 키 값을 지정합니다**. 알려진 분할에서 꼭짓점을 가져오면 성능을 달성할 수 있습니다.

- **가능한 경우 모서리를 쿼리할 때마다 돌출된 방향을 사용합니다**. 위에서 언급한 것처럼 모서리는 돌출 방향의 원본 꼭짓점과 함께 저장됩니다. 따라서 이 패턴을 염두에 두고 데이터 및 쿼리를 설계하면 교차 파티션 쿼리를 다시 정렬할 가능성이 최소화됩니다.

- **데이터를 파티션에 균등하게 분산하는 파티션 키를 선택합니다**. 이러한 선택은 솔루션의 데이터 모델에 크게 좌우됩니다. [Azure Cosmos DB에서 분할 및 확장](partition-data.md)에서 해당 파티션 키를 만드는 방법에 대해 자세히 알아봅니다.

- **파티션 경계 내의 데이터를 가져오도록 쿼리를 최적화합니다**. 최적의 분할 전략은 쿼리 패턴에 맞추는 것입니다. 단일 파티션의 데이터를 가져오는 쿼리는 가능한 최상의 성능을 제공합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 분할 및 확장](partition-data.md)에 대해 알아봅니다.
* [Gremlin API의 Gremlin 지원](gremlin-support.md)에 대해 알아봅니다.
* [Gremlin API 소개](graph-introduction.md)에 대해 알아봅니다.

<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->