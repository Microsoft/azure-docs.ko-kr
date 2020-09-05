---
title: Lucene 쿼리 구문
titleSuffix: Azure Cognitive Search
description: 와일드 카드, 유사 항목 검색, RegEx 및 기타 고급 쿼리 구문에 대 한 Azure Cognitive Search에 사용 되는 전체 Lucene 쿼리 구문에 대 한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
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
ms.openlocfilehash: 6ea8bc2551df4f85e4b856dc9cf1c06a9bd571fd
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923452"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 Lucene 쿼리 구문

특수 쿼리 형식에 대 한 리치 [Lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 구문을 기반으로 Azure Cognitive Search에 대 한 쿼리를 작성할 수 있습니다. 와일드 카드, 유사 항목 검색, 근접 검색, 정규식은 몇 가지 예입니다. 모든 Lucene 쿼리 파서 구문은 azure [Cognitive Search에서 그대로 구현](search-lucene-query-architecture.md)되며, 식을 통해 azure Cognitive Search에 생성 되는 *범위 검색* 을 제외 합니다 `$filter` . 

> [!NOTE]
> Full Lucene 구문은 [검색 문서](/rest/api/searchservice/search-documents) api의 **검색** 매개 변수에 전달 된 쿼리 식에 사용 되며, 해당 api의 [$filter](search-filters.md) 매개 변수에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 되지 않습니다. 이러한 다른 구문에는 쿼리를 생성 하 고 문자열을 이스케이프 처리 하는 데 사용할 수 있는 고유 규칙이 있습니다.

## <a name="invoke-full-parsing"></a>전체 구문 분석 호출

`queryType` 검색 매개 변수를 설정하여 사용할 파서를 지정합니다. 유효한 값에는 기본값이 `simple`인 `simple|full`과 Lucene용 `full`이 포함됩니다. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>전체 구문을 보여 주는 예제

다음 예제에서는 `queryType=full` 매개 변수에 명확하게 나타나는 Lucene 쿼리 구문을 사용하여 인덱스에서 문서를 찾습니다. 이 쿼리는 범주 필드에 “예산"이라는 용어가 포함된 호텔 및 “최근 혁신된" 문구가 포함된 모든 검색 가능 필드를 반환합니다. 용어 boost 값(3)의 결과로 "최근 혁신된" 문구가 포함된 문서가 더 높은 순위가 됩니다.  

`searchMode=all` 매개 변수가 이 예제에 해당 합니다. 쿼리에서 연산자를 사용할 때마다 일반적으로 `searchMode=all`을 설정하여 *모든* 조건이 일치하는지 확인해야 합니다.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2020-06-30&querytype=full
```

 또는 다음과 같이 POST를 사용합니다.  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

추가 예제는 [Azure Cognitive Search에서 쿼리를 작성 하기 위한 Lucene 쿼리 구문 예제](search-query-lucene-examples.md)를 참조 하세요. 쿼리 매개 변수를 완전히 지정 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search REST API&#41;&#40;문서 검색 ](/rest/api/searchservice/Search-Documents)을 참조 하세요.

> [!NOTE]  
>  Azure Cognitive Search는 간단한 키워드 검색에 사용할 수 있는 단순 하 고 강력한 쿼리 언어인 [간단한 쿼리 구문도](query-simple-syntax.md)지원 합니다.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 구문 기본 사항  

다음 구문 기초는 Lucene 구문을 사용 하는 모든 쿼리에 적용 됩니다.  

### <a name="operator-evaluation-in-context"></a>컨텍스트의 연산자 평가

배치에 따라 기호가 연산자로 해석될지 아니면 단지 문자열의 다른 문자로 해석될지가 결정됩니다.

예를 들어, Lucene 전체 구문의 경우, 유사 항목 검색 및 근접 검색에 물결표(~)를 사용합니다. 따옴표로 묶은 구 다음에 ~를 배치하면 근접 검색을 호출합니다. 용어 끝에 ~를 배치하면 유사 항목 검색을 호출합니다.

"business~analyst"와 같은 용어 내에서 해당 문자는 연산자로 평가되지 않습니다. 이 경우 쿼리가 단어 또는 구 쿼리라고 가정할 경우 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 사용한 [전체 텍스트 검색](search-lucene-query-architecture.md)은 ~를 제거하고 용어 "business~analyst"를 business OR analyst의 두 용어로 분할합니다.

위의 예제는 물결표(~)이지만 모든 연산자에 동일한 원칙이 적용됩니다.

### <a name="escaping-special-characters"></a>특수 문자 이스케이프

검색 텍스트의 일부로 검색 연산자를 사용 하려면 단일 백슬래시 ()를 접두사로 사용 하 여 문자를 이스케이프 `\` 합니다. 예를 들어에서 와일드 카드 검색의 경우 `https://` `://` 는 쿼리 문자열의 일부 이며은를 지정 `search=https\:\/\/*` 합니다. 마찬가지로 이스케이프 된 전화 번호 패턴은 다음과 같을 수 있습니다 `\+1 \(800\) 642\-7676` .

이스케이프를 필요로 하는 특수 문자는 다음과 같습니다.  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 이스케이프는 토큰을 함께 유지 하지만 인덱싱 중에 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis) 은 제거 될 수 있습니다. 예를 들어 표준 Lucene 분석기는 하이픈, 공백 및 기타 문자에서 단어를 구분 합니다. 쿼리 문자열에 특수 문자가 필요한 경우 인덱스에 해당 문자가 유지 되는 분석기가 필요할 수 있습니다. 일부 옵션에는 하이픈을 넣은 단어를 유지 하는 Microsoft 자연어 [분석기](index-add-language-analyzers.md)또는 보다 복잡 한 패턴을 위한 사용자 지정 분석기가 포함 됩니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자](search-query-partial-matching.md)를 참조 하세요.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어, ' # '은 URL의 조각/앵커 식별자 이므로 안전 하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. ' & ' 및 ' = '는 매개 변수를 구분 하 고 Azure Cognitive Search에서 값을 지정 하는 예약 된 문자의 예입니다. 자세한 내용은 [RFC1738: URL (Uniform Resource locator)](https://www.ietf.org/rfc/rfc1738.txt) 을 참조 하세요.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 쿼리 크기 제한

 Azure Cognitive Search에 보낼 수 있는 쿼리 크기에는 제한이 있습니다. 특히, 최대 1024개 절(AND, OR 등으로 구분된 식)을 사용할 수 있습니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다.  

### <a name="precedence-operators-grouping"></a>선행 연산자 (그룹화)

 괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi||luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

필드 그룹화는 유사하지만 그룹화 범위가 단일 필드입니다. 예를 들어, `hotelAmenities:(gym+(wifi||pool))`은 필드 "hotelAmenities"에서 "gym"과 "wifi" 또는 "gym"과 "pool"을 검색합니다.  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a> 부울 검색

 항상 텍스트 부울 연산자(AND, OR, NOT)는 모두 대문자로 지정합니다.  

### <a name="or-operator-or-or-"></a>OR 연산자 `OR` 또는 `||`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi || luxury`는 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다. OR은 기본 결합 연산자이므로 `wifi luxury`가 `wifi || luxury`와 동일한 것처럼 생략할 수도 있습니다.

### <a name="and-operator-and--or-"></a>AND 연산자 `AND`, `&&` 또는 `+`

AND 연산자는 앰퍼샌드 또는 더하기 기호입니다. 예를 들어, `wifi && luxury`는 "wifi"와 "luxury"를 둘 다 포함하는 문서를 검색합니다. 더하기 문자(+)는 필수 용어에 사용됩니다. 예를 들어, `+wifi +luxury`는 두 용어가 단일 문서의 필드에 나타나야 한다고 명시합니다.

### <a name="not-operator-not--or--"></a>NOT 연산자 `NOT`, `!` 또는 `-`

NOT 연산자는 빼기 기호입니다. 예를 들어는 `wifi –luxury` 및/를 포함 하는 문서를 검색 하거나 없는 문서를 검색 `wifi` `luxury` 합니다.

쿼리 요청에 대 한 **Searchmode** 매개 변수는 not 연산자를 사용 하는 용어가 쿼리에서 다른 용어를 사용 하 여 And 또는 ORed 여부를 제어 합니다 ( `+` 다른 조건에 or 연산자가 없는 것으로 가정 `|` ). 유효한 값은 `any`나 `all`입니다.

`searchMode=any` 더 많은 결과를 포함 하 여 쿼리 회수를 향상 시키고 기본적으로 `-` "OR NOT"으로 해석 됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다.

`searchMode=all` 더 작은 결과를 포함 하 여 쿼리의 전체 자릿수를 늘리고 기본적으로-는 "AND NOT"으로 해석 됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이러한 동작이 `-` 연산자의 좀 더 간단한 동작일 것입니다. 따라서 `searchMode=all` `searchMode=any` 회수 대신 전체 자릿수에 대 한 검색을 최적화 하 *고* 사용자가 `-` 검색에서 연산자를 자주 사용 하는 경우 대신를 사용 하는 것을 고려해 야 합니다.

**Searchmode** 설정을 결정할 때 다양 한 응용 프로그램의 쿼리에 대 한 사용자 상호 작용 패턴을 고려 합니다. 정보를 검색 하는 사용자는 더 많은 기본 제공 탐색 구조를 포함 하는 전자 상거래 사이트와는 달리 쿼리에 연산자를 포함할 가능성이 높습니다.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 필드 지정 검색

구문을 사용 하 여 필드 지정 search 작업을 정의할 수 있습니다 `fieldName:searchExpression` . 여기서 검색 식은 단일 단어나 구 또는 괄호 안의 보다 복잡 한 식입니다. 선택적으로 부울 연산자를 사용할 수 있습니다. 몇 가지 예는 다음과 같습니다.  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

두 문자열이 단일 엔터티로 평가되길 원하는 경우(이 경우에 `artists` 필드에서 두 개의 다른 예술가 검색) 여러 문자열을 인용 부호로 묶어야 합니다.  

`fieldName:searchExpression`에 지정한 필드는 `searchable` 필드여야 합니다.  필드 정의에서 인덱스 특성이 사용되는 방법에 대한 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.  

> [!NOTE]
> 필드 지정 검색 식을 사용할 때 `searchFields` 각 필드 지정 검색 식에 명시적으로 지정 된 필드 이름이 있으므로 매개 변수를 사용할 필요가 없습니다. 그러나 `searchFields` 일부 부분의 범위가 특정 필드로 지정 되 고 나머지는 여러 필드에 적용 될 수 있는 쿼리를 실행 하려는 경우에도 매개 변수를 사용할 수 있습니다. 예를 들어 쿼리는 필드와 일치 하지만 `search=genre:jazz NOT history&searchFields=description` `jazz` 필드와 일치 하지 않습니다 `genre` `NOT history` `description` . 에서 제공 하는 필드 이름이 `fieldName:searchExpression` 항상 `searchFields` 매개 변수 보다 우선적으로 적용 됩니다. 따라서이 예제에서는 `genre` 매개 변수에를 포함할 필요가 없습니다 `searchFields` .

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> 유사 항목 검색

유사 항목 검색은 유사한 구성이 있는 일치 항목을 찾아 두 개 이하의 거리 기준을 충족 하는 최대 50의 용어로 확장 합니다. 자세한 내용은 [유사 항목 검색](search-query-fuzzy.md)을 참조 하세요.

 유사 항목 검색을 수행하려면 한 단어의 끝에 물결표("~") 기호를 붙입니다. 그리고 선택적으로 편집 거리를 지정하는 0과 2(기본값) 사이의 수를 매개 변수로 붙입니다. 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

 유사 항목 검색은 구가 아니라 용어에만 적용할 수 있지만 각 용어에 물결표를 여러 부분으로 구성 된 이름이 나 구에 추가할 수 있습니다. 예를 들어 "Unviersty ~ of ~" Wshington ~ "는" 대학 대학 "과 일치 합니다.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 근접 검색

근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, `"hotel airport"~5`는 문서에서 서로 5개의 단어 내에서 “hotel”과 “airport”라는 용어를 찾게 됩니다.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 용어 상승

용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 이것은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다.  

다음 예제는 차이점을 설명하는 데 도움이 됩니다. [musicstoreindex 예제](index-add-scoring-profiles.md#bkmk_ex)에서 *genre*와 같이 특정 필드에서 일치 항목을 상승시키는 점수 매기기 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, `rock^2 electronic`은 genre 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 *rock*이라는 검색어가 포함된 문서는 *electronic*이라는 다른 검색어보다 높은 순위로 매겨집니다.  

 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용합니다. 또한 구를 상승시킬 수도 있습니다. 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 더 관련성이 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수여야 하지만, 1 미만일 수도 있습니다(예: 0.20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> 정규식 검색  
 정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)에 설명 된 대로 Apache Lucene에서 유효한 패턴을 기준으로 일치 하는 항목을 찾습니다. Azure Cognitive Search에서 정규식은 슬래시로 묶여 `/` 있습니다.

 예를 들어 "motel" 또는 "호텔"를 포함하는 문서를 찾으려면 `/[mh]otel/`을 지정합니다. 정규식 검색은 단일 단어를 기준으로 일치 항목을 찾습니다.

일부 도구 및 언어는 추가 이스케이프 문자 요구 사항을 적용 합니다. JSON의 경우 슬래시를 포함 하는 문자열은 백슬래시를 사용 하 여 이스케이프 됩니다. "microsoft.com/azure/"는 `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` 가 정규식을 설정 하 고 `microsoft.com\/azure\/` 이스케이프 된 슬래시가 있는 문자열입니다.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> 와일드 카드 검색

일반적으로 여러 ( `*` ) 또는 단일 ( `?` ) 문자 와일드 카드 검색에 인식 된 구문을 사용할 수 있습니다. 예를 들어의 쿼리 식은 `search=alpha*` "영숫자" 또는 "사전순"을 반환 합니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

Full Lucene 구문은 접두사, 중 위 및 접미사 일치를 지원 합니다. 그러나 접두사 일치가 필요한 경우 간단한 구문을 사용할 수 있습니다 (접두사 일치는 양쪽에서 지원 됨).

접미사 일치 ( `*` `?` 의 경우 `search=/.*numeric./` ) 또는 중 위 일치는 전체 Lucene 구문 뿐만 아니라 정규식 슬래시 구분 기호를 사용 해야 `/` 합니다. 검색의 첫 문자로 * 또는 ? 단어의 첫 번째 문자 또는 용어 내의 기호 (제외) `/` 입니다. 

> [!NOTE]  
> 규칙으로 패턴 일치는 속도가 느리므로 용어에서 문자 시퀀스에 대 한 토큰을 만드는 edge n-영문법 토큰화와 같은 대체 메서드를 탐색 하는 것이 좋습니다. 인덱스는 더 크지만 인덱싱 중인 문자열의 패턴 생성 및 길이에 따라 쿼리가 더 빠르게 실행 될 수 있습니다.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>분석기의 와일드 카드 쿼리 영향

쿼리 구문 분석 중에 접두사, 접미사, 와일드 카드 또는 정규식으로 작성 된 쿼리는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 우회 하 여 쿼리 트리에 그대로 전달 됩니다. 쿼리에 지정 된 형식의 문자열이 인덱스에 포함 된 경우에만 일치가 검색 됩니다. 대부분의 경우 부분 용어 및 패턴 일치가 성공 하도록 문자열 무결성을 유지 하는 인덱싱 중에 분석기가 필요 합니다. 자세한 내용은 [Azure Cognitive Search 쿼리에서 부분 용어 검색](search-query-partial-matching.md)을 참조 하세요.

검색 쿼리 ' terminat * '에서 ' terminate ', ' 종료 ' 및 ' 종료 '와 같은 용어를 포함 하는 결과를 반환 하도록 하려는 경우를 고려해 보세요.

En-us (영어 Lucene) 분석기를 사용 하는 경우 각 용어의 적극적 형태소 분석을 적용 합니다. 예를 들어 ' terminate ', ' 종료 ', ' 종료 '는 모두 인덱스의 ' termi ' 토큰으로 토큰화 됩니다. 반면, 와일드 카드 또는 유사 항목 검색을 사용 하는 쿼리의 용어는 전혀 분석 되지 않으므로 ' terminat * ' 쿼리와 일치 하는 결과가 없습니다.

다른 측면에서 Microsoft 분석기 (이 경우에는 en-us)는 약간 더 고급 이며 형태소 분석 대신 분류 정리를 사용 합니다. 즉, 생성 된 모든 토큰은 유효한 영어 단어 여야 합니다. 예를 들어 ' terminate ', ' terminate ' 및 ' 종료 '는 대부분 인덱스에서 전반적으로 유지 되며 와일드 카드 및 유사 항목 검색에 많은 영향을 주는 시나리오에 적합 합니다.

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a> 와일드카드 및 정규식 쿼리 점수 매기기

Azure Cognitive Search는 텍스트 쿼리에 대해 빈도 기반 점수 매기기 ([TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf))를 사용 합니다. 그러나 용어 범위가 광범위할 수 있는 와일드카드 및 정규식 쿼리의 경우 순위 오류가 발생하여 더 드물게 나오는 용어가 검색되지 않도록 빈도 계수가 무시됩니다. 모든 일치 항목은 와일드카드 및 정규식에 대한 동일하게 처리됩니다.

## <a name="see-also"></a>참조

+ [단순 검색을 위한 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색에 대 한 쿼리 예제](search-query-lucene-examples.md)
+ [문서 검색](/rest/api/searchservice/Search-Documents)
+ [필터 및 정렬을 위한 OData 식 구문](query-odata-filter-orderby-syntax.md)   
+ [Azure Cognitive Search의 단순 쿼리 구문](query-simple-syntax.md)