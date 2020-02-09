---
title: Azure Cosmos DB의 인덱싱
description: Azure Cosmos DB에서 인덱싱이 작동 하는 방식을 이해 하 고 범위, 공간, 복합 인덱스와 같은 다양 한 종류의 인덱스를 지원 합니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873627"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 인덱싱-개요

Azure Cosmos DB은 스키마를 포함 하지 않는 데이터베이스 이며 스키마 또는 인덱스 관리를 처리할 필요 없이 응용 프로그램에서 반복할 수 있습니다. 기본적으로 Azure Cosmos DB는 스키마를 정의 하거나 보조 인덱스를 구성 하지 않고도 [컨테이너](databases-containers-items.md#azure-cosmos-containers) 의 모든 항목에 대한 모든 속성을 자동으로 인덱싱합니다.

이 문서에서는 Azure Cosmos DB의 데이터 인덱싱 방법과 인덱스를 사용하여 쿼리 성능을 개선하는 방법을 설명합니다. [인덱싱 정책을](index-policy.md)사용자 지정 하는 방법을 살펴보기 전에이 섹션을 진행 하는 것이 좋습니다.

## <a name="from-items-to-trees"></a>항목에서 트리로

항목이 컨테이너에 저장 될 때마다 해당 콘텐츠는 JSON 문서로 프로젝션 된 후 트리 표현으로 변환 됩니다. 즉, 해당 항목의 모든 속성은 트리의 노드로 표시 됩니다. 의사 루트 노드는 항목의 모든 첫 번째 수준 속성에 대한 부모로 만들어집니다. 리프 노드에는 항목에서 전달 하는 실제 스칼라 값이 포함 됩니다.

예를 들어 다음 항목을 살펴보세요.

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

다음 트리로 표시 됩니다.

![트리로 표시 된 이전 항목입니다.](./media/index-overview/item-as-tree.png)

트리에서 배열을 인코딩하는 방법에 유의 하세요. 배열의 모든 항목은 배열 내에서 해당 항목의 인덱스 (0, 1 등)로 레이블이 지정 된 중간 노드를 가져옵니다.

## <a name="from-trees-to-property-paths"></a>트리에서 속성 경로로

Azure Cosmos DB 항목을 트리로 변환 하는 이유는 해당 트리 내에서 속성을 참조할 수 있기 때문입니다. 속성의 경로를 가져오기 위해 루트 노드에서 해당 속성에 대한 트리를 트래버스 하 고 각 트래버스 노드의 레이블을 연결할 수 있습니다.

위에서 설명한 예제 항목의 각 속성에 대한 경로는 다음과 같습니다.

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

항목을 쓸 때 Azure Cosmos DB는 각 속성의 경로와 해당 값을 효과적으로 인덱싱합니다.

## <a name="index-kinds"></a>인덱스 종류

현재 Azure Cosmos DB에서는 세 가지 종류의 인덱스를 지원 합니다.

### <a name="range-index"></a>범위 인덱스

**범위** 인덱스는 순서가 지정 된 트리 형식 구조를 기반으로 합니다. 범위 인덱스 종류는 다음에 사용 됩니다.

- 같음 쿼리:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   배열 요소의 같음 일치
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- 범위 쿼리:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  `>`, `<`, `>=`, `<=`, `!=`에 대해 작동 합니다.

- 속성이 있는지 확인 하는 중:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 문자열 접두사 일치 (CONTAINS 키워드는 범위 인덱스를 활용 하지 않음):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY` 쿼리:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` 쿼리:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

범위 인덱스는 스칼라 값 (문자열 또는 숫자)에 사용할 수 있습니다.

### <a name="spatial-index"></a>공간 인덱스

**공간** 인덱스는 요소, 선, 다각형 및 multipolygon과 같은 지리 공간적 개체에 대한 효율적인 쿼리를 가능 하 게 합니다. 이러한 쿼리는 ST_DISTANCE, ST_WITHIN ST_INTERSECTS 키워드를 사용 합니다. 공간 인덱스 종류를 사용 하는 몇 가지 예는 다음과 같습니다.

- 지리 공간적 거리 쿼리:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 쿼리 내의 지리 공간적:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- 지리 공간적 교차 쿼리:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

올바른 형식의 [GeoJSON](geospatial.md) 개체에 대해 공간 인덱스를 사용할 수 있습니다. Points, LineStrings, Polygon 및 MultiPolygons은 현재 지원 됩니다.

### <a name="composite-indexes"></a>복합 인덱스

**복합** 인덱스는 여러 필드에 대한 작업을 수행할 때 효율성을 높입니다. 복합 인덱스 종류는 다음에 사용 됩니다.

- 여러 속성에 대해 쿼리를 `ORDER BY` 합니다.

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 필터 및 `ORDER BY`를 사용 하 여 쿼리 합니다. 이러한 쿼리는 filter 속성이 `ORDER BY` 절에 추가 된 경우 복합 인덱스를 활용할 수 있습니다.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 하나 이상의 속성이 같음 필터 인 두 개 이상의 속성에 대한 필터를 사용 하 여 쿼리

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

하나의 필터 조건자가 인덱스 종류의에서 사용 하는 한 쿼리 엔진은 나머지를 검색 하기 전에이를 먼저 평가 합니다. 예를 들어 SQL 쿼리 (예: `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`)가 있는 경우

* 위의 쿼리에서는 먼저 인덱스를 사용 하 여 firstName = "Andrew" 인 항목을 필터링 합니다. 그런 다음, 다음 파이프라인을 통해 firstName = "Andrew" 항목을 모두 전달 하 여 CONTAINS 필터 조건자를 평가 합니다.

* 인덱스를 사용 하는 추가 필터 조건자를 추가 하 여 인덱스를 사용 하지 않는 함수 (예: 포함)를 사용 하는 경우 쿼리 속도를 높이고 전체 컨테이너 검색을 피할 수 있습니다. 필터 절의 순서는 중요 하지 않습니다. 쿼리 엔진은 조건자를 선택 하 고 그에 따라 쿼리를 실행 합니다.


## <a name="querying-with-indexes"></a>인덱스를 사용한 쿼리

데이터를 인덱싱할 때 추출 된 경로를 통해 쿼리를 처리할 때 인덱스를 쉽게 조회할 수 있습니다. 쿼리의 `WHERE` 절을 인덱싱된 경로 목록과 일치 시켜 쿼리 조건자와 일치 하는 항목을 매우 빠르게 식별할 수 있습니다.

예를 들어 다음 쿼리를 참조 하십시오. `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. 쿼리 조건자 (항목에 대한 필터링, 국가에 "프랑스"가 있는 위치)는 아래 빨간색으로 강조 표시 된 경로와 일치 합니다.

![트리 내의 특정 경로 일치](./media/index-overview/matching-path.png)

> [!NOTE]
> 단일 속성을 기준으로 정렬 하는 `ORDER BY` 절은 *항상* 범위 인덱스가 필요 하며 참조 하는 경로에는이 인덱스가 없는 경우 실패 합니다. 마찬가지로, 여러 속성으로 정렬 하는 `ORDER BY` 쿼리는 *항상* 복합 인덱스가 필요 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 정책](index-policy.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
