---
title: Full Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: Lucene Azure Cognitive Search 서비스에서 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 및 와일드 카드 검색을 위한 쿼리 구문입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9d3f8208af9d5997f5a9e025a54b54b5b035fb85
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934976"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"Full" Lucene 검색 구문 사용 (Azure Cognitive Search의 고급 쿼리)

Azure Cognitive Search에 대 한 쿼리를 생성 하는 경우 기본 [단순 쿼리 파서](query-simple-syntax.md) 를 [azure Cognitive Search의 더 광범위 한 Lucene 쿼리 파서로](query-lucene-syntax.md) 대체 하 여 특수화 된 고급 쿼리 정의를 작성할 수 있습니다. 

Lucene 파서는 필드 범위 쿼리, 유사 항목 검색, 중 위 및 접미사 와일드 카드 검색, 근접 검색, 용어 상승, 정규식 검색 등의 복잡 한 쿼리 구문을 지원 합니다. 성능이 늘어나면 처리 요구 사항도 늘어나므로 실행 시간이 약간 길어진다고 예상해야 합니다. 이 문서에서는 전체 구문을 사용할 때 사용할 수 있는 쿼리 작업을 보여 주는 예제를 단계별로 진행할 수 있습니다.

> [!Note]
> 전체 Lucene 쿼리 구문을 통해 사용하도록 설정된 특수화된 쿼리 구문 대부분이 [텍스트 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않으므로 형태소 분석 또는 기본형 분석을 예상한 경우에 당황할 수 있습니다. 어휘 분석은 완전한 용어(용어 쿼리 또는 구 쿼리)에만 수행됩니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 부분 쿼리 용어에 대해 수행 되는 유일한 변환은 소문자로입니다. 
>

## <a name="formulate-requests-in-postman"></a>Postman에서 요청 작성

다음 예제는 [City of New York OpenData](https://opendata.cityofnewyork.us/) 이니셔티브에서 제공하는 데이터 세트에 기반하여 사용 가능한 작업으로 구성된 NYC Jobs 검색 인덱스를 활용합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 인덱스는 Microsoft에서 제공 하는 샌드박스 서비스에 있습니다. 즉, 이러한 쿼리를 사용해 볼 수 있는 Azure 구독 또는 Azure Cognitive Search 필요 하지 않습니다.

따라서 GET에서 HTTP 요청을 실행하기 위한 Postman 또는 동급의 도구만 있으면 됩니다. 자세한 내용은 [REST 클라이언트로 탐색](search-get-started-postman.md)을 참조하세요.

### <a name="set-the-request-header"></a>요청 헤더 설정

1. 요청 헤더에서 **Content-Type**을 `application/json`으로 설정합니다.

2. **api-key**를 추가한 후 `252044BE3886FE4A8E3BAA4F595114BB` 문자열로 설정합니다. 이것은 NYC Jobs 인덱스를 호스트하는 샌드박스 Search 서비스용 쿼리 키입니다.

요청 헤더를 지정한 후 **search=** 문자열만 교환하여 이 문서의 모든 쿼리에 다시 사용할 수 있습니다. 

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>요청 URL 설정

Request는 Azure Cognitive Search 끝점 및 검색 문자열을 포함 하는 URL과 쌍을 이루는 GET 명령입니다.

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL 구성에는 다음 요소가 있습니다.

+ **`https://azs-playground.search.windows.net/`** 는 Azure Cognitive Search development 팀에서 유지 관리 하는 샌드박스 검색 서비스입니다. 
+ **`indexes/nycjobs/`** 는 해당 서비스의 인덱스 컬렉션에 있는 NYC Jobs 인덱스입니다. 요청에는 서비스 이름과 인덱스가 둘 다 필요합니다.
+ **`docs`** 검색 가능한 모든 콘텐츠를 포함 하는 documents 컬렉션입니다. 요청 헤더에 제공되는 쿼리 api-key는 문서 컬렉션을 대상으로 하는 읽기 작업에만 작동합니다.
+ **`api-version=2020-06-30`** 모든 요청에서 필수 매개 변수인 api-version을 설정 합니다.
+ **`search=*`** 초기 쿼리에서 null 인 쿼리 문자열이 며, 기본적으로 첫 번째 50 결과를 반환 합니다.

## <a name="send-your-first-query"></a>첫 번째 쿼리 전송

확인 단계에서 다음 요청을 GET에 붙여넣고 **보내기**를 클릭합니다. 결과는 자세한 JSON 문서로 반환됩니다. 모든 필드와 모든 값을 볼 수 있는 전체 문서가 반환 됩니다.

이 URL을 REST 클라이언트에 유효성 검사 단계로 붙여넣고 문서 구조를 봅니다.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

쿼리 문자열는 **`search=*`** null 또는 빈 검색에 해당 하는 지정 되지 않은 검색입니다. 가장 간단한 검색을 수행할 수 있습니다.

필요에 따라 URL에 추가 하 여 **`$count=true`** 검색 조건과 일치 하는 문서 수를 반환할 수 있습니다. 빈 검색 문자열에서는 인덱스(NYC 작업의 경우 2800)에 있는 모든 문서입니다.

## <a name="how-to-invoke-full-lucene-parsing"></a>전체 Lucene 구문 분석을 호출하는 방법

**queryType=full**을 추가하여 전체 쿼리 구문을 호출하고 기본 단순 쿼리 구문을 재정의합니다. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

이 문서의 모든 예제는 전체 구문을 나타내는 **queryType=full** 검색 매개 변수가 Lucene 쿼리 파서에서 처리되도록 지정합니다. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>예제 1: 필드 목록으로 범위가 지정 된 쿼리

이 첫 번째 예제는 Lucene에 한정 되지 않지만, 첫 번째 기본 쿼리 개념인 필드 범위를 소개 하는 데 사용 됩니다. 이 예에서는 전체 쿼리와 응답의 범위를 몇 개의 특정 필드로 지정 합니다. 도구가 Postman 또는 Search 탐색기인 경우 판독 가능한 JSON 응답을 구성하는 방법을 파악하는 것이 중요합니다. 

간단히 하기 위해 쿼리는 *business_title*만을 대상으로 하며, 직함만 반환되도록 지정합니다. **Searchfields** 매개 변수는 쿼리 실행을 business_title 필드로 제한 하 고 응답에 포함할 필드 **를 지정 합니다** .

### <a name="search-expression"></a>검색 식

```http
&search=*&searchFields=business_title&$select=business_title
```

쉼표로 구분 된 목록에 여러 필드가 있는 동일한 쿼리는 다음과 같습니다.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

쉼표 뒤의 공백은 선택 사항입니다.

> [!Tip]
> 응용 프로그램 코드에서 REST API를 사용 하는 경우 및와 같은 URL 인코딩 매개 변수를 잊지 마십시오 `$select` `searchFields` .

### <a name="full-url"></a>전체 URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  ![Postman 샘플 응답](media/search-query-lucene-examples/postman-sample-results.png)

응답에서 검색 점수를 보았을 수 있습니다. 순위가 없으면 검색이 전체 텍스트 검색이 아니거나 어떤 조건도 적용되지 않기 때문에 균일하게 점수 1이 지정됩니다. 조건 없는 Null 검색의 경우 행은 임의의 순서로 반환됩니다. 실제 검색 조건을 포함 하는 경우 검색 점수가 의미 있는 값으로 증가 하는 것을 볼 수 있습니다.

## <a name="example-2-fielded-search"></a>예제 2: 필드 지정 검색

Full Lucene 구문은 특정 필드에 대 한 개별 검색 식의 범위 지정을 지원 합니다. 이 예제에서는 junior 라는 용어가 포함 된 비즈니스 타이틀을 검색 합니다.

### <a name="search-expression"></a>검색 식

```http
$select=business_title&search=business_title:(senior NOT junior)
```

다음은 여러 필드를 포함 하는 동일한 쿼리입니다.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>전체 URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Postman 샘플 응답](media/search-query-lucene-examples/intrafieldfilter.png)

**FieldName: searchexpression** 구문을 사용 하 여 필드 지정 search 작업을 정의할 수 있습니다. 여기서 검색 식은 단일 단어나 구 또는 괄호 안의 보다 복잡 한 식입니다. 선택적으로 부울 연산자를 사용할 수 있습니다. 몇 가지 예는 다음과 같습니다.

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

이 경우 필드에서 두 개의 고유 위치를 검색 하는 경우 처럼 두 문자열을 단일 엔터티로 평가 하려면 여러 문자열을 따옴표로 묶어야 합니다 `state` . 또한, NOT과 AND와 같이 연산자는 대문자로 표시해야 합니다.

**FieldName: searchExpression** 에 지정 된 필드는 검색 가능한 필드 여야 합니다. 인덱스 특성을 필드 정의에서 사용 하는 방법에 대 한 자세한 내용은 [인덱스 만들기 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) 를 참조 하세요.

> [!NOTE]
> 위의 예에서는 `searchFields` 쿼리의 각 부분에 명시적으로 지정 된 필드 이름이 있으므로 매개 변수를 사용할 필요가 없습니다. 그러나 `searchFields` 일부 부분의 범위가 특정 필드로 지정 되 고 나머지는 여러 필드에 적용 될 수 있는 쿼리를 실행 하려는 경우에도 매개 변수를 사용할 수 있습니다. 예를 들어 쿼리는 `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` 필드에만 일치 하지만 `business_title` 필드와 "external"을 일치 시킵니다 `posting_type` . **FieldName: searchExpression** 에 제공 된 필드 이름이 항상 매개 변수 보다 우선 합니다. 즉, `searchFields` 이 예제에서는 매개 변수에를 포함할 필요가 없습니다 `business_title` `searchFields` .

## <a name="example-3-fuzzy-search"></a>예제 3: 유사 항목 검색

전체 Lucene 구문은 비슷한 구문을 갖는 용어를 일치시키는 유사 항목 검색도 지원합니다. 유사 항목 검색을 수행하려면 편집 거리를 지정하는 0과 2 사이의 값을 선택적 매개 변수로 포함하여 단일 단어의 끝에 물결표`~` 기호를 추가합니다. 예를 들어, `blue~` 또는 `blue~1`은 blue, blues 및 glue를 반환합니다.

### <a name="search-expression"></a>검색 식

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

구는 직접 지원 되지 않지만 구의 구성 요소 부분에서 유사 항목 일치를 지정할 수 있습니다.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>전체 URL

이 쿼리는 “associate”(일부러 맞춤법을 틀림)라는 용어가 포함된 직업을 검색합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![유사 항목 검색 응답](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> 유사 항목 쿼리는 [분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-4-proximity-search"></a>예제 4: 근접 검색
근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, "hotel airport"~5는 문서에서 서로 5개의 단어 내에서 hotel과 airport라는 용어를 찾게 됩니다.

### <a name="search-expression"></a>검색 식

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>전체 URL

이 쿼리에서는 단 한 단어로 구분된 "senior analyst"라는 용어가 포함된 직업을 검색합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![근접 쿼리](media/search-query-lucene-examples/proximity-before.png)

"senior analyst"라는 용어 사이에 단어를 제거하여 다시 시도합니다. 이전 쿼리에서는 10개 문장이 반환되지만 이번 쿼리에서는 8개 문서이 반환됩니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>예제 5: 용어 상승
용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용하십시오. 

### <a name="full-urls"></a>전체 Url

이 “before” 쿼리에서는 *computer analyst* 용어가 포함된 직업을 검색합니다. 여기서 *computer*와 *analyst* 두 단어가 포함된 결과는 없지만, *computer* 직업은 결과의 맨 위에 나옵니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![before 용어 상승](media/search-query-lucene-examples/termboostingbefore.png)

"after" 쿼리에서는 검색을 반복합니다. 이번에는 두 단어가 존재하지 않을 경우 용어 *computer*보다 용어 *analyst*가 포함된 결과를 상승시킵니다. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
위의 쿼리에서 인간이 이해하기 쉬운 버전은 `search=business_title:computer analyst^2`입니다. 작동 가능한 쿼리를 위해 `^2`가 이해하기 좀 더 어려운 `%5E2`로 인코딩됩니다.

  ![after 용어 상승](media/search-query-lucene-examples/termboostingafter.png)

용어 상승은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다. 다음 예제는 차이점을 설명하는 데 도움이 됩니다.

musicstoreindex 예제에서 **genre** 와 같이, 특정 필드에서 일치 항목을 상승시키는 평가 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, "rock^2 electronic"은 **genre** 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 "rock"이라는 검색어가 포함된 문서는 "electronic"이라는 다른 검색어보다 높은 순위로 매겨집니다.

계수 수준을 설정할 때 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 관련성이 더 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수이어야 하지만, 1보다 작을 수 있습니다(예: 0.2).


## <a name="example-6-regex"></a>예제 6: 정규식

정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.

### <a name="search-expression"></a>검색 식

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>전체 URL

이 쿼리에서는 중역 또는 Junior 이라는 용어를 사용 하 여 작업을 검색 `search=business_title:/(Sen|Jun)ior/` 합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex 쿼리](media/search-query-lucene-examples/regex.png)

> [!Note]
> 정규식 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-7-wildcard-search"></a>예제 7: 와일드카드 검색
일반적으로 다중(\*) 또는 단일(?) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

### <a name="search-expression"></a>검색 식

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>전체 URL

이 쿼리에서 programming 및 programmer라는 용어가 있는 직함이 포함된 접두사 'prog'가 포함되어 있는 직업을 검색합니다. 검색의 첫 문자로 * 또는 ? 기호를 사용할 수 없습니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![와일드카드 쿼리](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> 와일드카드 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="next-steps"></a>다음 단계
코드에서 Lucene 쿼리 파서를 지정해 보십시오. 다음 링크에서는 .NET와 REST API 모두에 대한 검색 쿼리를 설정하는 방법에 대해 설명합니다. 링크는 기본 단순 구문을 사용하므로 **queryType**을 지정하려면 이 문서에서 배운 내용을 적용해야 합니다.

* [.NET SDK를 사용 하 여 인덱스 쿼리](./search-get-started-dotnet.md)
* [REST API를 사용 하 여 인덱스 쿼리](./search-get-started-powershell.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [단순 구문 쿼리 예제](search-query-simple-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [전체 Lucene 쿼리 구문](/rest/api/searchservice/lucene-query-syntax-in-azure-search)