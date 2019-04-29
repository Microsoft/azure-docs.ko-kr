---
title: 쿼리 유형 및 구성 - Azure Search
description: Azure Search에서 검색 쿼리 작성, 매개 변수를 사용하여 결과 필터링, 선택 및 정렬에 대한 기본 사항입니다.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.custom: seodec2018
ms.openlocfilehash: cfc9b44963f6880e97859bc7ab77bff12d258471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283528"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Azure Search에서 쿼리를 작성하는 방법

Azure Search에서 쿼리는 왕복 작업의 전체 사양입니다. 요청의 매개 변수는 인덱스에서 문서를 찾는 일치 조건, 엔진에 대한 실행 지침 및 응답을 형성하는 지시문을 제공합니다. 

쿼리 요청은 풍부한 구문으로, 범위 내 필드, 검색 방법, 반환할 필드, 정렬 또는 필터링 여부 등을 지정할 수 있습니다. 지정하지 않을 경우, 쿼리는 검색 가능한 모든 필드에 대해 전체 텍스트 검색 작업으로 실행되고, 점수가 매겨지지 않은 결과 집합을 임의의 순서로 반환합니다.

## <a name="apis-and-tools-for-testing"></a>테스트를 위한 API 및 도구

다음 표에는 쿼리를 제출하기 위한 API 및 도구 기반 접근 방법이 나와 있습니다.

| 방법 | 설명 |
|-------------|-------------|
| [검색 탐색기(포털)](search-explorer.md) | 검색 표시줄 및 인덱스와 api-version 선택을 위한 옵션을 제공합니다. 결과는 JSON 문서로 반환됩니다. <br/>[자세한 정보](search-get-started-portal.md#query-index) | 
| [Postman 또는 Fiddler](search-fiddler.md) | 웹 테스트 도구는 REST 호출 작성에 적합한 선택 항목입니다. REST API는 Azure Search에서 가능한 모든 작업을 지원합니다. 이 문서에서는 Azure Search로 요청을 전송하기 위해 HTTP 요청 헤더 및 본문을 설정하는 방법을 알아봅니다.  |
| [SearchIndexClient(.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Search 인덱스를 쿼리하는 데 사용할 수 있는 클라이언트입니다.  <br/>[자세한 정보](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Search Documents(REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 인덱스에 대한 GET 또는 POST 메서드로, 추가 입력을 위해 쿼리 매개 변수를 사용합니다.  |

## <a name="a-first-look-at-query-requests"></a>쿼리 요청 소개

예제는 새로운 개념을 소개하는 데 유용합니다. [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)로 작성된 대표 쿼리인 이 예제는 [부동산 데모 인덱스](search-get-started-portal.md)를 대상으로 하고 공통 매개 변수를 포함합니다.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** 은 파서를 설정하는데 Azure Search에서는 이것이 [간단한 기본 쿼리 파서](search-query-simple-examples.md)이거나 고급 쿼리 구문(예: 정규식, 근접 검색, 유사 항목 검색 및 와일드 카드 검색)에 사용되는 [전체 Lucene 쿼리 파서](search-query-lucene-examples.md)일 수 있습니다.

+ **`search`** 는 일치 조건을 제공하며, 일반적으로 텍스트이지만 부울 연산자가 자주 사용됩니다. 독립형 단일 용어는 용어 쿼리입니다. 따옴표로 묶인 여러 부분 쿼리는 핵심 구문 쿼리입니다. 검색은 **`search=*`** 처럼 정의되지 않을 수 있지만 예제에 나타나는 것과 유사한 용어, 구문 및 연산자로 구성될 가능성이 큽니다.

+ **`searchFields`** 는 선택적이며 쿼리 실행을 특정 필드로 제한하는 데 사용됩니다.

응답은 쿼리에 포함된 매개 변수에 의해 형성됩니다. 예제에서 결과 집합은 **`select`** 문에 나열된 필드로 구성됩니다. 이 쿼리에서는 상위 10개 항목만 반환되지만 **`count`** 는 전체적으로 일치하는 문서의 수를 알려줍니다. 이 쿼리에서 행은 daysOnMarket에 따라 정렬됩니다.

Azure Search에서 쿼리 실행은 항상 하나의 인덱스에 대해 수행되며 요청에 제공된 API 키를 사용하여 인증됩니다. REST에서 둘 다 요청 헤더에 제공됩니다.

### <a name="how-to-run-this-query"></a>이 쿼리를 실행하는 방법

이 쿼리를 실행하려면 [검색 탐색기와 부동산 데모 인덱스](search-get-started-portal.md)를 사용합니다. 

이 쿼리 문자열을 탐색기의 검색 창에 붙여넣으면 됩니다.`search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>인덱스로 쿼리 작업을 사용하는 방법

인덱스 디자인과 쿼리 디자인은 Azure Search와 밀접하게 결합됩니다. 먼저 알아야 하는 중요한 사실은 각 필드의 특성과 함께 인덱스 스키마에 의해 빌드할 수 있는 쿼리의 종류가 결정된다는 것입니다. 

필드의 인덱스 특성은 허용된 작업(예: 인덱스에서 필드를 검색할 수 있는지(*searchable*), 결과에서 검색이 가능한지(*retrievable*), 정렬이 가능한지(*sortable*), 필터링이 가능한지(*filterable*) 등)을 설정합니다. 예제 쿼리 문자열에서 `"$orderby": "daysOnMarket"`은 daysOnMarket 필드가 인덱스 스키마에서 정렬 가능(*sortable*)으로 표시되어 있기 때문에 작동합니다. 

![부동산 샘플에 대한 인덱스 정의](./media/search-query-overview/realestate-sample-index-definition.png "부동산 샘플에 대한 인덱스 정의")

위의 스크린샷은 부동산 샘플에 대한 인덱스 특성의 부분 목록입니다. 전체 인덱스 스키마는 포털에서 볼 수 있습니다. 인덱스 특성에 대한 자세한 내용은 [인덱스 REST API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

> [!Note]
> 일부 쿼리 기능은 필드별로 활성화되기 보다는 인덱스 전체에서 사용하도록 설정됩니다. 이러한 기능에 포함 됩니다: [동의어 맵을](search-synonyms.md)를 [사용자 지정 분석기](index-add-custom-analyzers.md)에 [suggester (자동 완성 및 제안 된 쿼리)에 생성](index-add-suggesters.md), [논리 점수 매기기 순위 결과 대 한](index-add-scoring-profiles.md)합니다.

## <a name="elements-of-a-query-request"></a>쿼리 요청의 요소

쿼리는 항상 단일 인덱스에 전달됩니다. 인덱스를 조인할 수도 없고 사용자 지정 또는 임시 데이터 구조를 쿼리 대상으로 만들 수도 없습니다. 

쿼리 요청의 필요한 요소에는 다음 구성 요소가 포함됩니다.

+ 고정 및 사용자 정의 구성 요소를 포함하는 URL로 표현되는 서비스 엔드포인트 및 인덱스 문서 컬렉션: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(REST만 해당), 항상 둘 이상의 API 버전을 사용할 수 있기 때문에 필요합니다. 
+ **`api-key`**(쿼리 또는 관리자 api-key)는 서비스에 대한 요청을 인증합니다.
+ **`queryType`**(단순 또는 전체). 기본 제공되는 간단한 구문을 사용하는 경우 생략할 수 있습니다.
+ **`search`** 또는 **`filter`** 는 일치 조건을 제공하며, 빈 검색을 수행하려면 지정하지 않을 수 있습니다. 두 가지 쿼리 유형 모두 간단한 파서로 설명되지만 고급 쿼리의 경우에도 복잡한 쿼리 식을 전달하기 위한 검색 매개 변수가 필요합니다.

모든 다른 검색 매개 변수는 선택 사항입니다. 전체 특성 목록은 [인덱스 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요. 처리 중에 매개 변수가 어떻게 사용되는지 자세히 알아보려면, [Azure Search에서 전체 텍스트 검색이 작동하는 원리](search-lucene-query-architecture.md)를 참조하세요.

## <a name="choose-a-parser-simple--full"></a>파서 선택: 단순 | 전체

Azure Search는 Apache Lucene을 기반으로 하고 일반적인 쿼리와 특수화된 쿼리를 처리하기 위한 두 개의 쿼리 파서 중에서 선택할 수 있도록 합니다. 단순 파서를 사용하는 요청은 [단순 쿼리 구문](query-simple-syntax.md)을 사용하여 형성되며 자유 형식 텍스트 쿼리에서 속도와 효율성의 기본값으로 선택됩니다. 이 구문은 AND, OR, NOT, 구, 접미사 및 우선 순위 연산자를 포함한 여러 일반 검색 연산자를 지원합니다.

요청에 `queryType=full`을 추가하면 사용되도록 설정되는 [전체 Lucene 쿼리 구문](query-Lucene-syntax.md#bkmk_syntax)은 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)의 일부로 개발된 널리 채택되고 표현적인 쿼리 언어를 공개합니다. 전체 구문은 단순 구문을 확장합니다. 단순 구문에 대해 작성한 쿼리는 전체 Lucene 파서에서 실행됩니다. 

다음 예제는 이런 점을 보여줍니다. 동일한 쿼리이지만 다른 queryType 설정을 사용하면 다른 결과가 나타납니다. 첫 번째 쿼리에서 `^3`은 검색어의 일부로 처리됩니다.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

전체 Lucene 파서를 사용하는 동일한 쿼리는 "ranch"에서 필드 내 부스트를 해석하여 특정 용어가 포함된 결과의 검색 순위를 높입니다.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>쿼리 유형

Azure Search는 광범위한 쿼리 유형을 지원합니다. 

| 쿼리 유형 | 사용 현황 | 예제 및 자세한 정보 |
|------------|--------|-------------------------------|
| 자유 형식 텍스트 검색 | 매개 변수와 파서 중 하나를 검색| 전체 텍스트 검색은 인덱스의 *검색 가능한* 모든 필드에서 하나 이상의 단어를 검색하고 Google 또는 Bing과 같은 검색 엔진이 작동할 것으로 예상되는 방식으로 작동합니다. 소개의 예는 전체 텍스트 검색입니다.<br/><br/>전체 텍스트 검색은 표준 Lucene 분석기(기본값)를 사용하여 모든 용어를 소문자 텍스트로 분석하여 "the"와 같은 중지 단어를 제거합니다. 텍스트 분석을 수정하는 [영어가 아닌 분석기](index-add-language-analyzers.md#language-analyzer-list) 또는 [특수 언어 중립적 분석기](index-add-custom-analyzers.md#AnalyzerTable)로 기본값을 재정의할 수 있습니다. 필드의 전체 내용을 단일 토큰으로 취급하는 [키워드](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)가 예입니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다. | 
| 필터링된 검색 | [OData 필터 식](query-odata-filter-orderby-syntax.md)과 파서 중 하나 | 필터 쿼리는 인덱스의 *필터링 가능한* 모든 필드에 걸쳐 부울 식을 계산합니다. 검색과 달리 필터 쿼리는 문자열 필드에서 대/소문자 구분을 포함하여 필드의 정확한 내용을 검색합니다. 또 다른 차이점은 필터 쿼리는 OData 구문으로 표현된다는 점입니다. <br/>[필터 식 예제](search-query-simple-examples.md#example-3-filter-queries) |
| 지리적 검색 | 필드, 필터 식 및 파서 중 하나에 [Edm.GeographyPoint 유형](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) | Edm.GeographyPoint가 있는 필드에 저장된 좌표는 "내 주변 찾기"또는 지도 기반 검색 컨트롤에 사용됩니다. <br/>[지리적 검색 예제](search-query-simple-examples.md#example-5-geo-search)|
| 범위 검색 | 필터 식 및 단순 파서 | Azure Search에서 범위 쿼리는 필터 매개 변수를 사용하여 작성됩니다. <br/>[범위 필터 예제](search-query-simple-examples.md#example-4-range-filters) | 
| [필드 내 필터링](query-lucene-syntax.md#bkmk_fields) | 매개 변수와 전체 파서 검색 | 단일 필드를 대상으로 복합 쿼리 식을 작성합니다. <br/>[필드 내 필터링 예제](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [유사 항목 검색](query-lucene-syntax.md#bkmk_fuzzy) | 매개 변수와 전체 파서 검색 | 유사한 구조 또는 철자가 포함된 용어를 검색합니다. <br/>[유사 항목 검색 예제](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [근접 검색](query-lucene-syntax.md#bkmk_proximity) | 매개 변수와 전체 파서 검색 | 문서에서 서로 가까이 있는 용어를 찾습니다. <br/>[근접 검색 예제](search-query-lucene-examples.md#example-4-proximity-search) |
| [용어 상승](query-lucene-syntax.md#bkmk_termboost) | 매개 변수와 전체 파서 검색 | 승격된 용어가 포함된 문서는 그렇지 않은 다른 문서보다 상대적으로 높은 순위를 매깁니다. <br/>[용어 상승 예제](search-query-lucene-examples.md#example-5-term-boosting) |
| [정규식 검색](query-lucene-syntax.md#bkmk_regex) | 매개 변수와 전체 파서 검색 | 정규식의 콘텐츠를 기반으로 검색합니다. <br/>[정규식 예제](search-query-lucene-examples.md#example-6-regex) |
|  [와일드 카드 또는 접두사 검색](query-lucene-syntax.md#bkmk_wildcard) | 매개 변수와 전체 파서 검색 | 접두사와 물결표(`~`) 또는 단일 문자(`?`)를 기반으로 검색합니다. <br/>[와일드 카드 검색 예제](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>검색 결과 관리 

.NET API를 사용하면 serialization이 기본으로 제공되지만 쿼리 결과는 REST API에서 JSON 문서로 스트리밍됩니다. 쿼리의 매개 변수를 설정하고 응답의 특정 필드를 선택하여 결과를 구체화할 수 있습니다.

다음과 같은 방법으로 쿼리에 매개 변수를 사용하여 결과 집합을 구성할 수 있습니다.

+ 결과의 문서 수 제한 또는 일괄 처리(기본적으로 50개)
+ 결과에 포함할 필드 선택
+ 정렬 순서 설정
+ 적중 강조 표시를 추가하여 검색 결과 본문에는 일치하는 용어 표시

### <a name="tips-for-unexpected-results"></a>예기치 않은 결과 대한 팁

경우에 따라, 결과의 구조가 아닌 본질을 예상할 수 없습니다. 쿼리 결과가 예상과 다른 경우 다음과 같이 쿼리를 수정하여 결과가 개선되는지 확인할 수 있습니다.

+ **`searchMode=any`**(기본값)를 **`searchMode=all`** 로 변경하면 임의의 조건이 아닌 모든 조건이 일치해야 합니다. 부울 연산자가 쿼리에 포함되는 경우 특히 이렇게 합니다.

+ 텍스트 또는 어휘 분석이 필요한데, 쿼리 형식이 언어 처리를 금지하는 경우 쿼리 방법을 변경합니다. 전체 텍스트 검색, 텍스트 또는 어휘 분석 치유 맞춤법 오류, 단수형 복수 단어 형태 및도 불규칙 한 동사 또는 명사가 있습니다. 유사 항목 또는 와일드카드 검색과 같은 일부 쿼리의 경우 텍스트 분석이 쿼리 구문 분석 파이프라인에 속하지 않습니다. 일부 시나리오에서는 정규식이 해결 방법으로 사용되고 있습니다. 

### <a name="paging-results"></a>페이징 결과
Azure Search를 사용하면 검색 결과의 페이징을 구현하기가 쉽습니다. **`top`** 및 **`skip`** 매개 변수를 사용하면 검색 요청을 원활하게 처리하여 검색 결과의 전체 집합을 다루기 쉽고 순서가 지정된 하위 집합으로 받을 수 있기 때문에 유용한 검색 UI 환경을 쉽게 구현할 수 있습니다. 이러한 작은 하위 집합 결과를 받을 때 총 검색 결과 집합에서 문서 수도 수신할 수 있습니다.

[Azure Search에서 검색 결과를 페이징하는 방법](search-pagination-page-layout.md)문서에서 페이징 검색 결과에 대해 자세히 알아볼 수 있습니다.

### <a name="ordering-results"></a>결과 정렬
검색 쿼리에 대한 결과를 받을 때 Azure Search에서 특정 필드의 값을 기준으로 결과를 제공하도록 요청할 수 있습니다. 기본적으로 Azure Search는 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)에서 파생되는 각 문서의 검색 점수 순위에 따라 검색 결과를 정렬합니다.

Azure Search에서 검색 결과를 검색 점수가 아닌 다른 값으로 정렬하여 반환되도록 하려면 **`orderby`** 검색 매개 변수를 사용하면 됩니다. 지리 공간적 값의 [**`geo.distance()` 함수**](query-odata-filter-orderby-syntax.md)에 대한 필드 이름과 호출을 포함하도록 **`orderby`** 매개 변수의 값을 지정할 수 있습니다. 식마다 뒤에 `asc`를 추가하여 결과가 오름차순으로 요청되었음을 나타내고 **`desc`** 를 추가하여 결과가 내림차순으로 요청되었음을 나타냅니다. 기본 순위는 오름차순입니다.


### <a name="hit-highlighting"></a>적중 항목 강조 표시
Azure Search에서 검색 쿼리와 일치하는 검색 결과의 정확한 부분을 강조하려면 **`highlight`**, **`highlightPreTag`** 및 **`highlightPostTag`** 매개 변수를 사용합니다. 어떤 *검색 가능한* 필드의 일치 텍스트를 강조할지를 지정할 수 있으며 Azure Search에서 반환하는 일치 텍스트의 시작 및 끝부분에 추가할 정확한 문자열 태그를 지정할 수 있습니다.

## <a name="see-also"></a>참고 항목

+ [Azure Search에서 전체 텍스트 검색이 작동하는 원리(아키텍처를 구문 분석하는 쿼리)](search-lucene-query-architecture.md)
+ [검색 탐색기](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](search-query-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](search-create-index-rest-api.md)
