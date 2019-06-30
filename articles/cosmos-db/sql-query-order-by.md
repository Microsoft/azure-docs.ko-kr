---
title: Azure Cosmos DB ORDER BY 절
description: Azure Cosmos DB에 대 한 SQL ORDER BY 절에 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342478"
---
# <a name="order-by-clause"></a>ORDER BY 절

선택적 ORDER BY 절은 쿼리에서 반환 된 결과 대 한 정렬 순서를 지정 합니다.

## <a name="syntax"></a>구문
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>인수
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열의 이름 또는 속성 별칭으로 지정할 수 있습니다.  
  
   여러 속성을 지정할 수 있습니다. 속성 이름은 고유 해야 합니다. ORDER BY 절에서 정렬 속성의 순서 정렬된 된 결과 집합의 구조를 정의합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   Select 목록에 속성 또는 속성을 정확 하 게 from 절에 지정 된 컬렉션에 정의 된 ORDER BY 절에서 참조 된 속성 이름은 일치 해야 합니다.  
  
- `<sort_expression>`  
  
   하나 이상의 속성 또는 쿼리 결과 집합을 정렬 하는 식을 지정 합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값을 오름차순 또는 내림차순으로 정렬하도록 지정합니다. ASC는 가장 낮은 값에서 가장 높은 값으로 정렬합니다. DESC는 가장 높은 값에서 가장 낮은 값으로 정렬합니다. ASC가 기본 정렬 순서입니다. 널 값은 가능한 가장 낮은 값으로 처리됩니다.  
  
## <a name="remarks"></a>설명  
  
   ORDER BY 절은 인덱싱 정책을 정렬할 필드에 대 한 인덱스를 포함 해야 합니다. Azure Cosmos DB 쿼리 런타임의 속성 이름에 대해 계산 된 속성 대해서가 아니라 정렬 지원 합니다. Azure Cosmos DB는 여러 ORDER BY 속성을 지원합니다. 여러 ORDER BY 속성을 사용 하 여 쿼리를 실행 하기 위해 정의 해야 하는 [복합 인덱스](index-policy.md#composite-indexes) 정렬 하 고 필드에 합니다.

## <a name="examples"></a>예

예를 들어 다음과 같습니다. 오름차순 상주 하는 도시의 이름으로 가족을 검색 하는 쿼리

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

다음 쿼리는 검색 제품군 `id`의 해당 항목 만든 날짜 순으로 합니다. 항목 `creationDate` 를 나타내는 숫자를 *epoch 시간*, 또는 1970 초에서 1 월 1 일 이후 경과 된 시간입니다.

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

또한 여러 속성으로 정렬할 수 있습니다. 여러 속성을 기준으로 정렬 하는 쿼리 필요는 [복합 인덱스](index-policy.md#composite-indexes)합니다. 다음 쿼리를 살펴보십시오.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

이 쿼리는 제품군 검색 `id` 군/시의 오름차순입니다. 쿼리를 기준으로 정렬 됩니다 여러 항목에 도시 이름이 같은 경우는 `creationDate` 내림차순으로 정렬 합니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [오프셋 LIMIT 절](sql-query-offset-limit.md)
