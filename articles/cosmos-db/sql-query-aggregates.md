---
title: Azure Cosmos DB에서 집계 함수
description: Azure Cosmos DB에 대 한 SQL 집계 함수 구문에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342551"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB에서 집계 함수

SELECT 절에는 값 집합에서 계산을 수행 하 고 단일 값을 반환 하는 집계 함수입니다. 다음 쿼리 내에서 항목의 수를 반환 하는 예를 들어를 `Families` 컨테이너:

## <a name="examples"></a>예

```sql
    SELECT COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "$1": 2
    }]
```

VALUE 키워드를 사용 하 여 집계의 스칼라 값만을 반환할 수도 있습니다. 예를 들어 다음 쿼리는 단일 숫자로 값의 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [ 2 ]
```

또한 필터를 사용 하 여 집계를 결합할 수 있습니다. 다음 쿼리 주소 상태를 사용 하 여 항목의 수를 반환 하는 예를 들어 `WA`합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

결과는 다음과 같습니다.

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>집계 함수 유형

SQL API는 다음과 같은 집계 함수를 지원합니다. 숫자 값에서 작동 하는 SUM 및 AVG 및 COUNT, MIN 및 MAX 작업 숫자, 문자열, 부울 및 null 합니다.

| 함수 | 설명 |
|-------|-------------|
| COUNT | 식에서 항목 수를 반환합니다. |
| 합계   | 식에서 모든 값의 합계를 반환합니다. |
| MIN   | 식에서 최소값을 반환합니다. |
| MAX   | 식에서 최대값을 반환합니다. |
| 평균   | 식에서 평균값을 반환합니다. |

배열 반복의 결과 대해 집계할 수 있습니다.

> [!NOTE]
> Azure portal의 데이터 탐색기에서 집계 쿼리 하나의 쿼리 페이지에 대해 부분 결과 집계할 수 있습니다. SDK는 모든 페이지에 걸쳐 단일 누적 값을 생성합니다. 코드를 사용 하 여 집계 쿼리를 수행 하려면.NET SDK 1.12.0,.NET Core SDK 1.1.0 또는 Java SDK 1.9.5 이상이 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)