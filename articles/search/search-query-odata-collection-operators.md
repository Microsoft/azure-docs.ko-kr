---
title: OData 컬렉션 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만들 때 컬렉션이 나 복합 컬렉션 필드에 필터를 사용 하는 경우 람다 식에 "any" 및 "all" 연산자를 사용 합니다.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934908"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure Cognitive Search의 OData 컬렉션 연산자- `any` 및 `all`

Azure Cognitive Search와 함께 사용할 [OData 필터 식을](query-odata-filter-orderby-syntax.md) 작성할 때 컬렉션 필드에 대해 필터링 하는 것이 유용한 경우가 많습니다. 및 연산자를 사용 하 여이를 달성할 수 있습니다 `any` `all` .

## <a name="syntax"></a>구문

다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 또는을 사용 하는 OData 식의 문법을 정의 합니다 `any` `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

컬렉션을 필터링 하는 식에는 세 가지 형식이 있습니다.

- 처음 두 개는 컬렉션 필드를 반복 하 여 람다 식의 형식으로 제공 된 조건자를 컬렉션의 각 요소에 적용 합니다.
  - 를 사용 하는 식은 `all` `true` 컬렉션의 모든 요소에 대해 조건자가 true 인 경우를 반환 합니다.
  - 를 사용 하는 식은 `any` `true` 컬렉션의 요소 중 하나 이상에 대해 조건자가 true 인 경우를 반환 합니다.
- 컬렉션 필터의 세 번째 형태는 `any` 컬렉션 필드가 비어 있는지 여부를 테스트 하기 위해 람다 식 없이를 사용 합니다. 컬렉션에 요소가 있으면를 반환 `true` 합니다. 컬렉션이 비어 있으면를 반환 `false` 합니다.

컬렉션 필터의 **람다 식은** 프로그래밍 언어의 루프 본문과 같습니다. 반복 하는 동안 컬렉션의 현재 요소를 보유 하는 **범위 변수**라고 하는 변수를 정의 합니다. 또한 컬렉션의 각 요소에 대 한 범위 변수에 적용할 필터 기준이 되는 또 다른 부울 식을 정의 합니다.

## <a name="examples"></a>예

해당 필드에 `tags` "wifi" 문자열이 정확히 포함 된 문서를 찾습니다.

```text
tags/any(t: t eq 'wifi')
```

필드의 모든 요소가 `ratings` 3 ~ 5 (포함) 사이인 문서를 찾습니다.

```text
ratings/all(r: r ge 3 and r le 5)
```

필드의 모든 지역 좌표가 지정 된 다각형 내에 있는 문서를 찾습니다 `locations` .

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

필드가 비어 있는 문서를 찾습니다 `rooms` .

```text
not rooms/any()
```

모든 대화방의 문서를 찾습니다 `rooms/amenities` .이 필드에는 "tv"가 포함 되어 있고 `rooms/baseRate` 100 미만 이어야 합니다.

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>제한 사항

필터 식의 모든 기능을 람다 식의 본문 내에서 사용할 수 있는 것은 아닙니다. 제한 사항은 필터링 하려는 컬렉션 필드의 데이터 형식에 따라 다릅니다. 다음 표에서는 제한 사항을 요약 합니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

이러한 제한 사항 및 예제에 대 한 자세한 내용은 [Azure Cognitive Search에서 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조 하세요. 이러한 제한 사항이 있는 이유에 대 한 자세한 내용은 [Azure Cognitive Search의 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)