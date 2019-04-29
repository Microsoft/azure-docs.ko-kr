---
title: 필터 및 order-by 절의 OData 식 구문 - Azure Search
description: Azure Search 쿼리의 필터 및 order-by 식 OData 구문입니다.
ms.date: 03/27/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317103"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Azure Search에서 필터 및 order-by 절의 OData 식 구문

Azure Search는 **$filter** 및 **$orderby** 식에 대해 OData 식 구문 하위 세트를 지원합니다. 필터 식은 쿼리 구문 분석 때 평가되고, 검색을 특정 필드로 제한하거나 인덱스 검색 때 사용되는 일치 조건을 추가합니다. Order-by 식은 결과 세트에 대한 후처리 단계로 적용됩니다. 필터 및 order-by 식은 둘 다 쿼리 요청에 포함되며, **search** 매개 변수에 사용되는 [simple](query-simple-syntax.md) 또는 [full](query-lucene-syntax.md) 쿼리 구문과는 별도로 OData 구문을 준수합니다. 이 문서에서는 필터 및 정렬 식에 사용되는 OData 식에 대한 참조 설명서를 제공합니다.

## <a name="filter-syntax"></a>필터 구문

**$filter** 식은 완전히 표현된 쿼리로서 독립적으로 실행되거나 추가 매개 변수가 있는 쿼리를 구체화할 수 있습니다. 다음 예제에서는 몇 가지 주요 시나리오를 보여 줍니다. 첫 번째 예제에서 필터는 쿼리의 핵심 요소입니다.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

다른 일반적인 사용 사례는 필터 조합 패싯입니다. 여기서 필터는 사용자 기반의 패싯 탐색 선택에 따라 쿼리 노출 영역을 줄여줍니다.

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>필터 연산자  

- 논리 연산자(and, or, not)  

- 비교 식(`eq, ne, gt, lt, ge, le`). 문자열 비교는 대/소문자를 구분합니다.  

- 지원되는 EDM([엔터티 데이터 모델](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)) 형식의 상수(지원되는 형식 목록은 [지원되는 데이터 형식 &#40;Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) 참조). 컬렉션 형식 상수는 지원되지 않습니다.  

- 필드 이름에 대한 참조. `filterable` 필드만 필터 식에 사용할 수 있습니다.  

- 매개 변수 없는 `any`. 이 항목은 `Collection(Edm.String)` 형식의 필드에 요소가 포함되어 있는지 여부를 테스트합니다.  

- 람다 식이 제한적으로 지원되는 `any` 및 `all`. 
    
  -   `any/all`은 `Collection(Edm.String)` 형식의 필드에서 지원됩니다. 
    
  -   `any`는 단순 같음 식 또는 `search.in` 함수에서만 사용할 수 있습니다. 단순 식은 단일 필드와 리터럴 값 간의 비교로 구성됩니다(예: `Title eq 'Magna Carta'`).
    
  -   `all`은 단순 같지 않음 식 또는 `not search.in`에서만 사용할 수 있습니다.   

- 지리 공간적 함수 `geo.distance` 및 `geo.intersects`. `geo.distance` 함수는 두 지점 사이의 거리(km)를 반환합니다. 한 지점은 필드이고, 다른 지점은 필터의 일부로 전달되는 상수입니다. `geo.intersects` 함수는 지정된 지점이 지정된 다각형 내에 있는 경우 true를 반환합니다. 여기서 지점은 필드이고 다각형은 필터의 일부로 전달되는 상수로 지정됩니다.  

  다각형은 바인딩된 링을 정의하는 점 시퀀스로 저장된 2차원 표면입니다(아래 예제 참조). 다각형은 닫혀야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. [다각형의 점은 시계 반대 방향 순서여야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance`는 Azure Search에서 km 단위의 거리를 반환합니다. 이 서비스는 OData 지리 공간적 작업을 지원하는 다른 서비스와는 다릅니다. 이러한 서비스는 일반적으로 m 단위 거리를 반환합니다.  

  > [!NOTE]  
  >  필터에 geo.distance를 사용할 경우 `lt`, `le`, `gt` 또는 `ge`를 사용하여 함수가 반환한 거리와 상수를 비교해야 합니다. 연산자 `eq` 및 `ne`는 거리를 비교할 때 사용할 수 없습니다. 예를 들어, geo.distance를 올바르게 사용한 것은 `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`입니다.  

- `search.in` 함수는 지정된 문자열 필드가 지정된 값 목록 중 하나와 같은지 여부를 테스트합니다. 문자열 컬렉션 필드의 단일 값을 지정된 값 목록과 비교하기 위해 아무 항목 또는 모든 항목에서 사용할 수 있습니다. 필드와 목록의 각 값 간의 같음 여부는 `eq` 연산자의 경우와 같이 대/소문자 구분 방식으로 결정됩니다. 따라서 `search.in(myfield, 'a, b, c')`과 같은 식은 `search.in`이 훨씬 더 나은 성능을 제공한다는 점을 제외하고 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`와 같습니다. 

   `search.in` 함수의 첫 번째 매개 변수는 문자열 필드 참조(또는 `search.in`이 `any` 또는 `all` 식 내에서 사용될 경우 문자열 컬렉션 필드에 대한 범위 변수)입니다. 
  
   두 번째 매개 변수는 공백 및/또는 쉼표로 구분된 값 목록을 포함하는 문자열입니다. 
  
   세 번째 매개 변수는 여기서 문자열의 각 문자나이 문자열의 하위 집합으로 처리 됩니다 구분 기호를 두 번째 매개 변수 값은 구문 분석할 때 문자열입니다. 값이 공백 및 쉼표를 포함하므로 다른 구분 기호를 사용해야 할 경우 `search.in`에 대해 선택적인 세 번째 매개 변수를 지정할 수 있습니다. 

  > [!NOTE]   
  > 일부 시나리오에서는 필드를 많은 수의 상수 값과 비교해야 합니다. 예를 들어, 필터를 사용하여 보안 조정을 구현하려면 문서 ID 필드를 요청 사용자에게 읽기 액세스 권한을 부여하는 ID 목록과 비교해야 할 수 있습니다. 이와 같은 시나리오에서는 좀 더 복잡한 같음 식 분리 대신, `search.in` 함수를 사용하는 것이 훨씬 바람직합니다. 예를 들어, `Id eq 123 or Id eq 456 or ....` 대신 `search.in(Id, '123, 456, ...')`을 사용합니다. 
  >
  > `search.in`을 사용하는 경우 두 번째 매개 변수에 수십만 개 값 목록이 포함되어 있을 때 1초 미만의 응답 시간을 기대할 수 있습니다. 여전히 최대 요청 크기로 제한되므로 `search.in`에 전달할 수 있는 항목 수에는 명시적인 제한이 없습니다. 그러나 값 수가 증가함에 따라 대기 시간도 증가합니다.

- `search.ismatch` 함수는 검색 쿼리를 필터 식의 일부로 평가합니다. 검색 쿼리와 일치하는 문서가 결과 집합에 반환됩니다. 이 함수의 다음 오버로드를 사용할 수 있습니다.
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  설명: 
  
  - `search`: 검색 쿼리([simple](query-simple-syntax.md) 또는 [full](query-lucene-syntax.md) 쿼리 구문) 
  - `queryType`: "simple" 또는 "full"이고 기본값은 "simple"입니다. `search` 매개 변수에 사용된 쿼리 언어를 지정합니다.
  - `searchFields`: 검색할 쉼표로 구분된 검색 가능 필드 목록으로, 기본값은 인덱스의 모든 검색 가능 필드입니다.    
  - `searchMode`: "any" 또는 "all"이고, 기본값은 "any"입니다. 문서를 일치 항목으로 계산하기 이해 검색 용어의 일부를 일치시켜야 하는지 또는 전부를 일치시켜야 하는지 지정합니다.

  위의 모든 매개 변수는 해당 [검색 요청 매개 변수](https://docs.microsoft.com/rest/api/searchservice/search-documents)와 같습니다.

- `search.ismatch` 함수와 같은 `search.ismatchscoring` 함수는 매개 변수로 전달된 검색 쿼리와 일치하는 문서에 대한 true를 반환합니다. 이 두 함수 간의 차이점은 `search.ismatchscoring` 쿼리와 일치하는 문서의 관련성 점수가 전반적인 문서 점수에 기여하지만, `search.ismatch`의 경우 문서 점수가 변경되지 않는다는 것입니다. 이 함수의 다음 오버로드는 `search.ismatch`의 매개 변수와 동일한 매개 변수에서 사용할 수 있습니다.
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  `search.ismatch` 및 `search.ismatchscoring` 함수는 서로, 그리고 필터 대수의 나머지 부분과 완전히 직교합니다. 즉, 두 함수를 동일한 필터 식에서 사용할 수 있습니다. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>경도 180도에 걸쳐 있는 지리 공간적 쿼리 및 다각형  
 많은 지리 공간적 쿼리 라이브러리의 경우, 경도 180도(날짜 변경선 근처)를 포함하는 쿼리를 작성하는 것은 금지되어 있으며, 다각형을 자오선 양쪽에 하나씩 2개로 분할하는 것과 같은 해결 방안이 요구됩니다.  

 Azure Search에서 180도 경도를 포함하는 지리 공간적 쿼리는 쿼리 모양이 직사각형이고 좌표가 경도 및 위도를 따라 그리드 레이아웃에 맞춰 정렬될 경우 예상대로 작동합니다(예: `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). 그렇지 않은 경우 사각형이 아닌 도형이나 정렬되지 않은 도형의 경우 분할된 다각형 접근 방법을 고려해야 합니다.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>필터 크기 제한 

 Azure Search로 보낼 수 있는 필터 식의 크기 및 복잡성은 제한되어 있습니다. 이러한 제한은 대략적으로 필터 식의 절 수와 관련이 있습니다. 경험에 따르면 수백 개의 절이 있는 경우 제한에 도달할 위험이 있습니다. 애플리케이션을 설계할 때 필터의 크기를 무제한으로 생성하지 않는 것이 좋습니다.  


## <a name="filter-examples"></a>필터 예제  

 $200 등급이 4 이상인 보다 작은 기본 모든 호텔 찾기:  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 2010 이후에 리모델링한 호텔 중에서 "Roach Motel"을 제외한 모든 호텔을 찾습니다.  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 날짜/시간 리터럴 태평양 표준시에 대 한 표준 시간대 정보를 포함 하는 2010 년 이후에 리모델링한 기본 요금이 $200 미만인 모든 호텔 찾기:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 주차장이 포함되어 있고 흡연은 허용하지 않는 모든 호텔을 찾습니다.  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- 또는 -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 럭셔리 호텔이거나 주차장이 포함되어 있고 등급이 5인 모든 호텔을 찾습니다.  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 "wifi" 태그가 있는 모든 호텔을 찾습니다(각 호텔은 Collection(Edm.String) 필드에 태그가 저장되어 있음).  

```
$filter=tags/any(t: t eq 'wifi')
```

 "motel" 태그가 없는 모든 호텔을 찾습니다.  

```
$filter=tags/all(t: t ne 'motel')
```

 태그가 있는 모든 호텔을 찾습니다.  

```
$filter=tags/any()
```

태그가 없는 모든 호텔 찾기:  

```
$filter=not tags/any()
```


 지정된 참조 지점에서 10km 이내에 있는 모든 호텔을 찾습니다(위치는 Edm.GeographyPoint 형식 필드임).  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 다각형으로 설명된 지정된 뷰포트 내의 모든 호텔을 찾습니다(위치는 Edm.GeographyPoint 형식 필드임). 다각형은 닫혀 있으며(첫 번째 점과 마지막 점 세트가 같음) [점은 시계 반대 방향 순서로 나열되어야 합니다](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 "description" 필드에 값이 없거나 값이 명시적으로 null로 설정된 모든 호텔을 찾습니다.  

```
$filter=description eq null
```

모든 호텔 찾기 이름의 'Roach motel' 또는 '예산 호텔' 같음). 구 기본 구분 기호는 공백을 포함 합니다. 세 번째 문자열 매개 변수로 specicfy 작은따옴표에서는 다른 구분 기호 수행할 수 있습니다.  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

이름이 '|'로 구분된 'Roach motel'이거나 'Budget hotel'인 모든 호텔을 찾습니다.  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

'wifi' 또는 'pool' 태그가 있는 모든 호텔을 찾습니다.  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

태그에 '가 열된 수건 랙' 또는 '헤어 드라이기 포함'와 같은 컬렉션 내의 구 일치를 찾습니다. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

'motel' 및 'cabin' 태그가 둘 다 없는 모든 호텔을 찾습니다.  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](https://docs.microsoft.com/rest/api/searchservice/search-documents)과 동일합니다.

```
$filter=search.ismatchscoring('waterfront')
```

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 `search.ismatchscoring` 함수를 사용해야만 표시할 수 있습니다.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

단어 "luxury"가 없는 문서를 찾습니다.

```
$filter=not search.ismatch('luxury') 
```

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `hotelName` 및 `description`에 대해서만 실행됩니다.
분리의 두 번째 절과만 일치하는 문서(등급이 5인 호텔)도 반환됩니다. 해당 문서가 점수를 매긴 식의 어떤 부분과도 일치하지 않는다는 것을 보다 명확히 나타내기 위해 점수가 0인 문서가 반환됩니다.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

용어 "hotel" 및 "airport"가 호텔 설명에서 서로 5개 단어 이내에 있고 흡연이 허용되지 않는 문서를 찾습니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Order-by 구문

**$orderby** 매개 변수는 `sort-criteria [asc|desc]` 형식의 식을 최대 32개 포함하는 쉼표로 구분된 목록을 수락합니다. 정렬 기준은 `sortable` 필드의 이름이거나 `geo.distance` 또는 `search.score` 함수의 호출일 수 있습니다. `asc` 또는 `desc`를 사용하여 정렬 순서를 명시적으로 지정할 수 있습니다. 기본 순서는 오름차순입니다.

여러 문서가 동일한 정렬 조건을 가지며 `search.score` 함수가 사용되지 않을 경우(예를 들어, 숫자 `rating` 필드를 기준으로 정렬하며 3개 문서가 모두 등급이 4인 경우) 내림차순의 문서 점수에 의해 동률이 깨집니다. 문서 점수가 동일하면(예를 들어 요청에 전체 텍스트 검색 쿼리가 지정되지 않은 경우) 동률 문서의 상대적 순서가 결정되지 않습니다.
 
여러 정렬 기준을 지정할 수 있습니다. 식의 순서는 최종 정렬 순서를 결정합니다. 예를 들어, 점수의 내림차순으로 정렬한 다음, 등급을 기준으로 정렬하려는 경우 구문은 `$orderby=search.score() desc,rating desc`가 됩니다.

**$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. **$orderby**에서 `geo.distance`를 사용하는 경우 적용되는 필드는 `Edm.GeographyPoint` 형식이어야 하고 `sortable`이어야 합니다.  

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다.  
 

## <a name="order-by-examples"></a>Order-by 예제

호텔을 기본 요금의 오름차순으로 정렬합니다.

```
$orderby=baseRate asc
```

호텔을 등급의 내림차순으로 정렬한 후 기본 요금의 오름차순으로 정렬합니다(오름차순이 기본값임).

```
$orderby=rating desc,baseRate
```

호텔을 등급의 내림차순으로 정렬한 후 지정된 좌표로부터의 거리 오름차순으로 정렬합니다.

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

호텔을 search.score 및 등급의 내림차순으로 정렬한 다음, 지정된 좌표로부터의 거리 오름차순으로 정렬하여 등급이 같은 두 호텔 사이에서는 가장 가까운 호텔이 먼저 나열되도록 합니다.

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>지원되지 않는 OData 구문

-   산술 식  

-   함수(거리 및 교차 지리 공간적 함수 제외)  

-   임의의 람다 식을 사용한 `any/all`  

## <a name="see-also"></a>참고 항목  

+ [Azure Search의 패싯 탐색](search-faceted-navigation.md) 
+ [Azure Search의 필터](search-filters.md) 
+ [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)   
