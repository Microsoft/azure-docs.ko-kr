---
title: OData 언어 개요
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에 대 한 필터, 선택 및 정렬에 대 한 OData 언어 개요입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923401"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>`$filter` `$orderby` Azure Cognitive Search의, 및에 대 한 OData 언어 개요 `$select`

Azure Cognitive Search는 **$filter**, **$orderby**및 **$select** 식의 OData 식 구문 하위 집합을 지원 합니다. 필터 식은 쿼리 구문 분석 때 평가되고, 검색을 특정 필드로 제한하거나 인덱스 검색 때 사용되는 일치 조건을 추가합니다. Order by 식은 반환 되는 문서를 정렬 하기 위해 결과 집합에 대 한 사후 처리 단계로 적용 됩니다. 식 선택은 결과 집합에 포함 되는 문서 필드를 결정 합니다. 이러한 식의 구문은 **검색** 매개 변수에 사용 되는 [단순](query-simple-syntax.md) 또는 [전체](query-lucene-syntax.md) 쿼리 구문과 구분 됩니다. 하지만 필드를 참조 하는 구문에는 약간의 차이가 있습니다.

이 문서에서는 필터, order by 및 select 식에 사용 되는 OData 식 언어의 개요를 제공 합니다. 언어가 가장 기본적인 요소부터 시작 하 여 "상향식"으로 표시 됩니다. 각 매개 변수에 대 한 최상위 구문은 별도의 문서에 설명 되어 있습니다.

- [$filter 구문](search-query-odata-filter.md)
- [$orderby 구문](search-query-odata-orderby.md)
- [$select 구문](search-query-odata-select.md)

OData 식의 범위는 단순에서 매우 복잡 하지만 모두 공통 요소를 공유 합니다. Azure Cognitive Search에서 OData 식의 가장 기본적인 부분은 다음과 같습니다.

- 인덱스의 특정 필드를 참조 하는 **필드 경로**입니다.
- **상수**는 특정 데이터 형식의 리터럴 값입니다.

> [!NOTE]
> Azure Cognitive Search의 용어는 [OData 표준과](https://www.odata.org/documentation/) 다릅니다. Azure Cognitive Search에서 **필드** 를 호출 하는 것은 OData에서 **속성** 이라고 하며, **필드 경로** 와 **속성 경로**에 대해서도 마찬가지입니다. Azure Cognitive Search의 **문서** 를 포함 하는 **인덱스** 를 일반적으로 OData에서 **엔터티**를 포함 하는 **엔터티 집합** 이라고 합니다. Azure Cognitive Search 용어는이 참조 전체에서 사용 됩니다.

## <a name="field-paths"></a>필드 경로

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))은 필드 경로의 문법을 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

필드 경로는 슬래시로 구분 된 하나 이상의 **식별자** 로 구성 됩니다. 각 식별자는 ASCII 문자 또는 밑줄로 시작 해야 하 고 ASCII 문자, 숫자 또는 밑줄만 포함 하는 문자 시퀀스입니다. 문자는 대문자나 소문자 일 수 있습니다.

식별자는 필드 이름 또는 필터의 [컬렉션 식](search-query-odata-collection-operators.md) (또는)의 컨텍스트에서 **범위 변수** 를 참조할 수 있습니다 `any` `all` . 범위 변수는 컬렉션의 현재 요소를 나타내는 루프 변수와 같습니다. 복합 컬렉션의 경우 해당 변수는 개체를 나타내므로 필드 경로를 사용 하 여 변수의 하위 필드를 참조할 수 있습니다. 이는 대부분의 프로그래밍 언어에서 점 표기법과 유사 합니다.

다음 표에서는 필드 경로의 예를 보여 줍니다.

| 필드 경로 | Description |
| --- | --- |
| `HotelName` | 인덱스의 최상위 필드를 참조 합니다. |
| `Address/City` | `City`인덱스에 있는 복합 필드의 하위 필드를 참조 합니다. `Address` `Edm.ComplexType` 이 예제에서는 형식입니다. |
| `Rooms/Type` | `Type`인덱스에 있는 복합 컬렉션 필드의 하위 필드를 참조 합니다. `Rooms` `Collection(Edm.ComplexType)` 이 예제에서는 형식입니다. |
| `Stores/Address/Country` | `Country`인덱스에 있는 복합 컬렉션 필드의 하위 필드에 대 한 하위 필드를 참조 합니다 `Address` . `Stores` 는 형식이 `Collection(Edm.ComplexType)` 며 `Address` `Edm.ComplexType` 이 예제에서는 형식입니다. |
| `room/Type` | 는 `Type` `room` 필터 식에서와 같이 범위 변수의 하위 필드를 참조 합니다. `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 는 `Country` `Address` `store` 필터 식에서와 같이 범위 변수의 하위 필드에 대 한 하위 필드를 참조 합니다. `Stores/any(store: store/Address/Country eq 'Canada')` |

필드 경로의 의미는 컨텍스트에 따라 달라 집니다. 필터에서 필드 경로는 현재 문서에 있는 필드의 *단일 인스턴스* 값을 나타냅니다. [전체 Lucene 구문에서](query-lucene-syntax.md#bkmk_fields) **$orderby**, **$select**또는 필드 지정 search와 같은 다른 컨텍스트에서 필드 경로는 필드 자체를 참조 합니다. 이러한 차이는 필터에서 필드 경로를 사용 하는 방법에 대 한 몇 가지 영향을 줍니다.

필드 경로를 고려 `Address/City` 합니다. 필터에서이는 "샌프란시스코"와 같이 현재 문서에 대 한 단일 도시를 나타냅니다. 이와 대조적으로는 `Rooms/Type` `Type` 많은 대화방의 하위 필드 (예: "standard", 두 번째 방에 대 한 "deluxe")를 참조 합니다. 는 `Rooms/Type` 하위 필드의 *단일 인스턴스* 를 참조 하지 않으므로 `Type` 필터에 직접 사용할 수 없습니다. 대신, 방 유형을 필터링 하려면 다음과 같이 범위 변수와 함께 [람다 식을](search-query-odata-collection-operators.md) 사용 합니다.

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

이 예에서는 범위 변수가 `room` 필드 경로에 표시 됩니다 `room/Type` . 이러한 방식으로는 `room/Type` 현재 문서에서 현재 대화방의 유형을 나타냅니다. 이는 하위 필드의 단일 인스턴스이며 `Type` 필터에 직접 사용할 수 있습니다.

### <a name="using-field-paths"></a>필드 경로 사용

필드 경로는 [Azure COGNITIVE SEARCH REST api](/rest/api/searchservice/)의 많은 매개 변수에 사용 됩니다. 다음 표에서는 사용할 수 있는 모든 위치 및 사용에 대 한 제한 사항을 보여 줍니다.

| API | 매개 변수 이름 | 제한 |
| --- | --- | --- |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `suggesters/sourceFields` | 없음 |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | **검색 가능한** 필드만 참조할 수 있습니다. |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | **필터링** 가능한 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `search``queryType`가 인 경우`full` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `facet` | **패싯 가능** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `highlight` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `searchFields` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [제안](/rest/api/searchservice/suggestions) 및 [자동 완성](/rest/api/searchservice/autocomplete) | `searchFields` | [확인 기](index-add-suggesters.md) 의 일부인 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents), [제안](/rest/api/searchservice/suggestions)및 [자동 완성](/rest/api/searchservice/autocomplete) | `$filter` | **필터링** 가능한 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) 및 [제안](/rest/api/searchservice/suggestions) | `$orderby` | **정렬** 가능한 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents), [제안](/rest/api/searchservice/suggestions)및 [조회](/rest/api/searchservice/lookup-document) | `$select` | **검색할** 수 있는 필드만 참조할 수 있습니다. |

## <a name="constants"></a>상수

OData의 상수는 지정 된 EDM ( [엔터티 데이터 모델](/dotnet/framework/data/adonet/entity-data-model) ) 형식의 리터럴 값입니다. Azure Cognitive Search에서 지원 되는 형식 목록은 [지원 되는 데이터 형식](/rest/api/searchservice/supported-data-types) 을 참조 하세요. 컬렉션 형식의 상수는 지원 되지 않습니다.

다음 표에서는 Azure Cognitive Search에서 지 원하는 각 데이터 형식에 대 한 상수의 예를 보여 줍니다.

| 데이터 형식 | 예제 상수 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>문자열 상수에서 특수 문자 이스케이프

OData의 문자열 상수는 작은따옴표로 구분 됩니다. 작은따옴표를 포함할 수 있는 문자열 상수를 사용 하 여 쿼리를 생성 해야 하는 경우 포함 된 따옴표를 두 배로 이스케이프할 수 있습니다.

예를 들어 "Alice의 car"와 같이 형식이 지정 되지 않은 아포스트로피를 사용 하는 구는 OData에서 문자열 상수로 표시 됩니다 `'Alice''s car'` .

> [!IMPORTANT]
> 프로그래밍 방식으로 필터를 생성 하는 경우 사용자 입력에서 제공 되는 문자열 상수를 이스케이프 해야 합니다. 이는 특히 필터를 사용 하 여 [보안 트리밍을](search-security-trimming-for-azure-search.md)구현할 때 [삽입 공격의](https://wikipedia.org/wiki/SQL_injection)가능성을 완화 하기 위한 것입니다.

### <a name="constants-syntax"></a>상수 구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 위의 표에 표시 된 대부분의 상수에 대 한 문법을 정의 합니다. 지역 공간 형식에 대 한 문법은 [Azure Cognitive Search의 OData 지역 공간 함수](search-query-odata-geo-spatial-functions.md)에서 찾을 수 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

## <a name="building-expressions-from-field-paths-and-constants"></a>필드 경로 및 상수에서 식 작성

필드 경로와 상수는 OData 식의 가장 기본적인 부분 이지만 이미 전체 식입니다. 실제로 Azure Cognitive Search의 **$select** 매개 변수는 아무것도 아니지만 쉼표로 구분 된 필드 경로 목록이 며 **$orderby** 는 **$select**보다 훨씬 복잡 하지 않습니다. 인덱스에 형식의 필드가 있는 경우에 `Edm.Boolean` 도 해당 필드의 경로는 아니지만 필터를 작성할 수 있습니다. 상수 `true` 와는 `false` 마찬가지로 유효한 필터입니다.

그러나 대부분의 경우에는 둘 이상의 필드와 상수를 참조 하는 더 복잡 한 식이 필요 합니다. 이러한 식은 매개 변수에 따라 다양 한 방식으로 빌드됩니다.

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))은 **$filter**, **$orderby**및 **$select** 매개 변수에 대 한 문법을 정의 합니다. 이러한 작업은 필드 경로와 상수를 참조 하는 간단한 식에서 빌드됩니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

**$Orderby** 및 **$select** 매개 변수는 간단한 식의 쉼표로 구분 된 목록입니다. **$Filter** 매개 변수는 보다 간단한 하위 식으로 구성 된 부울 식입니다. 이러한 하위 식은,, 등의 논리 연산자 [ `and` `or` `not` ](search-query-odata-logical-operators.md)를 사용 하 여 결합 되 고,, 등의 비교 연산자와 [ `any` 및 `all` ](search-query-odata-collection-operators.md)등 [ `eq` `lt` `gt` 의](search-query-odata-comparison-operators.md)컬렉션 연산자를 사용 하 여 결합 됩니다.

**$Filter**, **$orderby**및 **$select** 매개 변수는 다음 문서에 자세히 설명 되어 있습니다.

- [Azure Cognitive Search의 OData $filter 구문](search-query-odata-filter.md)
- [Azure Cognitive Search의 OData $orderby 구문](search-query-odata-orderby.md)
- [Azure Cognitive Search의 OData $select 구문](search-query-odata-select.md)

## <a name="see-also"></a>참조  

- [Azure Cognitive Search의 패싯 탐색](search-faceted-navigation.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Cognitive Search의 단순 쿼리 구문](query-simple-syntax.md)