---
title: Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결
description: Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 34f5de01df72b48d275448e028ab0f8cb71e51f8
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132057"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결

이 문서에서는 Azure Cosmos DB의 쿼리 문제를 해결 하기 위한 일반적인 권장 방법을 안내 합니다. 이 문서에 설명 된 단계는 잠재적 쿼리 문제에 대 한 "모두"로 간주 되지 않지만 여기에는 가장 일반적인 성능 팁이 포함 되어 있습니다. 이 문서는 Azure Cosmos DB의 코어 (SQL) API에서 속도가 느리거나 비용이 많이 드는 쿼리 문제를 해결 하기 위한 시작 위치로 사용 해야 합니다. [진단 로그](cosmosdb-monitor-resource-logs.md) 를 사용 하 여 속도가 느리거나 처리량이 많은 쿼리를 식별할 수도 있습니다.

쿼리 최적화를 광범위 하 게 분류 하 여 쿼리 최적화의 비용을 줄이고 대기 시간을 줄이는 최적화를 Azure Cosmos DB 수 있습니다. 쿼리의 작업량을 줄여 대기 시간을 거의 줄일 수 있습니다.

이 문서에서는 [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합을 사용 하 여 다시 만들 수 있는 예제를 사용 합니다.

### <a name="obtaining-query-metrics"></a>쿼리 메트릭 가져오기:

Azure Cosmos DB에서 쿼리를 최적화 하는 경우 첫 번째 단계는 항상 쿼리에 대 한 [쿼리 메트릭을 가져오는](profile-sql-api-query.md) 것입니다. 이러한 항목은 아래와 같이 Azure Portal를 통해서도 사용할 수 있습니다.

[쿼리 메트릭을 가져오는 ![](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

쿼리 메트릭을 구한 후 검색 된 문서 수를 쿼리의 출력 문서 수와 비교 합니다. 이 비교를 사용 하 여 아래에서 참조할 관련 섹션을 식별 합니다.

검색 된 문서 수는 쿼리를 로드 하는 데 필요한 문서 수입니다. 출력 문서 수는 쿼리 결과에 필요한 문서 수입니다. 검색 된 문서 수가 출력 문서 수보다 크게 높으면 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

아래 섹션을 참조 하 여 시나리오에 대 한 관련 쿼리 최적화를 이해할 수 있습니다.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 과도 한 요금이 너무 높음

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>검색 된 문서 수가 출력 문서 수보다 크게 큽니다.

- [인덱싱 정책에 필요한 경로를 포함 합니다.](#include-necessary-paths-in-the-indexing-policy)

- [인덱스를 활용 하는 시스템 함수 이해](#understand-which-system-functions-utilize-the-index)

- [필터 및 ORDER BY 절을 모두 사용 하 여 쿼리](#queries-with-both-a-filter-and-an-order-by-clause)

- [하위 쿼리를 사용 하 여 조인 식 최적화](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색 된 문서 수가 출력 문서 수와 거의 동일 합니다.

- [파티션 간 쿼리 방지](#avoid-cross-partition-queries)

- [여러 속성에 대 한 필터](#filters-on-multiple-properties)

- [필터 및 ORDER BY 절을 모두 사용 하 여 쿼리](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 과도 한 요금이 허용 되지만 대기 시간이 너무 높습니다.

- [근접성 향상](#improve-proximity)

- [프로 비전 된 처리량 증가](#increase-provisioned-throughput)

- [MaxConcurrency 늘리기](#increase-maxconcurrency)

- [MaxBufferedItemCount 증가](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색 된 문서 개수가 출력 문서 수를 초과 하는 쿼리

 검색 된 문서 수는 쿼리를 로드 하는 데 필요한 문서 수입니다. 출력 문서 수는 쿼리 결과에 필요한 문서 수입니다. 검색 된 문서 수가 출력 문서 수보다 크게 높으면 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

 다음은 인덱스에서 완전히 처리 되지 않은 검색 쿼리의 예입니다.

쿼리:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

쿼리 메트릭:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

검색 된 문서 수 (60951)가 출력 문서 수 (7) 보다 훨씬 크므로이 쿼리는 검색을 수행 하는 데 필요 합니다. 이 경우 시스템 함수 [UPPER ()](sql-query-upper.md) 는 인덱스를 사용 하지 않습니다.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>인덱싱 정책에 필요한 경로를 포함 합니다.

인덱싱 정책은 `WHERE` 절, `ORDER BY` 절, `JOIN`및 대부분의 시스템 함수에 포함 된 모든 속성을 포함 해야 합니다. 인덱스 정책에 지정 된 경로는 JSON 문서의 속성과 일치 해야 합니다 (대/소문자 구분).

[영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대 한 간단한 쿼리를 실행 하는 경우에는 `WHERE` 절의 속성이 인덱싱되는 경우 매우 낮은 요금이 발생 합니다.

### <a name="original"></a>Original

쿼리:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**작업 요금:** 409.51 r u s

### <a name="optimized"></a>최적화됨

업데이트 된 인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**작업 요금:** 2.98 r u s

쓰기 가용성 또는 성능에 영향을 주지 않고 언제 든 지 인덱싱 정책에 추가 속성을 추가할 수 있습니다. 인덱스에 새 속성을 추가 하는 경우이 속성을 사용 하는 쿼리는 사용 가능한 새 인덱스를 즉시 활용 합니다. 쿼리를 작성 하는 동안 새 인덱스를 활용 합니다. 따라서 인덱스 다시 작성이 진행 중일 때 쿼리 결과가 일치 하지 않을 수 있습니다. 새 속성이 인덱싱되는 경우에는 인덱스를 다시 작성 하는 동안 기존 인덱스만 활용 하는 쿼리는 영향을 받지 않습니다. [인덱스 변환 진행률을 추적할](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)수 있습니다.

## <a name="understand-which-system-functions-utilize-the-index"></a>인덱스를 활용 하는 시스템 함수 이해

식을 문자열 값 범위로 변환할 수 있는 경우 인덱스를 사용할 수 있으며 그렇지 않은 경우에는 사용할 수 없습니다.

인덱스를 활용할 수 있는 문자열 함수의 목록은 다음과 같습니다.

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

인덱스를 사용 하지 않고 각 문서를 로드 해야 하는 일반적인 시스템 함수는 다음과 같습니다.

| **시스템 함수**                     | **최적화에 대 한 아이디어**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 전체 텍스트 검색에 Azure Search 사용                        |
| 위쪽/아래쪽                             | 비교할 때마다 시스템 함수를 사용 하 여 데이터를 정규화 하는 대신 삽입 시 대/소문자를 정규화 합니다. 그러면 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```와 같은 쿼리가 ```SELECT * FROM c WHERE c.name = 'BOB'``` 됩니다. |
| 수치 연산 함수 (비 집계) | 쿼리에서 값을 자주 계산 해야 하는 경우이 값을 JSON 문서에 속성으로 저장 하는 것이 좋습니다. |

------

인덱스를 사용 하지 않는 시스템 함수에도 불구 하 고 쿼리의 다른 부분은 인덱스를 계속 사용할 수 있습니다.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>필터 및 ORDER BY 절을 모두 사용 하 여 쿼리

필터와 `ORDER BY` 절을 사용 하는 쿼리는 일반적으로 범위 인덱스를 사용 하지만 복합 인덱스에서 제공 될 수 있는 경우에는 더 효율적입니다. 인덱싱 정책을 수정 하는 것 외에도 복합 인덱스의 모든 속성을 `ORDER BY` 절에 추가 해야 합니다. 이 쿼리를 수정 하면 복합 인덱스를 활용 하 게 됩니다.  [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대해 쿼리를 실행 하 여 영향을 관찰할 수 있습니다.

### <a name="original"></a>Original

쿼리:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

인덱싱 정책:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**작업 요금:** 44.28 r u s

### <a name="optimized"></a>최적화됨

업데이트 된 쿼리 (`ORDER BY` 절의 두 속성 포함):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

업데이트 된 인덱싱 정책:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**작업 요금:** 8.86 r u s

## <a name="optimize-join-expressions-by-using-a-subquery"></a>하위 쿼리를 사용 하 여 조인 식 최적화
다중 값 하위 쿼리는 `WHERE` 절의 모든 크로스 조인이 아니라 각 select-many 식 뒤에 조건자를 푸시하여 `JOIN` 식을 최적화할 수 있습니다.

다음과 같은 쿼리를 고려해 보세요.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**작업 요금:** 167.62 r u s

이 쿼리의 경우 인덱스는 "infant formula" 라는 이름의 태그가 있는 모든 문서와 일치 하 고, 0 보다 크고 nutritionValue 1 보다 큰 값을 제공 합니다. 여기에서 `JOIN` 식은 필터를 적용 하기 전에 일치 하는 각 문서에 대 한 태그, nutrients 및 배열 항목의 모든 항목에 대 한 교차곱을 수행 합니다. 그런 다음 `WHERE` 절은 각 `<c, t, n, s>` 튜플에 필터 조건자를 적용 합니다.

예를 들어, 일치 하는 문서에 각각 3 개의 배열에 10 개의 항목이 있는 경우 1 x 10 x 10 x 10 (즉, 1000) 튜플로 확장 됩니다. 여기서 하위 쿼리를 사용 하면 다음 식으로 조인 하기 전에 조인 된 배열 항목을 필터링 하는 데 도움이 될 수 있습니다.

이 쿼리는 위의 쿼리와 동일 하지만 하위 쿼리를 사용 합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**작업 요금:** 22.17 r u s

태그 배열의 한 항목만 필터와 일치 하 고 nutrients 및 servings 배열 모두에 대해 5 개의 항목이 있다고 가정 합니다. 그러면 `JOIN` 식은 첫 번째 쿼리의 1000 항목과 달리 1 x 1 x 5 x 5 = 25 개 항목으로 확장 됩니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색 된 문서 수가 출력 문서 수와 같은 쿼리

검색 된 문서 수가 출력 문서 수와 거의 같으면 쿼리가 불필요 한 많은 문서를 검색할 필요가 없음을 의미 합니다. TOP 키워드를 사용 하는 것과 같은 많은 쿼리의 경우 검색 된 문서 수는 출력 문서 수를 1로 초과할 수 있습니다. 이는 문제가 발생 하지 않습니다.

## <a name="avoid-cross-partition-queries"></a>파티션 간 쿼리 방지

Azure Cosmos DB에서는 [분할](partitioning-overview.md) 을 사용 하 여 개별 컨테이너를 요청 단위로 확장 하 고 데이터 저장소를 증가 시켜야 합니다. 각 실제 파티션에는 별도의 독립 된 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치 하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인 하면 됩니다. 이러한 최적화를 통해 쿼리에 필요한 전체 수를 줄일 수 있습니다.

프로 비전 된 r u의 많은 수 (3만 이상) 또는 저장 된 데이터 양이 많은 경우 (100 GB 초과) 쿼리를 크게 줄일 수 있는 충분 한 컨테이너가 있을 수 있습니다.

예를 들어 파티션 키가 foodGroup 인 컨테이너를 만들 경우 다음 쿼리는 단일 실제 파티션만 확인 하면 됩니다.

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

이러한 쿼리는 또한 쿼리에 파티션 키를 포함 하 여 최적화 됩니다.

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

파티션 키에 대해 범위 필터를 포함 하거나 파티션 키에 필터가 없는 쿼리는 "팬 아웃" 하 고 결과에 대 한 모든 물리적 파티션의 인덱스를 확인 해야 합니다.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>여러 속성에 대 한 필터

여러 속성에 대 한 필터를 포함 하는 쿼리는 일반적으로 범위 인덱스를 사용 하지만 복합 인덱스에서 제공 될 수 있는 경우에는 더 효율적입니다. 적은 양의 데이터에 대해 이러한 최적화는 상당한 영향을 주지 않습니다. 그러나 많은 양의 데이터에 대해 유용 하 게 사용할 수 있습니다. 복합 인덱스 당 최대 하나의 일치 하지 않는 필터만 최적화할 수 있습니다. 쿼리에 일치 하지 않는 필터가 여러 개 있는 경우 복합 인덱스를 사용할 하나를 선택 해야 합니다. 나머지는 범위 인덱스를 계속 활용 합니다. 같지 않음 필터는 복합 인덱스에서 마지막으로 정의 되어야 합니다. [복합 인덱스에 대해 자세히 알아보기](index-policy.md#composite-indexes)

다음은 복합 인덱스를 사용 하 여 최적화할 수 있는 쿼리의 몇 가지 예입니다.

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

다음은 관련 복합 인덱스입니다.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>쿼리 대기 시간을 줄이는 최적화:

대부분의 경우에는 많은 요금이 부과 될 수 있지만 쿼리 대기 시간은 여전히 너무 높습니다. 아래 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁의 개요를 제공 합니다. 동일한 쿼리를 동일한 데이터 집합에서 여러 번 실행 하는 경우에는 매번 동일한 실행 요금이 발생 합니다. 그러나 쿼리 대기 시간은 쿼리 실행 마다 다를 수 있습니다.

## <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정이 아닌 다른 지역에서 실행 되는 쿼리는 동일한 지역 내에서 실행 된 경우 보다 대기 시간이 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행 하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내에서 가상 컴퓨터의 가상 컴퓨터에서 발생 한 경우 보다 수십 또는 수백 (또는 그 이상) 밀리초가 될 것으로 간주 해야 합니다. 데이터를 앱에 더 가깝게 가져올 수 있도록 [Azure Cosmos DB 데이터를 전역적으로 배포](distribute-data-globally.md) 하는 것이 간단 합니다.

## <a name="increase-provisioned-throughput"></a>프로 비전 된 처리량 증가

Azure Cosmos DB에서 프로 비전 된 처리량은 (요청 단위)로 측정 됩니다. 5 개의 처리량을 사용 하는 쿼리가 있다고 가정 합니다. 예를 들어 1000 r u s를 프로 비전 하는 경우 초당 해당 200 쿼리를 실행할 수 있습니다. 사용 가능한 처리량이 충분 하지 않은 경우 쿼리를 실행 하려고 하면 Azure Cosmos DB HTTP 429 오류가 반환 됩니다. 현재 코어 (SQL) API sdk는 잠깐 기다린 후이 쿼리를 자동으로 다시 시도 합니다. 제한 된 요청은 시간이 오래 걸리므로 프로 비전 된 처리량이 증가 하면 쿼리 대기 시간이 향상 될 수 있습니다. Azure Portal의 메트릭 블레이드에서 [제한 된 요청의 총 수](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) 를 확인할 수 있습니다.

## <a name="increase-maxconcurrency"></a>MaxConcurrency 늘리기

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동 합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 순차적으로 가져오기 됩니다. 따라서 파티션 수에 대 한 MaxConcurrency를 조정 하는 것은 다른 모든 시스템 조건을 동일 하 게 유지 하는 경우 가장 성능이 뛰어난 쿼리를 달성할 수 있는 최대 기회를 제공 합니다. 파티션 수를 모르는 경우 MaxConcurrency (또는 이전 sdk 버전의 MaxDegreesOfParallelism)를 높은 숫자로 설정 하면 시스템에서 최소 (파티션 수, 사용자가 제공한 입력)를 최대 병렬 처리 수준으로 선택할 수 있습니다.

## <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount 증가

쿼리는 결과의 현재 배치가 클라이언트에서 처리 되는 동안 결과를 미리 인출 하도록 디자인 되었습니다. 프리페치는 쿼리의 전체 대기 시간 개선 사항에 도움이 됩니다. MaxBufferedItemCount를 설정 하면 프리페치 된 결과의 수가 제한 됩니다. 이 값을 반환 된 예상 결과 수 (또는 더 높은 수)로 설정 하면 쿼리는 미리 페치를 통해 최대한의 이점을 얻을 수 있습니다. 이 값을-1로 설정 하면 시스템에서 버퍼링 할 항목 수를 자동으로 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
쿼리 별 RUs를 측정 하는 방법, 쿼리를 튜닝 하기 위한 실행 통계 가져오기 등의 문서를 참조 하세요.

* [.NET SDK를 사용하여 SQL 쿼리 실행 메트릭 가져오기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](sql-api-sql-query-metrics.md)
* [.NET SDK용 성능 팁](performance-tips.md)
