---
title: Azure Cosmos DB 쿼리 언어의 LEFT
description: Azure Cosmos DB의 SQL 시스템 함수 LEFT에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 647bee87283f26b0d1ce832b2b2aaac690c05b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333735"
---
# <a name="left-azure-cosmos-db"></a>LEFT(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정된 수의 문자로 문자열의 왼쪽 부분을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자를 추출할 문자열 식입니다.  
  
*num_expr*  
   문자 수를 지정하는 숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 왼쪽 부분을 반환합니다.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
