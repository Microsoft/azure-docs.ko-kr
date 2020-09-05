---
title: 업그레이드 REST API 버전
titleSuffix: Azure Cognitive Search
description: API 버전의 차이점을 검토 하 고 기존 코드를 최신 Azure Cognitive Search service REST API 버전으로 마이그레이션하는 데 필요한 작업에 대해 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 0f1050bf58e0cd8d9a601d60a4c5dc22a5420483
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949034"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Azure Cognitive Search에서 최신 REST API로 업그레이드

이전 버전의 [**검색 REST API**](/rest/api/searchservice/)를 사용 하는 경우이 문서는 응용 프로그램을 일반적으로 사용할 수 있는 최신 API 버전인 **2020-06-30**로 업그레이드 하는 데 도움이 됩니다.

버전 2020-06-30에는 중요 한 새 기능 ([기술 자료 저장소](knowledge-store-concept-intro.md))이 포함 되어 있으며 몇 가지 사소한 동작 변경 내용이 도입 되었습니다. 따라서 이전 버전 (2019-05-06)에서 업그레이드 하는 경우에는이 버전이 이전 버전과 호환 되므로 코드를 변경 해야 합니다.

> [!NOTE]
> 검색 서비스는 이전 버전을 포함 하 여 다양 한 REST API 버전을 지원 합니다. 이러한 API 버전을 계속 사용할 수 있지만 새 기능에 액세스할 수 있도록 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다. 시간이 지남에 따라 REST API의 가장 오래 된 버전은 더 이상 지원 되지 않으며 [더 이상 지원 되지](search-api-versions.md#unsupported-versions)않습니다.

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>업그레이드하는 방법

새 버전으로 업그레이드 하는 경우 버전 번호를 변경 하는 것 외에는 코드를 변경 하지 않아도 됩니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.

* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

* 코드는 2019-05-06를 이전의 API 버전을 참조 하며 해당 릴리스의 주요 변경 내용 중 하나 이상이 적용 됩니다. [2019-05-06로 업그레이드](#upgrade-to-2019-05-06) 섹션에서 자세한 정보를 제공 합니다. 

이러한 상황 중 하나라도 해당 하는 경우 코드를 적절 하 게 변경 해야 할 수 있습니다. 그렇지 않으면 새 버전에 추가 된 기능을 사용 하 여 시작 하는 것이 좋습니다.

## <a name="upgrade-to-2020-06-30"></a>2020-06-30로 업그레이드

버전 2020-06-30은 REST API의 새로운 일반 공급 릴리스입니다. 주요 변경 내용은 없지만 몇 가지 동작 차이가 있습니다. 

현재이 API 버전에서 제공 되는 기능은 다음과 같습니다.

* [기술 자료 저장소](knowledge-store-concept-intro.md), 기술력과을 통해 만든 보강 콘텐츠의 영구 저장소로, 다운스트림 분석을 위해 생성 되 고 다른 응용 프로그램을 통해 처리 됩니다. 이 기능을 사용 하는 경우 인덱서 기반 AI 보강 파이프라인은 검색 인덱스 외에도 기술 자료 저장소를 채울 수 있습니다. 이 기능의 미리 보기 버전을 사용 하는 경우 일반적으로 사용 가능한 버전과 동일 합니다. 유일 하 게 코드를 변경 해야 합니다. api 버전을 수정 하는 것입니다.

동작 변경 내용은 다음과 같습니다.

* [BM25 순위 알고리즘](index-ranking-similarity.md) 은 이전 순위 알고리즘을 최신 기술로 대체 합니다. 새 서비스는이 알고리즘을 자동으로 사용 합니다. 기존 서비스의 경우 새 알고리즘을 사용 하도록 매개 변수를 설정 해야 합니다.

* 이 버전에서 null 값에 대 한 순서가 지정 된 결과가 변경 되었습니다. 정렬이 인 경우에는 null 값이 먼저 표시 되 고 정렬이 이면 `asc` 마지막으로 나타납니다 `desc` . Null 값을 정렬 하는 방법을 처리 하는 코드를 작성 한 경우이 변경 내용을 알고 있어야 합니다.

## <a name="upgrade-to-2019-05-06"></a>2019-05-06로 업그레이드

버전 2019-05-06은 이전에 출시 된 REST API 버전입니다. 이 API 버전에서 일반적으로 사용할 수 있는 기능은 다음과 같습니다.

* [자동 완성](index-add-suggesters.md) 은 부분적으로 지정 된 용어 입력을 완료 하는 미리 입력 기능입니다.
* [복합 형식은](search-howto-complex-data-types.md) 검색 인덱스의 구조적 개체 데이터에 대 한 기본 지원을 제공 합니다.
* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드](search-howto-index-json-blobs.md)는 JSON 엔터티 마다 줄 바꿈으로 구분 된 하나의 검색 문서를 만듭니다.
* [Ai 보강](cognitive-search-concept-intro.md) 는 Cognitive Services ai 보강 엔진을 활용 하는 인덱싱을 제공 합니다.

### <a name="breaking-changes"></a>호환성이 손상되는 변경

이전 API 버전에 대해 작성 된 기존 코드는 api-version = 2019-05-06 이상에서 중단 됩니다. 코드에는 다음 기능이 포함 되어 있습니다.

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB-datasource의 인덱서는 이제 "type": "cosmosdb"입니다.

[Cosmos DB 인덱서](search-howto-index-cosmosdb.md )를 사용 하는 경우를로 변경 해야 `"type": "documentdb"` 합니다 `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>인덱서 실행 결과 오류에 상태가 더 이상 없습니다.

이전에 인덱서 실행에 대 한 오류 구조에 `status` 요소가 있습니다. 이 요소는 유용한 정보를 제공 하지 않기 때문에 제거 되었습니다.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>인덱서 데이터 원본 API는 더 이상 연결 문자열을 반환 하지 않습니다.

API 버전 2019-05-06 및 2019-05-06-미리 보기부터 데이터 원본 API는 더 이상 REST 작업에 대 한 응답으로 연결 문자열을 반환 하지 않습니다. 이전 API 버전에서 POST를 사용 하 여 만든 데이터 원본에 대해 Azure Cognitive Search는 **201** 을 반환한 다음 OData 응답을 반환 합니다. 여기에는 일반 텍스트로 연결 문자열이 포함 되어 있습니다.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>명명 된 엔터티 인식 인식 기술이 이제 중단 되었습니다.

코드에서 [이름 엔터티 인식](cognitive-search-skill-named-entity-recognition.md) 기술을 호출한 경우 호출이 실패 합니다. 대체 기능은 [엔터티 인식](cognitive-search-skill-entity-recognition.md)입니다. 기술 참조를 다른 변경 내용 없이 바꿀 수 있습니다. API 서명은 두 버전에 대해 동일 합니다. 

### <a name="upgrading-complex-types"></a>복합 형식 업그레이드

API 버전 2019-05-06는 복합 형식에 대 한 공식적인 지원을 추가 했습니다. 코드에서 2017-11-11-Preview 또는 2016-09-01-Preview와 같은 복합 유형에 대 한 이전 권장 사항을 구현 하는 경우 주의 해야 하는 버전 2019-05-06부터 변경 된 몇 가지 새로운 제한이 있습니다.

+ 하위 필드 수준에 대 한 제한과 인덱스 당 복합 컬렉션 수가 감소 했습니다. Preview api 버전을 사용 하 여 이러한 제한을 초과 하는 인덱스를 만든 경우 API 버전 2019-05-06을 사용 하 여 해당 인덱스를 업데이트 하거나 다시 만들려고 하면 실패 합니다. 이 경우 새 제한에 맞게 스키마를 다시 디자인 한 다음 인덱스를 다시 작성 해야 합니다.

+ 문서 당 복합 컬렉션의 요소 수에 대 한 api 버전 2019-05-06부터 새로운 제한이 있습니다. Preview api 버전을 사용 하 여 이러한 제한을 초과 하는 문서를 사용 하 여 인덱스를 만든 경우에는 api-version 2019-05-06를 사용 하 여 해당 데이터를 다시 인덱싱 하려고 하면 실패 합니다. 사용자에 게 적용 되는 경우 데이터를 인덱스를 만들기 전에 문서당 복합 컬렉션 요소 수를 줄여야 합니다.

자세한 내용은 [Azure Cognitive Search에 대 한 서비스 제한](search-limits-quotas-capacity.md)을 참조 하세요.

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>이전 복합 형식 구조를 업그레이드 하는 방법

코드에서 이전 preview API 버전 중 하나를 사용 하 여 복합 형식을 사용 하는 경우 다음과 같은 인덱스 정의 형식을 사용할 수 있습니다.

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

인덱스 필드를 정의 하는 최신 트리와 유사한 형식은 API 버전 2017-11-11-미리 보기에서 도입 되었습니다. 새 형식에서 각 복합 필드에는 하위 필드가 정의 된 fields 컬렉션이 있습니다. API 버전 2019-05-06에서는이 새 형식이 독점적으로 사용 되며 이전 형식을 사용 하 여 인덱스를 만들거나 업데이트 하는 데 실패 합니다. 이전 형식을 사용 하 여 인덱스를 만든 경우 api 버전 2017-11-11-Preview를 사용 하 여 API 버전 2019-05-06을 사용 하 여 관리 하려면 먼저 새 형식으로 업데이트 해야 합니다.

API 버전 2017-11-11-Preview를 사용 하 여 다음 단계를 수행 하 여 "플랫" 인덱스를 새 형식으로 업데이트할 수 있습니다.

1. GET 요청을 수행 하 여 인덱스를 검색 합니다. 이미 새 형식이 면 완료 됩니다.

2. 인덱스를 "flat" 형식에서 새 형식으로 변환 합니다. 이 문서를 작성할 때 사용할 수 있는 샘플 코드가 없기 때문에이에 대 한 코드를 작성 해야 합니다.

3. PUT 요청을 수행 하 여 인덱스를 새 형식으로 업데이트 합니다. 인덱스 업데이트 API에서 허용 되지 않기 때문에 필드의 높이려는/filterability 같은 인덱스의 다른 세부 정보는 변경 하지 않도록 해야 합니다.

> [!NOTE]
> Azure Portal에서 이전 "플랫" 형식을 사용 하 여 만든 인덱스는 관리할 수 없습니다. 가장 빠른 편의를 위해 "플랫" 표현에서 "트리" 표현으로 인덱스를 업그레이드 하세요.

## <a name="next-steps"></a>다음 단계

검색 REST API 참조 설명서를 검토 합니다. 문제가 발생 하는 경우 [Stack Overflow](https://stackoverflow.com/) 에 대 한 도움을 요청 하거나 [고객 지원에 문의](https://azure.microsoft.com/support/community/?product=search)하세요.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](/rest/api/searchservice/)