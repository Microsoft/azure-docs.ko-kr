---
title: Azure Cosmos DB 집계 함수
description: Azure Cosmos DB에서 지 원하는 집계 함수 유형 및 SQL 집계 함수 구문에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871842"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 집계 함수

집계 함수는 SELECT 절의 값 집합에 대해 계산을 수행 하 고 단일 값을 반환 합니다. 예를 들어 다음 쿼리는 `Families` 컨테이너 내의 항목 수를 반환 합니다.

## <a name="examples"></a>예시

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

VALUE 키워드를 사용 하 여 집계의 스칼라 값만 반환할 수도 있습니다. 예를 들어 다음 쿼리는 단일 숫자로 값의 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [ 2 ]
```

또한 집계를 필터와 결합할 수 있습니다. 예를 들어 다음 쿼리는 `WA`주소 상태의 항목 수를 반환 합니다.

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

SQL API는 다음과 같은 집계 함수를 지원 합니다. 숫자, 문자열, 부울 및 null에 대한 SUM 및 AVG는 숫자 값, 개수, 최소값 및 최대값에 대해 작동 합니다.

| 함수 | 설명 |
|-------|-------------|
| 개수 | 식에서 항목 수를 반환합니다. |
| 합계   | 식에서 모든 값의 합계를 반환합니다. |
| 최소   | 식에서 최소값을 반환합니다. |
| 최대   | 식에서 최대값을 반환합니다. |
| 평균   | 식에서 평균값을 반환합니다. |

배열 반복의 결과를 집계할 수도 있습니다.

> [!NOTE]
> Azure Portal의 데이터 탐색기에서 집계 쿼리는 하나의 쿼리 페이지에 대한 일부 결과만 집계할 수 있습니다. SDK는 모든 페이지에 걸쳐 단일 누적 값을 생성 합니다. 코드를 사용 하 여 집계 쿼리를 수행 하려면 .NET SDK 1.12.0, .NET Core SDK 1.1.0 또는 Java SDK 1.9.5 이상이 필요 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)