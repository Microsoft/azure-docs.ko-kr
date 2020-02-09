---
title: Azure Cosmos DB 오프셋 제한 절
description: OFFSET LIMIT 절을 사용 하 여에서 쿼리할 때 특정 값을 건너뛰거나 가져오는 방법에 대해 알아봅니다 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771580"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 오프셋 제한 절

OFFSET LIMIT 절은 건너뛸 선택적 절입니다 .이 절은 쿼리에서 특정 개수의 값을 사용 합니다. Offset LIMIT 절에는 오프셋 수와 제한 수가 필요 합니다.

OFFSET LIMIT를 ORDER BY 절과 함께 사용 하면 결과 집합은 skip을 수행 하 고 순서가 지정 된 값을 사용 하 여 생성 됩니다. ORDER BY 절을 사용 하지 않으면 값의 결정적 순서가 됩니다.

## <a name="syntax"></a>구문
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>인수

- `<offset_amount>`

   쿼리 결과에서 건너뛸 항목의 정수 수를 지정 합니다.

- `<limit_amount>`
  
   쿼리 결과에 포함 해야 하는 정수 항목 수를 지정 합니다.

## <a name="remarks"></a>설명
  
  `OFFSET` 개수와 `LIMIT` 개수는 모두 `OFFSET LIMIT` 절에 필요 합니다. 선택적 `ORDER BY` 절을 사용 하는 경우에는 정렬 된 값에 대해 skip을 수행 하 여 결과 집합을 생성 합니다. 그렇지 않은 경우 쿼리는 값의 고정 순서를 반환 합니다.

  `OFFSET LIMIT`로 쿼리를 사용 하는 경우 오프셋 수가 증가 함에 따라 비용이 증가 합니다. 결과의 여러 페이지가 있는 쿼리의 경우 일반적으로 연속 토큰을 사용 하는 것이 좋습니다. 연속 토큰은 나중에 쿼리를 다시 시작할 수 있는 위치에 대한 "책갈피"입니다. `OFFSET LIMIT`사용 하는 경우 "책갈피"가 없습니다. 쿼리의 다음 페이지를 반환 하려는 경우 처음부터 시작 해야 합니다.
  
  문서 전체를 건너뛰고 클라이언트 리소스를 저장 하려는 경우에 `OFFSET LIMIT`를 사용 해야 합니다. 예를 들어, 1000th 쿼리 결과로 건너뛰려면 결과 1 ~ 999을 볼 필요가 없는 경우 `OFFSET LIMIT`를 사용 해야 합니다. 백 엔드에서는 건너뛴 문서를 포함 하 여 각 문서를 로드 하는 `OFFSET LIMIT`. 성능 이점은 불필요 한 문서 처리를 방지 하 여 클라이언트 리소스를 절약 하는 것입니다.

## <a name="examples"></a>예시

예를 들어 다음은 첫 번째 값을 건너뛰고 두 번째 값 (상주 도시 이름 순)을 반환 하는 쿼리입니다.

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

다음은 첫 번째 값을 건너뛰고 순서를 지정 하지 않고 두 번째 값을 반환 하는 쿼리입니다.

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
