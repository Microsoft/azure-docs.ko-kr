---
title: Azure Cosmos DB에 대 한 SQL 키워드
description: Azure Cosmos DB에 대 한 SQL 키워드에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342617"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB의 키워드
이 문서에서는 Azure Cosmos DB SQL 쿼리에 사용할 수 있는 키워드를 자세히 설명 합니다.

## <a name="between"></a>간에

ANSI SQL 에서처럼 문자열 또는 숫자 값의 범위에 대 한 쿼리를 표현 하는 BETWEEN 키워드를 사용할 수 있습니다. 예를 들어 다음 쿼리는 첫 번째 자식의 등급이 1-5를 포괄 하는 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

와 달리 ANSI sql에서 사용할 수 있습니다도 BETWEEN 절 다음 예제와 같이 FROM 절에서.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

ANSI SQL과 달리 SQL api에서 혼합 형식의 속성에 대해 범위 쿼리를 표현할 수 있습니다. 예를 들어 `grade` ' 좋아요 '를 숫자 않을 `5` 일부 항목에 같은 문자열 `grade4` 다른 합니다. 이러한 경우 JavaScript에서와 같이 두 가지 유형 간의 비교 결과 `Undefined`이므로 항목을 건너뜁니다.

> [!TIP]
> 쿼리 실행 시간을 단축 BETWEEN 절에서 필터링 하는 모든 숫자 속성 또는 경로 대 한 범위 인덱스 형식을 사용 하는 인덱싱 정책을 만듭니다.

## <a name="distinct"></a>DISTINCT

DISTINCT 키워드의 쿼리 프로젝션에서 중복 항목을 제거합니다.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

이 예에서 쿼리는 각 성에 대 한 값을 프로젝션합니다.

결과는 다음과 같습니다.

```json
[
    "Andersen"
]
```

고유한 개체를 프로젝션 할 수도 있습니다. 이 경우 쿼리는 빈 개체를 반환 하므로 lastName 필드를 두 개 문서 중 하나에 존재 하지 않습니다.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

결과는 다음과 같습니다.

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

하위 쿼리 내에서 프로젝션에 DISTINCT은 사용할 수도 있습니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

이 쿼리는 중복 제거를 사용 하 여 각 자식의 givenName를 포함 하는 배열을 프로젝션 합니다. 이 배열 ChildNames 별칭이 및 외부 쿼리 프로젝션 합니다.

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```
## <a name="in"></a> IN

IN 키워드를 사용 하 여 지정된 된 값 목록에 있는 값이 일치 하는지 확인 합니다. 다음 쿼리는 모든 제품군 항목을 반환 하는 예를 들어 여기서는 `id` 됩니다 `WakefieldFamily` 또는 `AndersenFamily`합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

다음 예제에서는 상태가 지정된 된 값의 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API에 대 한 지원 제공 [JSON 배열 반복](sql-query-object-array.md#Iteration), FROM 소스에서 in 키워드를 통해 추가 된 새 구문을 사용 하 여 합니다. 

## <a name="top"></a>맨 위로 이동

TOP 키워드 첫 번째 개체가 반환 `N` 정의 되지 않은 순서로 쿼리 결과의 수입니다. 모범 사례로, 상위 ORDER BY 절을 사용 하 여 첫 번째 결과 제한 하려면 사용 `N` 정렬 된 값의 수입니다. 이러한 두 개의 절을 결합 하는 유일한 방법은 예측 가능한 방식으로 위쪽에 영향을 줍니다는 행을 나타냅니다.

다음 예제와 같이 상수 값, 또는 매개 변수가 있는 쿼리를 사용 하 여 변수 값을 사용 하 여 TOP을 사용할 수 있습니다.

```sql
    SELECT TOP 1 *
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [조인](sql-query-join.md)
- [하위 쿼리](sql-query-subquery.md)