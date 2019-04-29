---
title: Azure Cosmos DB의 인덱싱
description: Azure Cosmos DB에서 인덱싱의 작동 방식을 파악하고
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 3bb8913725acf04f71aba8b4c4350235f2c44dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61051888"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB-개요의 인덱싱

Azure Cosmos DB는 스키마 나 인덱스 관리를 사용 하 여 처리할 필요 없이 응용 프로그램에서 반복할 수 있도록 하는 스키마 제약 없는 데이터베이스입니다. 기본적으로 Azure Cosmos DB 자동 인덱싱의 모든 항목에 대 한 모든 속성에 [컨테이너](databases-containers-items.md#azure-cosmos-containers) 모든 스키마를 정의 하거나 보조 인덱스를 구성 하지 않고도 합니다.

이 문서의 목표는 쿼리 성능 향상을 위해 인덱스를 사용 하는 방법 및 Azure Cosmos DB에서 데이터를 인덱스 하는 방법을 설명 합니다. 사용자 지정 하는 방법을 살펴보기 전에이 섹션을 통해 이동 하는 것이 좋습니다 [인덱싱 정책](index-policy.md)합니다.

## <a name="from-items-to-trees"></a>트리 항목에서

항목은 컨테이너에 저장 될 때마다 해당 콘텐츠는 JSON 문서를으로 프로젝션 하 고 트리 표현으로 변환. 따라서 해당 항목의 모든 속성을 가져옵니다 트리에서 노드로 표시 한다는 것입니다. 의사 루트 노드를 부모 항목의 모든 첫 번째 수준 속성에 만들어집니다. 리프 노드는 항목으로 전달 하는 실제 스칼라 값을 포함 합니다.

예를 들어이 항목을 고려 합니다.

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

다음 트리를 나타내는 수 됩니다.

![트리로 표시 되는 이전 항목](./media/index-overview/item-as-tree.png)

트리에서 배열을 인코딩하는 방법을 참고: 배열에서 모든 항목 배열 내에서 해당 항목의 인덱스를 사용 하 여 레이블이 지정 된 중간 노드를 가져옵니다 (0, 1 등.).

## <a name="from-trees-to-property-paths"></a>속성 경로 트리에서

Azure Cosmos DB 항목 트리로 변환 하는 이유는 이유는 해당 트리 내에서 해당 경로에서 참조 하는 속성 수 있기 때문입니다. 속성에 대 한 경로 가져오려고 수 그 속성에 루트 노드에서 트리를 탐색 하 고 트래버스된 각 노드의 레이블을 연결 합니다.

위에서 설명한 예제에서는 항목의 각 속성에 대 한 경로 다음과 같습니다.

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

항목 기록 되는 경우 Azure Cosmos DB 각 속성의 경로 및 해당 값 효율적으로 인덱싱합니다.

## <a name="index-kinds"></a>인덱스 종류

Azure Cosmos DB는 현재 두 가지 유형의 인덱스를 지원합니다.

합니다 **범위** 인덱스 종류에 사용 됩니다.

- 같음 쿼리: `SELECT * FROM container c WHERE c.property = 'value'`
- 범위 쿼리: `SELECT * FROM container c WHERE c.property > 'value'` (적합 `>`, `<`, `>=`합니다 `<=`, `!=`)
- `ORDER BY` 쿼리: `SELECT * FROM container c ORDER BY c.property`
- `JOIN` 쿼리: `SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'`

스칼라 값 (문자열 또는 숫자)에 대해 범위 인덱스를 사용할 수 있습니다.

합니다 **공간** 인덱스 종류에 사용 됩니다.

- 지리 공간적 거리 쿼리: `SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40`
- 쿼리 내에서 지리 공간: `SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })`

형식이 올바르게 지정에서 공간 인덱스를 사용할 수 있습니다 [GeoJSON](geospatial.md) 개체입니다. Points, LineStrings 및 Polygons는 현재 지원 됩니다.

## <a name="querying-with-indexes"></a>인덱스를 사용한 쿼리

경로 데이터를 인덱싱할 때 추출한 쉽게 쿼리를 처리 하는 경우 인덱스를 조회 합니다. 일치 하는 `WHERE` 쿼리 조건자를 매우 신속 하 게 일치 하는 항목을 식별할 수는 인덱싱된 경로의 목록 사용 하 여 쿼리 절.

예를 들어 다음 쿼리는 것이 좋습니다: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`합니다. (항목, 모든 위치에 "프랑스"으로 해당 국가 필터링) 쿼리 조건자에는 아래 빨간색으로 강조 표시 된 경로 일치는:

![트리 내에서 특정 경로 일치 하는](./media/index-overview/matching-path.png)

> [!NOTE]
> `ORDER BY` 절 *항상* 범위 필요한 인덱스 및 참조 경로가 없는 경우 실패 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 정책](index-policy.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
