---
title: 실행 프로필을 사용 하 여 Azure Cosmos DB Gremlin API의 쿼리를 평가 합니다.
description: 실행 프로필 단계를 사용 하 여 Gremlin 쿼리 문제를 해결 하 고 개선 하는 방법에 대해 알아봅니다.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441845"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>실행 프로필 단계를 사용 하 여 Gremlin 쿼리를 평가 하는 방법

이 문서에서는 Azure Cosmos DB Gremlin API graph 데이터베이스에 대해 실행 프로필 단계를 사용 하는 방법에 대한 개요를 제공 합니다. 이 단계에서는 문제 해결 및 쿼리 최적화에 대한 관련 정보를 제공 하며, Cosmos DB Gremlin API 계정에 대해 실행할 수 있는 모든 Gremlin 쿼리와 호환 됩니다.

이 단계를 사용 하려면 Gremlin 쿼리 끝에 `executionProfile()` 함수 호출을 추가 하면 됩니다. **Gremlin 쿼리가 실행** 되 고 작업 결과가 쿼리 실행 프로필을 사용 하 여 JSON 응답 개체를 반환 합니다.

예:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

`executionProfile()` 단계를 호출한 후 응답은 실행 된 Gremlin 단계, 소요 된 총 시간 및 문이 일으킨 Cosmos DB 런타임 연산자의 배열을 포함 하는 JSON 개체가 됩니다.

> [!NOTE]
> 이 실행 프로필 구현은 Apache Tinkerpop 사양에 정의 되어 있지 않습니다. Azure Cosmos DB Gremlin API의 구현에만 적용 됩니다.


## <a name="response-example"></a>응답 예제

다음은 반환 되는 출력에 대한 주석이 추가 된 예제입니다.

> [!NOTE]
> 이 예제에는 응답의 일반 구조를 설명 하는 주석이 포함 되어 있습니다. 실제 executionProfile 응답에는 설명이 포함 되지 않습니다.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> ExecutionProfile 단계는 Gremlin 쿼리를 실행 합니다. 여기에는 `addV` 또는 `addE`단계가 포함 되며,이를 통해 생성 되 고 쿼리에 지정 된 변경 내용을 커밋합니다. 따라서 Gremlin 쿼리에 의해 생성 된 요청 단위에도 요금이 부과 됩니다.

## <a name="execution-profile-response-objects"></a>실행 프로필 응답 개체

ExecutionProfile () 함수의 응답은 다음 구조를 사용 하 여 JSON 개체의 계층 구조를 생성 합니다.
  - **Gremlin operation 개체**: 실행 된 전체 Gremlin 작업을 나타냅니다. 에는 다음 속성이 포함 되어 있습니다.
    - `gremlin`: 실행 된 명시적 Gremlin 문입니다.
    - `totalTime`: 단계 실행이에서 발생 하는 시간 (밀리초)입니다. 
    - `metrics`: 쿼리를 수행 하기 위해 실행 된 각 Cosmos DB 런타임 연산자를 포함 하는 배열입니다. 이 목록은 실행 순서 대로 정렬 됩니다.
    
  - **Cosmos DB 런타임 연산자**: 전체 Gremlin 작업의 각 구성 요소를 나타냅니다. 이 목록은 실행 순서 대로 정렬 됩니다. 각 개체에는 다음 속성이 포함 되어 있습니다.
    - `name`: 운영자의 이름입니다. 이는 평가 되 고 실행 된 단계의 유형입니다. 아래 표를 참조 하세요.
    - `time`: 지정 된 연산자가 걸린 시간 (밀리초)입니다.
    - `annotations`: 실행 된 연산자와 관련 된 추가 정보를 포함 합니다.
    - `annotations.percentTime`: 특정 연산자를 실행 하는 데 걸린 총 시간의 백분율입니다.
    - `counts`:이 연산자에 의해 저장소 계층에서 반환 된 개체 수입니다. 이는 내의 `counts.resultCount` 스칼라 값에 포함 되어 있습니다.
    - `storeOps`: 하나 또는 여러 파티션에 걸쳐 있을 수 있는 저장소 작업을 나타냅니다.
    - `storeOps.fanoutFactor`:이 특정 저장소 작업에 액세스 한 파티션의 수를 나타냅니다.
    - `storeOps.count`:이 저장소 작업이 반환 하는 결과 수를 나타냅니다.
    - `storeOps.size`: 지정 된 저장소 작업의 결과 크기 (바이트)를 나타냅니다.

Cosmos DB Gremlin Runtime 연산자|Description
---|---
`GetVertices`| 이 단계에서는 지 속성 계층에서 예측 된 개체 집합을 가져옵니다. 
`GetEdges`| 이 단계는 꼭 짓 점 집합에 인접 한 가장자리를 가져옵니다. 이 단계를 수행 하면 하나 이상의 저장소 작업이 발생할 수 있습니다.
`GetNeighborVertices`| 이 단계에서는 가장자리 집합에 연결 된 꼭 짓 점을 가져옵니다. 가장자리에는 원본 및 대상 꼭 짓 점의 파티션 키와 ID가 포함 됩니다.
`Coalesce`| 이 단계에서는 `coalesce()` Gremlin 단계가 실행 될 때마다 두 작업을 평가 하는 작업을 수행 합니다.
`CartesianProductOperator`| 이 단계에서는 두 데이터 집합 간의 데카르트 곱을 계산 합니다. 일반적으로 조건자 `to()` 또는 `from()` 사용 될 때마다 실행 됩니다.
`ConstantSourceOperator`| 이 단계에서는 상수 값을 생성 하는 식을 계산 합니다.
`ProjectOperator`| 이 단계에서는 이전 작업의 결과를 사용 하 여 응답을 준비 하 고 직렬화 합니다.
`ProjectAggregation`| 이 단계에서는 집계 작업에 대한 응답을 준비 하 고 직렬화 합니다.

> [!NOTE]
> 이 목록은 새 운영자가 추가 될 때 계속 업데이트 됩니다.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>실행 프로필 응답을 분석 하는 방법에 대한 예제

다음은 실행 프로필 응답을 사용 하 여 검색할 수 있는 일반적인 최적화의 예입니다.
  - 블라인드 팬 아웃 쿼리입니다.
  - 필터링 되지 않은 쿼리.

### <a name="blind-fan-out-query-patterns"></a>블라인드 팬 아웃 쿼리 패턴

**분할 된 그래프**에서 다음 실행 프로필 응답을 가정 합니다.

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

여기에서 다음과 같은 결론을 수행할 수 있습니다.
- Gremlin 문이 `g.V('id')`패턴을 따르며 쿼리는 단일 ID 조회입니다.
- `time` 메트릭에서 심사이 쿼리의 대기 시간은 [단일 지점 읽기 작업의 10ms 보다 더](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)많은 것 같습니다.
- `storeOps` 개체를 살펴보면 `fanoutFactor` `5`되는 것을 볼 수 있습니다. 즉,이 작업에서 [5 개의 파티션을](https://docs.microsoft.com/azure/cosmos-db/partition-data) 액세스 했음을 의미 합니다.

이 분석을 완료 하면 첫 번째 쿼리가 필요한 것 보다 더 많은 파티션에 액세스 하 고 있음을 확인할 수 있습니다. 쿼리에 조건자로 분할 키를 지정 하면이 문제를 해결할 수 있습니다. 이로 인해 대기 시간이 줄어들고 쿼리 당 비용이 줄어듭니다. [그래프 분할](graph-partitioning.md)에 대해 자세히 알아보세요. 더 최적의 쿼리를 `g.V('tt0093640').has('partitionKey', 't1001')`합니다.

### <a name="unfiltered-query-patterns"></a>필터링 되지 않은 쿼리 패턴

다음 두 실행 프로필 응답을 비교 합니다. 간단한 설명을 위해이 예제에서는 분할 된 단일 그래프를 사용 합니다.

이 첫 번째 쿼리는 `tweet` 레이블이 있는 모든 꼭 짓 점을 검색 한 다음 인접 한 꼭 짓 점을 가져옵니다.

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

동일한 쿼리의 프로필을 확인 하 고, 이제 추가 필터를 사용 하 여 인접 한 꼭 짓 점을 탐색 하기 전에 `has('lang', 'en')`합니다.

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

이러한 두 쿼리는 동일한 결과에 도달 했지만, 첫 번째 쿼리는 인접 한 항목을 쿼리 하기 전에 더 큰 초기 데이터 집합을 반복 해야 하므로 더 많은 요청 단위가 필요 합니다. 두 응답에서 다음 매개 변수를 비교할 때이 동작의 표시기를 볼 수 있습니다.
- 첫 번째 응답에서 `metrics[0].time` 값이 더 높습니다 .이 경우이 단일 단계를 확인 하는 데 시간이 오래 걸렸습니다.
- 초기 작업 데이터 집합이 더 크다는 것을 나타내는 첫 번째 응답의 `metrics[0].counts.resultsCount` 값도 높습니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB에서 [지원 되는 Gremlin 기능](gremlin-support.md) 에 대해 알아봅니다. 
* [Azure Cosmos DB의 GREMLIN API](graph-introduction.md)에 대해 자세히 알아보세요.
