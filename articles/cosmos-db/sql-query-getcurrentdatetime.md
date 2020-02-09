---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentDateTime
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentDateTime에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351031"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 현재 UTC (협정 세계시) 날짜와 시간을 ISO 8601 문자열로 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>반환 유형
  
  현재 UTC 날짜 및 시간 ISO 8601 문자열 값을 `YYYY-MM-DDThh:mm:ss.sssZ` 형식으로 반환 합니다.
  
  |||
  |-|-|
  |YYYY|네 자리 연도|
  |MM|두 자리 월 (01 = 1 월 등)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소 시작에 대한 signifier|
  |hh|2 자리 시간 (00-23)|
  |MM|2 자리 분 (00-59)|
  |ss|두 자리 초 (00-59)|
  |. sss|초의 소수 부분 3 자리|
  |Z|UTC (협정 세계시) 지정자||
  
  ISO 8601 형식에 대한 자세한 내용은 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601) 를 참조 하세요.

## <a name="remarks"></a>설명

  GetCurrentDateTime ()은 비결 정적 함수입니다. 
  
  반환 된 결과는 UTC입니다.

## <a name="examples"></a>예
  
  다음 예제에서는 GetCurrentDateTime () 기본 제공 함수를 사용 하 여 현재 UTC 날짜 시간을 가져오는 방법을 보여 줍니다.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 예제 결과 집합은 다음과 같습니다.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
