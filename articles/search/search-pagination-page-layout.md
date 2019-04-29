---
title: 검색 결과 작업 방법 - Azure Search
description: 검색 결과를 구조화 및 정렬하고 문서 수를 가져오고 Azure Search의 검색 결과에 콘텐츠 탐색을 추가합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7a4423449931b74afa15ca238c611f54b071402f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283846"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Azure Search에서 검색 결과 작업 방법
이 문서에서는 총 개수, 문서 검색, 정렬 순서 및 탐색과 같은 검색 결과 페이지의 표준 요소를 구현하는 방법에 대한 지침을 제공합니다. 데이터 또는 정보를 검색 결과에 적용하는 페이지 관련 옵션은 Azure Search Service에 전송된 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 요청을 통해 지정됩니다. 

REST API의 요청에는 GET 명령, 경로 및 서비스에 필요한 것과 응답을 작성하는 방법을 서비스에 알려주는 쿼리 매개 변수가 포함됩니다. .NET SDK에서 해당하는 API는 [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet)입니다.

몇 가지 코드 샘플에는 웹 프런트 엔드 인터페이스가 포함되어 있으며 다음에서 확인할 수 있습니다. [New York City 작업 데모 앱](https://azjobsdemo.azurewebsites.net/) 및 [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)

> [!NOTE]
> 유효한 요청에는 서비스 URL 및 경로, HTTP 동사, `api-version` 등과 같은 요소의 숫자가 포함됩니다. 요약하자면, 페이지 매김에 관련된 구문만 강조하기 위해 예제를 잘라냈습니다. 요청 구문에 대 한 자세한 내용은 참조 하세요. [Azure Search 서비스 REST](https://docs.microsoft.com/rest/api/searchservice)합니다.
>

## <a name="total-hits-and-page-counts"></a>총 적중 수 및 페이지 수
쿼리에서 반환된 결과의 총 수를 표시한 후 해당 결과를 더 작은 청크로 반환하는 것은 모든 검색 페이지의 기반이 됩니다.

![][1]

Azure Search에서는 `$count`, `$top` 및 `$skip` 매개 변수를 사용하여 이러한 값을 반환합니다. 다음 예제에서는 `@OData.count`로 반환되는 "onlineCatalog" 인덱스의 총 방문 횟수에 대한 샘플 요청을 보여줍니다.

        GET /indexes/onlineCatalog/docs?$count=true

첫 페이지에서 시작하여 15개 그룹에 대한 문서를 검색하고, 총 적중 수도 표시합니다.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

페이지 매김 결과에는 `$top`이(가) 일괄적으로 반환할 항목의 수를 지정하고 `$skip`은(는) 건너뛸 항목 수를 지정하는 `$top` 및 `$skip`이(가) 모두 필요합니다. 다음 예제에서는 각 페이지에서 `$skip` 매개 변수로 증분 점프하여 증가하는 다음 15개 항목을 보여줍니다.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>레이아웃
검색 결과 페이지에서 축소판 이미지, 필드의 하위 집합 및 전체 제품 페이지에 대한 링크를 표시할 수 있습니다.

 ![][2]

Azure Search에서는 `$select` 및 조회 명령을 사용하여 이 환경을 구현합니다.

타일화된 레이아웃에 대한 필드의 하위 집합을 반환하는 방법:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

이미지 및 미디어 파일은 직접 검색할 수 없으며, 비용을 줄이기위해 Azure Blob Storage와 같은 다른 스토리지 플랫폼에 저장해야 합니다. 인덱스 및 문서에서 외부 콘텐츠의 URL 주소를 저장하는 필드를 정의합니다. 그러면 필드를 이미지 참조로 사용할 수 있습니다. 이미지에 대한 URL은 문서에 있어야 합니다.

**onClick** 이벤트에 대한 제품 설명 페이지를 검색하려면 [문서 조회](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 를 사용하여 검색할 문서의 키에 전달합니다. 키의 데이터 형식은 `Edm.String`입니다. 이 예제에서는 *246810*입니다. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>관련성, 등급, 또는 가격 기준으로 정렬
정렬 순서는 종종 기본적으로 관련도에 따르지만, 고객이 다른 순위 순서로 기존 결과를 신속하게 바꿀 수 있도록 대체 정렬 순서를 준비해 놓는 것이 일반적입니다.

 ![][3]

Azure Search에서 정렬은 `"Sortable": true.`로 인덱싱되는 모든 필드에 대해 `$orderby` 식을 기반으로 합니다. `$orderby` 절은 OData 식입니다. 구문에 대한 자세한 내용은 [필터 및 order-by 절의 OData 식 구문](query-odata-filter-orderby-syntax.md)을 참조하세요.

관련성은 프로필 점수 매기기와 강력하게 연관됩니다. 검색 단어가 더 많이 또는 더 강력하게 일치되는 문서에 더 높은 점수를 매기는 텍스트 분석 및 통계에 따라 모든 결과의 순서를 정하는 기본 점수를 사용할 수 있습니다.

대체 정렬 순서는 일반적으로 정렬 순서를 작성하는 메서드를 다시 호출하는 **onClick** 이벤트에 관련이 있습니다. 예를 들어 이 페이지 요소를 가정합니다.

 ![][4]

선택한 정렬 옵션을 입력으로 받아 메서드를 생성하고 해당 옵션과 관련된 조건에 대한 정렬된 목록을 반환할 수 있습니다.

 ![][5]

> [!NOTE]
> 기본 점수 매기기는 다양한 시나리오에 적용할 수 있으므로 대신 사용자 지정 점수 매기기 프로필의 관련성에 기반하는 것이 좋습니다. 사용자 지정 점수 매기기 프로필은 비즈니스에 더 많은 이점을 제공하는 항목 강화 방법을 제공합니다. 자세한 내용은 [점수 매기기 프로필 추가](index-add-scoring-profiles.md)를 참조하세요. 
> 
> 

## <a name="faceted-navigation"></a>패싯 탐색
종종 페이지의 옆쪽 또는 위쪽에 있는 검색 탐색은 결과 페이지에서 일반적입니다. Azure Search에서는 미리 정의된 필터에 따라 패싯 탐색이 자기 주도 탐색을 제공합니다. 자세한 내용은 [Azure Search의 패싯 탐색](search-faceted-navigation.md)을 참조하십시오.

## <a name="filters-at-the-page-level"></a>페이지 수준의 필터
솔루션 디자인이 특정 유형의 콘텐츠에 대한 전용 검색 페이지를 포함하는 경우(예를 들어 페이지 위쪽에 부서 목록이 있는 온라인 소매상 애플리케이션), [필터 식](search-filters.md)과 함께 미리 필터링된 상태로 페이지를 여는 **onClick** 이벤트를 삽입할 수 있습니다. 

검색 식의 사용 여부에 관계 없이 필터를 보낼 수 있습니다. 예를 들어 다음 요청은 브랜드 이름으로 필터링하고 일치하는 문서만 반환합니다.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

`$filter` 식에 대한 자세한 내용은 [문서 검색(Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)을 참조하십시오.

## <a name="see-also"></a>관련 항목
* [Azure Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice)
* [인덱스 작업](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [문서 작업](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Azure Search의 패싯 탐색](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
