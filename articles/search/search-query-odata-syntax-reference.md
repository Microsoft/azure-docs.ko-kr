---
title: OData 식 구문 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 OData 식에 대 한 공식적인 문법 및 구문 사양입니다.
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
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928856"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 OData 식 구문 참조

Azure Cognitive Search는 API 전체에서 [OData 식을](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) 매개 변수로 사용 합니다. 가장 일반적으로는 OData 식이 `$orderby` 및 `$filter` 매개 변수에 사용 됩니다. 이러한 식은 여러 절, 함수 및 연산자를 포함 하는 복잡할 수 있습니다. 그러나 속성 경로와 같은 간단한 OData 식은 Azure Cognitive Search REST API의 여러 부분에서 사용 됩니다. 예를 들어 경로 식은 [확인 기](index-add-suggesters.md)의 하위 필드, [점수 매기기 함수](index-add-scoring-profiles.md), `$select` 매개 변수 또는 [Lucene 쿼리에서 필드 지정 검색](query-lucene-syntax.md)을 나열 하는 경우와 같이 API의 모든 위치에서 복합 필드의 하위 필드를 참조 하는 데 사용 됩니다.

이 문서에서는 공식적인 문법을 사용 하는 이러한 모든 형식의 OData 식에 대해 설명 합니다. 문법을 시각적으로 탐색 하는 데 도움이 되는 [대화형 다이어그램](#syntax-diagram) 도 있습니다.

## <a name="formal-grammar"></a>공식 문법

EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 문법을 사용 하 여 Azure Cognitive Search에서 지 원하는 OData 언어의 하위 집합을 설명할 수 있습니다. 규칙은 "하향식"으로 나열 되며 가장 복잡 한 식부터 시작 하 여 더 많은 기본 식으로 구분 됩니다. 위쪽에는 Azure Cognitive Search REST API의 특정 매개 변수에 해당 하는 문법 규칙이 있습니다.

- [`$filter`](search-query-odata-filter.md)규칙으로 정의 된 `filter_expression` 입니다.
- [`$orderby`](search-query-odata-orderby.md)규칙으로 정의 된 `order_by_expression` 입니다.
- [`$select`](search-query-odata-select.md)규칙으로 정의 된 `select_expression` 입니다.
- 규칙에 의해 정의 되는 필드 경로 `field_path` 입니다. 필드 경로는 API 전체에서 사용 됩니다. 인덱스의 최상위 필드 또는 하나 이상의 [복합 field](search-howto-complex-data-types.md) 상위 항목이 있는 하위 필드를 참조할 수 있습니다.

EBNF는 검색 가능한 [구문 다이어그램](https://en.wikipedia.org/wiki/Syntax_diagram) 으로, 문법을 대화형으로 탐색 하 고 해당 규칙 간의 관계를 탐색할 수 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>구문 다이어그램

Azure Cognitive Search에서 지 원하는 OData 언어 문법을 시각적으로 살펴보려면 대화형 구문 다이어그램을 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>참조  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Cognitive Search의 단순 쿼리 구문](query-simple-syntax.md)