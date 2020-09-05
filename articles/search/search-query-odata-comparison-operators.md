---
title: OData 비교 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 비교 연산자 (eq, ne, gt, lt, ge 및 le)를 사용 하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934874"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure Cognitive Search의 OData 비교 연산자- `eq` , `ne` ,,, `gt` `lt` `ge` 및 `le`

Azure Cognitive Search의 [OData 필터 식](query-odata-filter-orderby-syntax.md) 에서 가장 기본적인 작업은 필드를 지정 된 값과 비교 하는 것입니다. 비교에는 같음 비교 및 범위 비교가 있습니다. 다음 연산자를 사용 하 여 필드를 상수 값과 비교할 수 있습니다.

같음 연산자:

- `eq`: 필드가 상수 값 **과 같은지** 여부를 테스트 합니다.
- `ne`: 필드가 상수 값 **과 같지** 않은지 여부를 테스트 합니다.

범위 연산자:

- `gt`: 필드가 상수 값 **보다 큰지** 여부를 테스트 합니다.
- `lt`: 필드가 상수 값 **보다 작지** 않은지 테스트 합니다.
- `ge`: 필드가 상수 값 **보다 크거나 같은지** 여부를 테스트 합니다.
- `le`: 필드가 상수 값 **보다 작거나 같은지** 여부를 테스트 합니다.

[논리 연산자](search-query-odata-logical-operators.md) 와 함께 범위 연산자를 사용 하 여 필드가 특정 값 범위 내에 있는지 여부를 테스트할 수 있습니다. 이 문서의 뒷부분에 나오는 [예제](#examples) 를 참조 하세요.

> [!NOTE]
> 원하는 경우 연산자의 좌 변에 상수 값을 입력 하 고 오른쪽에 필드 이름을 넣을 수 있습니다. 범위 연산자의 경우 비교의 의미가 반대로 바뀝니다. 예를 들어 상수 값이 왼쪽에 있으면는 `gt` 상수 값이 필드 보다 큰지 여부를 테스트 합니다. 비교 연산자를 사용 하 여와 같은 함수의 결과를 값과 비교할 수도 있습니다 `geo.distance` . 과 같은 부울 함수의 경우 `search.ismatch` 결과를 or로 비교 하는 `true` `false` 것이 선택 사항입니다.

## <a name="syntax"></a>구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 비교 연산자를 사용 하는 OData 식의 문법을 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

비교 식에는 두 가지 형태가 있습니다. 상수는 연산자의 왼쪽 또는 오른쪽에 표시 되는지 여부에 따라 다릅니다. 연산자의 다른 쪽에 있는 식은 **변수** 또는 함수 호출 이어야 합니다. 변수는 필드 이름 이거나 [람다 식](search-query-odata-collection-operators.md)의 경우 범위 변수일 수 있습니다.

## <a name="data-types-for-comparisons"></a>비교를 위한 데이터 형식

비교 연산자의 양쪽에 있는 데이터 형식은 호환 되어야 합니다. 예를 들어 좌 변에 형식의 필드인 경우 `Edm.DateTimeOffset` 오른쪽은 날짜-시간 상수 여야 합니다. 숫자 데이터 형식은 더 유연 합니다. 다음 표에서 설명 하는 것 처럼 몇 가지 제한 사항으로 숫자 형식의 변수와 함수를 다른 숫자 형식의 상수와 비교할 수 있습니다.

| 변수 또는 함수 형식 | 상수 값 형식 | 제한 사항 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 비교는 다음 [에 대 한 `NaN` 특수 규칙](#special-case-nan) 을 따릅니다. |
| `Edm.Double` | `Edm.Int64` | 상수가로 변환 되어 `Edm.Double` 크기가 큰 값의 전체 자릿수가 손실 됩니다. |
| `Edm.Double` | `Edm.Int32` | 해당 없음 |
| `Edm.Int64` | `Edm.Double` | , 또는에 대 한 비교 `NaN` `-INF` `INF` 는 허용 되지 않습니다. |
| `Edm.Int64` | `Edm.Int64` | 해당 없음 |
| `Edm.Int64` | `Edm.Int32` | 상수는 비교 전에로 변환 됩니다. `Edm.Int64` |
| `Edm.Int32` | `Edm.Double` | , 또는에 대 한 비교 `NaN` `-INF` `INF` 는 허용 되지 않습니다. |
| `Edm.Int32` | `Edm.Int64` | 해당 없음 |
| `Edm.Int32` | `Edm.Int32` | 해당 없음 |

형식의 필드를과 비교 하는 것과 같이 허용 되지 않는 비교를 위해 `Edm.Int64` `NaN` Azure Cognitive Search REST API는 "HTTP 400: 잘못 된 요청" 오류를 반환 합니다.

> [!IMPORTANT]
> 숫자 형식 비교가 유연 하더라도 필터에서 비교를 작성 하는 것이 좋습니다. 이렇게 하면 상수 값이 비교할 변수나 함수와 동일한 데이터 형식이 됩니다. 이는 부동 소수점 및 정수 값을 혼합할 때 특히 정확도를 잃지 않는 암시적 변환이 가능 하다는 경우에 특히 중요 합니다.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>및에 대 한 특수 한 경우 `null``NaN`

비교 연산자를 사용 하는 경우 Azure Cognitive Search의 컬렉션이 아닌 모든 필드가 잠재적으로 될 수 있다는 점을 기억해 야 `null` 합니다. 다음 표에서는 둘 중 하나가 될 수 있는 비교 식의 가능한 모든 결과를 보여 줍니다 `null` .

| 연산자 | 필드 또는 변수만 인 경우의 결과 `null` | 상수가 인 경우의 결과 `null` | 필드 또는 변수와 상수가 모두 인 경우의 결과 `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `lt` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `ge` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `le` | `false` | HTTP 400: 잘못 된 요청 오류 | HTTP 400: 잘못 된 요청 오류 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

요약 하면 `null` 는 자신과 같으며 다른 값 보다 작거나 크지 않습니다.

인덱스에 형식의 필드가 `Edm.Double` 있고 이러한 필드에 값을 업로드 하는 경우 `NaN` 필터를 작성할 때이를 고려해 야 합니다. Azure Cognitive Search는 값을 처리 하기 위해 IEEE 754 표준을 구현 `NaN` 하 고, 이러한 값을 사용한 비교는 다음 표에 나와 있는 것 처럼 명확 하지 않은 결과를 생성 합니다.

| 연산자 | 하나 이상의 피연산자가 인 경우의 결과 `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

요약 하자면 `NaN` 는 자신을 포함 하 여 어떤 값도 아닙니다.

### <a name="comparing-geo-spatial-data"></a>지리적 공간 데이터 비교

형식의 필드를 상수 값과 직접 비교할 수는 `Edm.GeographyPoint` 없지만 함수를 사용할 수 있습니다 `geo.distance` . 이 함수는 형식의 값을 반환 `Edm.Double` 하므로 상수 지역 공간 좌표에서의 거리를 기준으로 필터링 하는 숫자 상수와 비교할 수 있습니다. 아래 [예제](#examples) 를 참조 하세요.

### <a name="comparing-string-data"></a>문자열 데이터 비교

및 연산자를 사용 하 여 정확 하 게 일치 하는 필터에서 문자열을 비교할 수 있습니다 `eq` `ne` . 이러한 비교는 대/소문자를 구분 합니다.

## <a name="examples"></a>예

`Rating`필드가 3에서 5 사이인 문서를 찾습니다 (포함).

```text
Rating ge 3 and Rating le 5
```

`Location`지정 된 위도 및 경도에서 필드가 2 킬로미터 미만인 문서를 찾습니다.

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

`LastRenovationDate`필드가 2015 년 1 월 1 일 (UTC) 보다 크거나 같은 문서를 찾습니다.

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

`Details/Sku`필드가 다음과 일치 하지 않는 문서를 찾습니다 `null` .

```text
Details/Sku ne null
```

하나 이상의 대화방에 "Deluxe Room" 유형이 있는 호텔 문서를 찾습니다. 여기서 필드의 문자열은 `Rooms/Type` 필터와 정확히 일치 합니다.

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)