---
title: OData 컬렉션 필터 이해
titleSuffix: Azure Cognitive Search
description: 컬렉션에 고유한 제한 사항 및 동작을 포함 하 여 Azure Cognitive Search 쿼리에서 OData 컬렉션 필터를 사용 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932919"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData 컬렉션 필터 이해

Azure Cognitive Search에서 컬렉션 필드를 [필터링](query-odata-filter-orderby-syntax.md) 하려면 [ `any` 및 `all` 연산자](search-query-odata-collection-operators.md) 를 **람다 식**과 함께 사용할 수 있습니다. 람다 식은 **범위 변수**를 참조 하는 부울 식입니다. `any`및 연산자는 루프 `all` `for` 변수의 역할을 취하는 범위 변수와 루프의 본문으로 사용 되는 람다 식을 사용 하는 대부분의 프로그래밍 언어의 루프와 유사 합니다. 범위 변수는 루프를 반복 하는 동안 컬렉션의 "current" 값을 사용 합니다.

개념적으로 작동 하는 방법은 이상입니다. 실제로 Azure Cognitive Search는 루프의 작동 방식에 대해 매우 다양 한 방법으로 필터를 구현 `for` 합니다. 이러한 차이는 사용자에 게 표시 되지 않지만 특정 상황에서는 표시 되지 않습니다. 최종 결과에는 람다 식을 작성할 때 따라야 하는 규칙이 있습니다.

이 문서에서는 Azure Cognitive Search에서 이러한 필터를 실행 하는 방법을 탐색 하 여 컬렉션 필터에 대 한 규칙이 있는 이유를 설명 합니다. 복잡 한 람다 식으로 고급 필터를 작성 하는 경우이 문서를 통해 필터 및 이유에 대 한 이해를 수립할 수 있습니다.

예제를 비롯 하 여 컬렉션 필터에 대 한 규칙에 대 한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조 하세요.

## <a name="why-collection-filters-are-limited"></a>컬렉션 필터를 제한 하는 이유

모든 유형의 컬렉션에 대해 모든 필터 기능이 지원 되는 이유는 다음과 같은 세 가지 기본적인 이유가 있습니다.

1. 특정 데이터 형식에 대해서는 특정 연산자만 지원 됩니다. 예를 들어, 부울 값을 비교 하 고, 등을 사용 하는 것은 의미가 없습니다 `true` `false` `lt` `gt` .
1. Azure Cognitive Search는 형식의 필드에 대 한 **상관 관계 검색** 을 지원 하지 않습니다 `Collection(Edm.ComplexType)` .
1. Azure Cognitive Search는 반전 된 인덱스를 사용 하 여 컬렉션을 비롯 한 모든 데이터 형식에 대 한 필터를 실행 합니다.

첫 번째 이유는 OData 언어 및 EDM 형식 시스템을 정의 하는 것입니다. 마지막 두 항목은이 문서의 나머지 부분에서 자세히 설명 합니다.

## <a name="correlated-versus-uncorrelated-search"></a>상호 관련 검색 및 상관 관계가 없는 검색

복합 개체의 컬렉션에 여러 필터 조건을 적용 하는 경우이 기준은 *컬렉션의 각 개체*에 적용 되므로 **상관 관계가** 지정 됩니다. 예를 들어 다음 필터는 100 미만의 deluxe 공간이 하나 이상 있는 호텔을 반환 합니다.

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

필터링이 *상관 관계가 없는*경우 위의 필터는 한 방 deluxe 하 고 다른 방에는 100 미만의 기본 요금이 있는 호텔을 반환할 수 있습니다. 람다 식의 두 절은 같은 범위 변수에 적용 되므로이는 의미가 `room` 없습니다. 이러한 필터가 상호 관련 되는 이유입니다.

그러나 전체 텍스트 검색에는 특정 범위 변수를 참조할 수 있는 방법이 없습니다. 필드 지정 search를 사용 하 여 다음과 같은 [전체 Lucene 쿼리](query-lucene-syntax.md) 를 실행 하는 경우

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

한 방에서 deluxe 하 고 다른 방에는 설명의 "도시 보기"를 언급 하는 호텔을 받을 수 있습니다. 예를 들어의 아래 문서는 `Id` `1` 쿼리와 일치 합니다.

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

그 이유는 `Rooms/Type` `Rooms/Type` 아래 표에 나와 있는 것 처럼 전체 문서에 있는 필드의 모든 분석 된 용어와의 유사 항목을 참조 하는 것입니다 `Rooms/Description` .

`Rooms/Type`전체 텍스트 검색에 대 한 저장 방법:

| 용어 `Rooms/Type` | 문서 Id |
| --- | --- |
| deluxe | 1, 2 |
| 표준 | 1 |

`Rooms/Description`전체 텍스트 검색에 대 한 저장 방법:

| 용어 `Rooms/Description` | 문서 Id |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| 가든 | 1 |
| 큰 | 1 |
| 모텔 | 2 |
| 객실 | 1, 2 |
| 표준 | 1 |
| tcp/ip | 1 |
| view | 1 |

위의 필터와는 달리, 기본적으로 "대화방이 `Type` ' Deluxe '과 같은 공간이 있고 **동일한 공간이** 100 미만입니다." 문서와 일치 하는 경우 `BaseRate` 검색 쿼리는 " `Rooms/Type` Deluxe" 라는 용어를 포함 하 고 "city view" 라는 구를 포함 하는 "일치 문서"를 의미 `Rooms/Description` 합니다. 후자의 경우에는 필드의 상관 관계를 지정할 수 있는 개별 대화방의 개념이 없습니다.

> [!NOTE]
> Azure Cognitive Search에 추가 된 상관 관계 검색에 대 한 지원을 보려면 [이 사용자 음성 항목](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)에 대해 투표 하세요.

## <a name="inverted-indexes-and-collections"></a>반전 인덱스 및 컬렉션

, 등의 간단한 컬렉션 보다 복잡 한 컬렉션에 비해 람다 식에 대 한 제한이 훨씬 적음을 발견할 수 있습니다 `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)` . Azure Cognitive Search는 복합 컬렉션을 하위 문서의 실제 컬렉션으로 저장 하는 반면 간단한 컬렉션은 컬렉션으로 저장 되지 않기 때문입니다.

예를 들어 `seasons` 온라인 대리점의 인덱스와 같은 필터링 가능한 문자열 컬렉션 필드를 생각해 보겠습니다. 이 인덱스에 업로드 된 일부 문서는 다음과 같습니다.

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

필드의 값은 `seasons` 다음과 같이 **반전 된 인덱스**라는 구조에 저장 됩니다.

| 용어 | 문서 Id |
| --- | --- |
| spring | 1, 2 |
| 절약 | 1 |
| 있게 | 1, 2 |
| 동계 | 2, 3 |

이 데이터 구조는 지정 된 용어가 표시 되는 문서에 대해 좋은 속도로 한 질문에 대답 하도록 디자인 되었습니다. 이 질문에 대 한 대답은 컬렉션에 대 한 루프와는 다른 일반적인 같음 검사 처럼 작동 합니다. 실제로 문자열 컬렉션의 경우 Azure Cognitive Search는 `eq` 의 람다 식 내에서 비교 연산자로만 허용 됩니다 `any` .

동일 하 게 구축 하 여 동일한 범위 변수에 여러 개의 같음 검사를 결합할 수 있는 방법에 대해 살펴보겠습니다 `or` . [수량자의 대 수 및 분배 속성](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에 작동 합니다. 이 식:

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

이는 다음과 동등합니다.

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

그리고 두 개의 `any` 하위 식이 반전 된 인덱스를 사용 하 여 효율적으로 실행 될 수 있습니다. 또한 [수량자의 부정 법칙](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에 다음 식은

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

이는 다음과 동등합니다.

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

따라서 및와 함께를 사용할 수 있습니다 `all` `ne` `and` .

> [!NOTE]
> 세부 정보는이 문서의 범위를 벗어나는 것 이지만 이러한 동일한 원칙이 [지역 공간 지점 컬렉션에 대 한 거리 및 교차 테스트](search-query-odata-geo-spatial-functions.md) 로도 확장 됩니다. 그 이유는 `any` 다음과 같습니다.
>
> - `geo.intersects` 부정할 수 없음
> - `geo.distance`또는를 사용 하 여 비교 해야 합니다. `lt``le`
> - 식은 `or` 와 결합 해야 합니다. `and`
>
> 반대의 규칙은에 적용 `all` 됩니다.

예를 들어 `lt` ,, `gt` `le` 및 `ge` 연산자를 지 원하는 데이터 형식의 컬렉션을 필터링 하는 경우 더 광범위 한 식이 허용 됩니다 `Collection(Edm.Int32)` . 특히를 사용 하 여 `and` `or` `any` 기본 비교 식이 **범위 비교** 로 결합 된 `and` 다음를 사용 하 여 추가로 결합 `or` 되는 한을 사용할 수 있습니다. 이러한 부울 식의 구조는 [DNF (분리 Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)라고 하며, 그렇지 않은 경우에는 "ORs of ANDs" 이라고 합니다. 반대로 `all` 이러한 데이터 형식에 대 한 람다 식은 [My.cnf (결합 Normal Form)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)에 있어야 합니다. 그렇지 않으면 "ANDs of ORs"로 알려져 있습니다. Azure Cognitive Search는 문자열에 대 한 빠른 용어 조회를 수행할 수 있는 것 처럼 반전 인덱스를 사용 하 여 효율적으로 실행할 수 있으므로 이러한 범위 비교를 허용 합니다.

요약 하자면, 람다 식에서 허용 되는 항목에 대 한 다음과 같은 규칙이 있습니다.

- 내 `any` 에서 *긍정 검사* 는 같음, 범위 비교, 또는 `geo.intersects` `geo.distance` or와 비교 하 여 항상 허용 `lt` 됩니다 `le` (거리를 확인 하는 경우 "거리"은 같음 같음).
- 내 `any` 에서 `or` 는 항상 허용 됩니다. `and`는 범위 검사를 표현할 수 있는 데이터 형식에만 사용할 수 있으며, DNF (ORs Of ANDs)를 사용 하는 경우에만 사용할 수 있습니다.
- 내 `all` 에서 규칙은 역방향으로 표시 됩니다. *음수 검사만* 허용 되며, `and` always를 사용할 수 있으며, `or` my.cnf (ANDs of ORs)로 표현 된 범위 검사에만 사용할 수 있습니다.

실제로 이러한 필터 유형 중에서 가장 많이 사용 되는 필터 유형입니다. 그러나 가능한 작업의 경계를 이해 하는 것도 도움이 됩니다.

허용 되는 필터 종류와 그렇지 않은 필터 종류에 대 한 구체적인 예는 [올바른 컬렉션 필터를 작성 하는 방법](search-query-troubleshoot-collection-filters.md#bkmk_examples)을 참조 하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)