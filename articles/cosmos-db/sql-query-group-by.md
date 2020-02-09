---
title: Azure Cosmos DB의 GROUP BY 절
description: Azure Cosmos DB에 대한 GROUP BY 절에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819100"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 GROUP BY 절

GROUP BY 절은 하나 이상의 지정 된 속성 값에 따라 쿼리의 결과를 나눕니다.

> [!NOTE]
> Azure Cosmos DB 현재 .NET SDK 3.3 이상 및 JavaScript SDK 3.4 이상에서 GROUP BY를 지원 합니다.
> 다른 언어 SDK의 지원은 현재 사용할 수 없지만 계획 되어 있습니다.

## <a name="syntax"></a>구문

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>인수

- `<scalar_expression_list>`

   쿼리 결과를 나누는 데 사용 되는 식을 지정 합니다.

- `<scalar_expression>`
  
   스칼라 하위 쿼리 및 스칼라 집계를 제외한 모든 스칼라 식이 허용 됩니다. 각 스칼라 식에는 적어도 하나 이상의 속성 참조가 포함 되어야 합니다. 개별 식의 수 나 각 식의 카디널리티에는 제한이 없습니다.

## <a name="remarks"></a>설명
  
  쿼리에서 GROUP BY 절을 사용 하는 경우 SELECT 절은 GROUP BY 절에 포함 된 속성 및 시스템 함수의 하위 집합만 포함할 수 있습니다. 한 가지 예외는 GROUP BY 절에 포함 되지 않고 SELECT 절에 나타날 수 있는 [집계 시스템 함수](sql-query-aggregates.md)입니다. SELECT 절에는 항상 리터럴 값을 포함할 수도 있습니다.

  GROUP BY 절은 SELECT, FROM 및 WHERE 절 뒤와 OFFSET LIMIT 절 앞에와 야 합니다. 현재 GROUP BY 절은 ORDER BY 절과 함께 사용할 수 없지만이는 계획 된 것입니다.

  GROUP BY 절은 다음을 허용 하지 않습니다.
  
- 별칭 속성 또는 별칭 시스템 함수 (별칭은 SELECT 절 내에서 계속 허용 됨)
- In
- 집계 시스템 함수 (SELECT 절 에서만 허용 됨)

## <a name="examples"></a>예

이 예에서는 [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo)를 통해 사용할 수 있는 영양 데이터 집합을 사용 합니다.

예를 들어 각 foodGroup 항목의 총 개수를 반환 하는 쿼리는 다음과 같습니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

일부 결과는 (TOP 키워드를 사용 하 여 결과를 제한)입니다.

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

이 쿼리에는 결과를 나누는 데 사용 되는 두 개의 식이 있습니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

일부 결과는 다음과 같습니다.

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

이 쿼리는 GROUP BY 절의 시스템 함수를 포함 합니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

일부 결과는 다음과 같습니다.

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

이 쿼리는 item 속성 식에 키워드와 시스템 함수를 모두 사용 합니다.

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

결과는 다음과 같습니다.

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [집계 함수](sql-query-aggregates.md)
