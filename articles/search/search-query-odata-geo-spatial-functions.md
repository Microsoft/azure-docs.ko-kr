---
title: OData 지역 공간 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 지역 공간 함수 사용에 대 한 구문 및 참조 설명서입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934840"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure Cognitive Search의 OData 지역 공간 함수- `geo.distance` 및 `geo.intersects`

Azure Cognitive Search는 및 함수를 통해 [OData 필터 식](query-odata-filter-orderby-syntax.md) 에서 지역 공간 쿼리를 지원 `geo.distance` `geo.intersects` 합니다. `geo.distance`함수는 두 요소 사이의 거리를 킬로미터 단위로 반환 합니다. 하나는 필드 또는 범위 변수가 되 고 하나는 필터의 일부로 전달 되는 상수입니다. `geo.intersects`함수는 `true` 지정 된 점이 지정 된 다각형 내에 있는 경우를 반환 합니다. 여기서 point는 필드 또는 범위 변수이 고 다각형은 필터의 일부로 전달 되는 상수로 지정 됩니다.

`geo.distance`함수를 [ **$orderby** 매개 변수에](search-query-odata-orderby.md) 사용 하 여 지정 된 지점 으로부터의 거리를 기준으로 검색 결과를 정렬할 수도 있습니다. **$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. $Orderby에서를 사용 하는 경우 `geo.distance` 이 필드가 적용 되는 필드는 형식 이어야 **$orderby** `Edm.GeographyPoint` 하며 **정렬할**수 있어야 합니다.

> [!NOTE]
> `geo.distance` **$Orderby** 매개 변수에서를 사용 하는 경우 함수에 전달 하는 필드는 단일 지리적 지점만 포함 해야 합니다. 즉, 형식이 아닌 형식 이어야 합니다 `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` . Azure Cognitive Search에서는 컬렉션 필드를 기준으로 정렬할 수 없습니다.

## <a name="syntax"></a>구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 `geo.distance` 및 함수 및 해당 `geo.intersects` 함수가 작동 하는 지역 공간 값의 문법을 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

### <a name="geodistance"></a>지역 거리

`geo.distance`함수는 형식의 두 매개 변수 `Edm.GeographyPoint` 를 사용 하 고 `Edm.Double` 킬로미터 사이의 거리로 값을 반환 합니다. 이는 일반적으로 미터 단위로 거리를 반환 하는 OData 지역 공간 작업을 지 원하는 다른 서비스와 다릅니다.

에 대 한 매개 변수 중 하나는 `geo.distance` geography point 상수 여야 하 고 다른 하나는 필드 경로 (또는 형식의 필드를 반복 하는 필터의 경우 범위 변수) 여야 합니다 `Collection(Edm.GeographyPoint)` . 이러한 매개 변수의 순서는 중요 하지 않습니다.

지리 점 상수는 형식입니다 `geography'POINT(<longitude> <latitude>)'` . 여기서 경도 및 위도는 숫자 상수입니다.

> [!NOTE]
> 필터에서를 사용 하는 경우 `geo.distance` ,, 또는를 사용 하 여 함수에서 반환 된 거리와 상수를 비교 해야 합니다 `lt` `le` `gt` `ge` . 연산자 `eq` 및 `ne`는 거리를 비교할 때 사용할 수 없습니다. 예를 들어,의 올바른 사용법은 `geo.distance` `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` 입니다.

### <a name="geointersects"></a>지역 교차

`geo.intersects`함수는 형식의 변수와 상수를 사용 `Edm.GeographyPoint` `Edm.GeographyPolygon` 하 고 `Edm.Boolean`  --  `true` 점이 다각형의 범위 내에 있으면를 반환 하 고 그렇지 않으면를 반환 `false` 합니다.

다각형은 경계 링을 정의 하는 일련의 점으로 저장 되는 2 차원 표면입니다 (아래 [예제](#examples) 참조). 다각형은 닫혀야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. [다각형의 점은 시계 반대 방향 순서여야 합니다](/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>180th 자오선를 확장 하는 지역 공간 쿼리 및 다각형

많은 지역 공간 쿼리 라이브러리의 경우 180th 자오선 (dateline 근처)를 포함 하는 쿼리를 수행 하는 것은 off 이거나 자오선의 한쪽에 다각형을 두 개로 분할 하는 등의 해결 방법이 필요 합니다.

Azure Cognitive Search에서는 쿼리 셰이프가 사각형이 고 좌표가 경도 및 위도 (예:)를 따라 모눈 레이아웃에 맞춰지도록 180도 경도를 포함 하는 지역 공간 쿼리가 예상 대로 작동 합니다 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` . 그렇지 않은 경우 사각형이 아닌 도형이나 정렬되지 않은 도형의 경우 분할된 다각형 접근 방법을 고려해야 합니다.  

### <a name="geo-spatial-functions-and-null"></a>지리적 공간 함수 및 `null`

Azure Cognitive Search의 다른 모든 비 컬렉션 필드와 마찬가지로 형식의 필드에는 `Edm.GeographyPoint` 값이 포함 될 수 있습니다 `null` . Azure Cognitive Search가 인 필드를 평가 하는 경우 `geo.intersects` `null` 결과는 항상 `false` 입니다. `geo.distance`이 경우의 동작은 컨텍스트에 따라 달라 집니다.

- 필터에서 `geo.distance` `null` 필드의 결과는 `null` 입니다. 즉, `null` null이 아닌 값이로 계산 되기 때문에 문서가 일치 하지 않습니다 `false` .
- **$Orderby**를 사용 하 여 결과를 정렬 하는 경우 `geo.distance` `null` 필드의 가능한 최대 거리가 발생 합니다. 이러한 필드를 사용 하는 문서는 정렬 방향을 사용 하는 경우 `asc` (기본값), 방향이 일 때 다른 모든 항목 보다 더 높은 순서로 정렬 됩니다 `desc` .

## <a name="examples"></a>예

### <a name="filter-examples"></a>필터 예시

지정 된 참조 지점의 10 킬로미터 이내에 있는 모든 호텔 찾기 (여기서 location은 형식의 필드 `Edm.GeographyPoint` ):

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

지정 된 뷰포트 내에서 polygon로 설명 된 모든 호텔을 찾습니다. 여기서 location은 형식의 필드입니다 `Edm.GeographyPoint` . 다각형은 닫혀 있으며(첫 번째 점과 마지막 점 세트가 같음) [점은 시계 반대 방향 순서로 나열되어야 합니다](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Order-by 예제

다음을 기준으로 내림차순으로 정렬 `rating` 한 다음 지정 된 좌표의 거리 만큼 오름차순으로 정렬 합니다.

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

및를 기준으로 내림차순으로 호텔을 정렬 한 `search.score` `rating` 다음, 동일한 등급이 지정 된 두 호텔 사이에서 가장 근접 한 항목을 가장 먼저 나열 합니다.

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)