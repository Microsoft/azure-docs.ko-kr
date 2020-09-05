---
title: 쿼리 형식 및 컴퍼지션
titleSuffix: Azure Cognitive Search
description: 매개 변수를 사용 하 여 결과를 필터링 하 고, 선택 하 고, 정렬 하는 Azure Cognitive Search에서 검색 쿼리를 빌드하기 위한 기본 사항입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 018c3fb08c7fa0ad35fa567bffbeae48b6fbbce9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928839"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure Cognitive Search의 쿼리 유형 및 컴퍼지션

Azure Cognitive Search에서 쿼리는 라운드트립 작업의 전체 사양입니다. 요청에는 엔진에 대 한 실행 명령 뿐만 아니라 응답이 반환 되는 매개 변수를 제공 하는 매개 변수가 있습니다. 지정 되지 않음 ( `search=*` ). 일치 조건이 없고 null 또는 기본 매개 변수를 사용 하는 경우 쿼리는 모든 검색 가능 필드에 대해 전체 텍스트 검색 작업으로 실행 되어 점수가 매겨지지 않은 결과 집합을 임의의 순서로 반환 합니다.

다음 예는 [REST API](/rest/api/searchservice/search-documents)에서 생성 된 대표적인 쿼리입니다. 이 예에서는 [호텔 데모 인덱스](search-get-started-portal.md) 를 대상으로 하 고 쿼리 형태에 대 한 아이디어를 얻을 수 있도록 공통 매개 변수를 포함 합니다.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`**[기본 단순 쿼리 파서](search-query-simple-examples.md) (전체 텍스트 검색에 최적) 인 파서 또는 정규식, 근접 검색, 유사 항목 및 와일드 카드 검색 등의 고급 쿼리 구문에 사용 되는 [전체 Lucene 쿼리 파서](search-query-lucene-examples.md) 를 설정 합니다.

+ **`search`** 일치 조건, 일반적으로 전체 용어 또는 문구를 제공 하지만 종종 부울 연산자와 함께 사용 됩니다. 독립형 단일 용어는 용어 쿼리입니다.** 따옴표 안에 포함 된 다중 파트 쿼리는 *구* 쿼리입니다. 검색은에서와 같이 정의 되지 않을 수 **`search=*`** 있지만, 일치 하는 조건이 없는 경우 결과 집합은 임의로 선택 된 문서로 구성 됩니다.

+ **`searchFields`** 쿼리 실행을 특정 필드로 제한 합니다. 인덱스 스키마에서 *검색* 가능으로 특성을 지정 하는 모든 필드는이 매개 변수에 대 한 후보입니다.

쿼리에 포함 하는 매개 변수도 응답 합니다.

+ **`select`** 응답에 반환할 필드를 지정 합니다. 인덱스에서 *검색할* 수 있는 것으로 표시 된 필드만 select 문에서 사용할 수 있습니다.

+ **`top`** 지정 된 수의 가장 일치 하는 문서를 반환 합니다. 이 예제에서는 10 개의 적중만 반환 합니다. Top 및 skip (표시 되지 않음)을 사용 하 여 결과를 표시할 수 있습니다.

+ **`count`** 전체 인덱스의 문서 수가 전체적으로 일치 하는지 여부를 알려 줍니다 .이는 반환 되는 것 보다 많을 수 있습니다. 

+ **`orderby`** 등급 또는 위치와 같은 값을 기준으로 결과를 정렬 하려는 경우에 사용 됩니다. 그렇지 않으면 기본값은 관련성 점수를 사용 하 여 결과의 순위를 결정 하는 것입니다.

Azure Cognitive Search에서 쿼리 실행은 항상 요청에 제공 된 api 키를 사용 하 여 인증 된 하나의 인덱스에 대해 수행 됩니다. REST에서 둘 다 요청 헤더에 제공됩니다.

### <a name="how-to-run-this-query"></a>이 쿼리를 실행하는 방법

코드를 작성 하기 전에 쿼리 도구를 사용 하 여 구문을 학습 하 고 다른 매개 변수를 사용 하 여 시험해 볼 수 있습니다. 가장 빠른 방법은 기본 제공 포털 도구인 [검색 탐색기](search-explorer.md)입니다.

이 [빠른 시작을 수행 하 여 호텔 데모 인덱스를 만든](search-get-started-portal.md)경우이 쿼리 문자열을 탐색기의 검색 창에 붙여넣어 첫 번째 쿼리를 실행할 수 있습니다. `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>인덱스로 쿼리 작업을 사용하는 방법

인덱스 디자인 및 쿼리 디자인은 Azure Cognitive Search와 긴밀 하 게 연관 되어 있습니다. 먼저 알아야 하는 중요한 사실은 각 필드의 특성과 함께 인덱스 스키마에 의해 빌드할 수 있는 쿼리의 종류가 결정된다는 것입니다.** 

필드의 인덱스 특성은 허용된 작업(예: 인덱스에서 필드를 검색할 수 있는지(*searchable*), 결과에서 검색이 가능한지(*retrievable*), 정렬이 가능한지(*sortable*), 필터링이 가능한지(*filterable*) 등)을 설정합니다. 예제 쿼리 문자열에서는 `"$orderby": "Rating"` 등급 필드가 인덱스 스키마에서 *정렬할* 수 있는 것으로 표시 되기 때문에만 작동 합니다. 

![호텔 샘플에 대 한 인덱스 정의](./media/search-query-overview/hotel-sample-index-definition.png "호텔 샘플에 대 한 인덱스 정의")

위의 스크린샷은 호텔 샘플에 대 한 인덱스 특성의 부분 목록입니다. 전체 인덱스 스키마는 포털에서 볼 수 있습니다. 인덱스 특성에 대한 자세한 내용은 [인덱스 REST API 만들기](/rest/api/searchservice/create-index)를 참조하세요.

> [!Note]
> 일부 쿼리 기능은 필드별로 활성화되기 보다는 인덱스 전체에서 사용하도록 설정됩니다. 이러한 기능에는 [동의어 맵](search-synonyms.md), [사용자 지정 분석기](index-add-custom-analyzers.md), [확인 기 구문 (자동 완성 및 제안 된 쿼리)](index-add-suggesters.md), [결과 순위 지정을 위한 점수 매기기 논리가](index-add-scoring-profiles.md)포함 됩니다.

## <a name="elements-of-a-query-request"></a>쿼리 요청의 요소

쿼리는 항상 단일 인덱스에 전달됩니다. 인덱스를 조인할 수도 없고 사용자 지정 또는 임시 데이터 구조를 쿼리 대상으로 만들 수도 없습니다. 

쿼리 요청의 필요한 요소에는 다음 구성 요소가 포함됩니다.

+ 고정 및 사용자 정의 구성 요소를 포함 하는 URL로 표현 되는 서비스 끝점 및 인덱스 문서 컬렉션: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (REST만 해당) 둘 이상의 API 버전을 항상 사용할 수 있기 때문에 필요 합니다. 
+ **`api-key`** 쿼리 또는 관리 api 키 중 하나는 서비스에 대 한 요청을 인증 합니다.
+ **`queryType`** 단순 또는 전체입니다. 기본 제공 단순 구문을 사용 하는 경우에는 생략할 수 있습니다.
+ **`search`** 또는 **`filter`** 빈 검색을 수행 하려는 경우 지정 하지 않을 수 있는 일치 조건을 제공 합니다. 두 가지 쿼리 유형 모두 간단한 파서로 설명되지만 고급 쿼리의 경우에도 복잡한 쿼리 식을 전달하기 위한 검색 매개 변수가 필요합니다.

모든 다른 검색 매개 변수는 선택 사항입니다. 전체 특성 목록은 [인덱스 만들기(REST)](/rest/api/searchservice/create-index)를 참조하세요. 처리 하는 동안 매개 변수를 사용 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search에서 전체 텍스트 검색이 작동 하는 방식](search-lucene-query-architecture.md)을 참조 하세요.

## <a name="choose-apis-and-tools"></a>Api 및 도구 선택

다음 표에는 쿼리를 제출하기 위한 API 및 도구 기반 접근 방법이 나와 있습니다.

| 방법론 | Description |
|-------------|-------------|
| [검색 탐색기 (포털)](search-explorer.md) | 검색 표시줄 및 인덱스와 api-version 선택을 위한 옵션을 제공합니다. 결과는 JSON 문서로 반환됩니다. 탐색, 테스트 및 유효성 검사에 권장 됩니다. <br/>[자세한 정보](search-get-started-portal.md#query-index) | 
| [Postman 또는 기타 REST 도구](search-get-started-postman.md) | 웹 테스트 도구는 REST 호출 작성에 적합한 선택 항목입니다. REST API는 Azure Cognitive Search에서 가능한 모든 작업을 지원 합니다. 이 문서에서는 Azure Cognitive Search에 요청을 보내기 위한 HTTP 요청 헤더 및 본문을 설정 하는 방법에 대해 알아봅니다.  |
| [SearchIndexClient(.NET)](/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Cognitive Search 인덱스를 쿼리 하는 데 사용할 수 있는 클라이언트입니다.  <br/>[자세한 정보](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Search Documents(REST API)](/rest/api/searchservice/search-documents) | 인덱스에 대한 GET 또는 POST 메서드로, 추가 입력을 위해 쿼리 매개 변수를 사용합니다.  |

## <a name="choose-a-parser-simple--full"></a>파서 선택: 단순 | 전체

Azure Cognitive Search는 Apache Lucene 위에 있으며 일반적인 쿼리 및 특수 쿼리를 처리 하는 두 개의 쿼리 파서 사이에서 선택할 수 있습니다. 단순 파서를 사용하는 요청은 [단순 쿼리 구문](query-simple-syntax.md)을 사용하여 형성되며 자유 형식 텍스트 쿼리에서 속도와 효율성의 기본값으로 선택됩니다. 이 구문은 AND, OR, NOT, 구, 접미사 및 우선 순위 연산자를 포함한 여러 일반 검색 연산자를 지원합니다.

요청에 `queryType=full`을 추가하면 사용되도록 설정되는 [전체 Lucene 쿼리 구문](query-Lucene-syntax.md#bkmk_syntax)은 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)의 일부로 개발된 널리 채택되고 표현적인 쿼리 언어를 공개합니다. 전체 구문은 단순 구문을 확장합니다. 단순 구문에 대해 작성한 쿼리는 전체 Lucene 파서에서 실행됩니다. 

다음 예제는 이런 점을 보여줍니다. 동일한 쿼리이지만 다른 queryType 설정을 사용하면 다른 결과가 나타납니다. 첫 번째 쿼리에서 `^3` after는 `historic` 검색 용어의 일부로 처리 됩니다. 이 쿼리에 대해 가장 순위가 높은 결과는 "Plaza & 도구 모음" 이며, 해당 설명에는 *바다* 가 있습니다.

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

전체 Lucene 파서를 사용 하는 동일한 쿼리는 `^3` 필드 내 용어 부스터로 해석 됩니다. 파서를 전환 하면 순위가 변경 되 고 맨 *위로 이동 하* 는 용어를 포함 하는 결과가 변경 됩니다.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>쿼리 유형

Azure Cognitive Search는 광범위 한 쿼리 유형을 지원 합니다. 

| 쿼리 유형 | 사용 | 예제 및 자세한 정보 |
|------------|--------|-------------------------------|
| 자유 형식 텍스트 검색 | 매개 변수와 파서 중 하나를 검색| 전체 텍스트 검색은 인덱스의 *검색 가능한* 모든 필드에서 하나 이상의 단어를 검색하고 Google 또는 Bing과 같은 검색 엔진이 작동할 것으로 예상되는 방식으로 작동합니다. 소개의 예는 전체 텍스트 검색입니다.<br/><br/>전체 텍스트 검색은 표준 Lucene 분석기 (기본적으로)를 사용 하 여 어휘 분석을 수행 하 여 모든 용어를 소문자로, "the"와 같은 중지 단어를 제거 합니다. [영어가 아닌 분석기](index-add-language-analyzers.md#language-analyzer-list) 또는 어휘 분석을 수정 하는 [특수 한 언어 관련 분석기](index-add-custom-analyzers.md#AnalyzerTable) 를 사용 하 여 기본값을 재정의할 수 있습니다. 필드의 전체 내용을 단일 토큰으로 취급하는 [키워드](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)가 예입니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다. | 
| 필터링된 검색 | [OData 필터 식](query-odata-filter-orderby-syntax.md)과 파서 중 하나 | 필터 쿼리는 인덱스의 *필터링 가능한* 모든 필드에 걸쳐 부울 식을 계산합니다. 검색과 달리 필터 쿼리는 문자열 필드에서 대/소문자 구분을 포함하여 필드의 정확한 내용을 검색합니다. 또 다른 차이점은 필터 쿼리는 OData 구문으로 표현된다는 점입니다. <br/>[필터 식 예제](search-query-simple-examples.md#example-3-filter-queries) |
| 지리적 검색 | 필드, 필터 식 및 파서 중 하나에 [Edm.GeographyPoint 유형](/rest/api/searchservice/supported-data-types) | Edm.GeographyPoint가 있는 필드에 저장된 좌표는 "내 주변 찾기"또는 지도 기반 검색 컨트롤에 사용됩니다. <br/>[지리적 검색 예제](search-query-simple-examples.md#example-5-geo-search)|
| 범위 검색 | 필터 식 및 단순 파서 | Azure Cognitive Search에서는 필터 매개 변수를 사용 하 여 범위 쿼리를 작성 합니다. <br/>[범위 필터 예제](search-query-simple-examples.md#example-4-range-filters) | 
| [필드 지정 검색](query-lucene-syntax.md#bkmk_fields) | 매개 변수와 전체 파서 검색 | 단일 필드를 대상으로 복합 쿼리 식을 작성합니다. <br/>[필드 지정 검색 예제](search-query-lucene-examples.md#example-2-fielded-search) |
| [유사 항목 검색](query-lucene-syntax.md#bkmk_fuzzy) | 매개 변수와 전체 파서 검색 | 유사한 구조 또는 철자가 포함된 용어를 검색합니다. <br/>[유사 항목 검색 예제](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [근접 검색](query-lucene-syntax.md#bkmk_proximity) | 매개 변수와 전체 파서 검색 | 문서에서 서로 가까이 있는 용어를 찾습니다. <br/>[근접 검색 예제](search-query-lucene-examples.md#example-4-proximity-search) |
| [용어 상승](query-lucene-syntax.md#bkmk_termboost) | 매개 변수와 전체 파서 검색 | 승격된 용어가 포함된 문서는 그렇지 않은 다른 문서보다 상대적으로 높은 순위를 매깁니다. <br/>[용어 상승 예제](search-query-lucene-examples.md#example-5-term-boosting) |
| [정규식 검색](query-lucene-syntax.md#bkmk_regex) | 매개 변수와 전체 파서 검색 | 정규식의 콘텐츠를 기반으로 검색합니다. <br/>[정규식 예제](search-query-lucene-examples.md#example-6-regex) |
|  [와일드 카드 또는 접두사 검색](query-lucene-syntax.md#bkmk_wildcard) | 매개 변수와 전체 파서 검색 | 접두사와 물결표(`~`) 또는 단일 문자(`?`)를 기반으로 검색합니다. <br/>[와일드카드 검색 예제](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>검색 결과 관리 

.NET API를 사용하면 serialization이 기본으로 제공되지만 쿼리 결과는 REST API에서 JSON 문서로 스트리밍됩니다. 쿼리의 매개 변수를 설정하고 응답의 특정 필드를 선택하여 결과를 구체화할 수 있습니다.

다음과 같은 방법으로 쿼리에 매개 변수를 사용하여 결과 집합을 구성할 수 있습니다.

+ 결과의 문서 수 제한 또는 일괄 처리(기본적으로 50개)
+ 결과에 포함할 필드 선택
+ 정렬 순서 설정
+ 적중 강조 표시를 추가하여 검색 결과 본문에는 일치하는 용어 표시

### <a name="tips-for-unexpected-results"></a>예기치 않은 결과 대한 팁

경우에 따라, 결과의 구조가 아닌 본질을 예상할 수 없습니다. 쿼리 결과가 예상과 다른 경우 다음과 같이 쿼리를 수정하여 결과가 개선되는지 확인할 수 있습니다.

+ **`searchMode=any`** **`searchMode=all`** 조건 대신 모든 조건에서 일치 항목을 요구 하도록를로 변경 합니다 (기본값). 부울 연산자가 쿼리에 포함되는 경우 특히 이렇게 합니다.

+ 텍스트 또는 어휘 분석이 필요한데, 쿼리 형식이 언어 처리를 금지하는 경우 쿼리 방법을 변경합니다. 전체 텍스트 검색에서 텍스트 또는 어휘 분석은 맞춤법 오류, 단일/복수 단어 형태, 불규칙 한 동사 또는 명사를 자동으로 수정 합니다. 유사 항목 또는 와일드 카드 검색과 같은 일부 쿼리에는 어휘 분석이 쿼리 구문 분석 파이프라인의 일부가 아닙니다. 일부 시나리오에서는 정규식이 해결 방법으로 사용되고 있습니다. 

### <a name="paging-results"></a>페이징 결과
Azure Cognitive Search을 사용 하면 검색 결과의 페이징을 쉽게 구현할 수 있습니다. **`top`** 및 매개 변수를 사용 하 여 검색 **`skip`** 결과의 전체 집합을 쉽게 검색할 수 있는 검색 요청을 원활 하 게 사용할 수 있도록 하는 검색 요청을 원활 하 게 실행할 수 있습니다. 이러한 작은 하위 집합 결과를 받을 때 총 검색 결과 집합에서 문서 수도 수신할 수 있습니다.

[Azure Cognitive Search에서 검색 결과를 페이징 하는 방법](search-pagination-page-layout.md)문서에서 페이징 검색 결과에 대해 자세히 알아볼 수 있습니다.

### <a name="ordering-results"></a>결과 정렬
검색 쿼리 결과를 받을 때 Azure Cognitive Search 특정 필드의 값을 기준으로 정렬 된 결과를 제공 하도록 요청할 수 있습니다. 기본적으로 Azure Cognitive Search는 [TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)에서 파생 된 각 문서 검색 점수의 순위에 따라 검색 결과를 정렬 합니다.

Azure Cognitive Search 검색 점수 이외의 값으로 정렬 된 결과를 반환 하도록 하려는 경우 검색 매개 변수를 사용할 수 있습니다 **`orderby`** . 매개 변수 값을 지정 하 여 **`orderby`** 필드 이름 및 지리 공간적 값에 대 한 [** `geo.distance()` 함수**](query-odata-filter-orderby-syntax.md) 호출을 포함할 수 있습니다. 각 식 뒤에는 `asc` 결과가 오름차순으로 요청 됨을 나타내고 **`desc`** 결과가 내림차순으로 요청 됨을 나타내는가 올 수 있습니다. 기본 순위는 오름차순입니다.


### <a name="hit-highlighting"></a>적중 항목 강조 표시
Azure Cognitive Search에서는 **`highlight`** , 및 매개 변수를 사용 하 여 검색 쿼리와 일치 하는 검색 결과의 정확한 부분을 쉽게 강조할 수 **`highlightPreTag`** **`highlightPostTag`** 있습니다. 일치 하는 텍스트를 강조 표시 하는 *검색 가능한* 필드를 지정 하 고, Azure Cognitive Search에서 반환 하는 일치 하는 텍스트의 시작과 끝에 추가할 정확한 문자열 태그를 지정할 수 있습니다.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search에서 전체 텍스트 검색이 작동 하는 방식 (쿼리 구문 분석 아키텍처)](search-lucene-query-architecture.md)
+ [검색 탐색기](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](./search-get-started-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](./search-get-started-powershell.md)