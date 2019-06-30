---
title: Azure Cosmos DB에 매개 변수가 있는 쿼리
description: 매개 변수가 있는 SQL 쿼리를 알아보기
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342881"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB에 매개 변수가 있는 쿼리

Cosmos DB는 익숙한 @ 표기법으로 표현 된 매개 변수가 있는 쿼리를 지원 합니다. 매개 변수가 있는 SQL 강력한 처리 및 사용자 입력의 이스케이프를 제공 하 고 SQL 주입을 통해 데이터가 실수로 노출 되는 것을 금지 합니다.

## <a name="examples"></a>예

예를 들어, 사용 하는 쿼리를 작성할 수 있습니다 `lastName` 하 고 `address.state` 매개 변수로 다양 한 값에 대해 실행 `lastName` 및 `address.state` 사용자 입력을 기반으로 합니다.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

그런 다음 다음과 같은 매개 변수가 있는 JSON 쿼리로 Cosmos DB에이 요청을 보낼 수 있습니다.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

다음 예제에서는 매개 변수가 있는 쿼리를 사용 하 여 상위 인수를 설정합니다. 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

매개 변수 값은 유효한 모든 JSON 수: 문자열, 숫자, 부울, null, 짝수 배열 또는 중첩 된 JSON입니다. Cosmos DB는 스키마 이므로 모든 형식에 대해 매개 변수의 유효성이 검사 되지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [문서 데이터 모델](modeling-data.md)
