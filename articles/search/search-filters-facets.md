---
title: 앱에서 검색 탐색을 위한 패싯 필터
titleSuffix: Azure Cognitive Search
description: Microsoft Azure의 호스티드 클라우드 검색 서비스인 Azure Cognitive Search에서 쿼리의 검색 결과를 줄이려면 사용자 보안 ID, 지리적 위치 또는 숫자 값별로 조건을 필터링합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9360fc000e01e1c52561cbaa3e2f2968e67e2fa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91740873"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Azure Cognitive Search에서 패싯 필터를 작성하는 방법 

패싯 탐색은 검색 앱의 쿼리 결과에 대한 자기 주도형 필터링에 사용됩니다. 여기서 애플리케이션은 검색 범위를 문서 그룹(예: 범주 또는 브랜드)으로 지정하기 위한 UI 컨트롤을 제공합니다. Azure Cognitive Search는 환경을 지원하는 데이터 구조를 제공합니다. 이 문서에서는 제공할 검색 환경을 뒷받침하는 패싯 탐색 구조를 만드는 기본 단계를 신속하게 검토합니다. 

> [!div class="checklist"]
> * 필터링 및 패싯을 위한 필드 선택
> * 필드에 특성 설정
> * 인덱스 빌드 및 데이터 로드
> * 쿼리에 패싯 필터 추가
> * 결과 처리

패싯은 동적이며 쿼리에서 반환됩니다. 검색 응답은 결과를 탐색하는 데 사용되는 패싯 범주를 가져옵니다. 패싯에 익숙하지 않은 경우 다음 예제는 패싯 탐색 구조를 보여줍니다.

:::image type="complex" source="media/search-filters-facets/facet-nav.png" alt-text="필터링된 검색 결과":::
“필터링된 검색 결과가 비즈니스 제목별로 그룹화된 검색 대화 상자 이미지. 화살표는 결과가 패싯 탐색 구조에 표시되는 패싯임을 나타냅니다.” :::image-end:::

패싯 탐색이 생소하여 자세한 정보가 필요한가요? [Azure Cognitive Search에서 패싯 탐색을 구현하는 방법](search-faceted-navigation.md)을 참조하세요.

## <a name="choose-fields"></a>필드 선택

패싯은 단일 값 필드 및 컬렉션을 통해 계산할 수 있습니다. 패싯 탐색에서 가장 성능이 뛰어난 필드는 카디널리티가 낮습니다. 즉, 검색 코퍼스의 문서 전체에서 반복되는 고유한 값(예 : 색, 국가/지역 또는 브랜드 이름 목록)이 적습니다. 

인덱스를 만들 때 `facetable` 특성을 `true`로 설정하여 필드별로 패싯을 사용하도록 설정합니다. 또한 최종 사용자가 선택한 패싯을 기준으로 검색 애플리케이션에서 필드를 필터링할 수 있도록 일반적으로 해당 필드의 `filterable` 특성도 `true`로 설정해야 합니다. 

REST API를 사용하여 인덱스를 만들 때 패싯 탐색에 사용할 수 있는 [필드 형식](/rest/api/searchservice/supported-data-types)은 기본적으로 `facetable`로 표시됩니다.

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 숫자 필드 형식: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ 위 형식의 컬렉션(예: `Collection(Edm.String)` 또는 `Collection(Edm.Double)`)

`Edm.GeographyPoint` 또는 `Collection(Edm.GeographyPoint)` 필드는 패싯 탐색에서 사용할 수 없습니다. 카디널리티가 낮은 필드에서 패싯 성능이 가장 뛰어납니다. 지리적 좌표를 확인하기 때문에 지정된 데이터 세트에서 두 개의 좌표 집합이 동일한 경우는 거의 없습니다. 따라서 지리적 좌표에는 패싯이 지원되지 않습니다. 위치별로 패싯을 만들려면 도시 또는 지역 필드가 필요합니다.

## <a name="set-attributes"></a>특성 설정

필드가 사용되는 방식을 제어하는 인덱스 특성은 인덱스의 개별 필드 정의에 추가됩니다. 다음 예제에서 패싯에 유용한 카디널리티가 낮은 필드는 `category`(hotel, motel, hostel), `tags`, `rating`으로 구성됩니다. 설명을 위해 다음 예제에서는 해당 필드의 `filterable` 및 `facetable` 특성이 명시적으로 설정되었습니다. 

> [!Tip]
> 성능 및 스토리지 최적화를 위한 최고의 방법으로, 패싯으로 사용하지 말아야 하는 필드에 대해 패싯을 해제합니다. 특히 ID 또는 제품 이름과 같은 고유한 값의 문자열 필드는 패싯 탐색에서 실수로(그리고 비효과적으로) 사용되지 않도록 `"facetable": false`로 설정해야 합니다.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> 이 인덱스 정의는 [REST API를 사용하여 Azure Cognitive Search 인덱스 만들기](./search-get-started-powershell.md)에서 복사됩니다. 필드 정의의 피상적인 차이점을 제외하고는 동일합니다. `filterable` 및 `facetable` 특성은 `category`, `tags`, `parkingIncluded`, `smokingAllowed`, `rating` 필드에서 명시적으로 추가됩니다. 실제로 REST API를 사용하는 경우 해당 필드에서 `filterable` 및 `facetable`이 기본적으로 사용하도록 설정됩니다. .NET SDK를 사용하는 경우에는 해당 특성을 명시적으로 사용하도록 설정해야 합니다.

## <a name="build-and-load-an-index"></a>인덱스 빌드 및 로드

중간(아마도 명백한) 단계에서 쿼리를 작성하기 전에 [인덱스를 빌드하고 채워야 합니다](./search-get-started-dotnet.md#1---create-an-index). 이 단계는 완전성을 위해 언급하는 것입니다. 인덱스 사용 가능 여부를 확인하는 한 가지는 [포털](https://portal.azure.com)에서 인덱스 목록을 확인하는 것입니다.

## <a name="add-facet-filters-to-a-query"></a>쿼리에 패싯 필터 추가

애플리케이션 코드에서 요청을 식으로 구성하는 데 사용되는 검색 식, 패싯, 필터, 채점 프로필을 비롯한 유효한 쿼리의 모든 부분을 지정하는 쿼리를 구문으로 작성하십시오. 다음 예제에서는 숙박 유형, 평점 및 기타 편의 시설에 따라 패싯 탐색을 만드는 요청을 작성합니다.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>클릭 이벤트로 필터링된 결과 반환하기

최종 사용자가 패싯 값을 클릭할 때 click 이벤트 처리기는 필터 식을 사용하여 사용자의 의도를 인식해야 합니다. `category` 패싯의 경우 “motel” 범주 클릭은 해당 숙박 유형을 선택하는 `$filter` 식을 사용하여 구현됩니다. 모텔만 표시되어야 한다고 알리기 위해 사용자가 “motel”을 클릭하면 애플리케이션이 보내는 다음 쿼리에 `$filter=category eq 'motel'`이 포함됩니다.

다음 코드 조각은 사용자가 범주 패싯에서 값을 선택하는 경우 범주를 필터에 추가합니다.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

사용자가 `tags`와 같은 컬렉션 필드의 패싯 값(예: “pool” 값)을 클릭하면 애플리케이션에서 다음 필터 구문을 사용해야 합니다. `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>팁 및 해결 방법

### <a name="initialize-a-page-with-facets-in-place"></a>패싯이 있는 페이지 초기화

패싯이 있는 페이지를 초기화하려는 경우, 페이지 초기화의 일환으로 쿼리를 보내서 초기 패싯 구조로 페이지를 시드할 수 있습니다.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>필터링된 결과와 비동기적으로 패싯 탐색 구조 유지

Azure Cognitive Search에서 패싯 탐색의 과제 중 하나는 패싯이 현재 결과에 대해서만 존재한다는 것입니다. 실제로 사용자가 역순으로 탐색하고 검색 콘텐츠를 통해 대체 경로를 탐색하는 단계를 거슬러 올라갈 수 있도록 정적 패싯 집합을 유지하는 것이 일반적입니다. 

이것이 일반적인 사용 사례이지만, 현재 패싯 탐색 구조에는 기본적으로 제공되지 않습니다. 정적 패싯을 원하는 개발자는 일반적으로 두 개의 필터링된 쿼리를 실행하여 제한 사항을 해결합니다. 하나는 결과에 적용되고 다른 하나는 탐색 용도로 패싯의 정적 목록을 만드는 데 사용됩니다.

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 필터](search-filters.md)
+ [인덱스 REST API 만들기](/rest/api/searchservice/create-index)
+ [문서 검색 REST API](/rest/api/searchservice/search-documents)