---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTicks
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentTicks에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606936"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

00:00:00 목요일부터 1 월 1 1970 일에 경과한 100 나노초 틱 수를 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값, Unix epoch 이후 경과한 현재 100 나노초 틱 수를 반환 합니다. 즉, GetCurrentTicks은 00:00:00 목요일부터 1 월 1 1970 일에 경과한 100 나노초 틱 수를 반환 합니다.

## <a name="remarks"></a>설명

GetCurrentTicks ()은 비결 정적 함수입니다. 반환 된 결과는 UTC (협정 세계시)입니다.

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="examples"></a>예제

다음은 틱 단위로 측정 되는 현재 시간을 반환 하는 예입니다.

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
