---
title: OData 참조 선택
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 검색 결과에 반환할 필드를 명시적으로 선택 하기 위한 구문 및 언어 참조입니다.
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919661"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData $select 구문

 [OData **$select** 매개 변수](query-odata-filter-orderby-syntax.md) 를 사용 하 여 Azure Cognitive Search의 검색 결과에 포함할 필드를 선택할 수 있습니다. 이 문서에서는 **$select** 구문에 대해 자세히 설명 합니다. 검색 결과를 표시할 때 **$select** 를 사용 하는 방법에 대 한 일반적인 정보는 [Azure Cognitive Search에서 검색 결과](search-pagination-page-layout.md)를 사용 하는 방법을 참조 하세요.

## <a name="syntax"></a>구문

**$Select** 매개 변수는 쿼리 결과 집합에 반환 되는 각 문서에 대 한 필드를 결정 합니다. 다음 EBNF ([Extended Backus-Backus-naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))은 **$select** 매개 변수에 대 한 문법을 정의 합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 전체 EBNF [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 를 참조 하세요.

**$Select** 매개 변수는 다음과 같은 두 가지 형식으로 제공 됩니다.

1. `*`모든 검색 가능 필드를 반환 해야 함을 나타내는 단일 별표 ()
1. 반환 해야 하는 필드를 식별 하는 쉼표로 구분 된 필드 경로 목록입니다.

두 번째 폼을 사용 하는 경우 목록에서 검색할 수 있는 필드만 지정할 수 있습니다.

하위 필드를 명시적으로 지정 하지 않고 복합 필드를 나열 하는 경우 검색 가능한 모든 하위 필드가 쿼리 결과 집합에 포함 됩니다. 예를 들어 인덱스에 `Address` `Street` `City` `Country` 모두 검색할 수 있는, 및 하위 필드가 있는 필드가 있다고 가정 합니다. `Address` **$Select**에서 지정 하는 경우 쿼리 결과에는 세 개의 하위 필드가 모두 포함 됩니다.

## <a name="examples"></a>예

`HotelId` `HotelName` `Rating` 의 하위 필드 뿐만 아니라 결과에, 및 최상위 필드를 `City` 포함 합니다 `Address` .

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

예제 결과는 다음과 같습니다.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`HotelName`결과에는 최상위 필드 뿐만 아니라의 모든 하위 필드와 `Address` `Type` `BaseRate` 컬렉션에 있는 각 개체의 및 하위 필드 `Rooms` 를 포함 합니다.

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

예제 결과는 다음과 같습니다.

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search에서 검색 결과를 사용 하는 방법](search-pagination-page-layout.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure Cognitive Search REST API &#40;문서 검색&#41;](/rest/api/searchservice/Search-Documents)