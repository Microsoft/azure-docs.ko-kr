---
title: Azure Cosmos DB의 ORDER BY 절
description: Azure Cosmos DB에 대 한 SQL ORDER BY 절에 대해 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188741"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 ORDER BY 절

선택적인 ORDER BY 절은 쿼리에서 반환 되는 결과에 대 한 정렬 순서를 지정 합니다.

## <a name="syntax"></a>구문
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>인수
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열은 이름이 나 속성 별칭으로 지정할 수 있습니다.  
  
   여러 속성을 지정할 수 있습니다. 속성 이름은 고유 해야 합니다. ORDER BY 절에서 정렬 속성의 순서는 정렬 된 결과 집합의 구성을 정의 합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   ORDER BY 절에서 참조 되는 속성 이름은 선택 목록의 속성 또는 모호성 없이 FROM 절에 지정 된 컬렉션에 정의 된 속성과 일치 해야 합니다.  
  
- `<sort_expression>`  
  
   쿼리 결과 집합을 정렬할 하나 이상의 속성이 나 식을 지정 합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값이 오름차순으로 정렬되는지 내림차순으로 정렬되는지를 지정합니다. ASC는 오름차순으로 정렬하고, DESC는 내림차순으로 정렬합니다. ASC가 기본 정렬 순서입니다. Null 값은 가능한 가장 작은 값으로 취급됩니다.  
  
## <a name="remarks"></a>설명  
  
   `ORDER BY` 절을 사용 하려면 정렬 하 고 있는 필드에 대 한 인덱스를 인덱싱 정책에 포함 해야 합니다. Azure Cosmos DB 쿼리 런타임은 계산 된 속성이 아닌 속성 이름에 대 한 정렬을 지원 합니다. Azure Cosmos DB는 여러 `ORDER BY` 속성을 지원 합니다. 여러 ORDER BY 속성을 사용 하 여 쿼리를 실행 하려면 정렬할 필드에 [복합 인덱스](index-policy.md#composite-indexes) 를 정의 해야 합니다.

> [!Note]
> 정렬 되는 속성이 일부 문서에 대해 정의 되지 않은 경우이를 ORDER BY 쿼리로 검색 하려면이 경로를 인덱스에 명시적으로 포함 해야 합니다. 기본 인덱싱 정책은 sort 속성이 정의 되지 않은 문서를 검색 하는 것을 허용 하지 않습니다. [일부 누락 된 필드가 있는 문서에 대 한 예제 쿼리를 검토](#documents-with-missing-fields)합니다.

## <a name="examples"></a>예

예를 들어 다음은 주 도시 이름의 오름차순으로 패밀리를 검색 하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음 쿼리는 항목을 만든 날짜 순으로 패밀리 `id`를 검색 합니다. 항목 `creationDate`은 *epoch 시간*또는 1 월 1 1970 일 이후 경과 된 시간 (초)을 나타내는 숫자입니다.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

또한 여러 속성을 기준으로 순서를 지정할 수 있습니다. 여러 속성으로 정렬 하는 쿼리에는 [복합 인덱스가](index-policy.md#composite-indexes)필요 합니다. 다음과 같은 쿼리를 고려해 보세요.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

이 쿼리는 도시 이름 오름차순으로 제품군 `id`를 검색 합니다. 여러 항목에 동일한 도시 이름이 있는 경우 쿼리는 `creationDate` 기준으로 내림차순으로 정렬 됩니다.

## <a name="documents-with-missing-fields"></a>누락 된 필드가 있는 문서

기본 인덱싱 정책을 사용 하 여 컨테이너에 대해 실행 되는 `ORDER BY` 쿼리는 sort 속성이 정의 되지 않은 문서를 반환 하지 않습니다. Sort 속성이 정의 되어 있지 않은 문서를 포함 하려는 경우에는 인덱싱 정책에이 속성을 명시적으로 포함 해야 합니다.

예를 들어 `"/*"`이외의 경로를 명시적으로 포함 하지 않는 인덱싱 정책이 있는 컨테이너는 다음과 같습니다.

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

`Order By` 절에 `lastName`를 포함 하는 쿼리를 실행 하면 `lastName` 속성이 정의 된 문서만 결과에 포함 됩니다. `lastName`에 대 한 명시적 포함 경로를 정의 하지 않았으므로 `lastName` 없는 문서는 쿼리 결과에 표시 되지 않습니다.

다음은 두 문서에 `lastName`를 기준으로 정렬 하는 쿼리입니다. 하나는 `lastName` 정의 되어 있지 않습니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과에는 `lastName`정의 된 문서만 포함 됩니다.

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

`lastName`에 대 한 경로를 명시적으로 포함 하도록 컨테이너의 인덱싱 정책을 업데이트 하는 경우 쿼리 결과에 정의 되지 않은 sort 속성이 있는 문서를 포함 합니다. 경로를 명시적으로 정의 하 여이 스칼라 값 (이외에는)을 지정 해야 합니다. 인덱싱 정책의 경로 정의에 `?` 문자를 사용 하 여 속성 `lastName`를 명시적으로 인덱싱하고 그 밖의 추가 중첩 경로를 사용 하지 않도록 해야 합니다.

다음은 정의 되지 않은 `lastName` 있는 문서가 쿼리 결과에 나타날 수 있는 샘플 인덱싱 정책입니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

동일한 쿼리를 다시 실행 하는 경우 누락 된 문서는 쿼리 결과에서 먼저 표시 `lastName`.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

정렬 순서를 `DESC`로 수정 하는 경우 `lastName` 누락 된 문서는 쿼리 결과의 마지막에 표시 됩니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB의 인덱싱 정책](index-policy.md)
- [OFFSET LIMIT 절](sql-query-offset-limit.md)
