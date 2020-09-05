---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 전체 텍스트 검색 쿼리에 사용 되는 단순 쿼리 구문에 대 한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923384"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 단순 쿼리 구문

Azure Cognitive Search는 두 가지 Lucene 기반 쿼리 언어 즉, [단순 쿼리 파서와](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) [lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 구현 합니다.

단순 파서는 보다 유연 하며, 완벽 하 게 구성 되지 않은 경우에도 요청을 해석 하려고 합니다. 이러한 유연성으로 인해 Azure Cognitive Search의 쿼리에 대 한 기본값입니다. 

단순 구문은 `search` 같은 검색 문서 API의 [$filter expressions](search-filters.md) 매개 변수에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 하지 않도록 [문서 검색 요청의](/rest/api/searchservice/search-documents)매개 변수에 전달 되는 쿼리 식에 사용 됩니다. `search`및 `$filter` 매개 변수의 구문은 서로 다르며 쿼리를 생성 하 고 문자열을 이스케이프 하는 등의 고유한 규칙을 사용 합니다.

단순 파서는 [Apache Lucene Simple 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 클래스를 기반으로 하지만 Azure Cognitive Search의 구현에서 유사 항목 검색은 제외 됩니다. [유사 항목 검색](search-query-fuzzy.md) 또는 기타 고급 쿼리 양식이 필요한 경우 대신 대체 [전체 Lucene 쿼리 구문을](query-lucene-syntax.md) 사용해 보세요.

## <a name="invoke-simple-parsing"></a>간단한 구문 분석 호출

단순 구문은 기본값입니다. 호출은 전체 구문에서 단순 구문으로 다시 설정하는 경우에만 필요합니다. 구문을 명시적으로 설정하려면 `queryType` 검색 매개 변수를 사용합니다. 유효한 값 `queryType=simple` 은 또는 `queryType=full` `simple` 입니다. 여기서은 기본값 이며 `full` 고급 쿼리를 위해 [full Lucene 쿼리 파서](query-lucene-syntax.md) 를 호출 합니다. 

## <a name="syntax-fundamentals"></a> 구문 기본 사항

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견 된 변형을 비롯 하 여 모든 용어 또는 모든 용어를 포함 하는 문서와 일치 합니다.

이와 같이 간단 하 게 하기 때문에 Azure Cognitive Search에서 예기치 않은 결과를 발생 *시킬 수* 있는 쿼리 실행의 한 가지 측면은 입력 문자열에 추가 된 용어 및 연산자가 추가 될 때 검색 결과를 줄이는 대신 향상 됩니다. 이 확장이 실제로 발생 하는지 여부는 NOT 연산자의 포함 여부에 따라 달라 지 며, AND 또는 OR 동작 측면에서 해석 되는 방법을 결정 하는 **Searchmode** 매개 변수 설정과 결합 되어 있습니다. 자세한 내용은 [NOT 연산자](#not-operator)를 참조하세요.

### <a name="precedence-operators-grouping"></a>선행 연산자 (그룹화)

괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi|luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

필드 그룹화는 유사하지만 그룹화 범위가 단일 필드입니다. 예를 들어, `hotelAmenities:(gym+(wifi|pool))`은 필드 "hotelAmenities"에서 "gym"과 "wifi" 또는 "gym"과 "pool"을 검색합니다.  

### <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

단순 구문에서 검색 연산자는 다음과 같은 문자를 포함 합니다. `+ | " ( ) ' \`  

이러한 문자가 인덱스에 있는 토큰의 일부인 경우 쿼리에서 단일 백슬래시 ()를 접두사로 사용 하 여 이스케이프 합니다 `\` . 예를 들어 전체 용어 토큰화를 위해 사용자 지정 분석기를 사용 하 고 인덱스에 "Luxury + 호텔" 문자열이 포함 되어 있다고 가정 합니다. 이 토큰과 정확히 일치 하는 항목을 가져오려면 이스케이프 문자를 삽입  `search=luxury\+hotel` 합니다. 

보다 일반적인 사례를 단순하게 만들기 위해이 규칙에는 이스케이프가 필요 하지 않은 두 가지 예외가 있습니다.  

+ NOT 연산자는 `-` 공백 뒤의 첫 번째 문자인 경우에만 이스케이프 해야 합니다. 가 `-` 중간에 나타나면 (예:의 경우 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ) 이스케이프를 건너뛸 수 있습니다.

+ 접미사 연산자는 `*` 공백 앞의 마지막 문자인 경우에만 이스케이프 해야 합니다. 가 `*` 중간에 나타나면 (예:의 경우 `4*4=16` ) 이스케이프가 필요 하지 않습니다.

> [!NOTE]  
> 기본적으로 표준 분석기는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)중 하이픈, 공백, 앰퍼샌드 및 기타 문자에서 단어를 삭제 하 고 중단 합니다. 쿼리 문자열에서 특수 문자를 유지 해야 하는 경우 인덱스에 해당 문자를 유지 하는 분석기가 필요할 수 있습니다. 일부 옵션에는 하이픈을 넣은 단어를 유지 하는 Microsoft 자연어 [분석기](index-add-language-analyzers.md)또는 보다 복잡 한 패턴을 위한 사용자 지정 분석기가 포함 됩니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자](search-query-partial-matching.md)를 참조 하세요.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어, ' # '은 URL의 조각/앵커 식별자 이므로 안전 하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. ' & ' 및 ' = '는 매개 변수를 구분 하 고 Azure Cognitive Search에서 값을 지정 하는 예약 된 문자의 예입니다. 자세한 내용은 [RFC1738: URL (Uniform Resource locator)](https://www.ietf.org/rfc/rfc1738.txt) 을 참조 하세요.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

### <a name="querying-for-special-characters"></a>특수 문자 쿼리

일부 경우에는 ' ❤ '가 수 또는 ' € ' 기호와 같은 특수 문자를 검색 하는 것이 좋습니다. 이 경우 사용 하는 분석기에서 해당 문자를 필터링 하지 않도록 합니다.  표준 분석기는 많은 특수 문자를 무시 하므로 인덱스에서 토큰이 되지 않습니다.

따라서 첫 번째 단계는 이러한 요소 토큰을 고려 하는 분석기를 사용 하는지 확인 하는 것입니다. 예를 들어 "공백" 분석기는 공백으로 구분 되는 문자 시퀀스를 토큰으로 간주 하므로 "❤" 문자열은 토큰으로 간주 됩니다. 또한 Microsoft English analyzer ("en-us")와 같은 분석기는 "€" 문자열을 토큰으로 고려 합니다. 분석기를 [테스트](/rest/api/searchservice/test-analyzer) 하 여 지정 된 쿼리에 대해 생성 되는 토큰을 확인할 수 있습니다.

유니코드 문자를 사용 하는 경우 쿼리 url에서 기호가 제대로 이스케이프 되었는지 확인 합니다. 예를 들어 "❤"은 이스케이프 시퀀스를 사용 `%E2%9D%A4+` 합니다. Postman은이 변환을 자동으로 수행 합니다.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 쿼리 크기 제한

 Azure Cognitive Search에 보낼 수 있는 쿼리 크기에는 제한이 있습니다. 특히, 최대 1024개 절(AND, OR 등으로 구분된 식)을 사용할 수 있습니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다.  

## <a name="boolean-search"></a>부울 검색

쿼리 문자열에 부울 연산자 (AND, OR, NOT)를 포함 하 여 일치 하는 문서를 찾을 수 있는 다양 한 조건 집합을 작성할 수 있습니다. 

### <a name="and-operator-"></a>AND 연산자 `+`

AND 연산자는 더하기 기호입니다. 예를 들어, `wifi + luxury`는 `wifi`와 `luxury`를 둘 다 포함하는 문서를 검색합니다.

### <a name="or-operator-"></a>OR 연산자 `|`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi | luxury`는 `wifi` 또는 `luxury` 중 하나를 포함하는 문서를 검색합니다.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 연산자 `-`

NOT 연산자는 빼기 기호입니다. 예를 들어는 `wifi –luxury` 및/를 포함 하는 문서를 검색 하거나 없는 문서를 검색 `wifi` `luxury` 합니다.

쿼리 요청에 대 한 **Searchmode** 매개 변수는 not 연산자를 사용 하는 용어가 쿼리에서 다른 용어를 사용 하 여 And 또는 ORed 여부를 제어 합니다 ( `+` 다른 조건에 or 연산자가 없는 것으로 가정 `|` ). 유효한 값은 `any`나 `all`입니다.

`searchMode=any` 더 많은 결과를 포함 하 여 쿼리 회수를 향상 시키고 기본적으로 `-` "OR NOT"으로 해석 됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다.

`searchMode=all` 더 작은 결과를 포함 하 여 쿼리의 전체 자릿수를 늘리고 기본적으로-는 "AND NOT"으로 해석 됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이러한 동작이 `-` 연산자의 좀 더 간단한 동작일 것입니다. 따라서 `searchMode=all` `searchMode=any` 회수 대신 전체 자릿수에 대 한 검색을 최적화 하 *고* 사용자가 `-` 검색에서 연산자를 자주 사용 하는 경우 대신를 사용 하는 것을 고려해 야 합니다.

**Searchmode** 설정을 결정할 때 다양 한 응용 프로그램의 쿼리에 대 한 사용자 상호 작용 패턴을 고려 합니다. 정보를 검색 하는 사용자는 더 많은 기본 제공 탐색 구조를 포함 하는 전자 상거래 사이트와는 달리 쿼리에 연산자를 포함할 가능성이 높습니다.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>와일드 카드 접두사 일치 (*,?)

"시작" 쿼리의 경우 나머지 용어에 대 한 자리 표시자로 접미사 연산자를 추가 합니다. `*`여러 문자 또는 단일 문자에 대해 별표를 사용 `?` 합니다. 예를 들어 `lingui*` 는 "언어적" 또는 "linguini"에서 일치 하 고 대/소문자를 무시 합니다. 

필터와 마찬가지로 접두사 쿼리는 정확히 일치 하는 항목을 찾습니다. 따라서 관련성 점수 매기기는 없습니다 (모든 결과가 1.0의 검색 점수를 수신 함). 특히 인덱스가 크고 접두사가 작은 수의 문자로 구성 된 경우 접두사 쿼리 속도가 느려질 수 있습니다. Edge n-영문법 토큰화와 같은 대체 방법은 더 빠르게 수행할 수 있습니다.

용어 끝 또는 중간에 대해 접미사 또는 중 위 일치와 같은 기타 와일드 카드 쿼리 변형의 경우 [와일드 카드 검색에 전체 Lucene 구문을](query-lucene-syntax.md#bkmk_wildcard)사용 합니다.

## <a name="phrase-search-"></a>구 검색 `"`

용어 검색은 하나 이상의 용어에 대 한 쿼리로 서 일치 항목으로 간주 됩니다. 구 검색은 따옴표로 묶인 정확한 구입니다 `" "` . 예를 들어, `Roach Motel` (따옴표 제외)는 `Roach` 임의의 순서로 및/또는 모든 위치를 포함 하는 문서를 검색 합니다 `Motel` `"Roach Motel"` . (따옴표 포함)은 해당 전체 구를 포함 하는 문서를 해당 순서 대로 일치 시킵니다 (어휘 분석은 여전히 적용 됨).

## <a name="see-also"></a>참조  

+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 검색을 위한 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색에 대 한 쿼리 예제](search-query-lucene-examples.md)
+ [문서 검색 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [OData 식 구문](query-odata-filter-orderby-syntax.md)