---
title: 검색 결과를 사용 하는 방법
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 검색 결과를 구조화 및 정렬 하 고, 문서 수를 가져오고, 검색 결과에 콘텐츠 탐색을 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 08641814e2a4fdf6f174f94b1e38e4124cf531d0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934925"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 결과를 사용 하는 방법

이 문서에서는 총 일치 문서 수, 페이지를 매긴 결과, 정렬 된 결과 및 적중 강조 표시 된 용어와 함께 반환 되는 쿼리 응답을 가져오는 방법을 설명 합니다.

응답의 구조는 쿼리의 매개 변수에 의해 결정 됩니다. REST API 또는 .NET SDK의 [DocumentSearchResult 클래스](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) 에 있는 [검색 문서를 검색](/rest/api/searchservice/Search-Documents) 합니다.

## <a name="result-composition"></a>결과 컴퍼지션

검색 문서는 많은 수의 필드로 구성 될 수 있지만 일반적으로 결과 집합의 각 문서를 표시 하는 데에는 몇 가지만 필요 합니다. 쿼리 요청에서을 추가 `$select=<field list>` 하 여 응답에 표시 되는 필드를 지정 합니다. 필드는 결과에 포함 될 인덱스에서 **검색할** 수 있도록 특성을 지정 해야 합니다. 

가장 잘 작동 하는 필드는 문서를 대조 하 고 구분 하는 필드를 포함 하 여 사용자의 특정 부분에 대 한 클릭 응답을 초대 하는 데 충분 한 정보를 제공 합니다. 전자 상거래 사이트에는 제품 이름, 설명, 브랜드, 색, 크기, 가격 및 등급이 있을 수 있습니다. 호텔-샘플 인덱스 기본 제공 샘플의 경우 다음 예제에서 필드가 될 수 있습니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 제품 사진이 나 로고와 같은 결과에 이미지 파일을 포함 하려면 Azure Cognitive Search 외부에 저장 하지만 검색 문서에서 이미지 URL을 참조 하는 필드를 인덱스에 포함 합니다. 결과에서 이미지를 지 원하는 샘플 인덱스에는이 [빠른](search-create-app-portal.md)시작에서 제공 되는 **realestate-us-sample-Us** 데모와 [뉴욕 도시 작업 데모 앱](https://aka.ms/azjobsdemo)이 포함 됩니다.

## <a name="paging-results"></a>페이징 결과

기본적으로 검색 엔진은 쿼리가 전체 텍스트 검색 인지 또는 정확한 일치 쿼리를 위한 임의의 순서로 검색 점수에 따라 결정 되는 처음 50 일치 항목까지 반환 합니다.

다른 개수의 일치 하는 문서를 반환 하려면 `$top` 및 `$skip` 매개 변수를 쿼리 요청에 추가 합니다. 다음 목록에서는 논리에 대해 설명 합니다.

+ `$count=true`인덱스 내에 있는 총 일치 문서 수를 가져오려면를 추가 합니다.

+ 15 개의 일치 하는 문서의 첫 번째 집합과 총 일치 항목 수를 반환 합니다. `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 두 번째 집합을 반환 하 고 첫 15를 건너뛰고 다음 15:을 가져옵니다 `$top=15&$skip=15` . 세 번째 15 번째 집합에 대해 동일한 작업을 수행 합니다. `$top=15&$skip=30`

기본 인덱스가 변경 되는 경우 페이지가 매겨진 쿼리 결과가 안정적이 지 않을 수 있습니다. 페이징은 `$skip` 각 페이지에 대 한 값을 변경 하지만 각 쿼리는 독립적 이며 쿼리 시의 인덱스에 있는 데이터의 현재 보기에서 작동 합니다. 즉, 일반적인 용도의 데이터베이스에 있는 것과 같은 결과의 캐싱 또는 스냅숏이 없습니다.
 
다음은 중복을 얻는 방법에 대 한 예입니다. 4 개의 문서를 포함 하는 인덱스를 가정 합니다.

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
이제 등급 별로 정렬 된 결과를 한 번에 두 번 반환 하려는 경우를 가정 합니다. 이 쿼리를 실행 하 여 결과의 첫 번째 페이지를 가져올 수 있습니다. 즉 `$top=2&$skip=0&$orderby=rating desc` , 다음과 같은 결과를 생성 합니다.

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
서비스에서는 쿼리 호출 사이에 다섯 번째 문서가 인덱스에 추가 된 것으로 가정 합니다. `{ "id": "5", "rating": 4 }`  잠시 후에 두 번째 페이지를 인출 하는 쿼리를 실행 하 `$top=2&$skip=2&$orderby=rating desc` 고 이러한 결과를 가져옵니다.

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
문서 2는 두 번 인출 됩니다. 새 문서 5는 등급에 대 한 값이 크므로 문서 2 보다 먼저 정렬 되 고 첫 번째 페이지에 도달 하기 때문입니다. 이 동작은 예기치 않을 수 있지만 검색 엔진의 동작 방식에 일반적입니다.

## <a name="ordering-results"></a>결과 정렬

전체 텍스트 검색 쿼리의 경우 결과는 검색 점수를 기준으로 자동으로 순위가 매겨집니다 .이 점수는 문서에서 용어 빈도 및 근접을 기준으로 계산 되며 검색 단어에 대 한 일치 항목이 나 더 강력 하 게 일치 하는 문서에 대 한 점수가 높아집니다. 

검색 점수는 동일한 결과 집합의 다른 문서와 비교 하 여 일치의 강도를 반영 하는 일반적인 관련성을 전달 합니다. 점수는 쿼리를 사용할 때와 항상 일치 하는 것은 아니므로 쿼리를 사용할 때 검색 문서 정렬 방법이 약간 달라질 수 있습니다. 이러한 상황이 발생할 수 있는 이유에 대 한 몇 가지 설명이 있습니다.

| 원인 | Description |
|-----------|-------------|
| 데이터 변동성 | 인덱스 내용에 따라 문서를 추가, 수정 또는 삭제할 수 있습니다. 인덱스 업데이트가 시간에 따라 처리 되 면 일치 하는 문서의 검색 점수에 영향을 주기 때문에 용어 빈도가 변경 됩니다. |
| 여러 복제본 | 여러 복제본을 사용 하는 서비스의 경우 각 복제본에 대해 병렬로 쿼리가 실행 됩니다. 검색 점수를 계산 하는 데 사용 되는 인덱스 통계는 복제본 별로 계산 되며 결과는 쿼리 응답에 병합 되 고 정렬 됩니다. 복제본은 대부분 서로 미러 되지만 상태 차이가 많지 않기 때문에 통계가 다를 수 있습니다. 예를 들어, 하나의 복제본이 해당 통계에 영향을 주는 문서를 삭제 했을 수 있습니다 .이 문서는 다른 복제본에 병합 되었습니다. 일반적으로 복제본 별 통계의 차이점은 작은 인덱스에서 더 두드러집니다. |
| 동일한 점수 | 여러 문서의 점수가 동일한 경우 그 중 하나는 먼저 표시 될 수 있습니다.  |

### <a name="consistent-ordering"></a>일관 된 순서 지정

결과 정렬에 flex가 지정 된 경우 일관성이 응용 프로그램 요구 사항인 경우 다른 옵션을 탐색 하는 것이 좋습니다. 가장 쉬운 방법은 등급 또는 날짜와 같은 필드 값을 기준으로 정렬 하는 것입니다. 등급 또는 날짜와 같은 특정 필드를 기준으로 정렬 하려는 시나리오의 경우 **정렬할**수 있는 것으로 인덱싱되는 모든 필드에 적용할 수 있는 [ `$orderby` 식을](query-odata-filter-orderby-syntax.md)명시적으로 정의할 수 있습니다.

또 다른 옵션은 [사용자 지정 점수 매기기 프로필](index-add-scoring-profiles.md)을 사용 하는 것입니다. 점수 매기기 프로필을 사용 하면 검색 결과의 항목 순위를 보다 세밀 하 게 제어할 수 있으며 특정 필드에서 찾은 일치 항목을 높일 수 있습니다. 추가 점수 매기기 논리는 각 문서에 대 한 검색 점수가 떨어져 있기 때문에 복제본 간의 사소한 차이를 무시 하는 데 도움이 될 수 있습니다. 이 방법에 대 한 [순위 알고리즘](index-ranking-similarity.md) 을 사용 하는 것이 좋습니다.

## <a name="hit-highlighting"></a>적중 항목 강조 표시

적중 항목 강조 표시는 결과에서 일치 하는 용어에 적용 되는 텍스트 서식 (예: 굵게 또는 노란색 강조 표시)을 참조 하 여 일치 항목을 쉽게 찾을 수 있도록 합니다. 적중 항목 강조 표시 명령은 [쿼리 요청](/rest/api/searchservice/search-documents)에 제공 됩니다. 

적중 항목 강조 표시를 사용 하도록 설정 하려면를 추가 `highlight=[comma-delimited list of string fields]` 하 여 강조 표시를 사용할 필드를 지정 합니다. 강조 표시는 일치 항목이 즉시 명확 하지 않은 설명 필드와 같은 긴 콘텐츠 필드에 유용 합니다. 적중 항목 강조 표시에 대 한 **검색** 가능으로 특성이 지정 된 필드 정의만 있습니다.

기본적으로 Azure Cognitive Search는 필드 당 최대 5 개의 강조 표시를 반환 합니다. 필드에 대시 다음에 정수를 추가 하 여이 수를 조정할 수 있습니다. 예를 들어 `highlight=Description-10` 은 설명 필드에서 일치 하는 내용에 대해 최대 10 개의 하이라이트를 반환 합니다.

서식 지정은 전체 용어 쿼리에 적용 됩니다. 서식 지정 형식은 태그, 및에 의해 결정 `highlightPreTag` `highlightPostTag` 되며, 코드에서 응답을 처리 합니다 (예: 굵은 글꼴 또는 노란색 배경 적용).

다음 예에서는 설명 필드 내에 "해변이", "샌드", "", "해변" 이라는 용어를 강조 표시 하도록 태그가 지정 됩니다. 유사 항목 및 와일드 카드 검색과 같은 엔진에서 쿼리 확장을 트리거하는 쿼리는 적중 항목 강조 표시를 제한적으로 지원 합니다.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>새 동작 (7 월 15 일부 터)

2020 년 7 월 15 일 이후에 만들어진 서비스는 다른 강조 표시 환경을 제공 합니다. 해당 날짜 이전에 만든 서비스는 강조 표시 동작에서 변경 되지 않습니다. 

새 동작을 사용 합니다.

* 전체 구 쿼리와 일치 하는 구가 반환 됩니다. "Super bowl" 쿼리는 다음과 같은 강조 표시를 반환 합니다.

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  *칩의 bowl* 용어는 전체 구와 일치 하지 않으므로 강조 표시 되지 않습니다.

적중 항목 강조 표시를 구현 하는 클라이언트 코드를 작성 하는 경우 이러한 변경 내용을 알고 있어야 합니다. 완전히 새로운 검색 서비스를 만들지 않는 한이 경우에는 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대 한 검색 페이지를 신속 하 게 생성 하려면 다음 옵션을 고려 합니다.

+ 포털에서 [응용 프로그램 생성기](search-create-app-portal.md)는 검색 창, 패싯 탐색 및 이미지를 포함 하는 결과 영역을 포함 하는 HTML 페이지를 만듭니다.
+ [C #에서 첫 번째 앱 만들기](tutorial-csharp-create-first-app.md) 는 기능적인 클라이언트를 구축 하는 자습서입니다. 샘플 코드는 페이지가 매겨진 쿼리, 적중 항목 강조 표시 및 정렬을 보여 줍니다.

몇 가지 코드 샘플에는 [뉴욕 도시 작업 데모 앱](https://aka.ms/azjobsdemo), [라이브 데모 사이트를 사용 하는 JavaScript 샘플 코드](https://github.com/liamca/azure-search-javascript-samples), [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)에서 찾을 수 있는 웹 프런트 엔드 인터페이스가 포함 되어 있습니다.