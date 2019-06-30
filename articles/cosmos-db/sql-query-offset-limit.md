---
title: Azure Cosmos DB에서 오프셋 LIMIT 절
description: Azure Cosmos DB에 대 한 오프셋 LIMIT 절에 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342511"
---
# <a name="offset-limit-clause"></a>오프셋 LIMIT 절

오프셋 LIMIT 절은 건너뛸 하 고 일부 쿼리에서 값 개수를 수행 하는 선택적 절. 오프셋 LIMIT 절 오프셋 수 및 수를 제한 해야 합니다.

ORDER BY 절과 함께에서 오프셋 제한을 사용할 결과 집합 skip을 수행 하 여 생성 되 고 순서가 지정 된 값입니다. ORDER BY 절 없이 사용 하는 경우 값의 결정적인 순서 대로 발생 합니다.

## <a name="syntax"></a>구문
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>인수

- `<offset_amount>`

   쿼리 결과 건너뛰어야 하는 항목의 정수를 지정 합니다.

- `<limit_amount>`
  
   쿼리 결과 포함 해야 하는 항목의 정수를 지정 합니다.

## <a name="remarks"></a>설명
  
  오프셋 LIMIT 절 사용에 오프셋 개수와 수를 제한 해야 합니다. 선택 사항인 `ORDER BY` 절을 사용, 건너뛰기 순서가 지정 된 값에 대해 수행 하 여 결과 집합 생성 됩니다. 이 고, 그렇지 쿼리는 고정된 값 순서를 반환 합니다. 현재이 절은 단일 파티션 내의 쿼리에 대 한 지원, 파티션 간 쿼리 아직 지원 하지 않습니다.

## <a name="examples"></a>예

예를 들어, 다음은 첫 번째 값을 건너뜁니다 (상주 하는 도시의 이름의 순서로) 초 값을 반환 하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

첫 번째 값을 건너뛰고 (순서) 없이 두 번째 값을 반환 하는 쿼리는 다음과 같습니다.

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [ORDER BY 절](sql-query-order-by.md)
