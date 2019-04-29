---
title: Azure Cosmos DB Gremlin API에 대 한 실행 프로필 함수를 사용 하 여 쿼리를 평가 합니다.
description: 문제를 해결 하는 실행 프로필 단계를 사용 하 여 Gremlin 쿼리를 개선 하는 방법을 알아봅니다.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888413"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>실행 프로필 단계를 사용 하 여 Gremlin 쿼리를 평가 하는 방법

이 문서에서는 Azure Cosmos DB Gremlin API 그래프 데이터베이스에 대 한 실행 프로필 단계를 사용 하는 방법의 개요를 제공 합니다. Cosmos DB Gremlin API 계정에 대해 실행할 수 있는 Gremlin 쿼리를 사용 하 여 호환 되 고 쿼리 최적화 및이 단계는 문제 해결을 위한 관련 정보를 제공 합니다.

이 단계를 사용 하려면 단순히 추가 `executionProfile()` 함수 Gremlin 쿼리 끝에 호출 합니다. **Gremlin 쿼리를 실행할 수는** 연산의 결과 쿼리 실행 프로필을 사용 하 여 JSON 응답 개체를 반환 합니다.

예를 들면 다음과 같습니다.

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

호출 후의 `executionProfile()` 단계 응답이 표시 됩니다 실행된 Gremlin 단계를 포함 하는 JSON 개체에 걸린 총 시간 및 문이 발생 하는 Cosmos DB 런타임 연산자의 배열입니다.

> [!NOTE]
> 이 구현은 실행 프로필에 대 한 Apache Tinkerpop 사양에 정의 되지 않았습니다. Azure Cosmos DB Gremlin API 구현에만 적용 됩니다.


## <a name="response-example"></a>응답 예제

다음은 반환 되는 출력의 주석이 추가 된 예제입니다.

> [!NOTE]
> 이 예제는 응답의 일반 구조를 설명 하는 주석을 사용 하 여 주석 처리 됩니다. 실제 executionProfile 응답에 주석이 포함 되지 않습니다.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> ExecutionProfile 단계 Gremlin 쿼리를 실행 합니다. 여기에 `addV` 또는 `addE`단계를 생성 하며 쿼리에 지정 된 변경 내용이 커밋됩니다. 결과적으로, Gremlin 쿼리에 의해 생성 된 요청 단위도 부과 됩니다.

## <a name="execution-profile-response-objects"></a>실행 프로필 응답 개체

ExecutionProfile() 함수의 응답 다음과 같은 구조를 사용 하 여 JSON 개체의 계층 구조를 생성 합니다.
  - **Gremlin 작업 개체**: 실행 된 전체 Gremlin 작업을 나타냅니다. 다음 속성을 포함합니다.
    - `gremlin`: 실행 된 명시적 Gremlin 문입니다.
    - `totalTime`: 시간을 밀리초 단위로 단계의 실행에서 발생 하는입니다. 
    - `metrics`: 각 쿼리를 충족 하기 위해 실행 된는 Cosmos DB 런타임 연산자를 포함 하는 배열입니다. 이 목록은 실행 순서 대로 정렬 됩니다.
    
  - **Cosmos DB 런타임 연산자**: 각 전체 Gremlin 작업의 구성 요소를 나타냅니다. 이 목록은 실행 순서 대로 정렬 됩니다. 각 개체에는 다음 속성이 포함 됩니다.
    - `name`: 운영자의 이름입니다. 평가 하 고 실행 된 단계 형식입니다. 아래 표에 자세히 알아보세요입니다.
    - `time`: 지정된 된 연산자는 밀리초 단위의 시간 간격입니다.
    - `annotations`: 실행 된 연산자에 대 한 추가 정보를 포함 합니다.
    - `annotations.percentTime`: 특정 연산자를 실행 하는 데 걸린 총 시간에 대 한 백분율입니다.
    - `counts`: 이 연산자가 저장소 계층에서 반환 된 개체 수입니다. 에 포함 된이 `counts.resultCount` 내에서 스칼라 값입니다.
    - `storeOps`: 하나 또는 여러 파티션에 걸쳐 있을 수 있는 저장소 작업을 나타냅니다.
    - `storeOps.fanoutFactor`: 이 특정 저장소 작업에 액세스 하는 파티션 수를 나타냅니다.
    - `storeOps.count`: 이 저장소 작업이 반환 하는 결과의 수를 나타냅니다.
    - `storeOps.size`: 지정 된 저장소 작업의 결과 바이트 단위로 크기를 나타냅니다.

Cosmos DB Gremlin 런타임 연산자|설명
---|---
`GetVertices`| 이 단계는 지 속성 계층에서 맵에 개체 집합을 가져옵니다. 
`GetEdges`| 이 단계는 꼭 짓 점 집합에 인접 한 가장자리를 가져옵니다. 이 단계는 하나 이상의 저장소 작업에서 발생할 수 있습니다.
`GetNeighborVertices`| 이 단계는 가장자리의 집합에 연결 된 꼭 짓 점을 가져옵니다. 가장자리에 파티션이 포함 키 및 ID가 원본과 대상 모두 꼭 짓 점입니다.
`Coalesce`| 이 단계는 두 작업의 평가 대 한 계정 때마다는 `coalesce()` Gremlin 단계가 실행 됩니다.
`CartesianProductOperator`| 이 단계는 두 데이터 집합 간에 카티션 곱을 계산합니다. 일반적으로 때마다 실행 조건자 `to()` 또는 `from()` 사용 됩니다.
`ConstantSourceOperator`| 이 단계는 상수 값이 결과적으로 생성 하는 식을 계산 합니다.
`ProjectOperator`| 이 단계는 준비 하 고 이전 작업의 결과 사용 하는 응답을 직렬화 합니다.
`ProjectAggregation`| 이 단계는 준비 및 집계 작업에 대 한 응답을 직렬화 합니다.

> [!NOTE]
> 이 목록은 새 연산자가 추가 됨에 따라 업데이트할 계속 됩니다.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>실행 프로필의 응답을 분석 하는 방법에 대 한 예제

다음은 실행 프로필 응답을 사용 하 여 확인할 수 있는 일반적인 최적화의 예입니다.
  - 블라인드 팬 아웃 쿼리 합니다.
  - 필터링 되지 않은 쿼리.

### <a name="blind-fan-out-query-patterns"></a>블라인드 팬아웃 쿼리 패턴

가정에서 다음 실행 프로필 응답을 **분할 된 그래프**:

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

다음과 같은 결론을 내릴 수 있습니다.
- Gremlin 문은 패턴을 따릅니다. 이후 쿼리는 단일 ID 조회를 `g.V('id')`입니다.
- 평가 `time` 메트릭에 이므로 높은 것이 쿼리의 대기 시간이 같습니다 [단일 지점 읽기 작업에 대 한 이상 10ms](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)합니다.
- 살펴보기 위해를 `storeOps` 개체를 확인할 수 있는 `fanoutFactor` 은 `5`, 즉 [5 파티션](https://docs.microsoft.com/azure/cosmos-db/partition-data) 이 작업에 액세스 했습니다.

이 분석의 결론에으로 첫 번째 쿼리에서 필요한 것 보다 더 많은 파티션이 액세스 하는 확인할 수 있습니다. 이 조건자로 쿼리에서 분할 키를 지정 하 여 해결할 수 있습니다. 그렇게 하면 쿼리 당 비용을 줄이고에 대기 시간이 짧습니다. 에 대해 자세히 알아보세요 [그래프 분할](graph-partitioning.md)합니다. 좀 더 최적의 쿼리 것 `g.V('tt0093640').has('partitionKey', 't1001')`입니다.

### <a name="unfiltered-query-patterns"></a>필터링 되지 않은 쿼리 패턴

다음 두 개의 실행 프로필 응답을 비교 합니다. 편의상, 이러한 예제는 단일 분할 된 그래프를 사용합니다.

이 첫 번째 쿼리는 레이블이 있는 모든 꼭 짓 점 검색 `tweet` 다음 해당 인접 한 꼭 짓 점을 가져옵니다.

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

동일한 쿼리 하지만 추가 필터를 사용 하 여 프로필 확인 `has('lang', 'en')`, 인접 한 꼭 짓 점을 탐색 하기 전에:

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

그러나이 두 쿼리 같은 결과 도달 하면, 통해 첫 번째 인접 한 항목을 쿼리 하기 전에 큰 초기 데이터 집합을 반복 해야 하므로 더 많은 요청 단위가 해야 합니다. 두 응답에서 다음 매개 변수를 비교 하는 경우이 동작에 대 한 표시기를 볼 수 있습니다.
- `metrics[0].time` 값이 단일 단계를 해결 하는 데 더 수행 되는 여부를 나타내는 첫 번째 응답에서 더 높습니다.
- `metrics[0].counts.resultsCount` 값 초기 작업 데이터 집합이 큰 되었는지 여부를 나타내는 첫 번째 응답에도 더 높습니다.

## <a name="next-steps"></a>다음 단계
* 에 대 한 자세한 합니다 [지원 되는 Gremlin 기능](gremlin-support.md) Azure Cosmos DB에서. 
* 에 대 한 자세한 정보는 [Azure Cosmos DB에서 Gremlin API](graph-introduction.md)합니다.
