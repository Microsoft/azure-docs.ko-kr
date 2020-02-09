---
title: Azure Cosmos DB에 대한 SQL 하위 쿼리
description: 에서 SQL 하위 쿼리 및 일반적인 사용 사례와 다양 한 하위 쿼리 형식에 대해 알아보세요 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870567"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 SQL 하위 쿼리 예제

하위 쿼리는 다른 쿼리 내에 중첩 된 쿼리입니다. 하위 쿼리를 내부 쿼리 또는 내부 선택이 라고도 합니다. 하위 쿼리를 포함 하는 문을 일반적으로 외부 쿼리 라고 합니다.

이 문서에서는 Azure Cosmos DB의 SQL 하위 쿼리 및 일반적인 사용 사례에 대해 설명 합니다. 이 문서의 모든 샘플 쿼리는 [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo)에 미리 로드 된 영양 데이터 집합에 대해 실행할 수 있습니다.

## <a name="types-of-subqueries"></a>하위 쿼리 유형

하위 쿼리는 다음과 같은 두 가지 기본 유형이 있습니다.

* **상관 관계가**지정 됨: 외부 쿼리에서 값을 참조 하는 하위 쿼리입니다. 하위 쿼리는 외부 쿼리에서 처리 하는 각 행에 대해 한 번씩 평가 됩니다.
* **상관 관계가 지정 되지 않음**: 외부 쿼리와 독립적인 하위 쿼리입니다. 외부 쿼리에 의존 하지 않고 자체적으로 실행할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 상호 관련 된 하위 쿼리만 지원 합니다.

하위 쿼리는 반환 되는 행 및 열 수를 기준으로 추가로 분류할 수 있습니다. 다음 세 가지 유형이 있습니다.
* **테이블**: 여러 행 및 여러 열을 반환 합니다.
* **다중 값**: 여러 행과 단일 열을 반환 합니다.
* **스칼라**: 단일 행과 단일 열을 반환 합니다.

Azure Cosmos DB의 SQL 쿼리는 항상 단일 열 (단순 값 또는 복잡 한 문서)을 반환 합니다. 따라서 Azure Cosmos DB에서 다중값 및 스칼라 하위 쿼리를 적용할 수 있습니다. 다중값 하위 쿼리를 FROM 절에 관계형 식 으로만 사용할 수 있습니다. 스칼라 하위 쿼리를 SELECT 또는 WHERE 절에서 스칼라 식으로 사용 하거나 FROM 절의 관계형 식으로 사용할 수 있습니다.

## <a name="multi-value-subqueries"></a>다중 값 하위 쿼리

다중 값 하위 쿼리는 문서 집합을 반환 하며 항상 FROM 절 내에서 사용 됩니다. 다음에 사용 됩니다.

* 조인 식 최적화. 
* 비용이 많이 드는 식 계산 및 여러 번 참조

## <a name="optimize-join-expressions"></a>조인 식 최적화

다중값 하위 쿼리는 WHERE 절의 모든 교차 조인이 아니라 각 select-many 식 다음에 조건자를 푸시하여 조인 식을 최적화할 수 있습니다.

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

이 쿼리의 경우 인덱스는 이름이 "infant formula" 인 태그가 있는 모든 문서와 일치 합니다. 0에서 10 사이의 값을 가진 nutrient 항목 이며, 1 보다 큰 양의 서비스 항목입니다. 여기서 조인 식은 필터를 적용 하기 전에 일치 하는 각 문서에 대한 태그, nutrients 및 배열 항목의 모든 항목에 대한 교차곱을 수행 합니다. 

그런 다음 WHERE 절은 각 < c, t, n, s > 튜플에 필터 조건자를 적용 합니다. 예를 들어, 일치 하는 문서에 각각 3 개의 배열에 10 개의 항목이 있는 경우 1 x 10 x 10 x 10 (즉, 1000) 튜플로 확장 됩니다. 여기서 하위 쿼리를 사용 하면 다음 식으로 조인 하기 전에 조인 된 배열 항목을 필터링 하는 데 도움이 될 수 있습니다.

이 쿼리는 위의 쿼리와 동일 하지만 하위 쿼리를 사용 합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

태그 배열의 한 항목만 필터와 일치 하 고 nutrients 및 servings 배열 모두에 대해 5 개의 항목이 있다고 가정 합니다. 그러면 조인 식은 첫 번째 쿼리의 1000 항목과 달리 1 x 1 x 5 x 5 = 25 개 항목으로 확장 됩니다.

## <a name="evaluate-once-and-reference-many-times"></a>한 번 평가 및 여러 번 참조

하위 쿼리는 Udf (사용자 정의 함수), 복잡 한 문자열 또는 산술 식과 같은 비용이 많이 드는 식으로 쿼리를 최적화 하는 데 도움이 됩니다. JOIN 식과 함께 하위 쿼리를 사용 하 여 식을 한 번만 계산 하 고이를 여러 번 참조할 수 있습니다.

다음 쿼리는 UDF `GetMaxNutritionValue`를 두 번 실행 합니다.

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

다음은 UDF를 한 번만 실행 하는 동일한 쿼리입니다.

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 조인 식의 외적 동작을 염두에 두어야 합니다. UDF 식이 undefined로 계산 될 수 있는 경우 조인 식이 값이 아닌 하위 쿼리에서 항상 개체를 반환 하 여 항상 단일 행을 생성 하도록 합니다.
>

다음은 값이 아닌 개체를 반환 하는 비슷한 예제입니다.

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

이 접근 방식은 Udf로 제한 되지 않습니다. 잠재적으로 비용이 많이 드는 식에 적용 됩니다. 예를 들어 `avg`수학 함수를 사용 하 여 동일한 방법을 사용할 수 있습니다.

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>외부 참조 데이터로 조인 모방

측정 단위 또는 국가 코드와 같이 거의 변경 되지 않는 정적 데이터를 참조 해야 하는 경우가 종종 있습니다. 각 문서에 대해 이러한 데이터를 중복 하지 않는 것이 좋습니다. 이 중복을 방지 하면 저장소에 저장 되 고 문서 크기를 작게 유지 하 여 쓰기 성능이 향상 됩니다. 하위 쿼리를 사용 하 여 내부 조인 의미 체계를 참조 데이터의 컬렉션과 모방할 수 있습니다.

예를 들어 다음과 같은 참조 데이터 집합을 고려해 보십시오.

| **단위** | **Name**            | **곱한** | **기본 단위** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | G          |
| µg       | 마이크로 문법           | 1.00E-06       | G          |
| mg       | Milligram           | 1.00E-03       | G          |
| g        | G                | 1.00 e + 00       | G          |
| kg       | Kilogram            | 1.00E+03       | G          |
| Mg       | Megagram            | 1.00 e + 06       | G          |
| Gg       | Gigagram            | 1.00 e + 09       | G          |
| nJ       | Nanojoule           | 1.00E-09       | 줄         |
| µJ       | 마이크로 joule          | 1.00E-06       | 줄         |
| mJ       | Millijoule          | 1.00E-03       | 줄         |
| J        | 줄               | 1.00 e + 00       | 줄         |
| kJ       | Kilojoule           | 1.00E+03       | 줄         |
| MJ       | Megajoule           | 1.00 e + 06       | 줄         |
| GJ       | Gigajoule           | 1.00 e + 09       | 줄         |
| 마다      | Calorie             | 1.00 e + 00       | calorie       |
| kcal     | Calorie             | 1.00E+03       | calorie       |
| IU       | 국제 단위 |                |               |


다음 쿼리는 단위의 이름을 출력에 추가 하도록이 데이터와의 조인을 모방 합니다.

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>스칼라 하위 쿼리

스칼라 하위 쿼리 식은 단일 값으로 계산 되는 하위 쿼리입니다. 스칼라 하위 쿼리 식의 값은 하위 쿼리의 프로젝션 (SELECT 절) 값입니다.  스칼라 식이 유효한 여러 위치에서 스칼라 하위 쿼리 식을 사용할 수 있습니다. 예를 들어 SELECT 절과 WHERE 절 모두에서 식에 스칼라 하위 쿼리를 사용할 수 있습니다.

그러나 스칼라 하위 쿼리를 사용 하는 것은 항상 최적화 하는 데 도움이 되지 않습니다. 예를 들어 스칼라 하위 쿼리를 시스템 또는 사용자 정의 함수에 인수로 전달 하면 리소스 단위 () 사용 또는 대기 시간에는 아무런 이점도 없습니다.

스칼라 하위 쿼리는 다음과 같이 추가로 분류할 수 있습니다.
* 단순 식 스칼라 하위 쿼리
* 스칼라 하위 쿼리 집계

## <a name="simple-expression-scalar-subqueries"></a>단순 식 스칼라 하위 쿼리

단순 식 스칼라 하위 쿼리는 집계 식이 포함 되지 않은 SELECT 절이 있는 상호 관련 된 하위 쿼리입니다. 이러한 하위 쿼리는 컴파일러에서 하나 큰 단순 식으로 변환 하기 때문에 최적화를 제공 하지 않습니다. 내부 쿼리와 외부 쿼리 사이에 상관 관계가 지정 된 컨텍스트가 없습니다.

다음은 몇 가지 예입니다.

**예 1**

```sql
SELECT 1 AS a, 2 AS b
```

간단한 식 스칼라 하위 쿼리를 사용 하 여 다음을 수행 하 여이 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

두 쿼리는 모두 다음 출력을 생성 합니다.

```json
[
  { "a": 1, "b": 2 }
]
```

**예 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

간단한 식 스칼라 하위 쿼리를 사용 하 여 다음을 수행 하 여이 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

쿼리 출력:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**예 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

간단한 식 스칼라 하위 쿼리를 사용 하 여 다음을 수행 하 여이 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>스칼라 하위 쿼리 집계

집계 스칼라 하위 쿼리는 단일 값으로 계산 되는 프로젝션 또는 필터에 집계 함수를 포함 하는 하위 쿼리입니다.

**예제 1:**

다음은 프로젝션에서 단일 집계 함수 식이 포함 된 하위 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**예 2**

여러 집계 함수 식이 포함 된 하위 쿼리는 다음과 같습니다.

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**예 3**

다음은 프로젝션 및 필터 모두에 집계 하위 쿼리가 포함 된 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

쿼리 출력:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

이 쿼리를 작성 하는 가장 좋은 방법은 하위 쿼리를 조인 하 고 SELECT 및 WHERE 절에서 하위 쿼리 별칭을 참조 하는 것입니다. 이 쿼리는 조인 문 내 에서만 하위 쿼리를 실행 해야 하 고 프로젝션 및 필터 둘 다에서 실행 해야 하기 때문에 더 효율적입니다.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 식

Azure Cosmos DB는 EXISTS 식을 지원 합니다. Azure Cosmos DB SQL API에 기본 제공 되는 집계 스칼라 하위 쿼리입니다. Exists는 하위 쿼리 식을 사용 하 고 하위 쿼리가 행을 반환 하는 경우 true를 반환 하는 부울 식입니다. 그렇지 않으면 false를 반환합니다.

Azure Cosmos DB SQL API는 부울 식과 다른 스칼라 식을 구분 하지 않으므로 SELECT 절과 WHERE 절 모두에 EXISTS를 사용할 수 있습니다. 이는 T-sql과 달리 부울 식 (예: EXISTS, BETWEEN 및 IN)은 필터로 제한 됩니다.

EXISTS 하위 쿼리가 정의 되지 않은 단일 값을 반환 하는 경우 EXISTS는 false로 평가 됩니다. 예를 들어 다음 쿼리는 false로 평가 됩니다.
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


이전 하위 쿼리의 VALUE 키워드를 생략 하면 쿼리는 true로 평가 됩니다.
```sql
SELECT EXISTS (SELECT undefined) 
```

하위 쿼리는 개체의 선택 된 목록에 있는 값 목록을 묶습니다. 선택한 목록에 값이 없으면 하위 쿼리는 단일 값 '{}'을 반환 합니다. 이 값은 정의 되어 있으므로 EXISTS가 true로 평가 됩니다.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>예: ARRAY_CONTAINS 재작성 및 EXISTS로 조인

ARRAY_CONTAINS의 일반적인 사용 사례는 배열에 항목이 있는지 여부에 따라 문서를 필터링 하는 것입니다. 이 경우 태그 배열에 "주황색" 이라는 항목이 포함 되어 있는지 확인 하 게 됩니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS를 사용 하 여 동일한 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

또한 ARRAY_CONTAINS은 값이 배열 내의 요소와 같은지만 확인할 수 있습니다. 배열 속성에 더 복잡 한 필터가 필요한 경우 JOIN을 사용 합니다.

배열의 단위 및 `nutritionValue` 속성을 기준으로 필터링 하는 다음 쿼리를 고려 하십시오. 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

컬렉션의 각 문서에 대해 해당 배열 요소를 사용 하 여 교차곱을 수행 합니다. 이 조인 작업을 사용 하면 배열 내에서 속성을 필터링 할 수 있습니다. 그러나이 쿼리의 과도 한 소비는 중요 합니다. 예를 들어 1000 문서에 각 배열의 100 항목이 있는 경우 1000 x 100 (즉, 10만) 튜플로 확장 됩니다.

EXISTS를 사용 하면 비용이 많이 드는 교차곱을 피할 수 있습니다.

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

이 경우 EXISTS 하위 쿼리 내에서 배열 요소를 필터링 합니다. 배열 요소가 필터와 일치 하면이를 프로젝션 하 고 EXISTS를 true로 평가 합니다.

또한 별칭이 존재 하 고 프로젝션에서 참조할 수 있습니다.

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

쿼리 출력:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>배열 식

배열 식을 사용 하 여 쿼리 결과를 배열로 프로젝션 할 수 있습니다. 이 식은 쿼리의 SELECT 절 내 에서만 사용할 수 있습니다.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

다른 하위 쿼리와 마찬가지로 배열 식이 있는 필터를 사용할 수 있습니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

쿼리 출력:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

배열 식은 하위 쿼리의 FROM 절 뒤에 나올 수도 있습니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
