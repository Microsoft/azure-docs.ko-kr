---
title: 인덱스에서 검색 결과의 범위를 지정하기 위한 필터 - Azure Search
description: Microsoft Azure의 호스트된 클라우드 검색 서비스인 Azure Search에서 쿼리에 대한 검색 결과를 줄이려면 사용자 보안 ID, 언어, 지리적 위치 또는 숫자 값별로 필터링합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a9e8d2cbc067fd92208fac778ba17c58bdc7a5e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289589"
---
# <a name="filters-in-azure-search"></a>Azure Search의 필터 

*필터*는 Azure Search 쿼리에 사용되는 문서를 선택하기 위한 조건을 제공합니다. 필터링되지 않은 검색에서는 모든 문서가 인덱스에 포함됩니다. 필터는 문서의 하위 집합으로 검색 쿼리의 범위를 지정합니다. 예를 들어, 필터를 사용하면 전체 텍스트 검색을 특정 임계값 이상의 가격대에서 특정 브랜드 또는 색상을 가진 제품으로 제한할 수 있습니다.

구현 과정에서 필터 요구 사항이 있는 검색 환경도 일부 있기는 하지만 *값 기반* 조건을 사용하여 검색을 제한하려는 경우는 언제든지 필터를 사용할 수 있습니다("Simon & Schuster"에서 출판된 "논픽션" 카테고리를 찾기 위해 제품 유형 검색 범위를 "책"으로 지정).

특정 데이터 *구조*로 검색 대상을 설정하려는 경우(검색 범위를 고객 리뷰 필드로 지정) 아래에 대체 방법이 나와 있습니다.

## <a name="when-to-use-a-filter"></a>필터를 사용하는 경우

필터는 "내 주변 찾기", 패싯 탐색, 사용자가 볼 수 있는 문서만 표시하는 보안 필터를 비롯한 여러 검색 환경의 기초입니다. 이러한 환경 중 하나를 구현하는 경우 필터가 필요합니다. 지리적 위치 좌표, 사용자가 선택한 패싯 범주 또는 요청자의 보안 ID를 제공하는 검색 쿼리에 연결된 필터입니다.

다음은 예제 시나리오입니다.

1. 인덱스의 데이터 값에 따라 인덱스를 분할하는 필터를 사용합니다. 도시, 주택 유형 및 편의 시설이 포함된 스키마가 제공되면 조건(시애틀, 콘도, 해안가)을 충족하는 문서를 명시적으로 선택하기 위한 필터를 만들 수 있습니다. 

   동일한 입력을 사용하는 전체 텍스트 검색도 종종 비슷한 결과가 나오기는 하지만 필터는 인덱스의 콘텐츠에 대해 필터 용어가 정확하게 일치해야 하기 때문에 더 정확합니다. 

2. 검색 환경에 필터 요구 사항이 포함된 경우 필터를 사용합니다.

   * [패싯 탐색](search-faceted-navigation.md)은 사용자가 선택한 패싯 범주를 다시 전달하는 데 필터를 사용합니다.
   * 지리적 검색은 "내 주변 찾기" 앱에서 현재 위치의 좌표를 전달하는 데 필터를 사용합니다. 
   * 보안 필터는 보안 식별자를 필터 조건으로 전달합니다. 여기서 인덱스의 일치 항목은 문서에 액세스할 수 있는 권한을 위한 프록시 역할을 합니다.

3. 숫자 필드에서 조건을 검색하려는 경우 필터를 사용합니다. 

   숫자 필드는 문서에서 검색할 수 있고 검색 결과에 표시되기도 하지만, 개별적으로 검색할 수는 없습니다(전체 텍스트 검색에 적용). 숫자 데이터를 기반으로 하는 선택 조건이 필요한 경우 필터를 사용합니다.

### <a name="alternative-methods-for-reducing-scope"></a>범위를 줄이기 위한 대체 방법

검색 결과를 효과적으로 줄이고 싶을 때 필터 외에 다른 방법도 있습니다. 목적에 따라 이러한 대안이 더 나을 수 있습니다.

 + `searchFields` 쿼리 매개 변수는 검색을 특정 필드로 고정합니다. 예를 들어, 인덱스에서 영어와 스페인어 설명을 위한 별도의 필드를 제공하는 경우 searchFields를 사용하여 전체 텍스트 검색에 사용할 대상 필드를 지정할 수 있습니다. 

+ `$select` 매개 변수는 결과 집합에 포함할 필드를 지정하는 데 사용되며 호출 애플리케이션에 보내기 전에 효과적으로 응답을 조정합니다. 이 매개 변수는 쿼리를 구체화하거나 문서 컬렉션을 줄이지 않지만 세부적인 응답이 목표인 경우 이 매개 변수 옵션을 고려할 수 있습니다. 

두 매개 변수에 대한 자세한 내용은 [문서 검색 > 요청 > 쿼리 매개 변수](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)를 참조하세요.


## <a name="filters-in-the-query-pipeline"></a>쿼리 파이프라인의 필터

쿼리 시 필터 파서는 조건을 입력으로 받아 표현식을 원자성 부울 식으로 변환하고 필터 트리를 빌드하여 요인덱스의 필터링이 가능한 필드를 통해 평가합니다.  

필터링은 검색 전에 이루어지며 문서 검색 및 관련성 평가를 위해 다운스트림 처리에 포함할 문서를 선별합니다. 필터가 검색 문자열과 함께 사용되면 후속 검색 작업의 노출 영역이 효과적으로 줄어듭니다. 단독으로 사용되면(예를 들어 `search=*` 같이 쿼리 문자열이 비어 있는 경우) 필터 조건이 유일한 입력입니다. 

## <a name="filter-definition"></a>필터 정의

필터는 [Azure Search에서 지원되는 OData V4 구문의 하위 집합](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 사용하여 표현된 OData 표현식입니다. 

**검색** 작업마다 하나의 필터를 지정할 수 있지만 필터 자체에는 여러 필드와 여러 조건을 포함할 수 있으며 **ismatch** 함수를 사용하면 여러 표현식을 사용할 수도 있습니다. 여러 부분으로 이루어진 필터 식에서 조건자의 순서는 임의로 지정할 수 있습니다. 특정한 순서로 조건자를 다시 정렬해도 성능에 별다른 도움은 되지 않습니다.

필터 식의 하드 제한은 요청의 최대 제한입니다. 필터를 포함한 전체 요청은 POST의 경우 최대 16MB, GET의 경우 최대 8KB가 될 수 있습니다. 소프트 제한은 필터 식의 절 수와 관련이 있습니다. 경험에 따르면 수백 개의 절이 있는 경우 제한에 도달할 위험이 있습니다. 애플리케이션을 설계할 때 필터의 크기를 무제한으로 생성하지 않는 것이 좋습니다.

다음 예제는 여러 API의 정형화된 필터 정의를 나타냅니다.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>필터 디자인 패턴

다음 예제에는 필터 시나리오에 대한 여러 디자인 패턴이 나와 있습니다. 자세한 내용은 [OData 식 구문 > 예제](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples)를 참조하세요.

+ 독립 실행형 **$filter**는 쿼리 문자열 없이, 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기 및 순위 지정 등이 없으며 검색 문자열은 비어 있습니다.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ 쿼리 문자열과 **$filter**의 조합에서 필터가 하위 집합을 만들면 쿼리 문자열이 필터링된 하위 집합에 대해 전체 텍스트 검색에 용어 입력을 제공합니다. 쿼리 문자열과 함께 필터를 사용하는 것이 가장 일반적인 코드 패턴입니다.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ "or"로 구분된 복합 쿼리에서는 각각 자체 필터 조건이 있습니다. 예를 들어, '개'는 '비글', '고양이'는 '샤미즈' 등입니다. OR 식은 개별적으로 평가되며 각각의 응답이 하나의 응답으로 결합되어 호출 애플리케이션으로 다시 전송됩니다. 이 디자인 패턴은 search.ismatch 함수를 통해 구현됩니다. 점수 매기기 아닌 버전(search.ismatch) 또는 점수 매기기 버전(search.ismatchscoring)을 사용할 수 있습니다.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

특정 사용 사례에 대한 포괄적인 지침은 다음 문서를 참조하세요.

+ [패싯 필터](search-filters-facets.md)
+ [언어 필터](search-filters-language.md)
+ [보안 조정](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>필터링을 위한 필드 요구 사항

REST API에서는 필터링 가능이 기본적으로 *설정*되어 있습니다. 필터링 가능 필드는 인덱스 크기가 늘어나기 때문에 필터에서 실제로 사용하지 않는 필드에 대해서는 `filterable=FALSE`로 설정합니다. 필드 정의 설정에 대한 자세한 내용은 [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)(인덱스 만들기)를 참조하세요.

.NET SDK에서는 필터링 가능이 기본적으로 *해제*되어 있습니다. 필터링 가능 속성 설정에 대한 API는 [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)입니다. 아래 예제에서는 BaseRate 필드 정의에 설정되어 있습니다.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>인덱스 다시 만들기 요구 사항

필드가 필터링 가능이 아니어서 필터링 가능으로 설정하려는 경우 새 필드를 추가하거나 기존 필드를 다시 빌드해야 합니다. 필드 정의를 변경하면 인덱스의 물리적 구조가 변경됩니다. Azure Search에서는 빠른 쿼리 속도를 위해 허용되는 모든 액세스 경로가 인덱싱되므로 필드 정의가 변경될 때 데이터 구조가 다시 빌드되어야 합니다. 

개별 필드를 다시 빌드하면 기존 문서 키와 연관된 값을 인덱스로 전송하는 병합 작업만 필요하고 각 문서의 나머지 부분은 그대로 두기 때문에 작업에 별로 영향을 미치지 않습니다. 다시 빌드 요구 사항이 발생 하면, 참조 [인덱싱 작업 (업로드, merge, mergeOrUpload 삭제)](search-what-is-data-import.md#indexing-actions) 옵션의 목록에 대 한 합니다.


## <a name="text-filter-fundamentals"></a>텍스트 필터 기본 사항

텍스트 필터는 검색 모음 내의 값을 기반으로 임의의 문서 모음을 일부 가져 오려는 문자열 필드에 유효합니다.

문자열로 구성된 텍스트 필터의 경우에는 어휘 분석 또는 단어 분리가 없으므로 정확한 일치 항목만 비교합니다. 예를 들어, 필드 *f*에 "sunny day"가 포함되어 있다고 가정합니다. `$filter=f eq 'Sunny'`는 일치하지 않지만 `$filter=f eq 'Sunny day'`는 일치합니다. 

텍스트 문자열은 대/소문자를 구분합니다. 대문자 단어에 대해 소문자를 검색하지 않습니다. 따라서 `$filter=f eq 'Sunny day'`는 'sunny day'를 찾지 않습니다.


| 접근 방식 | 설명 | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | 지정된 필드에 대해 쉼표로 구분된 문자열 목록을 제공하는 함수입니다. 문자열은 필터 조건을 구성하며 쿼리의 범위에 있는 모든 필드에 적용됩니다. <br/><br/>`search.in(f, ‘a, b, c’)`는 값 목록이 클 때 훨씬 빠르게 실행된다는 점을 제외하고는 `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`와 의미가 동일합니다.<br/><br/>[보안 필터](search-security-trimming-for-azure-search.md) 및 지정된 필드의 값과 일치하는 원시 텍스트로 구성된 필터의 경우 **search.in** 함수를 사용하는 것이 좋습니다. 이 방식은 속도를 고려해 설계되었습니다. 수백에서 수천 개의 값을 처리하는 데 1초도 걸리지 않습니다. 함수에 전달할 수 있는 항목 수에 명시적인 제한은 없지만 제공하는 문자열의 수에 비례하여 대기 시간이 늘어납니다. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | 동일한 필터 식에서 전체 텍스트 검색 작업과 엄격한 부울 필터 작업을 혼합할 수 있게 해주는 함수입니다. 하나의 요청에서 여러 개의 쿼리 필터 조합을 사용합니다. 크기가 더 큰 문자열 내에서 부분적으로 문자열을 필터링하기 위해 *contains* 필터에 대해 이를 사용할 수도 있습니다. |  
| [$filter=필드 연산자 문자열](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | 필드, 연산자 및 값으로 구성된 사용자 정의 식입니다. | 

## <a name="numeric-filter-fundamentals"></a>숫자 필터 기본 사항

숫자 필드는 전체 텍스트 검색의 컨텍스트에서 `searchable`이 아닙니다. 문자열만 전체 텍스트 검색이 가능합니다. 예를 들어, 검색어로 99.99를 입력하면 $99.99로 책정된 항목이 반환되지 않고 문서의 문자열 필드에 숫자 99가 있는 항목이 표시됩니다. 따라서 숫자 데이터가 있는 경우 범위, 패싯, 그룹 등을 포함하여 필터에 사용한다고 가정합니다. 

숫자 필드(가격, 크기, SKU, ID)를 포함하는 문서는 필드가 `retrievable`로 표시되면 검색 결과에서 해당 값을 제공합니다. 여기서 핵심은 전체 텍스트 검색 자체가 숫자 필드 유형에는 적용되지 않는다는 것입니다.

## <a name="next-steps"></a>다음 단계

먼저 포털의 **검색 탐색기**에서 **$filter** 매개 변수를 사용하여 쿼리를 제출합니다. 필터링된 다음 쿼리를 검색 창에 붙여넣으면 [부동산 샘플 인덱스](search-get-started-portal.md)에서 해당 쿼리에 대한 흥미로운 결과를 제공합니다.

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

더 많은 예제를 사용하려면 [OData 필터 식 구문 > 예제](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples)를 참조하세요.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [지원되는 데이터 유형](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
