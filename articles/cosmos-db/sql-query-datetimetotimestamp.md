---
title: Azure Cosmos DB 쿼리 언어의 DateTimeToTimestamp
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeToTimestamp에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608825"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)

지정 된 날짜/시간을 타임 스탬프로 변환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>인수

*DateTime*  
   UTC 날짜 및 시간 ISO 8601 문자열 값 (형식 `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
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

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값, Unix epoch 이후 경과 된 현재 시간 (밀리초)을 반환 합니다 (예: 00:00:00 목요일 이후 경과 된 시간 (밀리초), 1 월 1970 일).

## <a name="remarks"></a>설명

`undefined`지정 된 DateTime 값이 잘못 된 경우 DateTimeToTimestamp에서를 반환 합니다.

## <a name="examples"></a>예제
  
다음 예에서는 DateTime을 타임 스탬프로 변환 합니다.

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

다른 예제는 다음과 같습니다.

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
