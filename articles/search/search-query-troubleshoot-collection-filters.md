---
title: OData 컬렉션 필터 문제 해결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 컬렉션 필터 오류를 해결 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932069"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData 컬렉션 필터 문제 해결

Azure Cognitive Search에서 컬렉션 필드를 [필터링](query-odata-filter-orderby-syntax.md) 하려면 [ `any` 및 `all` 연산자](search-query-odata-collection-operators.md) 를 **람다 식**과 함께 사용할 수 있습니다. 람다 식은 컬렉션의 각 요소에 적용 되는 하위 필터입니다.

필터 식의 모든 기능을 람다 식 내에서 사용할 수 있는 것은 아닙니다. 사용할 수 있는 기능은 필터링 하려는 컬렉션 필드의 데이터 형식에 따라 달라 집니다. 이 경우 해당 컨텍스트에서 지원 되지 않는 람다 식의 기능을 사용 하려고 하면 오류가 발생할 수 있습니다. 컬렉션 필드에 대해 복잡 한 필터를 작성 하는 동안 이러한 오류가 발생 하는 경우이 문서는 문제를 해결 하는 데 도움이 됩니다.

## <a name="common-collection-filter-errors"></a>공통 컬렉션 필터 오류

다음 표에서는 컬렉션 필터를 실행 하려고 할 때 발생할 수 있는 오류를 보여 줍니다. 이러한 오류는 람다 식 내에서 지원 되지 않는 필터 식의 기능을 사용 하는 경우에 발생 합니다. 각 오류는 오류를 방지 하기 위해 필터를 다시 작성할 수 있는 방법에 대 한 몇 가지 지침을 제공 합니다. 이 테이블에는 해당 오류를 방지 하는 방법에 대 한 자세한 정보를 제공 하는이 문서의 관련 단원에 대 한 링크도 포함 되어 있습니다.

| 오류 메시지 | 상황 | 자세한 내용은 다음을 참조하세요. |
| --- | --- | --- |
| ' Ismatch ' 함수에 범위 변수 '에 바인딩된 매개 변수가 없습니다. 람다 식 (' any ' 또는 ' all ') 내에서는 바인딩된 필드 참조만 지원 됩니다. ' Ismatch ' 함수가 람다 식 외부에 있도록 필터를 변경 하 고 다시 시도 하세요. | `search.ismatch` `search.ismatchscoring` 람다 식 내에서 또는 사용 | [복합 컬렉션 필터링 규칙](#bkmk_complex) |
| 람다 식이 잘못 되었습니다. 컬렉션 형식 (Edm. String)의 필드를 반복 하는 람다 식에서 반대쪽이 예상 되는 같음 또는 같지 않은지 테스트를 발견 했습니다. ' Any '의 경우 ' x eq y ' 또는 ' search.in (...) ' 형식의 식을 사용 하세요. ' A l l '의 경우 ' x ne y ', ' not (x eq y) ' 또는 ' not search.in (...) ' 형식의 식을 사용 하세요. | 형식의 필드에 대 한 필터링 `Collection(Edm.String)` | [문자열 컬렉션을 필터링 하는 규칙](#bkmk_strings) |
| 람다 식이 잘못 되었습니다. 지원 되지 않는 형태의 복합 부울 식을 찾았습니다. ' A l l '의 경우 ' ORs of ANDs ' 인 식을 사용 하세요 (분리 Normal Form이 라고도 함). 예: ' (a, b) or (c 및 d) ' 여기서 a, b, c, d는 비교 또는 같음 하위 식입니다. ' A l l '의 경우 ' ANDs of ORs ' 인 식을 사용 하세요 (결합 Normal Form이 라고도 함). 예: ' (a 또는 b) and (c 또는 d) ' 여기서 a, b, c, d는 비교 또는 같지 않음 하위 식입니다. 비교 식의 예: ' x gt 5 ', ' x le 2 '. 같음 식의 예: ' x eq 5 '. 같지 않음 식의 예: ' x ne 5 '. | `Collection(Edm.DateTimeOffset)`,, `Collection(Edm.Double)` `Collection(Edm.Int32)` 또는 형식의 필드에 대 한 필터링`Collection(Edm.Int64)` | [비교 가능한 컬렉션 필터링 규칙](#bkmk_comparables) |
| 람다 식이 잘못 되었습니다. 컬렉션 형식 (GeographyPoint)의 필드를 반복 하는 람다 식에서 지원 되지 않는 지역 () 또는 지역 ()을 발견 했습니다. ' Any '의 경우 ' lt ' 또는 ' le ' 연산자를 사용 하 여 geo distance ()를 비교 하 고 geo ()의 사용이 부정 되지 않도록 해야 합니다. ' 모두 '의 경우 ' gt ' 또는 ' ge ' 연산자를 사용 하 여 geo distance ()를 비교 하 고, geo ()의 사용이 부정 되는지 확인 해야 합니다. | 형식의 필드에 대 한 필터링 `Collection(Edm.GeographyPoint)` | [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 람다 식이 잘못 되었습니다. 복합 부울 식은 type Collection (GeographyPoint)의 필드를 반복 하는 람다 식에서 지원 되지 않습니다. ' Any '의 경우 하위 식에 ' or '를 추가 하세요. ' and '는 지원 되지 않습니다. ' A l l '의 경우 하위 식에 ' and '를 추가 하세요. ' or '는 지원 되지 않습니다. | 또는 형식의 필드에 대 한 필터링 `Collection(Edm.String)``Collection(Edm.GeographyPoint)` | [문자열 컬렉션을 필터링 하는 규칙](#bkmk_strings) <br/><br/> [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 람다 식이 잘못 되었습니다. 비교 연산자 (' lt ', ' le ', ' gt ' 또는 ' ge ' 중 하나)를 찾았습니다. 형식 컬렉션 (Edm. String)의 필드를 반복 하는 람다 식에는 같음 연산자도 사용할 수 있습니다. ' Any '의 경우 ' x eq y ' 형식의 식을 사용 하세요. ' A l l '의 경우 ' x ne y ' 또는 ' not (x eq y) ' 형식의 식을 사용 하세요. | 형식의 필드에 대 한 필터링 `Collection(Edm.String)` | [문자열 컬렉션을 필터링 하는 규칙](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>유효한 컬렉션 필터를 작성 하는 방법

유효한 컬렉션 필터를 작성 하는 규칙은 각 데이터 형식 마다 다릅니다. 다음 섹션에서는 지원 되는 필터 기능 및 그 외의 예제를 보여 주는 규칙에 대해 설명 합니다.

- [문자열 컬렉션을 필터링 하는 규칙](#bkmk_strings)
- [부울 컬렉션 필터링 규칙](#bkmk_bools)
- [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints)
- [비교 가능한 컬렉션 필터링 규칙](#bkmk_comparables)
- [복합 컬렉션 필터링 규칙](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>문자열 컬렉션을 필터링 하는 규칙

문자열 컬렉션에 대 한 람다 식 내에서 사용할 수 있는 유일한 비교 연산자는 `eq` 및 `ne` 입니다.

> [!NOTE]
> Azure Cognitive Search는 `lt` / `le` / `gt` / `ge` 람다 식의 내부 또는 외부에 관계 없이 문자열에 대 한 연산자를 지원 하지 않습니다.

의 본문은 `any` 같은지 여부만 테스트할 수 있지만의 본문은 `all` 같지 않은지 테스트할 수 있습니다.

의 본문에서를 통해 여러 식을 결합 하 `or` `any` 고 `and` 의 본문에서를 통해를 사용할 수도 있습니다 `all` . 함수는 `search.in` 와 같음 검사를 결합 하는 것과 동일 하므로 `or` 의 본문 에서도 허용 됩니다 `any` . 반대로는 `not search.in` 의 본문에서 허용 됩니다 `all` .

예를 들어 다음과 같은 식을 사용할 수 있습니다.

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

이러한 식은 허용 되지 않습니다.

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>부울 컬렉션 필터링 규칙

형식은 `Edm.Boolean` 및 연산자만 지원 `eq` `ne` 합니다. 따라서 동일한 범위 변수를 확인 하는 절을 결합 하는 것이 `and` / `or` 항상 tautologies 또는 모순로 이어질 수 있으므로 이러한 절을 결합 하는 것은 적합 하지 않습니다.

다음은 허용 되는 부울 컬렉션에 대 한 필터의 몇 가지 예입니다.

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

문자열 컬렉션과 달리 부울 컬렉션에는 람다 식의 형식에 사용할 수 있는 연산자에 대 한 제한이 없습니다. 및는 둘 다 `eq` `ne` 또는의 본문에서 사용할 수 있습니다 `any` `all` .

부울 컬렉션에는 다음과 같은 식이 허용 되지 않습니다.

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>GeographyPoint 컬렉션 필터링 규칙

`Edm.GeographyPoint`컬렉션에서 형식의 값은 서로 직접 비교할 수 없습니다. 대신 및 함수에 대 한 매개 변수로 사용 해야 `geo.distance` 합니다 `geo.intersects` . `geo.distance`그런 다음 비교 연산자,, 또는 중 하나를 사용 하 여이 함수를 distance 값과 비교 해야 합니다 `lt` `le` `gt` `ge` . 이러한 규칙은 컬렉션이 아닌 GeographyPoint 필드에도 적용 됩니다.

문자열 컬렉션과 마찬가지로 컬렉션에는 `Edm.GeographyPoint` 지역 공간 함수를 사용 하 고 다양 한 형식의 람다 식에서 결합할 수 있는 몇 가지 규칙이 있습니다.

- 함수와 함께 사용할 수 있는 비교 연산자는 `geo.distance` 람다 식의 형식에 따라 달라 집니다. 의 경우 `any` 또는만 사용할 수 `lt` 있습니다 `le` . 의 경우 `all` 또는만 사용할 수 `gt` 있습니다 `ge` . 와 관련 된 식을 부정할 수 `geo.distance` 있지만 비교 연산자를 변경 해야 합니다 (가이 `geo.distance(...) lt x` 되 `not (geo.distance(...) ge x)` 고 `geo.distance(...) le x` 이 됨 `not (geo.distance(...) gt x)` ).
- 의 본문에서 `all` 함수는 부정 되어야 합니다 `geo.intersects` . 반대로의 본문에서 `any` `geo.intersects` 함수는 부정 하지 않아야 합니다.
- 의 본문에서 `any` 지역 공간 식은을 사용 하 여 결합할 수 있습니다 `or` . 의 본문에서는 `all` 이러한 식을를 사용 하 여 결합할 수 있습니다 `and` .

위의 제한 사항은 문자열 컬렉션에 대 한 같음/같지 않음 제한과 유사한 이유로 존재 합니다. 이러한 이유를 자세히 살펴보기 위해 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md) 를 참조 하세요.

다음은 허용 되는 컬렉션에 대 한 필터의 몇 가지 예입니다 `Edm.GeographyPoint` .

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

컬렉션에는 다음과 같은 식이 허용 되지 않습니다 `Edm.GeographyPoint` .

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>비교 가능한 컬렉션 필터링 규칙

이 섹션은 다음 데이터 형식 모두에 적용 됩니다.

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

및와 같은 `Edm.Int32` 형식은 `Edm.DateTimeOffset` ,,,, 및의 6 가지 비교 연산자를 모두 지원 `eq` `ne` `lt` `le` `gt` `ge` 합니다. 이러한 형식의 컬렉션에 대 한 람다 식에는 이러한 연산자를 사용 하는 간단한 식이 포함 될 수 있습니다. 이는 및에 모두 적용 됩니다 `any` `all` . 예를 들어 다음과 같은 필터를 사용할 수 있습니다.

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

그러나 람다 식 내에서 이러한 비교 식을 보다 복잡 한 식으로 결합 하는 방법에는 다음과 같은 제한 사항이 있습니다.

- 규칙 `any` :
  - 단순한 같지 않음 식은 다른 식과 유용한 결합할 수 없습니다. 예를 들어이 식은 다음과 같이 사용할 수 있습니다.
    - `ratings/any(r: r ne 5)`

    그러나 다음 식은 그렇지 않습니다.
    - `ratings/any(r: r ne 5 and r gt 2)`

    이 식은 허용 되지만 조건이 겹치면 유용 하지 않습니다.
    - `ratings/any(r: r ne 5 or r gt 7)`
  - ,,, 또는를 포함 하는 간단한 비교 식은 `eq` `lt` `le` `gt` `ge` 와 함께 사용할 수 있습니다 `and` / `or` . 예를 들면
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - (접속사)와 결합 된 비교 식은을 `and` 사용 하 여 추가로 결합할 수 있습니다 `or` . 이 형식은 부울 논리에서 Dnf ([분리 Normal form](https://en.wikipedia.org/wiki/Disjunctive_normal_form))로 알려져 있습니다. 예를 들면
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 규칙 `all` :
  - 단순 같음 식은 다른 식과 유용한 결합할 수 없습니다. 예를 들어이 식은 다음과 같이 사용할 수 있습니다.
    - `ratings/all(r: r eq 5)`

    그러나 다음 식은 그렇지 않습니다.
    - `ratings/all(r: r eq 5 or r le 2)`

    이 식은 허용 되지만 조건이 겹치면 유용 하지 않습니다.
    - `ratings/all(r: r eq 5 and r le 7)`
  - ,,, 또는를 포함 하는 간단한 비교 식은 `ne` `lt` `le` `gt` `ge` 와 함께 사용할 수 있습니다 `and` / `or` . 예를 들면
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - (Disjunctions)과 결합 된 비교 식은을 `or` 사용 하 여 추가로 결합할 수 있습니다 `and` . 이 형식은 부울 논리에서 "[결합 Normal form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (my.cnf)로 알려져 있습니다. 예를 들면
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>복합 컬렉션 필터링 규칙

복합 컬렉션에 대 한 람다 식은 기본 형식의 컬렉션에 대해 람다 식 보다 훨씬 더 유연한 구문을 지원 합니다. 외부에서 사용할 수 있는 람다 식 내에서 필터 구문을 사용할 수 있습니다. 단, 두 가지 예외만 있으면 됩니다.

첫째, 함수와 함수 `search.ismatch` 는 `search.ismatchscoring` 람다 식 내에서 지원 되지 않습니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조 하세요.

둘째, 범위 변수에 *바인딩되지* 않은 (따라서 *무료 변수*) 필드를 참조 하는 것은 허용 되지 않습니다. 예를 들어 다음과 같은 두 가지 동일한 OData 필터 식을 살펴보겠습니다.

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

첫 번째 식은 허용 되지만 두 번째 형태는 범위 변수에 바인딩되지 않기 때문에 거부 됩니다 `details/margin` `s` .

또한이 규칙은 외부 범위에서 변수가 바인딩된 식으로 확장 됩니다. 이러한 변수는 표시 되는 범위에 대해 무료입니다. 예를 들어 첫 번째 식은 허용 되지만, `s/name` 는 범위 변수의 범위를 기준으로 사용이 가능 하기 때문에 두 번째 식이 허용 되지 않습니다 `a` .

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

람다 식에 바인딩된 변수만 포함 되도록 항상 필터를 생성할 수 있기 때문에 이러한 제한은 실제로는 문제가 되지 않습니다.

## <a name="cheat-sheet-for-collection-filter-rules"></a>컬렉션 필터 규칙에 대 한 참고 자료 시트

다음 표에서는 각 컬렉션 데이터 형식에 대해 유효한 필터를 생성 하는 규칙을 요약 하 여 보여 줍니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

각 사례에 대해 유효한 필터를 구성 하는 방법에 대 한 예제는 [올바른 컬렉션 필터를 작성 하는 방법](#bkmk_examples)을 참조 하세요.

필터를 자주 작성 하 고 첫 번째 원칙의 규칙을 이해 하는 것이 기억 하는 데 도움이 되는 경우 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)