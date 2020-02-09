---
title: Azure Cosmos DB에서 배열 및 개체 작업
description: Azure Cosmos DB에서 배열 및 개체를 만드는 SQL 구문에 대해 알아봅니다. 또한이 문서에서는 배열 개체에 대한 작업을 수행 하는 몇 가지 예제를 제공 합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870924"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB에서 배열 및 개체 작업

Azure Cosmos DB SQL API의 주요 기능은 배열 및 개체 만들기입니다.

## <a name="arrays"></a>배열

다음 예제와 같이 배열을 생성할 수 있습니다.

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

[배열 식을](sql-query-subquery.md#array-expression) 사용 하 여 [하위 쿼리의](sql-query-subquery.md) 결과에서 배열을 생성할 수도 있습니다. 이 쿼리는 배열에서 자식의 지정 된 고유 이름을 모두 가져옵니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>반복

SQL API는 JSON 배열에 대한 반복을 지원 하며, FROM 소스에 있는 [in 키워드](sql-query-keywords.md#in) 를 통해 새 구문을 추가 합니다. 다음 예제에서는

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

다음 쿼리는 `Families` 컨테이너의 `children` 반복을 수행 합니다. 출력 배열은 앞의 쿼리와 다릅니다. 이 예제에서는 `children`를 분할 하 고 결과를 단일 배열로 평면화 합니다.  

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

다음 예제와 같이 배열의 각 개별 항목을 필터링 할 수 있습니다.

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

배열 반복의 결과를 집계할 수도 있습니다. 예를 들어 다음 쿼리는 모든 패밀리에서 자식의 수를 계산 합니다.

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
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [조인](sql-query-join.md)