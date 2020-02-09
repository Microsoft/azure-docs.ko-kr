---
title: Azure Cosmos DB의 WHERE 절
description: Azure Cosmos DB에 대한 SQL WHERE 절 알아보기
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: tisande
ms.openlocfilehash: 31653b598f0f3a79bf7f9c09231b1d111f167a16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982232"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 WHERE 절

선택적 WHERE 절 (`WHERE <filter_condition>`)은 쿼리를 결과에 포함 하기 위해 원본 JSON 항목이 충족 해야 하는 조건을 지정 합니다. JSON 항목은 지정 된 조건을 평가 하 여 결과에 대해 고려 되도록 `true` 해야 합니다. 인덱스 계층은 WHERE 절을 사용 하 여 결과에 포함 될 수 있는 소스 항목의 가장 작은 하위 집합을 결정 합니다.
  
## <a name="syntax"></a>구문
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>인수

- `<filter_condition>`  
  
   반환할 문서에 대해 충족하는 조건을 지정합니다.  
  
- `<scalar_expression>`  
  
   계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 을 참조 하세요.  
  

## <a name="remarks"></a>설명
  
  문서를 반환하려면 필터 조건으로 지정된 식을 true로 평가해야 합니다. true 부울 값만 조건을 충족하고, 다른 값(undefined, null, false, 숫자, 배열 또는 개체)은 조건을 충족하지 않습니다. 

## <a name="examples"></a>예시

다음 쿼리는 값이 `AndersenFamily`되는 `id` 속성을 포함 하는 항목을 요청 합니다. `id` 속성이 없거나 해당 값이 `AndersenFamily`일치 하지 않는 항목은 제외 됩니다.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 절의 스칼라 식

앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. SQL API는 다양 한 [스칼라 식](sql-query-scalar-expressions.md)도 지원 합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다.

다음과 같이 지원 되는 이항 연산자를 사용할 수 있습니다.  

|**연산자 유형**  | **값** |
|---------|---------|
|산술 | +,-,*,/,% |
|비트 단위    | \|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) |
|논리    | AND, OR, NOT      |
|비교 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\|(연결) |

다음 쿼리는 이항 연산자를 사용 합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

다음 예제와 같이 쿼리에는 없고 단항 연산자 +,-, ~를 사용할 수도 있습니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

쿼리에서 속성 참조를 사용할 수도 있습니다. 예를 들어 `SELECT * FROM Families f WHERE f.isRegistered`은 `true`와 같은 값을 가진 `isRegistered` 속성을 포함 하는 JSON 항목을 반환 합니다. `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`또는 `<array>`와 같은 다른 값은 결과에서 항목을 제외 합니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [IN 키워드](sql-query-keywords.md#in)
- [FROM 절](sql-query-from.md)