---
title: Azure Cosmos DB 인덱싱 정책
description: Azure Cosmos DB의 자동 인덱싱 및 성능 향상을 위해 기본 인덱싱 정책을 구성 하 고 변경 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: tisande
ms.openlocfilehash: f723d7ac218869313f02212d27d9f96b74bb7f0f
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607519"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책

Azure Cosmos DB 모든 컨테이너에는 컨테이너의 항목을 인덱싱하는 방법을 지정 하는 인덱싱 정책이 있습니다. 새로 만든 컨테이너에 대 한 기본 인덱싱 정책은 모든 항목의 모든 속성을 인덱싱하고 모든 문자열 또는 숫자에 대해 범위 인덱스를 적용 합니다. 이를 통해 인덱싱 및 인덱스 관리를 사전에 고려 하지 않고도 높은 쿼리 성능을 얻을 수 있습니다.

사용자 요구 사항에 맞게 이 자동 동작을 재정의할 수 있는 상황이 있습니다. *인덱싱 모드*를 설정 하 여 컨테이너의 인덱싱 정책을 사용자 지정 하 고 *속성 경로*를 포함 하거나 제외할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명 하는 인덱싱 정책 업데이트 방법은 Azure Cosmos DB의 SQL (Core) API에만 적용 됩니다. [MongoDB 용 AZURE COSMOS DB API](mongodb-indexing.md) 의 인덱싱에 대해 알아보기

## <a name="indexing-mode"></a>인덱싱 모드

Azure Cosmos DB는 두 가지 인덱싱 모드를 지원 합니다.

- **일관성**: 항목이 생성, 업데이트 또는 삭제 될 때 인덱스가 동기적으로 업데이트 됩니다. 즉, 읽기 쿼리의 일관성은 [계정에 대해 구성 된 일관성](consistency-levels.md)이 됩니다.
- **없음**: 컨테이너에서 인덱싱을 사용할 수 없습니다. 이는 컨테이너를 보조 인덱스가 없어도 순수 키-값 저장소로 사용할 때 일반적으로 사용 됩니다. 이를 사용 하 여 대량 작업의 성능을 향상 시킬 수도 있습니다. 대량 작업이 완료 되 면 인덱스 모드를 일관 된 상태로 설정 하 고 완료 될 때까지 [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) 를 사용 하 여 모니터링할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 지연 인덱싱 모드도 지원 합니다. 지연 인덱싱은 엔진이 다른 작업을 수행 하지 않을 때 훨씬 낮은 우선 순위 수준으로 인덱스 업데이트를 수행 합니다. 이로 인해 **일치 하지 않거나 불완전** 한 쿼리 결과가 발생할 수 있습니다. Cosmos 컨테이너를 쿼리하려면 지연 인덱스를 선택 하면 안 됩니다. 6 월 2020에는 새 컨테이너를 지연 인덱싱 모드로 설정 하는 것이 더 이상 허용 되지 않는 변경 내용이 도입 되었습니다. Azure Cosmos DB 계정에 이미 지연 인덱싱이 있는 컨테이너가 하나 이상 포함 되어 있는 경우이 계정은 변경에서 자동으로 제외 됩니다. [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 에 문의 하 여 예외를 요청할 수도 있습니다 (지연 인덱싱을 지원 하지 않는 [서버](serverless.md) 리스 모드에서 azure Cosmos 계정을 사용 하는 경우 제외).

기본적으로 인덱싱 정책은로 설정 됩니다 `automatic` . `automatic`인덱싱 정책에서 속성을로 설정 하 여 구현 `true` 합니다. 이 속성을로 설정 `true` 하면 Azure CosmosDB가 작성 된 문서를 자동으로 인덱싱할 수 있습니다.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a> 속성 경로 포함 및 제외

사용자 지정 인덱싱 정책은 명시적으로 포함 되거나 인덱싱에서 제외 되는 속성 경로를 지정할 수 있습니다. 인덱싱된 경로 수를 최적화 하 여 쓰기 작업의 대기 시간 및 대기 시간을 크게 줄일 수 있습니다. 이러한 경로는 [인덱싱 개요 섹션에 설명 된 방법](index-overview.md#from-trees-to-property-paths) 에 따라 다음과 같이 추가 됩니다.

- 스칼라 값 (문자열 또는 숫자)으로 시작 하는 경로는 다음으로 끝납니다. `/?`
- 배열의 요소는 `/[]` 표기법 (대신)을 통해 함께 처리 됩니다 `/0` `/1` .
- `/*`와일드 카드를 사용 하 여 노드 아래의 모든 요소를 일치 시킬 수 있습니다.

동일한 예제를 다시 수행 합니다.

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters`의 `employees` 경로는입니다.`/headquarters/employees/?`

- `locations`' `country` 경로는입니다.`/locations/[]/country/?`

- 아래의 모든 항목에 대 한 경로는 `headquarters` 입니다. `/headquarters/*`

예를 들어 경로를 포함할 수 있습니다 `/headquarters/employees/?` . 이 경로는 employees 속성을 인덱싱하는 것을 보장 하지만이 속성 내에서 중첩 된 추가 JSON은 인덱싱하지 않습니다.

## <a name="includeexclude-strategy"></a>포함/제외 전략

모든 인덱싱 정책은 루트 경로를 `/*` 포함 된 경로 또는 제외 된 경로로 포함 해야 합니다.

- 인덱싱할 필요가 없는 경로를 선택적으로 제외 하려면 루트 경로를 포함 합니다. 이 방법은 모델에 추가할 수 있는 새 속성을 사전에 인덱싱할 Azure Cosmos DB 있도록 하는 권장 방법입니다.
- 인덱싱할 필요가 있는 경로를 선택적으로 포함 하려면 루트 경로를 제외 합니다.

- 영숫자 문자 및 _ (밑줄)를 포함 하는 일반 문자를 포함 하는 경로의 경우 큰따옴표를 기준으로 경로 문자열을 이스케이프할 필요가 없습니다 (예: "/path/?"). 다른 특수 문자를 포함 하는 경로의 경우 큰따옴표 앞뒤에 경로 문자열을 이스케이프 해야 합니다 (예: "/ \" 경로-abc \" /?"). 경로에 특수 문자가 필요한 경우 안전을 위해 모든 경로를 이스케이프할 수 있습니다. 모든 경로와 특수 문자를 포함 하는 모든 경로를 이스케이프 하는 경우에는 차이점이 없습니다.

- `_etag`인덱싱에 대 한 포함 된 경로에 etag를 추가 하지 않는 한 시스템 속성은 기본적으로 인덱싱에서 제외 됩니다.

- 인덱싱 모드를 **일관**되 게 설정 하면 시스템 속성 `id` 및 `_ts` 가 자동으로 인덱싱됩니다.

경로를 포함 하거나 제외 하는 경우 다음과 같은 특성이 발생할 수 있습니다.

- `kind` 또는 중 하나일 수 있습니다 `range` `hash` . 범위 인덱스 기능은 해시 인덱스의 모든 기능을 제공 하므로 범위 인덱스를 사용 하는 것이 좋습니다.

- `precision` 는 포함 된 경로에 대 한 인덱스 수준에서 정의 된 숫자입니다. 값은 `-1` 최대 전체 자릿수를 나타냅니다. 이 값을 항상로 설정 하는 것이 좋습니다 `-1` .

- `dataType` 또는 중 하나일 수 있습니다 `String` `Number` . 이는 인덱싱되는 JSON 속성의 형식을 나타냅니다.

지정 하지 않으면 이러한 속성의 기본값이 다음과 같이 지정 됩니다.

| **속성 이름**     | **기본값** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 및 `Number` |

경로를 포함 하 고 제외 하는 인덱싱 정책 예제는 [이 섹션](how-to-manage-indexing-policy.md#indexing-policy-examples) 을 참조 하세요.

## <a name="includeexclude-precedence"></a>포함/제외 우선 순위

포함 된 경로와 제외 된 경로에 충돌이 있는 경우 보다 정확한 경로가 우선 적용 됩니다.

예를 들면 다음과 같습니다.

**포함 된 경로**: `/food/ingredients/nutrition/*`

**제외 된 경로**: `/food/ingredients/*`

이 경우 포함 된 경로는 보다 정확 하기 때문에 제외 된 경로 보다 우선 적용 됩니다. 이러한 경로를 기반으로 하는 `food/ingredients` 경로 또는 중첩 된 내의 모든 데이터는 인덱스에서 제외 됩니다. 예외는 포함 된 경로 () 내의 데이터 `/food/ingredients/nutrition/*` 입니다 .이는 인덱싱됩니다.

다음은 Azure Cosmos DB의 포함 및 제외 경로 우선 순위에 대 한 몇 가지 규칙입니다.

- 더 깊은 경로는 보다 정밀한 경로 보다 더 정확 합니다. 예를 들어 `/a/b/?` 는 보다 정확 `/a/?` 합니다.

- 는 `/?` 보다 정확 `/*` 합니다. 예를 들어 `/a/?` 보다 정확 하 게를 사용 하는 것이 `/a/*` `/a/?` 우선 합니다.

- 경로는 `/*` 포함 된 경로 또는 제외 된 경로 여야 합니다.

## <a name="spatial-indexes"></a>공간 인덱스

인덱싱 정책에 공간 경로를 정의 하는 경우 ```type``` 해당 경로에 적용 해야 하는 인덱스를 정의 해야 합니다. 공간 인덱스에 사용할 수 있는 형식은 다음과 같습니다.

* Point

* Polygon

* MultiPolygon

* LineString

기본적으로 Azure Cosmos DB는 공간 인덱스를 만들지 않습니다. 공간 SQL 기본 제공 함수를 사용 하려면 필요한 속성에 공간 인덱스를 만들어야 합니다. 공간 인덱스를 추가 하는 인덱싱 정책 예제는 [이 섹션](sql-query-geospatial-index.md) 을 참조 하세요.

## <a name="composite-indexes"></a>복합 인덱스

두 개 이상의 속성이 있는 절이 있는 쿼리에는 `ORDER BY` 복합 인덱스가 필요 합니다. 복합 인덱스를 정의 하 여 여러 개의 같음 및 범위 쿼리의 성능을 향상 시킬 수도 있습니다. 기본적으로 복합 인덱스는 정의 되지 않으므로 필요에 따라 [복합 인덱스를 추가](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) 해야 합니다.

포함 되거나 제외 된 경로와 달리 와일드 카드를 사용 하 여 경로를 만들 수 없습니다 `/*` . 모든 복합 경로에는 `/?` 지정할 필요가 없는 경로 끝에 암시적이 있습니다. 복합 경로에는 스칼라 값이 포함 되 고이 값은 복합 인덱스에 포함 됩니다.

복합 인덱스를 정의 하는 경우 다음을 지정 합니다.

- 두 개 이상의 속성 경로입니다. 속성 경로가 정의 되는 순서입니다.

- 순서 (오름차순 또는 내림차순)입니다.

> [!NOTE]
> 복합 인덱스를 추가 하는 경우 새 복합 인덱스 추가가 완료 될 때까지 쿼리에서 기존 범위 인덱스를 활용 합니다. 따라서 복합 인덱스를 추가 하는 경우 성능 향상을 즉시 관찰할 수 없습니다. [Sdk 중 하나를 사용 하 여](how-to-manage-indexing-policy.md)인덱스 변환의 진행률을 추적할 수 있습니다.

### <a name="order-by-queries-on-multiple-properties"></a>여러 속성에 대 한 ORDER BY 쿼리:

두 개 이상의 속성이 있는 절이 있는 쿼리에 복합 인덱스를 사용 하는 경우 다음 사항을 고려해 야 합니다 `ORDER BY` .

- 복합 인덱스 경로가 절의 속성 시퀀스와 일치 하지 않는 경우 `ORDER BY` 복합 인덱스는 쿼리를 지원할 수 없습니다.

- 복합 인덱스 경로 (오름차순 또는 내림차순)의 순서는 절의와도 일치 해야 합니다 `order` `ORDER BY` .

- 복합 인덱스는 `ORDER BY` 모든 경로에서 순서가 반대인 절도 지원 합니다.

복합 인덱스가 속성 이름, age 및 _ts에 정의 된 다음 예를 살펴보세요.

| **복합 인덱스**     | **예제 `ORDER BY` 쿼리**      | **복합 인덱스에서 지원 되나요?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

필요한 모든 쿼리를 제공할 수 있도록 인덱싱 정책을 사용자 지정 해야 합니다 `ORDER BY` .

### <a name="queries-with-filters-on-multiple-properties"></a>여러 속성에 대한 필터가 있는 쿼리

쿼리가 둘 이상의 속성에 대 한 필터를 포함 하는 경우 이러한 속성에 대 한 복합 인덱스를 만드는 것이 유용할 수 있습니다.

예를 들어 다음 쿼리는 두 속성에 대 한 같음 필터를 포함 합니다.

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

(Name ASC, age ASC)에서 복합 인덱스를 활용할 수 있는 경우이 쿼리는 더 효율적이 고 시간을 적게 차지 하며 더 적게 사용 됩니다.

범위 필터를 사용 하는 쿼리는 복합 인덱스를 사용 하 여 최적화할 수도 있습니다. 그러나이 쿼리에는 단일 범위 필터만 있을 수 있습니다. 범위 필터 `>` 에는,, `<` `<=` , `>=` 및 `!=` 가 포함 됩니다. 범위 필터는 복합 인덱스에서 마지막으로 정의 되어야 합니다.

같음 필터와 범위 필터를 모두 사용 하는 다음 쿼리를 살펴보십시오.

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

이 쿼리는 (name ASC, age ASC)의 복합 인덱스를 사용 하 여 더 효율적입니다. 그러나 복합 인덱스에서 같음 필터를 먼저 정의 해야 하므로 쿼리는 (age ASC, name ASC)에 복합 인덱스를 사용 하지 않습니다.

여러 속성에 대 한 필터가 있는 쿼리에 대 한 복합 인덱스를 만들 때 다음 사항을 고려해 야 합니다.

- 쿼리의 필터에 있는 속성은 복합 인덱스의 속성과 일치 해야 합니다. 속성이 복합 인덱스에 있지만 쿼리에 필터로 포함 되지 않은 경우 쿼리는 복합 인덱스를 사용 하지 않습니다.
- 쿼리에 복합 인덱스에서 정의 되지 않은 추가 속성이 필터에 있는 경우 복합 인덱스와 범위 인덱스의 조합을 사용 하 여 쿼리를 계산 합니다. 범위 인덱스를 사용 하는 경우 보다 덜 필요 합니다.
- 속성에 범위 필터 ( `>` ,, `<` , 또는)가 있는 경우 `<=` `>=` `!=` 이 속성은 복합 인덱스에서 마지막으로 정의 되어야 합니다. 쿼리에 둘 이상의 범위 필터가 있는 경우 복합 인덱스를 사용 하지 않습니다.
- 여러 필터를 사용 하 여 쿼리를 최적화 하기 위해 복합 인덱스를 만들 때 `ORDER` 복합 인덱스의는 결과에 영향을 주지 않습니다. 이 속성은 선택 사항입니다.
- 여러 속성에서 필터를 사용 하 여 쿼리에 대 한 복합 인덱스를 정의 하지 않은 경우에도 쿼리는 성공적으로 수행 됩니다. 그러나 복합 인덱스를 사용 하 여 쿼리 비용을 줄일 수 있습니다.

복합 인덱스가 속성 이름, 나이 및 타임 스탬프에 정의 되어 있는 다음 예제를 고려 합니다.

| **복합 인덱스**     | **예제 쿼리**      | **복합 인덱스에서 지원 되나요?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>필터 및 ORDER BY 절이 포함된 쿼리

쿼리가 하나 이상의 속성에 대해 필터링 하 고 ORDER BY 절에서 다른 속성을 갖는 경우 필터의 속성을 절에 추가 하면 도움이 될 수 있습니다 `ORDER BY` .

예를 들어 필터의 속성을 ORDER BY 절에 추가 하면 다음 쿼리를 다시 작성 하 여 복합 인덱스를 활용할 수 있습니다.

범위 인덱스를 사용 하 여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

복합 인덱스를 사용 하 여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

동일한 패턴 및 쿼리 최적화는 여러 개의 같음 필터를 사용 하는 쿼리에 대해 일반화 될 수 있습니다.

범위 인덱스를 사용 하 여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

복합 인덱스를 사용 하 여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

다음은 필터 및 절을 사용 하 여 쿼리를 최적화 하기 위해 복합 인덱스를 만들 때 사용 하는 고려 사항입니다 `ORDER BY` .

* 쿼리가 속성을 필터링 하는 경우 이러한 속성은 먼저 절에 포함 되어야 합니다 `ORDER BY` .
* 한 속성에 대 한 필터와 다른 속성을 사용 하는 별도의 절이 있는 쿼리에 복합 인덱스를 정의 하지 않으면 `ORDER BY` 쿼리는 성공 합니다. 그러나 특히 절의 속성에 높은 카디널리티가 있는 경우 복합 인덱스를 사용 하 여 쿼리의 사용 비용을 줄일 수 있습니다 `ORDER BY` .
* 여러 속성을 포함 하는 쿼리에 대해서도 복합 인덱스를 만들 때의 모든 고려 사항은 `ORDER BY` 계속 적용 됩니다.


| **복합 인덱스**                      | **예제 `ORDER BY` 쿼리**                                  | **복합 인덱스에서 지원 되나요?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>인덱싱 정책 수정

컨테이너의 인덱싱 정책은 [Azure Portal 또는 지원 되는 sdk 중 하나를 사용 하](how-to-manage-indexing-policy.md)여 언제 든 지 업데이트할 수 있습니다. 인덱싱 정책에 대 한 업데이트는 이전 인덱스에서 새 인덱스로의 변환을 트리거합니다 .이 변환은 온라인 및 내부에서 수행 되므로 작업 중에 추가 저장소 공간이 소비 되지 않습니다. 이전 정책의 인덱스는 컨테이너에 대해 프로 비전 된 처리량, 읽기 가용성 또는 처리량에 영향을 주지 않고 새 정책으로 효율적으로 변환 됩니다. 인덱스 변환은 비동기 작업이 며 완료 하는 데 걸리는 시간은 프로 비전 된 처리량, 항목 수 및 크기에 따라 달라 집니다.

> [!IMPORTANT]
> 인덱스 변환은 [요청 단위](request-units.md)를 사용 하는 작업입니다. 인덱스 변환에서 사용 하는 요청 단위는 [서버](serverless.md) 를 사용 하지 않는 컨테이너를 사용 하는 경우 현재 청구 되지 않습니다. 이러한 요청 단위는 서버 리스가 일반 공급 되 면 청구 됩니다.

> [!NOTE]
> [Sdk 중 하나를 사용 하 여](how-to-manage-indexing-policy.md)인덱스 변환의 진행률을 추적할 수 있습니다.

인덱스를 변환 하는 동안에는 쓰기 가용성에 영향을 주지 않습니다. 인덱스 변환은 프로 비전 된 RUs를 사용 하지만 CRUD 작업 또는 쿼리 보다 낮은 우선 순위로 사용 됩니다.

새 인덱스를 추가 하는 경우 읽기 가용성에는 영향을 주지 않습니다. 인덱스 변환이 완료 되 면 쿼리는 새 인덱스만 활용 합니다. 인덱스를 변환 하는 동안 쿼리 엔진은 기존 인덱스를 계속 사용 하므로 인덱싱 변경을 시작 하기 전에 관찰 한 내용에 대해 인덱싱 변환 중에 유사한 읽기 성능을 확인할 수 있습니다. 새 인덱스를 추가 하는 경우 쿼리 결과가 불완전 하거나 일치 하지 않을 수도 있습니다.

인덱스를 제거 하 고 삭제 된 인덱스를 필터링 하는 쿼리를 즉시 실행 하는 경우에는 일관성 또는 전체 쿼리 결과가 보장 되지 않습니다. 여러 인덱스를 제거 하 고 하나의 단일 인덱싱 정책 변경에서이 작업을 수행 하는 경우 쿼리 엔진은 인덱스 변환 전체에서 일관성 있고 완전 한 결과를 보장 합니다. 그러나 여러 인덱싱 정책 변경을 통해 인덱스를 제거 하는 경우 쿼리 엔진은 모든 인덱스 변환이 완료 될 때까지 일관 되거나 완전 한 결과를 보장 하지 않습니다. 대부분의 개발자는 인덱스를 삭제 하 고 이러한 인덱스를 활용 하는 쿼리를 즉시 실행 하려고 하므로 실제로는 그렇지 않습니다.

> [!NOTE]
> 가능 하면 항상 여러 인덱싱 변경 내용을 하나의 단일 인덱싱 정책 수정으로 그룹화 하려고 합니다.

## <a name="indexing-policies-and-ttl"></a>인덱싱 정책 및 TTL

[TTL (time-to-live) 기능](time-to-live.md) 을 사용 하려면 인덱싱이 필요 합니다. 이는 다음을 의미합니다.

- 인덱싱 모드가 None으로 설정 된 컨테이너에서는 TTL을 활성화할 수 없습니다.
- TTL이 활성화 된 컨테이너에서는 인덱싱 모드를 None으로 설정할 수 없습니다.

속성 경로를 인덱싱하지 않아도 되지만 TTL이 필요한 시나리오의 경우 다음과 같은 인덱싱 정책을 사용할 수 있습니다.

- 일관적인로 설정 된 인덱싱 모드
- 포함 된 경로 없음
- `/*` 제외 된 유일한 경로입니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
