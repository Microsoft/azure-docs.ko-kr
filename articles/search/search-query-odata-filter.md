---
title: OData 필터 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만드는 데 사용 되는 전체 구문 및 OData 언어 참조입니다.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934857"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData $filter 구문

Azure Cognitive Search는 [OData 필터 식을](query-odata-filter-orderby-syntax.md) 사용 하 여 전체 텍스트 검색 용어 외에 추가 조건을 검색 쿼리에 적용 합니다. 이 문서에서는 필터의 구문에 대해 자세히 설명 합니다. 필터 및 필터를 사용 하 여 특정 쿼리 시나리오를 실현 하는 방법에 대 한 일반적인 내용은 [Azure Cognitive Search의 필터](search-filters.md)를 참조 하세요.

## <a name="syntax"></a>구문

OData 언어의 필터는 부울 식입니다 .이 식은 다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에 표시 된 것과 같이 식의 여러 유형 중 하나일 수 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

부울 식의 형식에는 다음이 포함 됩니다.

- 또는를 사용 하는 컬렉션 필터 식 `any` `all` 입니다. 이러한 필터 조건은 컬렉션 필드에 적용 됩니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md)를 참조 하세요.
- , 및 연산자를 사용 하 여 다른 부울 식을 결합 하는 논리 식입니다 `and` `or` `not` . 자세한 내용은 [Azure Cognitive Search의 OData 논리 연산자](search-query-odata-logical-operators.md)를 참조 하세요.
- 연산자,,,, 및를 사용 하 여 필드 또는 범위 변수를 상수 값과 비교 하는 비교 식 `eq` `ne` `gt` `lt` `ge` `le` . 자세한 내용은 [Azure Cognitive Search의 OData 비교 연산자](search-query-odata-comparison-operators.md)를 참조 하세요. 비교 식은 함수를 사용 하 여 지리적 공간 좌표 사이의 거리를 비교 하는 데도 사용 됩니다 `geo.distance` . 자세한 내용은 [Azure Cognitive Search의 OData 지역 공간 함수](search-query-odata-geo-spatial-functions.md)를 참조 하세요.
- 부울 리터럴 `true` 및 `false` 입니다. 이러한 상수는 프로그래밍 방식으로 필터를 생성 하는 경우에 유용할 수 있지만 그렇지 않은 경우에는 실제로 사용 되지 않습니다.
- 다음을 포함 하 여 부울 함수를 호출 합니다.
  - `geo.intersects`지정 된 점이 지정 된 다각형 내에 있는지 여부를 테스트 하는입니다. 자세한 내용은 [Azure Cognitive Search의 OData 지역 공간 함수](search-query-odata-geo-spatial-functions.md)를 참조 하세요.
  - `search.in`-필드 또는 범위 변수와 값 목록의 각 값을 비교 합니다. 자세한 내용은 [ `search.in` Azure Cognitive Search의 OData 함수](search-query-odata-search-in-function.md)를 참조 하세요.
  - `search.ismatch``search.ismatchscoring`필터 컨텍스트에서 전체 텍스트 검색 작업을 실행 하는 및 자세한 내용은 [Azure Cognitive Search의 OData 전체 텍스트 검색 기능](search-query-odata-full-text-search-functions.md)을 참조 하세요.
- 형식의 필드 경로 또는 범위 변수 `Edm.Boolean` 예를 들어 인덱스에 라는 부울 필드가 `IsEnabled` 있고이 필드가 인 모든 문서를 반환 하려면 `true` 필터 식이 이름일 수 있습니다 `IsEnabled` .
- 괄호 안의 부울 식입니다. 괄호를 사용 하면 필터의 작업 순서를 명시적으로 결정 하는 데 도움이 됩니다. OData 연산자의 기본 우선 순위에 대 한 자세한 내용은 다음 섹션을 참조 하십시오.

### <a name="operator-precedence-in-filters"></a>필터의 연산자 우선 순위

하위 식 주위에 괄호를 사용 하지 않고 필터 식을 작성 하는 경우 Azure Cognitive Search는 연산자 우선 순위 규칙 집합에 따라 계산 합니다. 이러한 규칙은 하위 식을 결합 하는 데 사용 되는 연산자를 기반으로 합니다. 다음 표에서는 연산자 그룹을 최고 우선 순위부터 최하위 순서로 나열 합니다.

| 그룹화 | 연산자 |
| --- | --- |
| 논리 연산자 | `not` |
| 비교 연산자 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 논리 연산자 | `and` |
| 논리 연산자 | `or` |

위의 표에 나와 있는 연산자는 다른 연산자 보다 해당 피연산자에 "더 밀접 하 게 바인딩" 할 수 있습니다. 예를 들어 `and` 는 보다 우선 순위가 높고 비교 연산자는 보다 우선 순위가 `or` 높기 때문에 다음 두 식은 동일 합니다.

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not`연산자는 비교 연산자 보다 높은 우선 순위를 갖습니다. 이러한 이유 때문에 다음과 같은 필터를 작성 하려고 합니다.

```odata-filter-expr
    not Rating gt 5
```

다음 오류 메시지가 표시 됩니다.

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

이 오류는 연산자가 `Rating` `Edm.Int32` 전체 비교 식이 아니라 형식의 필드에만 연결 되어 있기 때문에 발생 합니다. 이 문제를 해결 하려면 피연산자를 `not` 괄호 안에 넣습니다.

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>필터 크기 제한

Azure Cognitive Search에 보낼 수 있는 필터 식의 크기 및 복잡성에는 제한이 있습니다. 이러한 제한은 대략적으로 필터 식의 절 수와 관련이 있습니다. 예를 들면, 수백 개의 절이 있는 경우 제한을 초과할 위험이 있습니다. 제한 없는 크기의 필터를 생성 하지 않는 방식으로 응용 프로그램을 디자인 하는 것이 좋습니다.

> [!TIP]
> 함수 호출이 단일 절로 계산 되기 때문에 같음 비교의 긴 분해 대신 [ `search.in` 함수를](search-query-odata-search-in-function.md) 사용 하면 필터 절 제한을 피할 수 있습니다.

## <a name="examples"></a>예

4 이상 등급의 기본 요금이 $200 미만인 모든 호텔을 찾습니다.

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

2010 이후 리모델링한 된 "해상 보기 Motel" 이외의 모든 호텔을 찾습니다.

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

2010 이상에서 리모델링한 된 호텔을 모두 찾습니다. Datetime 리터럴에는 태평양 표준시에 대 한 표준 시간대 정보가 포함 됩니다.  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

파킹 포함 및 모든 대화방이 흡연 않은 모든 호텔을 찾습니다.

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- 또는 -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

럭셔리 호텔이거나 주차장이 포함되어 있고 등급이 5인 모든 호텔을 찾습니다.  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

하나 이상의 대화방에서 "wifi" 태그가 있는 모든 호텔을 찾습니다 (각 방에는 필드에 저장 된 태그가 있음 `Collection(Edm.String)` ).  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

모든 방에 모든 호텔 찾기:  

```odata-filter-expr
    $filter=Rooms/any()
```

대화방이 없는 모든 호텔 찾기:

```odata-filter-expr
    $filter=not Rooms/any()
```

지정 된 참조 지점의 10 킬로미터 이내에 있는 모든 호텔 찾기 (여기서 `Location` 는 유형의 필드 `Edm.GeographyPoint` ):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

지정 된 뷰포트 내에서 polygon로 설명 된 모든 호텔을 찾습니다 `Location` . 여기서은 GeographyPoint 형식의 필드입니다. 다각형이 닫혀 있어야 합니다. 즉, 첫 번째 및 마지막 점 집합이 동일 해야 합니다. 또한 [지점은 반시계 방향으로 나열](/rest/api/searchservice/supported-data-types#Anchor_1)되어야 합니다.

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

"설명" 필드가 null 인 모든 호텔을 찾습니다. 필드는 설정 되지 않은 경우 null이 고 명시적으로 null로 설정 된 경우에는 null입니다.  

```odata-filter-expr
    $filter=Description eq null
```

이름이 ' 해상 보기 motel ' 또는 ' 예산 호텔 '과 같은 모든 호텔을 찾습니다. 이러한 구에는 공백이 포함 되 고 공백은 기본 구분 기호입니다. 세 번째 문자열 매개 변수로 작은따옴표에 대체 구분 기호를 지정할 수 있습니다.  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

' | '로 구분 된 ' 해상 보기 motel ' 또는 ' 예산 호텔 '과 동일한 이름의 모든 호텔을 찾습니다.  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

모든 방에 ' wifi ' 또는 ' 드라이기 ' 태그가 있는 모든 호텔 찾기:

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

태그에서 ' 열 수건 랙 ' 또는 ' hairdryer 포함 ' 등의 구에 대해 일치 하는 항목을 찾습니다.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](/rest/api/searchservice/search-documents)과 동일합니다.

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 을 `search.ismatchscoring` 사용 하 여 필터 작업과 함께 전체 텍스트 검색을 결합 하므로 함수 없이이 요청을 표현할 수 없습니다 `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

단어 "luxury"가 없는 문서를 찾습니다.

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Description`에 대해서만 실행됩니다. 분리의 두 번째 절과 일치 하는 문서만 반환 됩니다. 즉, 호텔은 `Rating` 5와 동일 합니다. 이러한 문서는 0과 같은 점수와 함께 반환 되어 식의 점수가 매겨진 부분과 일치 하지 않는 것을 명확 하 게 합니다.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

"호텔" 및 "공항" 이라는 용어는 설명에는 5 개 이하의 단어가 있고 모든 대화방은 흡연이 아닌 호텔을 찾습니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)