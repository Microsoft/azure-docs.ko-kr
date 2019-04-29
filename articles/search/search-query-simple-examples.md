---
title: "\"단순\" 검색 구문-Azure Search를 사용 하 여 쿼리 예제"
description: 전체 텍스트 검색, 필터 검색, 지리적 검색, 패싯 검색 및 Azure Search 인덱스 쿼리에 사용되는 기타 쿼리 문자열에 대한 단순 쿼리 예제입니다.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9b7147971bd320a11606a93ab4d988e924cf93b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61297103"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>"단순" 검색 구문을 사용 하 여 Azure Search의 쿼리 예제

[단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)은 Azure Search 인덱스에 대해 전체 텍스트 검색 쿼리를 실행하기 위해 기본 쿼리 파서를 호출합니다. 단순 쿼리 분석기는 신속하며, Azure Search에서 전체 텍스트 검색, 필터링 및 패싯 검색, 지리적 검색 등의 일반적인 시나리오를 처리합니다. 이 문서에서는 단순 구문을 사용할 때 사용할 수 있는 쿼리 작업을 보여 주는 예제를 단계별로 진행합니다.

대체 쿼리 구문은 유사 항목 및 와일드 카드 검색과 같은 보다 복잡한 쿼리 구조를 지원하며 처리하는 데 추가 시간이 소요될 수 있는 [전체 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)입니다. 자세한 내용 및 전체 구문을 보여 주는 예제에 대해서는 [Lucene 구문 쿼리 예제](search-query-lucene-examples.md)를 참조하세요.

## <a name="formulate-requests-in-postman"></a>Postman에서 요청 작성

다음 예제는 [City of New York OpenData](https://nycopendata.socrata.com/) 이니셔티브에서 제공하는 데이터 세트에 기반하여 사용 가능한 작업으로 구성된 NYC Jobs 검색 인덱스를 활용합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 이 인덱스는 Microsoft에서 제공하는 Sandbox 서비스에 있으며 이러한 쿼리를 시도하기 위해 Azure 구독 또는 Azure Search가 필요하지 않음을 의미합니다.

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

쿼리 문자열 **`search=*`** 은 null에 해당하는 미지정 검색이거나 빈 검색입니다. 이 검색은 그리 유용하지 않지만 할 수 있는 가장 간단한 검색입니다.

필요에 따라 URL에 **`$count=true`** 를 추가하여 검색 조건과 일치하는 문서 수를 반환할 수 있습니다. 빈 검색 문자열에서는 인덱스(NYC 작업의 경우 2800)에 있는 모든 문서입니다.

## <a name="how-to-invoke-simple-query-parsing"></a>단순 쿼리 구문 분석을 호출하는 방법

대화형 쿼리의 경우 어떤 것도 지정할 필요가 없습니다. simple이 기본값입니다. 코드에서 이전에 전체 쿼리 구문에 대해 **queryType=full**을 호출한 경우 **queryType=simple**을 사용하여 기본값을 재설정할 수 있습니다.

## <a name="example-1-field-scoped-query"></a>예제 1: 필드 범위 쿼리

이 첫 번째 예제는 파서 특정적이지는 않지만 Azure에서는 이를 사용하여 첫 번째 기본 쿼리 개념인 포함을 소개하려고 합니다. 이 예제에서는 쿼리 실행 및 몇 가지 특정 필드에 대한 응답의 범위를 지정합니다. 도구가 Postman 또는 Search 탐색기인 경우 판독 가능한 JSON 응답을 구성하는 방법을 파악하는 것이 중요합니다. 

간단히 하기 위해 쿼리는 *business_title*만을 대상으로 하며, 직함만 반환되도록 지정합니다. 이 구문은 business_title 필드로만 쿼리 실행을 제한하며 응답에 포함되는 필드를 지정하도록 **선택**하는 **searchFields**입니다.

### <a name="partial-query-string"></a>일부 쿼리 문자열

```http
searchFields=business_title&$select=business_title&search=*
```

쉼표로 구분 된 목록에서 여러 필드를 사용 하 여 동일한 쿼리는 다음과 같습니다.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>전체 URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  ![Postman 샘플 응답](media/search-query-lucene-examples/postman-sample-results.png)

응답에서 검색 점수를 보았을 수 있습니다. 순위가 없으면 검색이 전체 텍스트 검색이 아니거나 어떤 조건도 적용되지 않기 때문에 균일하게 점수 1이 지정됩니다. 조건 없는 Null 검색의 경우 행은 임의의 순서로 반환됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-look-up-by-id"></a>예 2: ID별 조회

이 예제는 약간 특이하지만, 검색 동작을 평가할 때 결과에 포함되거나 제외된 이유를 이해하기 위해 특정 문서 전체를 확인하려고 할 수 있습니다. 단일 문서 전체를 반환하려면 [조회 작업](https://docs.microsoft.com/rest/api/searchservice/lookup-document)을 사용하여 문서 ID를 전달합니다.

모든 문서에는 고유 식별자가 있습니다. 조회 쿼리를 위한 구문을 사용하려면 사용할 문서를 찾을 수 있도록 먼저 문서 ID 목록을 반환합니다. NYC Jobs의 경우 식별자는 `id` 필드에 저장됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
```

다음 예제는 이전 응답에서서 맨 처음에 나오는 `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5"를 기준으로 특정 문서를 반환하는 조회 쿼리입니다. 다음 쿼리는 선택한 필드만이 아닌 전체 문서를 반환합니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>예 3: 쿼리 필터링

[구문 필터링](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples)은 **검색** 또는 자체로 사용할 수 있는 OData 식입니다. 검색 매개 변수가 없는 독립 실행형 필터는 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기(모든 점수는 1) 및 순위 지정 등이 없습니다. 검색 문자열은 비어 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

이러한 항목을 함께 사용하는 경우 필터가 전체 인덱스에 먼저 적용된 다음 필터의 결과에 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

  ![쿼리 응답 필터링](media/search-query-simple-examples/filtered-query.png)

GET을 사용하여 Postman에서 이 필터링을 시도하려는 경우 이 문자열에 붙여넣을 수 있습니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

필터 및 검색을 결합하는 강력한 다른 방법은 필터 내에서 검색 쿼리를 사용할 수 있는 필터 식의 **`search.ismatch*()`** 을 통하는 것입니다. 이 필터 식은 plan, planner, planning 등의 용어가 포함된 business_title을 선택하려면 *plan*에 대한 와일드 카드를 사용합니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

함수에 대한 자세한 내용은 ["필터 예제"의 search.ismatch](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples)를 참조하세요.

## <a name="example-4-range-filters"></a>예제 4: 범위 필터

범위 필터링은 모든 데이터 형식에 대한 **`$filter`** 식을 통해 지원됩니다. 다음 예제에서는 숫자 및 문자열 필드를 검색합니다. 

데이터 형식은 범위 필터에서 중요하며, 숫자 데이터가 숫자 필드에 있고 문자열 데이터가 문자열 필드에 있으면 가장 잘 작동합니다. Azure Search에서 숫자 문자열을 비교할 수 없으므로 문자열 필드의 숫자 데이터가 범위에 적합하지 않습니다. 

다음 예제는 가독성(숫자 범위, 다음으로 텍스트 범위)에 대한 POST 형식으로 돼 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![숫자 범위에 대한 범위 필터](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![텍스트 범위에 대한 범위 필터](media/search-query-simple-examples/rangefiltertext.png)

GET을 사용하여 Postman에서 이러한 필터를 시도해 볼 수 있습니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> 값 범위에 대한 패싯은 일반적인 검색 애플리케이션 요구 사항입니다. 패싯 탐색 구조용 필터 빌딩에 대한 예제 및 자세한 내용은 [*패싯 탐색 구현 방법*의 "범위 기준 필터"](search-faceted-navigation.md#filter-based-on-a-range)를 참조하세요.

## <a name="example-5-geo-search"></a>예제 5: 지리적 검색

샘플 인덱스에는 위도 및 경도 좌표를 사용한 geo_location 필드가 포함됩니다. 이 예제에서는 시작 지점의 원주 내에서 사용자가 지정하는 임의의 거리(킬로미터 단위)에 문서를 필터링하는 [geo.distance 함수](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples)를 사용합니다. 쿼리 노출 영역을 줄이거나 확장하려면 쿼리(4)에서 마지막 값을 조정할 수 있습니다.

다음 예제는 가독성을 위해 POST 형식으로 돼 있습니다.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
좀 더 읽기 쉬운 결과 대 한 작업 ID, 직책 및 작업 위치를 포함 하도록 검색 결과 잘립니다. 인덱스(Staten 섬에 작업 위치가 있는 경우)의 임의 문서에서 시작 좌표를 가져왔습니다.

GET을 사용하여 Postman에서 이를 시도해 볼 수 있습니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>예제 6: 검색 정확도

용어 쿼리는 단일 용어로, 대부분이 개별적으로 평가될 것입니다. 구 쿼리는 따옴표로 묶인 후 약어 문자열로 평가됩니다. 일치의 정확도는 연산자 및 searchMode로 제어됩니다.

예제 1: **`&search=fire`** 는 모든 일치에 문서 임의 위치에 단어 fire를 포함되는 150개 결과를 반환합니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

예제 2: **`&search=fire department`** 는 2002개 결과를 반환합니다. fire 또는 department를 포함하는 문서가 일치하는 것으로 반환됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

예제 3: **`&search="fire department"`** 는 82개 결과를 반환합니다. 문자열을 따옴표로 묶으면 두 용어에 대해 약어 검색이 수행되고, 인덱스에서 결합된 용어로 구성된 토큰화된 용어에 대해 일치 문서가 검색됩니다. 이것은 **`search=+fire +department`** 와 같은 검색이 동일하지 않은 이유를 설명합니다. 두 용어는 필수하지만 별도로 검색됩니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>예제 7: searchMode를 사용한 부울

간단한 구문은 문자 형태의 부울 연산자를 지원합니다(`+, -, |`). searchMode 매개 변수는 재현율에 좀 더 유리한 `searchMode=any`(조건 중 하나라도 일치할 경우 결과 집합에 문서 포함함) 및 정확도에 좀 더 유리한 `searchMode=all`(모든 조건이 일치해야 함)을 사용하여 정확도와 재현율 간의 절충을 알립니다. 기본값은 `searchMode=any`로, 여러 연산자를 사용하여 쿼리를 누적하고 좁은 범위의 결과 대신 좀 더 넓은 범위의 결과를 가져오는 경우 혼동을 일으킬 수 있습니다. 결과에 특정 용어를 “포함하지 않는” 모든 문서를 포함하게 되는 NOT을 사용할 경우에는 특히 이러한 현상이 두드러집니다.

기본 searchMode (any)를 사용하는 경우 합성어인 "fire department"를 포함하는 문서와 용어 "Metrotech Center"를 포함하지 않는 모든 문서에 해당하는 2,800개 문서가 반환됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![모든 검색 모드](media/search-query-simple-examples/searchmodeany.png)

searchMode를 `all`로 변경하면 조건에 대해 누적 효과가 적용되고 전체 구인 "fire department"를 포함하는 문서에서 Metrotech Center 주소에 직장이 있는 경우를 제외하여 더 적은 결과 집합인 21개 문서가 반환됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![모든 검색 모드](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>예제 8: 결과 구성

여러 매개 변수가 결과 집합에 포함된 필드, 각 일괄 처리로 반환되는 문서의 수 및 정렬 순서를 제어합니다. 이 예제에서는 이전 예제의 일부를 다시 사용하고 **$select** 문과 약어 검색 조건을 사용하여 결과를 특정 필드로 제한한 후 82개 일치 항목을 반환합니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
이전 예제에 덧붙여, 제목을 기준으로 정렬할 수 있습니다. civil_service_title이 인덱스에서 *정렬 가능*하므로 이 정렬을 사용할 수 있습니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

페이징 결과가 **$top** 매개 변수를 사용하여 구현되어, 상위 5개 문서가 반환됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

다음 5개 문서를 가져오려면 첫 번째 일괄 처리를 건너뜁니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>다음 단계
코드에서 쿼리를 지정합니다. 다음 링크에서는 기본 단순 구문을 사용하여 .NET와 REST API 모두에 대한 검색 쿼리를 설정하는 방법에 대해 설명합니다.

* [.NET SDK를 사용하여 Azure Search 인덱스 쿼리](search-query-dotnet.md)
* [REST API를 사용하여 Azure Search 인덱스 쿼리](search-create-index-rest-api.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [전체 Lucene 쿼리](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [필터 및 Orderby 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
