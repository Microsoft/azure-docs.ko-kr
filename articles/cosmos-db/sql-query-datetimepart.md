---
title: Azure Cosmos DB 쿼리 언어의 DateTimePart
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimePart에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227386"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)

지정 된 날짜/시간 사이에 지정 된 DateTimePart의 값을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>인수
  
*DateTimePart*  
   DateTimePart가 값을 반환할 날짜 부분입니다. 다음 표에서는 모든 유효한 DateTimePart 인수를 보여 줍니다.

| DateTimePart | 약어        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| 월        | "month", "mm", "m"   |
| 일          | "day", "dd", "d"     |
| 시간         | "시간", "hh"         |
| Minute       | "minute", "mi", "n"  |
| 초       | "second", "ss", "s"  |
| Millisecond  | "밀리초", "ms"  |
| 마이크로초  | "마이크로초", "mcs" |
| 나노초   | "나노초", "ns"   |

*DateTime*  
   형식의 UTC 날짜 및 시간 ISO 8601 문자열 값 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>반환 형식

양의 정수 값을 반환 합니다.

## <a name="remarks"></a>설명

DateTimePart는 `undefined` 다음과 같은 이유로를 반환 합니다.

- 지정 된 DateTimePart 값이 잘못 되었습니다.
- DateTime은 유효한 ISO 8601 DateTime이 아닙니다.

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="examples"></a>예제

다음은 해당 월의 정수 값을 반환 하는 예입니다.

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

마이크로초 수를 반환 하는 예는 다음과 같습니다.

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
