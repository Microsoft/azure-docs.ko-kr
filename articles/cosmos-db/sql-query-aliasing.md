---
title: Azure Cosmos DB에서 별칭
description: Azure Cosmos DB SQL 쿼리의 별칭 값에 알아봅니다
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342738"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB에서 별칭

하면 명시적으로 별칭 쿼리에서 값입니다. 쿼리에 동일한 이름 가진 두 속성이 별칭 사용 하 여 프로젝션된 된 결과에서 명확 하 게 하는 되도록 속성 중 하나 또는 모두를 이름을 바꿉니다.

## <a name="examples"></a>예

두 번째 값을 프로젝션 할 때 다음 예제에 표시 된 대로 별칭 지정에 사용 되는 키워드는 선택 사항이 as `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 절](sql-query-select.md)
- [FROM 절](sql-query-from.md)
