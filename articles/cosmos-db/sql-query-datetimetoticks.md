---
title: Azure Cosmos DB 쿼리 언어의 DateTimeToTicks
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeToTicks에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605141"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

지정 된 날짜/시간을 틱으로 변환 합니다. 단일 틱은 100 나노초 또는 1 10-1 1/1000000 초를 나타냅니다. 

## <a name="syntax"></a>구문
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>인수
  
*DateTime*  
   형식의 UTC 날짜 및 시간 ISO 8601 문자열 값 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값, Unix epoch 이후 경과한 현재 100 나노초 틱 수를 반환 합니다. 즉, DateTimeToTicks는 00:00:00 목요일부터 1 월 1 1970 일에 경과 된 100 나노초 틱 수를 반환 합니다.

## <a name="remarks"></a>설명

`undefined`Datetime이 유효한 ISO 8601 datetime이 아니면 DateTimeDateTimeToTicks는를 반환 합니다.

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="examples"></a>예제

틱 수를 반환 하는 예는 다음과 같습니다.

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

소수 자릿수 초를 지정 하지 않고 틱 수를 반환 하는 예는 다음과 같습니다.

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
