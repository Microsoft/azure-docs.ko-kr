---
title: Lucene 쿼리 예제 - Azure Search
description: Lucene은 Azure Search 서비스에서 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 및 와일드카드 검색에 대해 구문을 쿼리합니다.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6f7fce7eab697f6517b351d00595cb02110d3641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61286432"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>"전체" Lucene 검색 구문 (Azure Search에서 고급 쿼리)를 사용 하 여 쿼리 예제

Azure Search에 대한 쿼리를 생성하는 경우 기본 [단순 쿼리 파서](query-simple-syntax.md)를 좀 더 복잡한 [Azure Search의 Lucene 쿼리 파서](query-lucene-syntax.md)로 바꾸어 특수 및 고급 쿼리 정의를 작성할 수 있습니다. 

Lucene 파서는 필드 범위 쿼리, 유사 항목 및 접두사 와일드 카드 검색, 근접 검색, 용어 상승 및 정규식 검색 등 복잡 한 쿼리 구조를 지원 합니다. 성능이 늘어나면 처리 요구 사항도 늘어나므로 실행 시간이 약간 길어진다고 예상해야 합니다. 이 문서에서는 전체 구문을 사용할 때 사용할 수 있는 쿼리 작업을 보여 주는 예제를 단계별로 진행할 수 있습니다.

> [!Note]
> 전체 Lucene 쿼리 구문을 통해 사용하도록 설정된 특수화된 쿼리 구문 대부분이 [텍스트 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않으므로 형태소 분석 또는 기본형 분석을 예상한 경우에 당황할 수 있습니다. 어휘 분석은 완전한 용어(용어 쿼리 또는 구 쿼리)에만 수행됩니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다. 
>

## <a name="formulate-requests-in-postman"></a>Postman에서 요청 작성

다음 예제는 [City of New York OpenData](https://opendata.cityofnewyork.us/) 이니셔티브에서 제공하는 데이터 세트에 기반하여 사용 가능한 작업으로 구성된 NYC Jobs 검색 인덱스를 활용합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 이 인덱스는 Microsoft에서 제공하는 Sandbox 서비스에 있으며 이러한 쿼리를 시도하기 위해 Azure 구독 또는 Azure Search가 필요하지 않음을 의미합니다.

따라서 GET에서 HTTP 요청을 실행하기 위한 Postman 또는 동급의 도구만 있으면 됩니다. 자세한 내용은 [REST 클라이언트로 탐색](search-fiddler.md)을 참조하세요.

### <a name="set-the-request-header"></a>요청 헤더 설정

1. 요청 헤더에서 **Content-Type**을 `application/json`으로 설정합니다.

2. **api-key**를 추가한 후 `252044BE3886FE4A8E3BAA4F595114BB` 문자열로 설정합니다. 이것은 NYC Jobs 인덱스를 호스트하는 샌드박스 Search 서비스용 쿼리 키입니다.

요청 헤더를 지정한 후 **search=** 문자열만 교환하여 이 문서의 모든 쿼리에 다시 사용할 수 있습니다. 

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>요청 URL 설정

요청은 Azure Search 끝점 및 검색 문자열을 포함하는 URL과 연결되는 GET 명령입니다.

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL 구성에는 다음 요소가 있습니다.

+ **`https://azs-playground.search.windows.net/`** 은 Azure Search 개발 팀에서 유지 관리하는 Sandbox Search 서비스입니다. 
+ **`indexes/nycjobs/`** 는 해당 서비스의 인덱스 컬렉션에 있는 NYC 작업 인덱스입니다. 요청에는 서비스 이름과 인덱스가 둘 다 필요합니다.
+ **`docs`** 는 모든 검색 가능한 콘텐츠를 포함하는 문서 컬렉션입니다. 요청 헤더에 제공되는 쿼리 api-key는 문서 컬렉션을 대상으로 하는 읽기 작업에만 작동합니다.
+ **`api-version=2017-11-11`** 은 모든 요청에서 필수 매개 변수인 api-version을 설정합니다.
+ **`search=*`** 는 초기 쿼리에서 null이며 처음 50개 결과(기본값)를 반환하는 쿼리 문자열입니다.

## <a name="send-your-first-query"></a>첫 번째 쿼리 전송

확인 단계에서 다음 요청을 GET에 붙여넣고 **보내기**를 클릭합니다. 결과는 자세한 JSON 문서로 반환됩니다. 모든 필드 및 모든 값을 볼 수 있는 전체 문서가 반환 됩니다.

유효성 검사 단계로 및 문서 구조를 보려면 REST 클라이언트에이 URL을 붙여 넣습니다.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

쿼리 문자열 **`search=*`** 은 null에 해당하는 미지정 검색이거나 빈 검색입니다. 것이 가장 간단한 검색 수행할 수 있습니다.

필요에 따라 URL에 **`$count=true`** 를 추가하여 검색 조건과 일치하는 문서 수를 반환할 수 있습니다. 빈 검색 문자열에서는 인덱스(NYC 작업의 경우 2800)에 있는 모든 문서입니다.

## <a name="how-to-invoke-full-lucene-parsing"></a>전체 Lucene 구문 분석을 호출하는 방법

**queryType=full**을 추가하여 전체 쿼리 구문을 호출하고 기본 단순 쿼리 구문을 재정의합니다. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

이 문서의 모든 예제는 전체 구문을 나타내는 **queryType=full** 검색 매개 변수가 Lucene 쿼리 파서에서 처리되도록 지정합니다. 

## <a name="example-1-field-scoped-query"></a>예제 1: 필드 범위 쿼리

이 첫 번째 예제에서는 Lucene 별 하지만 첫 번째 기본 쿼리 개념을 소개 된 발생 했습니다: 포함 합니다. 이 예제에서는 쿼리 실행 및 몇 가지 특정 필드에 대한 응답의 범위를 지정합니다. 도구가 Postman 또는 Search 탐색기인 경우 판독 가능한 JSON 응답을 구성하는 방법을 파악하는 것이 중요합니다. 

간단히 하기 위해 쿼리는 *business_title*만을 대상으로 하며, 직함만 반환되도록 지정합니다. 이 구문은 business_title 필드로만 쿼리 실행을 제한하며 응답에 포함되는 필드를 지정하도록 **선택**하는 **searchFields**입니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
&search=*&searchFields=business_title&$select=business_title
```

쉼표로 구분 된 목록에서 여러 필드를 사용 하 여 동일한 쿼리는 다음과 같습니다.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>전체 URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  ![Postman 샘플 응답](media/search-query-lucene-examples/postman-sample-results.png)

응답에서 검색 점수를 보았을 수 있습니다. 순위가 없으면 검색이 전체 텍스트 검색이 아니거나 어떤 조건도 적용되지 않기 때문에 균일하게 점수 1이 지정됩니다. 조건 없는 Null 검색의 경우 행은 임의의 순서로 반환됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-intra-field-filtering"></a>예 2: 필드 내 필터링

전체 Lucene 구문은 필드 내의 식을 지원합니다. 이 예제에서는, 하지만 하지 junior senior 라는 용어가 포함 된 비즈니스 제목을 검색합니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

여러 필드를 사용 하 여 동일한 쿼리는 다음과 같습니다.

```http
searchFields=business_title, posting_type&$select=business_title, posting_type&search=business_title:senior+NOT+junior AND posting_type:external
```

### <a name="full-url"></a>전체 URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:senior+NOT+junior
```

  ![Postman 샘플 응답](media/search-query-lucene-examples/intrafieldfilter.png)

**fieldname:searchterm** 구조를 정의하여 필드 지정 쿼리 작업을 정의할 수 있습니다. 여기서 필드는 단일 단어이고, 검색어도 선택적으로 부울 연산이 포함된 단일 단어 또는 구입니다. 몇 가지 예제는 다음과 같습니다. 

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")
* business_title:(senior NOT junior) AND posting_type:external

이 경우에 위치 필드에서 두 개의 다른 도시를 검색하고 있으므로 두 문자열이 단일 엔터티로 평가되길 원하는 경우 여러 문자열을 인용 부호로 묶어야 합니다. 또한, NOT과 AND와 같이 연산자는 대문자로 표시해야 합니다.

**fieldname:searchterm** 에서 지정된 필드는 검색 가능 필드이어야 합니다. 필드 정의에서 인덱스 특성이 사용되는 방법에 대한 자세한 내용은 [인덱스 만들기(Azure Search 서비스 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

## <a name="example-3-fuzzy-search"></a>예 3: 유사 항목 검색

전체 Lucene 구문은 비슷한 구문을 갖는 용어를 일치시키는 유사 항목 검색도 지원합니다. 유사 항목 검색을 수행하려면 편집 거리를 지정하는 0과 2 사이의 값을 선택적 매개 변수로 포함하여 단일 단어의 끝에 물결표`~` 기호를 추가합니다. 예를 들어, `blue~` 또는 `blue~1`은 blue, blues 및 glue를 반환합니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

구 직접 지원 되지 않습니다 하지만 구 구성 요소 부분에 유사 항목 일치를 지정할 수 있습니다.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>전체 URL

이 쿼리는 “associate”(일부러 맞춤법을 틀림)라는 용어가 포함된 직업을 검색합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![유사 항목 검색 응답](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> 유사 항목 쿼리는 [분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-4-proximity-search"></a>예제 4: 근접 검색
근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, "hotel airport"~5는 문서에서 서로 5개의 단어 내에서 hotel과 airport라는 용어를 찾게 됩니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>전체 URL

이 쿼리에서는 단 한 단어로 구분된 "senior analyst"라는 용어가 포함된 직업을 검색합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![근접 쿼리](media/search-query-lucene-examples/proximity-before.png)

"senior analyst"라는 용어 사이에 단어를 제거하여 다시 시도합니다. 이전 쿼리에서는 10개 문장이 반환되지만 이번 쿼리에서는 8개 문서이 반환됩니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>예제 5: 용어 상승
용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용하십시오. 

### <a name="full-urls"></a>전체 Url

이 “before” 쿼리에서는 *computer analyst* 용어가 포함된 직업을 검색합니다. 여기서 *computer*와 *analyst* 두 단어가 포함된 결과는 없지만, *computer* 직업은 결과의 맨 위에 나옵니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![before 용어 상승](media/search-query-lucene-examples/termboostingbefore.png)

"after" 쿼리에서는 검색을 반복합니다. 이번에는 두 단어가 존재하지 않을 경우 용어 *computer*보다 용어 *analyst*가 포함된 결과를 상승시킵니다. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
위의 쿼리에서 인간이 이해하기 쉬운 버전은 `search=business_title:computer analyst^2`입니다. 작동 가능한 쿼리를 위해 `^2`가 이해하기 좀 더 어려운 `%5E2`로 인코딩됩니다.

  ![after 용어 상승](media/search-query-lucene-examples/termboostingafter.png)

용어 상승은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다. 다음 예제는 차이점을 설명하는 데 도움이 됩니다.

musicstoreindex 예제에서 **genre** 와 같이, 특정 필드에서 일치 항목을 상승시키는 평가 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, "rock^2 electronic"은 **genre** 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 "rock"이라는 검색어가 포함된 문서는 "electronic"이라는 다른 검색어보다 높은 순위로 매겨집니다.

계수 수준을 설정할 때 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 관련성이 더 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수이어야 하지만, 1보다 작을 수 있습니다(예: 0.2).


## <a name="example-6-regex"></a>예제 6: Regex

정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>전체 URL

이 쿼리에서 용어 Senior 또는 Junior 중 하나를 사용 하 여 작업에 대 한 검색: `search=business_title:/(Sen|Jun)ior/`합니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex 쿼리](media/search-query-lucene-examples/regex.png)

> [!Note]
> 정규식 쿼리는 [분석](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-7-wildcard-search"></a>예제 7: 와일드카드 검색
일반적으로 다중(\*) 또는 단일(?) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>전체 URL

이 쿼리에서 programming 및 programmer라는 용어가 있는 직함이 포함된 접두사 'prog'가 포함되어 있는 직업을 검색합니다. 검색의 첫 문자로 * 또는 ? 기호를 사용할 수 없습니다.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![와일드카드 쿼리](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> 와일드카드 쿼리는 [분석](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="next-steps"></a>다음 단계
코드에서 Lucene 쿼리 파서를 지정해 보십시오. 다음 링크에서는 .NET와 REST API 모두에 대한 검색 쿼리를 설정하는 방법에 대해 설명합니다. 링크는 기본 단순 구문을 사용하므로 **queryType**을 지정하려면 이 문서에서 배운 내용을 적용해야 합니다.

* [.NET SDK를 사용하여 Azure Search 인덱스 쿼리](search-query-dotnet.md)
* [REST API를 사용하여 Azure Search 인덱스 쿼리](search-create-index-rest-api.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [단순 구문 쿼리 예제](search-query-simple-examples.md)
+ [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [전체 Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)