---
title: 복합 데이터 형식을 모델링 하는 방법
titleSuffix: Azure Cognitive Search
description: ComplexType 및 Collections 데이터 형식을 사용 하 여 Azure Cognitive Search 인덱스에서 중첩 또는 계층적 데이터 구조를 모델링할 수 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190990"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure Cognitive Search에서 복합 데이터 형식을 모델링 하는 방법

Azure Cognitive Search 인덱스를 채우는 데 사용 되는 외부 데이터 집합은 많은 셰이프에서 제공 될 수 있습니다. 계층적 또는 중첩 된 하위 구조체를 포함 하는 경우도 있습니다. 예에는 단일 고객에 대 한 여러 주소, 단일 SKU에 대 한 여러 색 및 크기, 단일 책의 여러 작성자 등이 포함 될 수 있습니다. 모델링 용어에서 *복합*, *복합*, *복합*또는 *집계* 데이터 형식 이라고 하는 이러한 구조를 볼 수 있습니다. 이 개념에 사용 되는 Azure Cognitive Search 용어는 **복합 유형**입니다. Azure Cognitive Search에서 복합 형식은 **복합 필드**를 사용 하 여 모델링 됩니다. 복합 필드는 다른 복합 형식을 포함 하 여 모든 데이터 형식일 수 있는 자식 (하위 필드)을 포함 하는 필드입니다. 이는 프로그래밍 언어에서 구조화 된 데이터 형식과 비슷한 방식으로 작동 합니다.

복합 필드는 데이터 형식에 따라 문서의 단일 개체 또는 개체의 배열을 나타냅니다. `Edm.ComplexType` 형식의 필드는 단일 개체를 나타내고, `Collection(Edm.ComplexType)` 형식의 필드는 개체의 배열을 나타냅니다.

Azure Cognitive Search는 기본적으로 복합 형식 및 컬렉션을 지원 합니다. 이러한 형식을 사용 하면 Azure Cognitive Search 인덱스에서 거의 모든 JSON 구조를 모델링할 수 있습니다. 이전 버전의 Azure Cognitive Search Api에서는 일반 행 집합만 가져올 수 있었습니다. 최신 버전에서 인덱스는 이제 원본 데이터와 더 밀접 하 게 일치 합니다. 즉, 원본 데이터에 복합 형식이 있는 경우 인덱스에도 복합 형식이 있을 수 있습니다.

시작 하려면 [호텔 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)을 권장 합니다 .이 데이터 집합은 Azure Portal의 **데이터 가져오기** 마법사에서 로드할 수 있습니다. 마법사는 원본의 복합 형식을 검색 하 고 검색 된 구조를 기반으로 인덱스 스키마를 제안 합니다.

> [!Note]
> 복합 형식에 대 한 지원은 일반적으로 `api-version=2019-05-06`에서 사용할 수 있습니다. 
>
> 검색 솔루션이 컬렉션의 일반 데이터 집합에 대 한 이전 해결 방법을 기반으로 하는 경우 최신 API 버전에서 지원 되는 복합 형식을 포함 하도록 인덱스를 변경 해야 합니다. API 버전을 업그레이드 하는 방법에 대 한 자세한 내용은 [최신 REST API 버전으로 업그레이드](search-api-migration.md) 또는 [최신 .net SDK 버전으로 업그레이드](search-dotnet-sdk-migration-version-9.md)를 참조 하세요.

## <a name="example-of-a-complex-structure"></a>복합 구조의 예

다음 JSON 문서는 간단한 필드와 복잡 한 필드로 구성 됩니다. `Address` 및 `Rooms`같은 복합 필드에는 하위 필드가 있습니다. `Address`은 문서의 단일 개체 이기 때문에 해당 하위 필드에 대 한 단일 값 집합을 가집니다. 반면, `Rooms`에는 컬렉션의 각 개체에 대해 하나씩, 하위 필드에 대 한 값 집합이 여러 개 있습니다.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>복합 필드 만들기

모든 인덱스 정의와 마찬가지로, 포털, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)또는 [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 를 사용 하 여 복합 형식을 포함 하는 스키마를 만들 수 있습니다. 

다음 예에서는 간단한 필드, 컬렉션 및 복합 형식을 사용 하는 JSON 인덱스 스키마를 보여 줍니다. 복합 형식 내에서 각 하위 필드는 형식을 가지 며 최상위 필드와 마찬가지로 특성을 가질 수 있습니다. 이 스키마는 위의 예제 데이터에 해당 합니다. `Address`은 컬렉션이 아닌 복합 필드입니다. 호텔에는 주소가 하나 있습니다. `Rooms`는 복합 컬렉션 필드입니다 (호텔에는 많은 방 있음).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>복합 필드 업데이트

일반적으로 필드에 적용 되는 모든 [인덱스 규칙](search-howto-reindex.md) 은 복잡 한 필드에도 적용 됩니다. 여기에 몇 가지 주요 규칙이 재작성 필드를 추가 하는 경우 인덱스를 다시 작성할 필요가 없지만 대부분의 수정 작업을 수행 합니다.

### <a name="structural-updates-to-the-definition"></a>정의에 대 한 구조적 업데이트

인덱스를 다시 작성할 필요 없이 언제 든 지 복합 필드에 새 하위 필드를 추가할 수 있습니다. 예를 들어 인덱스에 최상위 필드를 추가 하는 것 처럼 `Address` 또는 "편의 시설 `Rooms`"에 "ZipCode"를 추가 하는 것이 허용 됩니다. 기존 문서에는 데이터를 업데이트 하 여 명시적으로 필드를 채울 때까지 새 필드에 대 한 null 값이 있습니다.

복합 형식 내에서 각 하위 필드는 형식을 가지 며 최상위 필드와 마찬가지로 특성을 가질 수 있습니다.

### <a name="data-updates"></a>데이터 업데이트

`upload` 작업을 사용 하 여 인덱스에서 기존 문서를 업데이트 하는 것은 복잡 하 고 간단한 필드에 대해 동일한 방식으로 작동 합니다. 즉, 모든 필드가 바뀝니다. 그러나 `merge` (또는 기존 문서에 적용 된 `mergeOrUpload`)는 모든 필드에서 동일 하 게 작동 하지 않습니다. 특히 `merge`는 컬렉션 내에서 요소를 병합 하는 것을 지원 하지 않습니다. 기본 형식 및 복합 컬렉션의 컬렉션에 대 한 이러한 제한 사항이 있습니다. 컬렉션을 업데이트 하려면 전체 컬렉션 값을 검색 하 고 변경한 다음 인덱스 API 요청에 새 컬렉션을 포함 해야 합니다.

## <a name="searching-complex-fields"></a>복합 필드 검색

자유 형식 검색 식은 복합 형식에서 예상 대로 작동 합니다. 문서에서 검색 가능한 필드 또는 하위 필드가 일치 하는 경우 문서 자체는 일치 항목입니다.

여러 개의 용어와 연산자가 있는 경우 쿼리를 사용 하는 경우에는 [Lucene 구문을](query-lucene-syntax.md)사용할 때와 같이 필드 이름이 지정 되는 미묘한 있습니다. 예를 들어이 쿼리는 주소 필드의 두 하위 필드에 대해 두 개의 용어 "포틀랜드" 및 "OR"를 일치 시 키 려 고 합니다.

    search=Address/City:Portland AND Address/State:OR

이와 같은 쿼리는 필터와 달리 전체 텍스트 검색에 대 한 *상관 관계가 없는* 입니다. 필터에서 복합 컬렉션의 하위 필드에 대 한 쿼리는 [`any` 또는 `all`](search-query-odata-collection-operators.md)의 범위 변수를 사용 하 여 상호 관련 됩니다. 위의 Lucene 쿼리는 Oregon의 다른 도시와 함께 "포틀랜드, Maine" 및 "포틀랜드, Oregon"를 모두 포함 하는 문서를 반환 합니다. 이는 각 절이 전체 문서에 있는 해당 필드의 모든 값에 적용 되므로 "현재 하위 문서"의 개념이 없다는 것입니다. 이에 대 한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조 하세요.

## <a name="selecting-complex-fields"></a>복합 필드 선택

`$select` 매개 변수는 검색 결과에서 반환 되는 필드를 선택 하는 데 사용 됩니다. 이 매개 변수를 사용 하 여 복합 필드의 특정 하위 필드를 선택 하려면 부모 필드와 하위 필드를 슬래시 (`/`)로 구분 하 여 포함 합니다.

    $select=HotelName, Address/City, Rooms/BaseRate

필드는 검색 결과에서 원하는 경우 인덱스에서 검색할 수 있는 것으로 표시 되어야 합니다. 검색 가능으로 표시 된 필드만 `$select` 문에서 사용할 수 있습니다.

## <a name="filter-facet-and-sort-complex-fields"></a>필터, 패싯 및 정렬 복합 필드

필터링 및 필드 지정 검색에 사용 되는 것과 동일한 [OData 경로 구문도](query-odata-filter-orderby-syntax.md) 검색 요청에서 필드를 패싯, 정렬 및 선택 하는 데 사용할 수 있습니다. 복합 형식의 경우 정렬 가능 또는 패싯 가능 표시할 하위 필드를 제어 하는 규칙이 적용 됩니다. 이러한 규칙에 대 한 자세한 내용은 [인덱스 API 참조 만들기](/rest/api/searchservice/create-index)를 참조 하세요.

### <a name="faceting-sub-fields"></a>패싯 하위 필드

`Edm.GeographyPoint` 또는 `Collection(Edm.GeographyPoint)`형식이 아니면 모든 하위 필드를 패싯 가능으로 표시할 수 있습니다.

패싯 결과에서 반환 되는 문서 개수는 부모 문서 (호텔)에 대해 계산 되며,이는 복합 컬렉션 (대화방)의 하위 문서가 아닙니다. 예를 들어 호텔에 "suite" 형식의 대화방이 20 개 있다고 가정 합니다. 이 패싯 매개 변수 `facet=Rooms/Type`지정 된 경우 패싯 수는 방에 대해 20이 아닌 호텔에 대해 1이 됩니다.

### <a name="sorting-complex-fields"></a>복합 필드 정렬

정렬 작업은 문서 (호텔)에 적용 되 고 하위 문서 (대화방)에는 적용 되지 않습니다. 방 같은 복합 형식 컬렉션을 사용할 경우에는 방을 정렬할 수 없다는 점을 알아야 합니다. 실제로는 컬렉션을 기준으로 정렬할 수 없습니다.

정렬 작업은 필드에 단일 값이 있을 때, 필드가 단순 필드 인지, 아니면 복합 형식의 하위 필드 인지에 따라 작동 합니다. 예를 들어 호텔 마다 주소가 하나 뿐 이기 때문에 `Address/City` 정렬할 수 있으므로 `$orderby=Address/City` 호텔을 도시별로 정렬 합니다.

### <a name="filtering-on-complex-fields"></a>복합 필드 필터링

필터 식에서 복합 필드의 하위 필드를 참조할 수 있습니다. 패싯에 사용 되는 것과 동일한 [OData 경로 구문을](query-odata-filter-orderby-syntax.md) 사용 하 고 필드를 정렬 하 고 선택 합니다. 예를 들어 다음 필터는 캐나다의 모든 호텔을 반환 합니다.

    $filter=Address/Country eq 'Canada'

복합 컬렉션 필드를 필터링 하려면 [`any` 및 `all` 연산자](search-query-odata-collection-operators.md)와 함께 **람다 식을** 사용할 수 있습니다. 이 경우 람다 식의 **범위 변수** 는 하위 필드가 있는 개체입니다. 표준 OData 경로 구문을 사용 하 여 이러한 하위 필드를 참조할 수 있습니다. 예를 들어 다음 필터는 하나 이상의 deluxe 대화방 및 모든 비 흡연 대화방을 포함 하는 모든 호텔을 반환 합니다.

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

최상위 단순 필드와 마찬가지로 복합 필드의 단순한 하위 필드는 인덱스 정의에서 **필터링** 가능한 특성이 `true` 설정 된 경우에만 필터에 포함 될 수 있습니다. 자세한 내용은 [인덱스 API 참조 만들기](/rest/api/searchservice/create-index)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

**데이터 가져오기** 마법사에서 [호텔 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) 을 사용해 봅니다. 데이터에 액세스 하려면 추가 정보에 제공 된 Cosmos DB 연결 정보가 필요 합니다.

이 정보를 사용 하 여 마법사의 첫 번째 단계는 새 Azure Cosmos DB 데이터 원본을 만드는 것입니다. 계속 해 서 마법사의 대상 인덱스 페이지에 복합 형식이 포함 된 인덱스가 표시 됩니다. 이 인덱스를 만들고 로드 한 다음 쿼리를 실행 하 여 새 구조를 이해 합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 가져오기, 인덱싱 및 쿼리를 위한 포털 마법사](search-get-started-portal.md)
