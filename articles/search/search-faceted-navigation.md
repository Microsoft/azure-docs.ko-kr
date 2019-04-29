---
title: 범주 계층에서 패싯 탐색을 구현하는 방법 - Azure Search
description: Microsoft Azure에서 클라우드 호스티드 Search 서비스인 Azure Search와 통합되는 애플리케이션에 패싯 탐색을 추가합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3b31e796b07bea8c11bccb3f2bb306a4279f2ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291662"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Azure Search에서 패싯 탐색을 구현하는 방법
패싯 탐색은 검색 애플리케이션에서 자기 주도형 드릴다운 탐색을 제공하는 필터링 메커니즘입니다. '패싯 탐색'이라는 용어가 낯설 수도 있지만 아마도 이전에 사용해 보셨을 것입니다. 다음 예제와 같이 패싯 탐색은 결과를 필터링하는 데 사용되는 범주일 뿐입니다.

 ![Azure Search 작업 포털 데모][1]

패싯 탐색은 검색의 대체 진입점입니다. 복잡한 검색 식을 직접 입력할 수 있는 편리한 대안을 제공합니다. 패싯을 사용하면 원하는 항목을 쉽게 찾을 수 있으며 항상 결과를 얻을 수 있습니다. 개발자는 패싯을 통해 검색 모음을 탐색하는 데 가장 유용한 검색 조건을 노출할 수 있습니다. 온라인 소매 애플리케이션에서는 종종 브랜드, 부서(어린이 신발), 크기, 가격, 인기도 및 등급에 대한 패싯 탐색이 작성됩니다. 

패싯 탐색의 구현은 검색 기술에 따라 다릅니다. Azure Search에서는 이전에 스키마에서 특성을 지정한 필드를 사용하여 쿼리 시 패싯 탐색이 작성됩니다.

-   애플리케이션에서 작성한 쿼리는 해당 문서 결과 집합에서 사용 가능한 패싯 필터 값을 받기 위해 *패싯 쿼리 매개 변수*를 보내야 합니다.

-   실제로 문서 결과 집합을 자르려면 애플리케이션에서 `$filter` 식도 적용해야 합니다.

애플리케이션 개발 측면에서 쿼리를 생성하는 코드 작성은 대량 작업을 구성합니다. 범위 정의 및 패싯 결과 수 가져오기에 대한 기본 제공 지원을 포함하여 패싯 탐색에서 예상하는 애플리케이션 동작의 대부분은 서비스에서 제공됩니다. 서비스에는 다루기 힘든 탐색 구조를 방지하는 데 도움이 되는 적절한 기본값이 포함되어 있습니다. 

## <a name="sample-code-and-demo"></a>샘플 코드 및 데모
이 문서에서는 구직 검색 포털을 예로 사용합니다. 이 예제는 ASP.NET MVC 애플리케이션으로 구현됩니다.

-   [Azure Search 구직 포털 데모](http://azjobsdemo.azurewebsites.net/)에서 온라인으로 작업 데모를 살펴보고 테스트하세요.

-   [GitHub의 Azure 샘플 리포지토리](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)에서 코드를 다운로드하세요.

## <a name="get-started"></a>시작하기
검색 개발을 처음 접하는 경우 패싯 탐색을 자기 주도형 검색에 대한 가능성을 보여 주는 것이라고 생각하는 것이 가장 좋습니다. 패싯 탐색은 포인트 클릭 동작을 통해 검색 결과의 범위를 신속하게 좁히는 데 사용되는 미리 정의된 필터를 기반으로 하는 드릴다운 검색 환경에 일종입니다. 

### <a name="interaction-model"></a>상호 작용 모델

패싯 탐색의 검색 환경은 대화형이므로 먼저 사용자 동작에 대한 응답으로 펼쳐지는 쿼리의 시퀀스라는 점을 이해해야 합니다.

시작 지점은 일반적으로 주변에서 패싯 탐색을 제공하는 애플리케이션 페이지입니다. 패싯 탐색은 각 값에 대한 확인란 또는 클릭할 수 있는 텍스트가 포함된 트리 구조인 경우가 많습니다. 

1. Azure Search로 전송된 쿼리는 하나 이상의 패싯 쿼리 매개 변수를 통해 패싯 탐색 구조를 지정합니다. 예를 들어 쿼리는 프레젠테이션을 구체화하는 `:values` 또는 `:sort` 옵션과 함께 `facet=Rating`이 포함할 수 있습니다.
2. 프레젠테이션 계층은 요청에 지정된 패싯을 사용하여 패싯 탐색을 제공하는 검색 페이지를 렌더링합니다.
3. 등급이 포함된 패싯 탐색 구조에서 사용자가 등급이 4 이상인 제품만 표시하기 위해 “4”를 클릭합니다. 
4. 이에 대한 응답으로 애플리케이션은 `$filter=Rating ge 4` 
5. 새 조건을 충족하는 항목(이 경우 등급이 4 이상인 제품)만 포함된 축소된 결과 집합을 표시하도록 프레젠테이션 계층에서 페이지를 업데이트합니다.

패싯은 쿼리 매개 변수이지만 쿼리 입력과 혼동해서는 안 됩니다. 쿼리의 선택 조건으로 사용되지 않습니다. 패싯 쿼리 매개 변수는 응답에서 반환되는 탐색 구조의 입력이라고 생각해야 합니다. 제공한 각 패싯 쿼리 매개 변수에 대해 Azure Search는 각 패싯 값의 부분 결과에 포함된 문서 수를 평가합니다.

4단계의 `$filter` 에 주목하세요. 필터는 패싯 탐색의 중요한 부분입니다. 패싯과 필터는 API에서 서로 독립적이지만 원하는 환경을 제공하려면 둘 다 필요합니다. 

### <a name="app-design-pattern"></a>앱 디자인 패턴

애플리케이션 코드에서 패턴은 패싯 쿼리 매개 변수를 사용하여 패싯 결과 및 $filter 식과 함께 패싯 탐색 구조를 반환합니다.  필터 식은 패싯 값의 클릭 이벤트를 처리합니다. `$filter` 식은 프레젠테이션 계층으로 반환되는 검색 결과를 실제로 조정하는 숨은 코드라고 생각하면 됩니다. 색상 패싯의 경우 빨간색을 클릭하는 것은 색상이 빨간색인 항목만 선택하는 `$filter` 식을 통해 구현됩니다. 

### <a name="query-basics"></a>쿼리 기본 사항

Azure Search에서는 하나 이상의 쿼리 매개 변수를 통해 요청이 지정됩니다(각 매개 변수에 대한 설명은 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 참조). 필수 사항인 쿼리 매개 변수는 없지만 쿼리가 유효하려면 하나 이상의 쿼리 매개 변수가 있어야 합니다.

관련 없는 적중 항목을 필터링하는 기능으로 이해되는 정밀도는 다음 두 식 중 하나 또는 둘 다를 통해 실현됩니다.

-   **search=**  
     이 매개 변수의 값은 검색 식을 구성합니다. 단일 텍스트 조각 또는 여러 항 및 연산자를 포함하는 복잡한 검색 식일 수 있습니다. 서버의 경우 검색 식은 인덱스의 검색 가능한 필드에서 일치하는 용어를 쿼리하고 순위대로 결과를 반환하는 전체 텍스트 검색에 사용됩니다. `search`를 null로 설정한 경우 쿼리는 전체 인덱스에서 실행됩니다(즉, `search=*`). 이 경우 쿼리의 다른 요소(예: `$filter` 또는 점수 매기기 프로필)는 반환되는 문서(`($filter`) 및 반환 순서(`scoringProfile` 또는 `$orderby`)에 영향을 주는 주요 요소입니다.

-   **$filter=**  
     필터는 특정 문서 특성 값을 기반으로 검색 결과의 크기를 제한하는 강력한 메커니즘입니다. `$filter` 가 먼저 평가된 후 사용 가능한 값과 각 값의 해당 개수를 생성하는 패싯 논리가 평가됩니다.

복잡한 검색 식은 쿼리 성능을 저하시킵니다. 되도록이면 효과적으로 작성된 필터 식을 사용하여 정밀도를 높이고 쿼리 성능을 향상시키세요.

필터를 통해 정밀도를 높이는 방법을 이해하려면 복잡한 검색 식을 필터 식이 포함된 식과 비교해 보세요.

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

두 쿼리 모두 유효하지만 Seattle에 주차장이 있는 모델이 아닌 곳을 찾으려는 경우에는 두 번째를 사용하는 것이 더 좋습니다.
-   첫 번째 쿼리는 Name, Description 및 기타 검색 가능한 데이터가 포함된 모든 필드와 같은 문자열 필드에 언급되거나 언급되지 않은 특정 단어를 기반으로 합니다.
-   두 번째 쿼리는 구조화된 데이터에서 정확히 일치하는 항목을 찾으므로 정확도가 훨씬 높을 수 있습니다.

패싯 탐색이 포함된 애플리케이션에서는 패싯 탐색 구조에 대한 각 사용자 작업이 수행된 후 검색 결과 범위를 좁혀야 합니다. 검색 결과 범위를 좁히려면 필터 식을 사용합니다.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>패싯 탐색 앱 구축
Azure Search를 사용하여 애플리케이션 코드에서 검색 요청을 작성하는 패싯 탐색을 구현합니다. 패싯 탐색은 이전에 정의된 스키마의 요소를 사용합니다.

검색 인덱스에 `Facetable [true|false]` 인덱스 특성이 미리 정의되어 있으므로 선택한 필드를 패싯 탐색 구조에서 사용하거나 사용하지 않도록 설정하면 됩니다. `"Facetable" = true`가 아니면 패싯 탐색에서 필드를 사용할 수 없습니다.

코드의 프레젠테이션 계층은 사용자 환경을 제공합니다. 레이블, 값, 확인란, 개수 등 패싯 탐색의 구성 부분을 나열합니다. Azure Search REST API는 플랫폼에 독립적이므로 사용자가 원하는 언어와 플랫폼을 사용합니다. 단, 각 추가 패싯이 선택될 때 업데이트된 UI 상태로 증분 새로 고침을 지원하는 UI 요소를 포함해야 합니다. 

쿼리 시 애플리케이션 코드는 패싯에 필드를 제공하는 요청 매개 변수인 `facet=[string]`을 포함하는 요청을 만듭니다. `&facet=color&facet=category&facet=rating`과 같이 각 패싯을 앰퍼샌드(&) 문자로 구분하여 여러 패싯을 쿼리에 지정할 수 있습니다.

또한 애플리케이션 코드는 패싯 탐색에서 클릭 이벤트를 처리할 `$filter` 식을 생성해야 합니다. `$filter` 는 패싯 값을 필터 조건으로 사용하여 검색 결과를 줄입니다.

Azure Search는 사용자가 입력한 하나 이상의 용어에 따라 검색 결과를 반환하고 패싯 탐색 구조를 업데이트합니다. Azure Search에서 패싯 탐색은 패싯 값과 각 값에 대해 발견된 결과 수로 이루어진 단일 수준 구성입니다.

다음 섹션에서는 각 부분을 작성하는 방법을 좀 더 자세히 살펴보겠습니다.

<a name="buildindex"></a>

## <a name="build-the-index"></a>인덱스 작성
인덱스 특성 `"Facetable": true`를 통해 인덱스에서 패싯을 필드별로 사용하도록 설정할 수 있습니다.  
패싯 탐색에 사용할 수 있는 모든 필드 형식은 기본적으로 `Facetable` 입니다. 이러한 필드 형식에는 `Edm.String`, `Edm.DateTimeOffset` 및 모든 숫자 필드 형식(기본적으로 모든 필드 형식은 패싯 탐색에서 사용할 수 없는 `Edm.GeographyPoint`를 제외하고 패싯 가능)이 포함됩니다. 

인덱스를 작성할 때 패싯으로 사용해서는 안 되는 필드에 대한 패싯을 명시적으로 해제하는 것이 가장 좋습니다.  특히 ID 또는 제품 이름과 같은 Singleton 값의 문자열 필드는 패싯 탐색에서 실수로(그리고 비효과적으로) 사용되지 않도록 `"Facetable": false`로 설정해야 합니다. 필요 없는 패싯을 해제하면 인덱스 크기를 작게 유지하는 데 도움이 되며 일반적으로 성능이 향상됩니다.

다음은 구직 포털 데모 샘플 앱의 스키마 중 일부이며 전체 크기를 줄이기 위해 일부 특성을 잘랐습니다.

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

샘플 스키마에서 보시는 것처럼, ID 값과 같이 패싯으로 사용하면 안 되는 문자열 필드에 대해 `Facetable`이 해제되어 있습니다. 필요 없는 패싯을 해제하면 인덱스 크기를 작게 유지하는 데 도움이 되며 일반적으로 성능이 향상됩니다.

> [!TIP]
> 각 필드의 전체 인덱스 특성 집합을 포함하는 것이 가장 좋습니다. `Facetable` 은 거의 모든 필드에 대해 기본적으로 설정되지만 각 특성을 의도적으로 설정하는 것이 각 스키마 의사 결정의 의미를 검토하는 데 도움이 됩니다. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>데이터 확인
데이터 품질은 패싯 탐색 구조가 예상대로 구체화되는지 여부에 직접적인 영향을 미칩니다. 결과 집합을 줄이기 위한 필터 작성의 용이성에도 영향을 줍니다.

Brand 또는 Price별로 패싯하려는 경우에는 각 문서에 필터 옵션으로 유효하고 일관적이며 생산적인 *BrandName* 및 *ProductPrice* 값이 포함되어야 합니다.

그 밖에도 다음과 같은 사항을 고려해야 합니다.

* 패싯하려는 모든 필드에 대해 자기 주도형 검색에서 필터로 적합한 값이 포함되어 있는지 확인합니다. 값은 간단하고, 설명을 포함하며, 경쟁 옵션 간에 명확한 선택을 제공할 수 있도록 구별되어야 합니다.
* 맞춤법 오류 또는 거의 일치하는 값. Color를 패싯할 때 필드 값에 Orange와 Ornage(맞춤법 오류)가 포함되어 있는 경우 Color 필드를 기반으로 하는 패싯은 둘 값을 모두 선택합니다.
* 대/소문자가 혼합된 텍스트도 패싯 탐색에서 나타날 수 있습니다. 예를 들어 orange와 Orange가 두 가지 값으로 표시됩니다. 
* 동일한 값의 단수 및 복수 버전은 각각에 대해 별도의 패싯이 발생할 수 있습니다.

따라서 데이터를 충실히 준비하는 것은 효과적인 패싯 탐색의 기본적인 사항입니다.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>UI 빌드
프레젠테이션 계층에서 다시 작업하는 것은 놓쳤을 수 있는 요구 사항을 파악하고 검색 환경에 기본적으로 필요한 기능을 이해하는 데 도움이 됩니다.

패싯 탐색의 경우 웹 또는 애플리케이션은 패싯 탐색 구조를 표시하고, 페이지에서 사용자 입력을 검색하며, 변경된 요소를 삽입합니다. 

웹 애플리케이션의 경우 주로 AJAX가 프레젠테이션 계층에서 사용되는데, 이는 증분 변경 내용을 새로 고칠 수 있기 때문입니다. ASP.NET MVC 또는 기타 HTTP를 통해 Azure Search에 연결할 수 있는 모든 시각화 플랫폼을 사용할 수도 있습니다. 이 문서 전체에 나오는 샘플 애플리케이션 **Azure Search 구직 포털 데모**는 ASP.NET MVC 애플리케이션입니다.

이 샘플에서 패싯 탐색은 검색 결과 페이지에 작성됩니다. 샘플 애플리케이션의 `index.cshtml` 파일에서 가져온 다음 예제는 검색 결과 페이지에 패싯 탐색을 표시하는 동적 HTML 구조를 표시합니다. 검색 용어를 제출하거나 패킷을 선택 또는 선택 취소하면 자동으로 패싯 목록이 작성되거나 다시 작성됩니다.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

`index.cshtml` 페이지의 다음 코드 조각은 첫 번째 패싯인 직함을 표시하는 HTML을 동적으로 빌드합니다. 그와 유사한 기능은 다른 패싯에 대한 HTML을 동적으로 빌드합니다. 각 패싯에는 해당 패싯 결과에 대해 발견된 항목 수를 표시하는 레이블과 수가 있습니다.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> 검색 결과 페이지를 디자인할 때 패싯을 지우는 메커니즘을 추가해야 합니다. 확인란을 추가하는 경우 필터를 지우는 방법을 쉽게 확인할 수 있습니다. 다른 레이아웃에는 이동 경로 탐색 패턴 또는 다른 창의적인 방법이 필요할 수 있습니다. 예를 들어 구직 검색 포털 샘플 애플리케이션에서 선택한 패싯 뒤에 있는 `[X]`를 클릭하여 패싯을 지울 수 있습니다.

<a name="buildquery"></a>

## <a name="build-the-query"></a>쿼리 작성
쿼리를 만들기 위해 작성하는 코드는 검색 식, 패싯, 필터, 점수 매기기 프로필 등 요청을 구성하는 데 사용되는 유효한 쿼리의 모든 부분을 지정해야 합니다. 이 섹션에서는 쿼리에 적합한 패싯의 위치 및 패싯과 함께 필터를 사용하여 결과 집합을 줄이는 방법에 대해 알아봅니다.

패싯은 이 샘플 애플리케이션의 필수 요소입니다. 구직 포털 데모의 검색 환경은 패싯 탐색 및 필터를 중심으로 디자인되었습니다. 페이지에서 패싯 탐색의 주요 위치는 그 중요성을 보여 줍니다. 

대부분 예제에서 시작하는 것이 좋습니다. `JobsSearch.cs` 파일에서 가져온 다음 예제는 직함, 위치, 게시 유형, 최소 급여를 기반으로 패싯 탐색을 만드는 요청을 작성합니다. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

패싯 쿼리 매개 변수는 필드로 설정되어 있으며, 데이터 형식에 따라 `count:<integer>`, `sort:<>`, `interval:<integer>` 및 `values:<list>`를 포함하는 쉼표로 구분된 목록으로 추가 매개 변수화할 수 있습니다. 값 목록은 범위를 설정할 때 숫자 데이터에 대해 지원됩니다. 자세한 내용은 [문서 검색(Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 을 참조하세요.

패싯과 함께, 애플리케이션에서 작성된 요청도 패싯 값 선택 항목에 따라 후보 문서 집합의 범위를 좁히는 필터를 작성해야 합니다. 자전거 매장의 경우 패싯 탐색은 *어떤 색상, 어떤 제조업체, 어떤 종류의 자전거를 판매합니까?* 와 같은 질문에 대한 단서를 제공합니다. 필터링은 *이 가격대의 빨간색 산악용 자전거는 무엇입니까?* 와 같은 질문에 답변합니다. 빨간색 제품만 표시되도록 사용자가 "빨간색"을 클릭하면 애플리케이션에서 보내는 다음 쿼리에 `$filter=Color eq ‘Red’`가 포함됩니다.

사용자가 직함 패싯에서 값을 선택하는 경우 `JobsSearch.cs` 페이지의 다음 코드 조각은 선택된 직함을 필터에 추가합니다.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>팁 및 모범 사례

### <a name="indexing-tips"></a>인덱싱 팁
**검색 상자를 사용하지 않을 경우 인덱스 효율성 향상**

애플리케이션에서 검색 상자 없이 패싯 탐색만 사용하는 경우 필드를 `searchable=false`, `facetable=true`로 표시하여 보다 압축된 인덱스를 생성할 수 있습니다. 또한 인덱싱은 여러 단어로 된 값의 구성 요소 부분에 대한 인덱싱 또는 단어 분리 없이 전체 패싯 값에서만 발생합니다.

**패싯으로 사용할 수 있는 필드 지정**

인덱스의 스키마에 따라 패싯으로 사용할 수 있는 필드가 결정됩니다. 필드가 패싯 가능한 경우 쿼리는 패싯할 필드를 지정합니다. 패싯할 필드는 레이블 아래에 표시되는 값을 제공합니다. 

각 레이블 아래에 표시되는 값은 인덱스에서 검색됩니다. 예를 들어 패싯 필드가 *Color*인 경우 추가 필터링에 사용할 수 있는 값은 해당 필드에 대한 값(Red, Black 등)입니다.

Numeric 및 DateTime 값에 한해, 패싯 필드에서 값을 명시적으로 설정할 수 있습니다(예: `facet=Rating,values:1|2|3|4|5`). 이러한 필드 형식에는 값 목록을 사용하여 패싯 결과를 연속 범위(숫자 값 또는 기간을 기반으로 하는 범위)로 구분하는 작업을 간소화할 수 있습니다. 

**기본적으로 한 수준의 패싯 탐색만 유지 가능** 

계층 구조에서 패싯 중첩은 직접 지원되지 않습니다. 기본적으로 Azure Search의 패싯 탐색은 하나의 필터 수준만 지원합니다. 그러나 해결 방법이 있습니다. `Collection(Edm.String)` 의 계층적 패싯 구조를 계층당 하나의 항목으로 인코딩할 수 있습니다. 이 해결책을 구현하는 방법은 이 문서의 범위를 벗어납니다. 

### <a name="querying-tips"></a>쿼리 팁
**필드의 유효성 검사**

신뢰할 수 없는 사용자 입력을 기반으로 패싯 목록을 동적으로 빌드하는 경우 패싯 필드의 이름이 올바른지 확인합니다. 또는 .NET에서 `Uri.EscapeDataString()`을 사용하거나 본인이 선택한 플랫폼에서 그에 상응하는 옵션을 사용하여 URL을 빌드할 때 이름을 이스케이프합니다.

### <a name="filtering-tips"></a>필터링 팁
**필터로 검색 정확도 향상**

 필터를 사용합니다. 검색 식에만 의존할 경우 형태소 분석으로 인해 해당 필드에 정확한 패싯 값이 없는 문서가 반환될 수 있습니다.

**필터로 검색 성능 향상**

 필터는 검색 후보 문서 집합의 범위를 좁히고 순위에서 제외합니다. 대량의 문서 집합이 있는 경우 엄선된 패싯 드릴다운을 사용하면 때로는 성능을 크게 향상시킬 수 있습니다.
  
**패싯 필드만 필터링**

패싯 드릴다운에서는 일반적으로 검색 가능한 모든 필드에 걸쳐 있지 않고 특정(패싯) 필드에만 패싯 값이 있는 문서만 포함할 수 있습니다. 필터를 추가하면 일치하는 값의 패싯 필터에서만 검색하도록 지시하여 대상 필드를 보강할 수 있습니다.

**추가 필터로 패싯 결과 자르기**

패싯 결과는 패싯 용어와 일치하는 검색 결과에서 발견된 문서입니다. 다음 예제의 *cloud computing*에 대한 검색 결과에서 254개 항목은 콘텐츠 형식이 *internal specification*입니다. 항목은 상호 배타적이지 않을 수 있습니다. 하나의 항목이 두 필터 조건을 모두 충족하는 경우에는 각각 하나로 계산됩니다. 이 중복은 주로 문서 태깅을 구현하는 데 사용되는 `Collection(Edm.String)` 필드를 패싯할 때 발생합니다.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

일반적으로 패싯 결과가 지속적으로 너무 큰 경우에는 필터를 더 추가하여 사용자에게 검색 범위를 좁힐 수 있는 추가 옵션을 제공하는 것이 좋습니다.

### <a name="tips-about-result-count"></a>결과 개수에 대한 팁

**패싯 탐색의 항목 수 제한**

탐색 트리의 각 패싯 필드는 기본적으로 10개의 값으로 제한됩니다. 이 기본값은 값 목록을 관리하기 쉬운 크기로 유지하므로 탐색 구조에 유용합니다. count에 값을 할당하여 기본값을 재정의할 수 있습니다.

* `&facet=city,count:5`는 상위 순위 결과에서 발견된 처음 5개 도시만 패싯 결과로 반환되도록 지정합니다. 검색 용어가 "공항"이고 일치 항목이 32개인 샘플 쿼리를 생각해 보세요. 쿼리에서 `&facet=city,count:5`를 지정하면 검색 결과에 문서 수가 가장 많은 상위 5개의 고유한 도시만 패싯 결과에 포함됩니다.

패싯 결과와 검색 결과의 차이에 주의하세요. 검색 결과는 쿼리와 일치하는 모든 문서입니다. 패싯 결과는 각 패싯 값에 대한 일치 항목입니다. 이 예제에서 검색 결과에는 패싯 분류 목록(이 예제의 경우 5)에 없는 City 이름이 포함됩니다. 패싯 탐색을 통해 필터링된 결과는 사용자가 패싯을 지우거나 City 이외의 다른 패싯을 선택한 경우에 표시됩니다. 

> [!NOTE]
> 두 가지 이상의 형식이 있을 때 `count`를 설명하면 혼동을 일으킬 수 있습니다. 다음 표에서는 Azure Search API, 샘플 코드 및 설명서에서 용어가 사용되는 방식에 대한 간략한 요약을 제공합니다. 

* `@colorFacet.count`<br/>
   프레젠테이션 코드에는 패싯 결과 수를 표시하는 데 사용되는 count 매개 변수가 패싯에 표시됩니다. 패싯 결과에서 count는 패싯 용어 또는 범위와 일치하는 문서 수를 나타냅니다.
* `&facet=City,count:12`<br/>
   패싯 쿼리에서는 count를 값으로 설정할 수 있습니다.  기본값은 10이지만 더 높거나 낮은 값으로 설정할 수 있습니다. `count:12` 를 설정하면 문서 수에 따라 패싯 결과에서 상위 12개의 일치하는 항목을 가져옵니다.
* "`@odata.count`"<br/>
   쿼리 응답에서 이 값은 검색 결과의 일치하는 항목 수를 나타냅니다. 검색 용어와 일치하지만 패싯 값이 일치하지 않는 항목이 존재하기 때문에 이는 평균적으로 모든 패싯 결과의 합계보다 큽니다.

**패싯 결과의 개수 가져오기**

패싯 쿼리에 필터를 추가할 때 패싯 문(예: `facet=Rating&$filter=Rating ge 4`)을 유지할 수 있습니다. 기술적으로는 facet=Rating이 필요하지 않지만 이를 유지하면 등급 4 이상에 대한 패싯 값의 개수가 반환됩니다. 예를 들어 사용자가 "4"를 클릭하고 쿼리에 "4" 이상에 대한 필터가 포함되어 있으면 4 이상인 각 등급의 개수가 반환됩니다.  

**정확한 수의 패싯을 가져오는지 확인**

경우에 따라 패싯 수가 결과 집합과 일치하지 않을 수 있습니다( [Azure Search의 패싯 탐색(포럼 게시물)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)참조).

패싯 수는 분할 아키텍처로 인해 부정확할 수 있습니다. 모든 검색 인덱스에는 여러 개의 분할된 데이터베이스가 있으며, 각 분할된 데이터베이스는 문서 수에 따라 상위 N개의 패싯을 보고합니다. 이 값이 단일 결과로 통합됩니다. 분할된 데이터베이스 중에 일치하는 값이 많은 것과 적은 것이 있는 경우 결과에서 일부 패싯 값이 누락되거나 적은 개수로 나타날 수 있습니다.

이 문제는이 동작이 발생 하는 경우 언제 든 지 변경 될 수, 있지만 해결할 수 있습니다이 값으로 인위적으로 수:\<수 > 많은 각 분할 된 데이터베이스에서 전체 보고 하도록 적용할 수 있습니다. count: 값이 필드의 고유 값 수보다 크거나 같으면 정확한 결과가 반환됩니다. 그러나 문서 수가 많은 경우에는 성능이 저하되므로 이 옵션을 신중하게 사용해야 합니다.

### <a name="user-interface-tips"></a>사용자 인터페이스 팁
**패싯 탐색의 각 필드에 대한 레이블 추가**

레이블은 일반적으로 HTML 양식(샘플 애플리케이션의 `index.cshtml`)으로 정의됩니다. Azure Search에는 패싯 탐색 레이블 또는 다른 메타데이터에 사용할 수 있는 API가 없습니다.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>범위를 기준으로 필터링
값 범위에 대한 패싯은 일반적인 검색 애플리케이션 요구 사항입니다. 범위는 숫자 데이터 및 DateTime 값에 대해 지원됩니다. 각 접근 방법에 대한 자세한 내용은 [문서 검색(Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)을 참조하세요.

Azure Search에서는 범위를 계산하는 두 가지 방법을 제공하여 범위 생성을 간소화합니다. 두 방법 모두에 대해 Azure Search에서는 사용자가 입력을 제공한 경우 적절한 범위를 만듭니다. 예를 들어 10|20|30 범위 값을 지정한 경우 0-10, 10-20, 20-30 범위가 자동으로 만들어집니다. 비어 있는 간격을 애플리케이션에서 선택적으로 제거할 수도 있습니다. 

**방법 1: interval 매개 변수 사용**  
10달러 단위로 증분되는 가격 패싯을 설정하려면 다음과 같이 지정합니다. `&facet=price,interval:10`

**방법 2: 값 목록 사용**  
 숫자 데이터의 경우 값 목록을 사용할 수 있습니다.  다음과 같이 렌더링된 `listPrice`의 패싯 범위를 가정해 보겠습니다.

  ![샘플 값 목록][5]

이전 스크린샷과 같은 패싯 범위를 지정하려면 값 목록을 사용합니다.

    facet=listPrice,values:10|25|100|500|1000|2500

각 범위는 0부터 작성되며, 목록의 값을 엔드포인트로 사용하고 이전 범위를 잘라 불연속 간격을 만듭니다. Azure Search는 이러한 작업을 패싯 탐색의 일부로 수행합니다. 각 간격을 구성하기 위해 코드를 작성할 필요가 없습니다.

### <a name="build-a-filter-for-a-range"></a>범위에 대한 필터 작성
사용자가 선택한 범위에 따라 문서를 필터링하려면 범위의 엔드포인트를 정의하는 두 부분으로 구성된 식에서 `"ge"` 및 `"lt"` 필터 연산자를 사용하면 됩니다. 예를 들어 `listPrice` 필드의 범위를 10-25로 선택하면 필터는 `$filter=listPrice ge 10 and listPrice lt 25`가 됩니다. 샘플 코드의 필터 식에서는 **priceFrom** 및 **priceTo** 매개 변수를 사용하여 엔드포인트를 설정합니다. 

  ![값 범위 쿼리][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>거리를 기준으로 필터링
일반적으로 필터를 사용하면 현재 위치와의 근접성에 따라 매장, 레스토랑 또는 목적지를 쉽게 선택할 수 있습니다. 이 유형의 필터는 패싯 탐색처럼 보일 수 있지만 단순한 필터입니다. 특별히 이와 같은 특정 디자인 문제에 대한 구현 조언을 구하는 경우 이 점에 주의해야 합니다.

Azure Search에는 **geo.distance** 및 **geo.intersects**라는 두 개의 지리 공간 함수가 있습니다.

* **geo.distance** 함수는 두 점 사이의 거리를 킬로미터 단위로 반환합니다. 한 점은 필드이고 다른 점은 필터의 일부로 전달되는 상수입니다. 
* **geo.intersects** 함수는 주어진 점이 주어진 다각형 내부에 있으면 true를 반환합니다. 점은 필드이고, 다각형은 필터의 일부로 전달되는 좌표의 상수 목록으로 지정됩니다.

필터 예제는 [OData 식 구문(Azure Search)](query-odata-filter-orderby-syntax.md)에서 확인할 수 있습니다.

<a name="tryitout"></a>

## <a name="try-the-demo"></a>데모 사용해 보기
Azure Search 구직 포털 데모에는 이 문서에 나와 있는 예제가 포함되어 있습니다.

-   [Azure Search 구직 포털 데모](https://azjobsdemo.azurewebsites.net/)에서 온라인으로 작업 데모를 살펴보고 테스트하세요.

-   [GitHub의 Azure 샘플 리포지토리](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)에서 코드를 다운로드하세요.

검색 결과로 작업할 때 쿼리 구문의 변경 내용에 대한 URL을 확인하세요. 이 애플리케이션은 선택 시 URI에 패싯을 추가합니다.

1. 데모 앱의 매핑 기능을 사용하려면 [Bing Maps 개발자 센터](https://www.bingmapsportal.com/)에서 Bing Maps 키를 가져옵니다. 가져온 키를 `index.cshtml` 페이지의 기존 키 위에 붙여 넣습니다. `Web.config` 파일의 `BingApiKey` 설정은 사용되지 않습니다. 

2. 애플리케이션을 실행합니다. 원한다면 대화 상자를 둘러보고 대화 상자를 닫습니다.
   
3. "분석가" 등의 검색 용어를 입력하고 검색 아이콘을 클릭합니다. 쿼리가 신속하게 실행됩니다.
   
   검색 결과와 함께 패싯 탐색 구조도 반환됩니다. 검색 결과 페이지의 패싯 탐색 구조에 각 패싯 결과의 개수가 포함됩니다. 패싯을 선택하지 않았으므로 일치하는 모든 결과가 반환됩니다.
   
   ![패싯을 선택하기 전의 검색 결과][11]

4. 직함, 위치 또는 최소 급여를 클릭합니다. 패싯은 초기 검색 시 null이었지만 값을 취하는 순간 더 이상 일치하지 않는 항목이 검색 결과에서 잘립니다.
   
   ![패싯을 선택한 후의 검색 결과][12]

5. 다른 쿼리 동작을 시도할 수 있도록 패싯 쿼리를 지우려면 선택한 패싯 뒤에 있는 `[X]`를 클릭하여 패싯을 지웁니다.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>자세한 정보
[Azure Search 심층 정보](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)를 살펴보세요. 45분 25초 구간에 패싯을 구현하는 방법에 대한 데모가 있습니다.

패싯 탐색의 디자인 원칙에 대한 자세한 내용은 다음 링크를 참조하는 것이 좋습니다.

* [패싯 검색을 위한 디자인](http://www.uie.com/articles/faceted_search/)
* [디자인 패턴: 패싯 탐색](https://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: https://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[https://www.odata.org/documentation/odata-version-2-0/overview/]: https://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

