---
title: Azure Cosmos DB에서 WHERE 절
description: Azure Cosmos DB에 대 한 SQL WHERE 절에 알아봅니다
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342692"
---
# <a name="where-clause"></a>WHERE 절

선택 사항인 WHERE 절 (`WHERE <filter_condition>`) 조건을 지정 소스 JSON 항목 결과에 포함 하려면 쿼리에 대 한 충족 해야 합니다. JSON 항목을 지정된 된 조건을 평가 해야 `true` 결과 대해 고려해 야 합니다. 인덱스 계층 결과 포함 될 수 있는 소스 항목의 가장 작은 하위 집합을 확인 하려면 WHERE 절을 사용 합니다.
  
## <a name="syntax"></a>구문
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>인수

- `<filter_condition>`  
  
   반환할 문서에 대해 충족하는 조건을 지정합니다.  
  
- `<scalar_expression>`  
  
   계산할 값을 나타내는 식입니다. 참조 [스칼라 식](sql-query-scalar-expressions.md) 세부 정보에 대 한 합니다.  
  

## <a name="remarks"></a>설명
  
  문서를 반환하려면 필터 조건으로 지정된 식을 true로 평가해야 합니다. true 부울 값만 조건을 충족하고, 다른 값(undefined, null, false, 숫자, 배열 또는 개체)은 조건을 충족하지 않습니다. 

## <a name="examples"></a>예

다음 쿼리를 포함 하는 요청 항목을 `id` 값이 속성의 `AndersenFamily`합니다. 제외 되지 않은 모든 항목을 `id` 속성 또는 값이 일치 하지 않습니다 `AndersenFamily`합니다.

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 절에 스칼라 식

앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. SQL API도 다양 한 지원 [스칼라 식](sql-query-scalar-expressions.md)합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다.

다음 지원 되는 이항 연산자를 사용할 수 있습니다.  

|**연산자 유형**  | **값** |
|---------|---------|
|산술 | +,-,*,/,% |
|비트    | \|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) |
|논리    | AND, OR, NOT      |
|비교 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\|(연결) |

다음 쿼리는 이항 연산자를 사용합니다.

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

단항 연산자를 사용할 수도 있습니다 +,-, ~, 및 쿼리를 다음 예제에 표시 된 대로 되지 않습니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

또한 쿼리에서 속성 참조를 사용할 수 있습니다. 예를 들어 `SELECT * FROM Families f WHERE f.isRegistered` 속성을 포함 하는 JSON 항목 반환 `isRegistered` 값과 같은 `true`합니다. 와 같은 다른 값을 `false`, `null`를 `Undefined`, `<number>`를 `<string>`를 `<object>`, 또는 `<array>`, 결과에서 항목을 제외 합니다. 

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM 절](sql-query-from.md)