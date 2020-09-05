---
title: Azure Cosmos DB 쿼리 언어 로그인
description: 지정 된 숫자 식의 자연 로그를 반환 하 Azure Cosmos DB의 로그 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798264"
---
# <a name="log-azure-cosmos-db"></a>로그 (Azure Cosmos DB)
 지정된 숫자 식의 자연 로그를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
*base*  
   로그의 밑을 설정하는 선택적 숫자 인수입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  LOG()는 기본적으로 자연 로그를 반환합니다. 선택적인 base 매개 변수를 사용하여 로그의 밑을 다른 값으로 변경할 수 있습니다.  
  
  자연 로그는 밑 **e**의 로그입니다. 여기서 **e**는 무리 상수(대략 2.718281828)입니다.  
  
  숫자의 지수의 자연 로그는 숫자 자체, 즉 LOG( EXP( n ) ) = n입니다. 그리고 숫자의 자연 로그의 지수는 숫자 자체, 즉 EXP( LOG( n ) ) = n입니다.

  이 시스템 함수는 인덱스를 활용 하지 않습니다.
  
## <a name="examples"></a>예제
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 로그 값을 반환합니다.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 다음 예에서는 수의 지수에 대한 `LOG`를 계산합니다.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
