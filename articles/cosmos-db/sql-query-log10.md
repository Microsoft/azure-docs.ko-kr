---
title: Azure Cosmos DB 쿼리 언어의 LOG10
description: 지정 된 숫자 식의 상용 로그를 반환 하 Azure Cosmos DB의 LOG10 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6869abb3040feb6431d60799536c9986c6ccb954
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798247"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 지정한 숫자 식의 상용 로그(밑 10)를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expression*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  LOG10과 POWER 함수는 서로 역함수 관계에 있습니다. 예를 들어 10 ^ LOG10(n) = n입니다. 이 시스템 함수는 인덱스를 활용 하지 않습니다.
  
## <a name="examples"></a>예제
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (100)의 LOG10 값을 반환합니다.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
