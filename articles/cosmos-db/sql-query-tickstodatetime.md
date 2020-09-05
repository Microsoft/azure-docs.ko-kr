---
title: Azure Cosmos DB 쿼리 언어의 TicksToDateTime
description: Azure Cosmos DB의 SQL 시스템 함수 TicksToDateTime에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608848"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

지정 된 틱 값을 DateTime으로 변환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>인수

*단위로*  

부호 있는 숫자 값으로, Unix epoch 이후 경과한 현재 100 나노초 틱 수입니다. 즉, 00:00:00 목요일부터 1 월 1 1970 일에 경과 된 100 나노초 틱의 수입니다.

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601 문자열 값을 다음 형식으로 반환 합니다 `YYYY-MM-DDThh:mm:ss.fffffffZ` .
  
  |서식|설명|
  |-|-|
  |YYYY|네 자리 연도|
  |MM|두 자리 월 (01 = 1 월 등)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소 시작에 대 한 signifier|
  |hh|두 자리 시간 (00-23)|
  |MM|두 자리 분 (00-59)|
  |ss|두 자리 초 (00-59)|
  |. fffffff|7 자리 소수 자릿수 초|
  |Z|UTC (협정 세계시) 지정자||
  
  ISO 8601 형식에 대 한 자세한 내용은을 참조 하십시오 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>설명

`undefined`지정한 틱 값이 유효 하지 않으면 TicksToDateTime가를 반환 합니다.

## <a name="examples"></a>예제
  
다음 예에서는 틱을 DateTime으로 변환 합니다.

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
