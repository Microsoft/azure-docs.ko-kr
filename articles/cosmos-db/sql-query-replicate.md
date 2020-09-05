---
title: Azure Cosmos DB 쿼리 언어로 복제
description: Azure Cosmos DB에서 SQL 시스템 함수 복제에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794314"
---
# <a name="replicate-azure-cosmos-db"></a>복제 (Azure Cosmos DB)
 지정한 횟수만큼 문자열 값을 반복합니다.
  
## <a name="syntax"></a>구문
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.
  
*num_expr*  
   숫자 식입니다. *Num_expr* 음수 이거나 한정 되지 않은 경우 결과가 정의 되지 않습니다.
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.
  
## <a name="remarks"></a>설명

  결과의 최대 길이는 1만 자입니다. 예를 들어 (length (*str_expr*) *  *num_expr*) <= 1만입니다. 이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="examples"></a>예제
  
  다음 예제에서는 쿼리에서를 사용 하는 방법을 보여 줍니다 `REPLICATE` .
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 결과 집합은 다음과 같습니다.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
