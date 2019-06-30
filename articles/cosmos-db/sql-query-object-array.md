---
title: 배열 및 Azure Cosmos DB의 개체 사용
description: Azure Cosmos DB에 대 한 배열 및 개체 만들기 SQL 구문에 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342816"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>배열 및 Azure Cosmos DB의 개체 사용

Azure Cosmos DB SQL API의 주요 기능은 배열 및 개체 만들기입니다.

## <a name="arrays"></a>배열

다음 예제에 표시 된 대로 배열을 생성할 수 있습니다.

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

사용할 수도 있습니다는 [배열 식](sql-query-subquery.md#array-expression) 배열을 생성 하 [하위 쿼리의](sql-query-subquery.md) 결과입니다. 배열에서 자식의 이름을 지정 하는 모든 고유이 쿼리 가져옵니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>반복

SQL API를 통해 추가 된 새 구문을 사용 하 여 JSON 배열 반복에 대 한 지원을 제공 합니다 [키워드의](sql-query-keywords.md#in) FROM 소스에서 합니다. 다음 예제에서는

```sql
    SELECT *
    FROM Families.children
```

결과는 다음과 같습니다.

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

다음 쿼리에서 반복을 수행 `children` 에 `Families` 컨테이너입니다. 출력 배열의 이전 쿼리와 다릅니다. 이 예제에서는 분할 `children`, 고 결과를 단일 배열로 평면화 합니다.  

```sql
    SELECT *
    FROM c IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

필터링 할 수 있습니다 다음 예제에 표시 된 대로 배열의 각 개별 항목에 추가 합니다.

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

결과는 다음과 같습니다.

```json
    [{
      "givenName": "Lisa"
    }]
```

배열 반복의 결과 대해 집계할 수 있습니다. 예를 들어 다음 쿼리는 모든 가족 간의 자식 수를 셉니다.

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [조인](sql-query-join.md)