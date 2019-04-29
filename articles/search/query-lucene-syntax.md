---
title: Lucene 쿼리 구문 - Azure Search
description: Azure Search에서 사용되는 전체 Lucene 구문에 대한 참조입니다.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
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
ms.openlocfilehash: 64a688df3b6ed8602bb440d72e7f061c5f5893d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317586"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Azure Search의 Lucene 쿼리 구문
특수한 쿼리 형식을 위한 풍부한 [Lucene 쿼리 파서](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 구문(예: 와일드카드, 유사 항목 검색, 근접 검색, 정규식)을 기준으로 Azure Search에 대한 쿼리를 작성할 수 있습니다. Lucene 쿼리 파서 구문의 상당 부분이 [Azure Search에서 그대로 구현](search-lucene-query-architecture.md)됩니다. 물론, Azure Search에서 `$filter` 식을 통해 생성되는 *범위 검색*과 같은 예외도 있습니다. 

## <a name="how-to-invoke-full-parsing"></a>전체 구문 분석을 호출하는 방법

`queryType` 검색 매개 변수를 설정하여 사용할 파서를 지정합니다. 유효한 값에는 기본값이 `simple`인 `simple|full`과 Lucene용 `full`이 포함됩니다. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>전체 구문을 보여 주는 예제

다음 예제에서는 `queryType=full` 매개 변수에 명확하게 나타나는 Lucene 쿼리 구문을 사용하여 인덱스에서 문서를 찾습니다. 이 쿼리는 범주 필드에 “예산"이라는 용어가 포함된 호텔 및 “최근 혁신된" 문구가 포함된 모든 검색 가능 필드를 반환합니다. 용어 boost 값(3)의 결과로 "최근 혁신된" 문구가 포함된 문서가 더 높은 순위가 됩니다.  

`searchMode=all` 매개 변수가 이 예제에 해당 합니다. 쿼리에서 연산자를 사용할 때마다 일반적으로 `searchMode=all`을 설정하여 *모든* 조건이 일치하는지 확인해야 합니다.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full
```

 또는 다음과 같이 POST를 사용합니다.  

```
POST /indexes/hotels/docs/search?api-version=2015-02-28
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

추가 예제에 대해서는 [Azure Search에서 쿼리를 만들기 위한 Lucene 쿼리 구문 예제](search-query-lucene-examples.md)를 참조하세요. 쿼리 매개 변수의 전체 불확정을 지정하는 방법에 대한 자세한 내용은 [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)를 참조하세요.

> [!NOTE]  
>  Azure Search는 간단한 키워드 검색에 사용할 수 있는 단순하면서 강력한 쿼리 언어인 [단순 쿼리 구문](query-simple-syntax.md)도 지원합니다.  

##  <a name="bkmk_syntax"></a> 구문 기본 사항  
 다음 구문 기본 사항은 Lucene 구문을 사용하는 모든 쿼리에 적용됩니다.  

### <a name="operator-evaluation-in-context"></a>컨텍스트의 연산자 평가

배치에 따라 기호가 연산자로 해석될지 아니면 단지 문자열의 다른 문자로 해석될지가 결정됩니다.

예를 들어, Lucene 전체 구문의 경우, 유사 항목 검색 및 근접 검색에 물결표(~)를 사용합니다. 따옴표로 묶은 구 다음에 ~를 배치하면 근접 검색을 호출합니다. 용어 끝에 ~를 배치하면 유사 항목 검색을 호출합니다.

"business~analyst"와 같은 용어 내에서 해당 문자는 연산자로 평가되지 않습니다. 이 경우 쿼리가 단어 또는 구 쿼리라고 가정할 경우 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 사용한 [전체 텍스트 검색](search-lucene-query-architecture.md)은 ~를 제거하고 용어 "business~analyst"를 business OR analyst의 두 용어로 분할합니다.

위의 예제는 물결표(~)이지만 모든 연산자에 동일한 원칙이 적용됩니다.

### <a name="escaping-special-characters"></a>특수 문자 이스케이프

 검색 텍스트의 일부로 사용하려면 특수 문자를 이스케이프해야 합니다. 백슬래시(\\)를 접두사로 사용하여 이스케이프할 수 있습니다. 이스케이프해야 하는 특수 문자는 다음과 같습니다.  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 예를 들어, 와일드카드 문자를 이스케이프하려면 \\*를 사용합니다.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

 URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어, '#'은 URL에서 조각/앵커 식별자이므로 안전하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. '&' 및 '='는 매개 변수를 구분하고 Azure Search에서 값을 지정하므로 예약된 문자의 예입니다. 자세한 내용은 [RFC1738: URL(Uniform Resource Locator)](https://www.ietf.org/rfc/rfc1738.txt)을 참조하세요.

 안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

### <a name="precedence-operators-grouping-and-field-grouping"></a>우선 순위 연산자: 그룹화 및 필드 그룹화  
 괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi||luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

필드 그룹화는 유사하지만 그룹화 범위가 단일 필드입니다. 예를 들어, `hotelAmenities:(gym+(wifi||pool))`은 필드 "hotelAmenities"에서 "gym"과 "wifi" 또는 "gym"과 "pool"을 검색합니다.  

### <a name="searchmode-parameter-considerations"></a>SearchMode 매개 변수 고려 사항  
 [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)에 설명된 것처럼 `searchMode`가 쿼리에 미치는 영향이 Lucene 쿼리 구문에도 동일하게 적용됩니다. 즉, NOT 연산자와 `searchMode` 연산자를 함께 사용하는 경우, 매개 변수를 설정하는 방법이 미치는 영향을 잘 모를 경우에는 이상하게 보일 수 있는 쿼리 결과가 나타날 수 있습니다. 기본값인 `searchMode=any`를 유지하고 NOT 연산자를 사용하면 연산은 OR 작업으로 계산되므로 “New York” NOT “Seattle”은 시애틀이 아닌 모든 도시를 반환합니다.  

##  <a name="bkmk_boolean"></a> 부울 연산자 (AND, OR, NOT) 
 항상 텍스트 부울 연산자(AND, OR, NOT)는 모두 대문자로 지정합니다.  

### <a name="or-operator-or-or-"></a>OR 연산자 `OR` 또는 `||`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi || luxury`는 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다. OR은 기본 결합 연산자이므로 `wifi luxury`가 `wifi || luxuery`와 동일한 것처럼 생략할 수도 있습니다.

### <a name="and-operator-and--or-"></a>AND 연산자 `AND`, `&&` 또는 `+`

AND 연산자는 앰퍼샌드 또는 더하기 기호입니다. 예를 들어, `wifi && luxury`는 "wifi"와 "luxury"를 둘 다 포함하는 문서를 검색합니다. 더하기 문자(+)는 필수 용어에 사용됩니다. 예를 들어, `+wifi +luxury`는 두 용어가 단일 문서의 필드에 나타나야 한다고 명시합니다.


### <a name="not-operator-not--or--"></a>NOT 연산자 `NOT`, `!` 또는 `-`

NOT 연산자는 느낌표 또는 빼기 기호입니다. 예를 들어, `wifi !luxury`는 “wifi” 용어를 포함 및/또는 “luxury”를 포함하지 않는 문서를 검색합니다. `searchMode` 옵션은 + 또는 || 연산자가 없는 경우 NOT 연산자가 있는 용어가 쿼리의 다른 용어와 AND로 연결될지 또는 OR로 연결될지를 제어합니다. 앞서 설명한 것처럼 `searchMode`를 `any`(기본값) 또는 `all`로 설정할 수 있습니다.

`searchMode=any`를 사용하는 경우 더 많은 결과를 포함하여 쿼리 재현율을 높이고, 기본적으로 -는 "OR NOT"으로 해석됩니다. 예를 들어, `wifi -luxury`는 용어 *wifi*를 포함하는 문서 또는 용어 *luxury*를 포함하지 않는 문서를 검색합니다.

`searchMode=all`을 사용하는 경우 더 적은 수의 결과를 포함하여 쿼리 정확도를 높이고, 기본적으로 -는 "AND NOT"으로 해석됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 `luxury`를 포함하지 않는 문서를 검색합니다. 이러한 동작이 - 연산자의 좀 더 간단한 동작일 것입니다. 따라서 재현율이 아니라 정확도에 따라 최적화하려고 *하며* 사용자가 검색에서 `-` 연산자를 자주 사용하는 경우 `searchMode=any`보다 `searchMode=all`을 선택하는 것이 좋습니다.

##  <a name="bkmk_querysizelimits"></a> 쿼리 크기 제한  
 Azure Search로 보낼 수 있는 쿼리의 크기는 제한됩니다. 특히, 최대 1024개 절(AND, OR 등으로 구분된 식)을 사용할 수 있습니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> 와일드카드 및 정규식 쿼리 점수 매기기
 Azure Search는 텍스트 쿼리에 빈도 기반 점수 매기기([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf))를 사용합니다. 그러나 용어 범위가 광범위할 수 있는 와일드카드 및 정규식 쿼리의 경우 순위 오류가 발생하여 더 드물게 나오는 용어가 검색되지 않도록 빈도 계수가 무시됩니다. 모든 일치 항목은 와일드카드 및 정규식에 대한 동일하게 처리됩니다.

##  <a name="bkmk_fields"></a> 필드 범위 쿼리  
 `fieldname:searchterm` 구조를 지정하여 필드 지정 쿼리 작업을 정의할 수 있습니다. 여기서 필드는 단일 단어이고, 검색어도 선택적으로 부울 연산이 포함된 단일 단어 또는 구입니다. 몇 가지 예제는 다음과 같습니다.   

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

  두 문자열이 단일 엔터티로 평가되길 원하는 경우(이 경우에 `artists` 필드에서 두 개의 다른 예술가 검색) 여러 문자열을 인용 부호로 묶어야 합니다.  

  `fieldname:searchterm`에 지정한 필드는 `searchable` 필드여야 합니다.  필드 정의에서 인덱스 특성이 사용되는 방법에 대한 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.  

##  <a name="bkmk_fuzzy"></a> 유사 항목 검색  
 유사 항목 검색은 용어에서 구조가 유사한 일치 항목을 찾습니다. [Lucene 문서](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)에 따라 유사 항목 검색은 [다메라우-레펜슈타인(Damerau-Levenshtein) 거리](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)에 기반합니다. 유사 항목 검색은 거리 조건을 충족하는 최대 50개 용어로 확장할 수 있습니다. 

 유사 항목 검색을 수행하려면 한 단어의 끝에 물결표("~") 기호를 붙입니다. 그리고 선택적으로 편집 거리를 지정하는 0과 2(기본값) 사이의 수를 매개 변수로 붙입니다. 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

 유사 항목 검색만 적용할 수 없습니다 구 말해 하지만 다중 부분으로 된 이름 또는 구를에서 개별적으로 각 용어에 물결표를 추가할 수 있습니다. 예를 들어, "Unviersty ~의 ~" Wshington ~ "" University of Washington "에 일치 합니다.
 

##  <a name="bkmk_proximity"></a> 근접 검색  
 근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, `"hotel airport"~5`는 문서에서 서로 5개의 단어 내에서 “hotel”과 “airport”라는 용어를 찾게 됩니다.  


##  <a name="bkmk_termboost"></a> 용어 상승  
 용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 이것은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다.  

다음 예제는 차이점을 설명하는 데 도움이 됩니다. [musicstoreindex 예제](index-add-scoring-profiles.md#bkmk_ex)에서 *genre*와 같이 특정 필드에서 일치 항목을 상승시키는 점수 매기기 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, `rock^2 electronic`은 genre 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 *rock*이라는 검색어가 포함된 문서는 *electronic*이라는 다른 검색어보다 높은 순위로 매겨집니다.  

 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용합니다. 또한 구를 상승시킬 수도 있습니다. 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 더 관련성이 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수여야 하지만, 1 미만일 수도 있습니다(예: 0.20).  

##  <a name="bkmk_regex"></a> 정규식 검색  
 정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.  

 예를 들어 "motel" 또는 "호텔"를 포함하는 문서를 찾으려면 `/[mh]otel/`을 지정합니다.  정규식 검색은 단일 단어를 기준으로 일치 항목을 찾습니다.   

##  <a name="bkmk_wildcard"></a> 와일드카드 검색  
 일반적으로 다중(*) 또는 단일(?) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.  

 예를 들어, "notebook" 또는 "notepad"와 같이 접두사가 "note"인 단어를 포함하는 문서를 찾으려면 "note*"를 지정합니다.  

> [!NOTE]  
>  검색의 첫 문자로 * 또는 ? 기호를 사용할 수 없습니다.  
>  와일드카드 검색 쿼리에서는 텍스트 분석이 수행되지 않습니다. 쿼리 타임 때 와일드카드 쿼리 용어는 검색 인덱스의 분석된 용어와 비교 후 확장됩니다.

## <a name="see-also"></a>참고 항목  

+ [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [필터 및 정렬을 위한 OData 식 구문](query-odata-filter-orderby-syntax.md)   
+ [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)   
