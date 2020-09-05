---
title: OData search.in 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 search.in 함수를 사용 하는 방법에 대 한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922823"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>`search.in`Azure Cognitive Search의 OData 함수

[OData 필터 식](query-odata-filter-orderby-syntax.md) 의 일반적인 시나리오는 각 문서의 단일 필드가 가능한 값 중 하 나와 같은지 여부를 확인 하는 것입니다. 예를 들어 일부 응용 프로그램은 쿼리를 실행 하는 사용자를 나타내는 보안 주체 id 목록에 대해 하나 이상의 보안 주체 Id를 포함 하는 필드를 확인 하 여 [보안 트리밍을](search-security-trimming-for-azure-search.md) 구현 하는 방법입니다. 다음과 같이 쿼리를 작성 하는 한 가지 방법은 및 연산자를 사용 하는 것입니다 [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) .

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

그러나 함수를 사용 하 여이를 보다 간단 하 게 작성할 수 있습니다 `search.in` .

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> 를 사용 하는 것이 더 짧고 읽기 쉬울 뿐만 아니라를 사용 하면 `search.in` [성능 이점도](#bkmk_performance) 제공 하 고 필터에 포함할 값이 수백 또는 수천 개인 경우 [필터의 특정 크기 제한을](search-query-odata-filter.md#bkmk_limits) 피할 수 있습니다. 따라서 `search.in` 같음 식의 더 복잡 한 분리 대신를 사용 하는 것이 좋습니다.

> [!NOTE]
> OData 표준 버전 4.01에는 최근에 Azure Cognitive Search 함수와 비슷한 동작이 있는 [ `in` 연산자](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)가 도입 되었습니다 `search.in` . 그러나 Azure Cognitive Search는이 연산자를 지원 하지 않으므로 대신 함수를 사용 해야 합니다 `search.in` .

## <a name="syntax"></a>구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))은 함수의 문법을 정의 합니다 `search.in` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

`search.in`함수는 지정 된 문자열 필드 또는 범위 변수가 지정 된 값 목록 중 하 나와 같은지 여부를 테스트 합니다. 변수와 목록의 각 값이 같은지 여부는 연산자의 경우와 동일 하 게 대/소문자를 구분 하는 방식으로 결정 됩니다 `eq` . 따라서 `search.in(myfield, 'a, b, c')`과 같은 식은 `search.in`이 훨씬 더 나은 성능을 제공한다는 점을 제외하고 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`와 같습니다.

함수에는 두 가지 오버 로드가 있습니다 `search.in` .

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

매개 변수는 다음 표에 정의 되어 있습니다.

| 매개 변수 이름 | Type | Description |
| --- | --- | --- |
| `variable` | `Edm.String` | 또는 식 내에서가 사용 되는 경우 문자열 필드 참조 (또는 문자열 컬렉션 필드의 범위 변수 `search.in` `any` `all` )입니다. |
| `valueList` | `Edm.String` | 매개 변수와 비교할 구분 된 값 목록을 포함 하는 문자열 `variable` 입니다. `delimiters`매개 변수가 지정 되지 않은 경우 기본 구분 기호는 공백과 쉼표입니다. |
| `delimiters` | `Edm.String` | 매개 변수를 구문 분석할 때 각 문자가 구분 기호로 처리 되는 문자열입니다 `valueList` . 이 매개 변수의 기본값은 `' ,'` 공백 및/또는 쉼표를 포함 하는 모든 값을 구분 하는 것을 의미 합니다. 공백 및 쉼표 이외의 구분 기호를 사용 해야 하는 경우 값이 해당 문자를 포함 하기 때문에 `'|'` 이 매개 변수에서와 같은 대체 구분 기호를 지정할 수 있습니다. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>성능 `search.in`

`search.in`을 사용하는 경우 두 번째 매개 변수에 수십만 개 값 목록이 포함되어 있을 때 1초 미만의 응답 시간을 기대할 수 있습니다. 최대 요청 크기로 제한 되더라도에 전달할 수 있는 항목 수에 대 한 명시적 제한은 없습니다 `search.in` . 그러나 값 수가 증가함에 따라 대기 시간도 증가합니다.

## <a name="examples"></a>예

이름이 ' 해상 보기 motel ' 또는 ' 예산 호텔 '과 같은 모든 호텔을 찾습니다. 구에는 기본 구분 기호인 공백이 포함 됩니다. 세 번째 문자열 매개 변수로 작은따옴표에 대체 구분 기호를 지정할 수 있습니다.  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

' | '로 구분 된 ' 해상 보기 motel ' 또는 ' 예산 호텔 '과 동일한 이름의 모든 호텔을 찾습니다.

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

' Wifi ' 또는 ' 드라이기 ' 태그가 있는 대화방을 사용 하 여 모든 호텔 찾기:

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

태그에서 ' 열 수건 랙 ' 또는 ' hairdryer 포함 ' 등의 구에 대해 일치 하는 항목을 찾습니다.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

' Motel ' 또는 ' cabin' ' 태그 없이 모든 호텔 찾기:

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)