---
title: 검색 인덱스 만들기
titleSuffix: Azure Cognitive Search
description: 스키마 정의 및 물리적 데이터 구조를 포함 하 여 Azure Cognitive Search의 인덱싱 개념 및 도구를 소개 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932885"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Azure Cognitive Search에서 기본 검색 인덱스 만들기

Azure Cognitive Search에서 *검색 인덱스* 는 전체 텍스트 및 필터링 된 쿼리에 사용 되는 검색 가능한 콘텐츠를 저장 합니다. 인덱스는 스키마에 의해 정의 되 고 서비스에 저장 되며, 데이터 가져오기는 두 번째 단계로 수행 됩니다. 

인덱스는 *문서*를 포함 합니다. 개념상, 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 소매점은 각 제품에 대 한 문서를 포함할 수 있으며, 뉴스 조직에는 각 문서에 대 한 문서가 포함 될 수 있습니다. 이러한 개념을 보다 친숙 한 데이터베이스에 매핑: *검색 인덱스* 는 *테이블과*같으며 *문서* 는 테이블의 *행* 과 거의 동일 합니다.

인덱스의 물리적 구조는 스키마에 의해 결정 되며,이 필드에 대해 반전 된 인덱스가 생성 되는 "검색 가능"으로 표시 된 필드가 포함 됩니다. 

다음 도구와 Api를 사용 하 여 인덱스를 만들 수 있습니다.

* Azure Portal에서 **인덱스 추가** 또는 **데이터 가져오기** 마법사를 사용 합니다.
* [Create Index (REST API)](/rest/api/searchservice/create-index) 사용
* [.NET SDK](./search-get-started-dotnet.md) 사용

포털 도구를 사용 하 여 보다 쉽게 배울 수 있습니다. 포털은 숫자 필드에 대 한 전체 텍스트 검색 기능을 허용 하지 않는 등의 특정 데이터 형식에 대 한 요구 사항 및 스키마 규칙을 적용 합니다. 인덱스를 사용할 수 있으면 [Get index (REST API)](/rest/api/searchservice/get-index) 를 사용 하 여 서비스에서 JSON 정의를 검색 하 고 솔루션에 추가 하 여 코드로 전환할 수 있습니다.

## <a name="recommended-workflow"></a>권장 워크플로

최종 인덱스 디자인은 반복적인 프로세스입니다. 일반적으로 포털에서 시작 하 여 초기 인덱스를 만든 다음 코드를 전환 하 여 원본 제어에서 인덱스를 저장 하는 것이 일반적입니다.

1. [**데이터 가져오기를**](search-import-data-portal.md)사용할 수 있는지 여부를 확인 합니다. 원본 데이터가 [Azure에서 지원 되는 데이터 원본 유형인](search-indexer-overview.md#supported-data-sources)경우 마법사는 전체 인덱서 기반 인덱싱을 수행 합니다.

1. **데이터 가져오기**를 사용할 수 없는 경우에는 **인덱스 추가** 로 시작 하 여 스키마를 정의 합니다.

   ![인덱스 추가 명령](media/search-what-is-an-index/add-index.png "인덱스 추가 명령")

1. 인덱스의 각 검색 문서를 고유 하 게 식별 하는 데 사용 되는 이름 및 키를 제공 합니다. 키는 필수 이며 Edm. String 형식 이어야 합니다. 가져오는 동안 원본 데이터의 고유 필드를이 필드에 매핑하는 계획을 세워야 합니다. 

   포털에서 `id` 키에 대 한 필드를 제공 합니다. 기본값을 재정의 하려면 새 필드 `id` (예: 라는 새 필드 정의)를 만든 `HotelId` 다음 **키**에서 선택 합니다.

   ![필수 속성 입력](media/search-what-is-an-index//field-attributes.png "필수 속성 입력")

1. 필드를 더 추가 합니다. 포털은 다양 한 데이터 형식에 사용할 수 있는 [필드 특성](#index-attributes) 을 보여 줍니다. 인덱스 설계를 처음 접하는 분들에게 유용합니다.

   들어오는 데이터가 계층적 이면 중첩 된 구조를 나타내는 [복합 유형](search-howto-complex-data-types.md) 데이터 형식을 할당 합니다. 기본 제공 샘플 데이터 집합 호텔은 각 호텔에 대해 일 대 일 관계를 갖는 주소 (여러 개의 하위 필드 포함)를 사용 하는 복합 형식을 보여 주며, 대화방 복합 컬렉션은 여러 개의 대화방에 연결 되어 있습니다. 

1. 인덱스를 만들기 전에 문자열 필드에 [분석기](#analyzers) 를 할당 합니다. 특정 필드에서 자동 완성을 사용 하도록 설정 하려는 경우 [확인 기](#suggesters) 에 대해 동일한 작업을 수행 합니다.

1. **만들기** 를 클릭 하 여 검색 서비스의 물리적 구조를 빌드합니다.

1. 인덱스를 만든 후 추가 명령을 사용 하 여 정의를 검토 하거나 더 많은 요소를 추가 합니다.

   ![데이터 유형별 특성을 보여 주는 인덱스 페이지 추가](media/search-what-is-an-index//field-definitions.png "데이터 유형별 특성을 보여 주는 인덱스 페이지 추가")

1. [인덱스 가져오기 (REST API)](/rest/api/searchservice/get-index) 및 [postman](search-get-started-postman.md)과 같은 웹 테스트 도구를 사용 하 여 인덱스 스키마를 다운로드 합니다. 이제 코드에 맞게 조정할 수 있는 인덱스의 JSON 표현이 있습니다.

1. [데이터가 포함된 인덱스를 로드합니다](search-what-is-data-import.md). Azure Cognitive Search는 JSON 문서를 허용 합니다. 데이터를 프로그래밍 방식으로 로드하려면 요청 페이로드의 JSON 문서에 Postman을 사용하면 됩니다. 데이터를 JSON으로 표현하기가 쉽지 않은 경우 이 단계에 가장 많은 노력이 필요할 것입니다. 

    인덱스가 데이터로 로드 된 후에는 대부분의 기존 필드를 편집할 때 인덱스를 삭제 하 고 다시 작성 해야 합니다.

1. 예상하는 결과가 나타나기 시작할 때까지 인덱스를 쿼리하고, 결과를 검사하고, 인덱스 스키마를 계속 반복합니다. [**Search 탐색기**](search-explorer.md) 또는 Postman을 사용하여 인덱스를 쿼리할 수 있습니다.

개발 하는 동안 자주 다시 작성 하는 계획을 세워야 합니다. 물리적 구조는 서비스에서 만들어지므로 기존 필드 정의를 대부분 수정 하려면 [인덱스를 삭제 하 고 다시 만드는](search-howto-reindex.md) 것이 필요 합니다. 보다 빠르게 다시 작성할 수 있도록 데이터 하위 집합을 사용하는 방안을 고려해 볼 수 있습니다. 

> [!Tip]
> 인덱스 디자인 및 데이터 가져오기 작업을 동시에 수행 하는 데는 포털 방법이 아닌 코드를 사용 하는 것이 좋습니다. 대안으로 사용 가능한 [Postman 및 REST API](search-get-started-postman.md) 같은 도구는 개발 프로젝트의 초기 단계에서 개념 증명 테스트에 도움이 됩니다. 요청 본문에서 인덱스 정의를 증분식으로 변경한 다음, 서비스에 요청을 보내서 업데이트된 스키마를 사용하여 인덱스를 다시 만들 수 있습니다.

## <a name="index-schema"></a>인덱스 스키마

필드 컬렉션에는 이름 및 지정 된 키 필드 하나 (Edm. 문자열)가 필요 합니다. 일반적으로 [‘필드 컬렉션’](#fields-collection)이 인덱스의 가장 큰 파트이고, 각 필드에 이름과 유형이 지정되며 사용 방법을 결정하는 허용 가능한 동작으로 특성이 지정됩니다.** 

다른 요소에는 [확인 기](#suggesters), [점수 매기기 프로필](#scoringprofiles), 분석기에서 지 원하는 언어 규칙 또는 기타 특성과 [CORS (크로스-원본 원격 스크립팅)](#corsoptions) 설정에 따라 토큰에 대 한 문자열을 처리 하는 데 사용 되는 [분석기](#analyzers) 가 포함 됩니다.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>필드 컬렉션 및 필드 특성

필드에는 이름, 저장 된 데이터를 분류 하는 형식 및 필드가 사용 되는 방법을 지정 하는 특성이 있습니다.

### <a name="data-types"></a>데이터 형식

| Type | Description |
|------|-------------|
| Edm.String |전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 텍스트입니다(단어 분리, 형태소 분석 등). |
| Collection(Edm.String) |전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 문자열 목록입니다. 컬렉션에 있는 항목 수에 이론적인 상한은 없지만 페이로드 크기의 16MB 상한이 컬렉션에 적용됩니다. |
| Edm.Boolean |true/false 값을 포함합니다. |
| Edm.Int32 |32비트 정수 값입니다. |
| Edm.Int64 |64비트 정수 값입니다. |
| Edm.Double |배정밀도 숫자 데이터입니다. |
| Edm.DateTimeOffset |날짜 시간 값을 OData V4 형식으로 표현합니다(예: `yyyy-MM-ddTHH:mm:ss.fffZ` 또는 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| Edm.GeographyPoint |전 세계의 지리적 위치를 나타내는 지점입니다. |

자세한 내용은 [지원 되는 데이터 형식](/rest/api/searchservice/Supported-data-types)을 참조 하세요.

<a name="index-attributes"></a>

### <a name="attributes"></a>특성

필드 특성에 따라 필드가 사용되는 방식(즉, 전체 텍스트 검색, 패싯 탐색, 정렬 작업 등)이 결정됩니다. 

문자열 필드는 "검색 가능" 및 "검색 가능"으로 표시 되는 경우가 많습니다. 검색 결과의 범위를 좁히는 데 사용 되는 필드에는 "정렬 가능한", "필터링 가능" 및 "패싯 가능"가 포함 됩니다.

|특성|Description|  
|---------------|-----------------|  
|가능한 |전체 텍스트 검색 가능하며, 인덱싱 중에 단어 분리 등의 어휘 분석이 적용됩니다. 검색 가능 필드를 “sunny day” 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 “sunny”와 “day”로 분할됩니다. 자세한 내용은 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)을 참조하세요.|  
|가능 |$filter 쿼리에서 참조됩니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필터링 가능 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 f를 “sunny day”로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. |  
|8601 |기본적으로 시스템은 점수에 따라 결과를 정렬하지만 문서에 있는 필드를 기반으로 정렬하도록 구성할 수 있습니다. 형식의 필드는 `Collection(Edm.String)` "정렬 가능"으로 지정할 수 없습니다. |  
|패싯 가능 |일반적으로 카테고리별 적중 횟수가 포함된 검색 결과를 표시하는 데 사용됩니다(예: 특정 도시의 호텔). 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. `Edm.String`필터링 가능, "정렬 가능" 또는 "패싯 가능" 형식의 필드는 최대 32 킬로바이트 길이입니다. 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 참조하세요.|  
|키인지 |인덱스 내의 문서에 대한 고유 식별자입니다. 정확히 하나의 필드를 키 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다.|  
|조회 가능 |검색 결과에서 필드를 반환할 수 있는지 여부를 결정합니다. *이익률* 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `true` for `key` 로 설정해야 합니다.|  

언제든지 새 필드를 추가할 수 있지만 기존 필드 정의는 인덱스 수명 동안 잠겨 있습니다. 이러한 이유로 개발자는 일반적으로 포털을 사용하여 간단한 인덱스를 만들거나 아이디어를 테스트하거나 포털 페이지를 사용하여 설정을 조회합니다. 인덱스를 쉽게 다시 작성할 수 있도록 코드 기반 접근 방식을 따르는 경우 인덱스 대한 빈번한 반복 디자인이 보다 효율적입니다.

> [!NOTE]
> 인덱스를 작성 하는 데 사용 하는 Api는 다양 한 기본 동작을 포함 합니다. [REST api](/rest/api/searchservice/Create-Index)의 경우 대부분의 특성은 기본적으로 사용 하도록 설정 됩니다. 예를 들어 "검색 가능" 및 "검색 가능"은 문자열 필드에 대해 true 이며,이를 해제 하려는 경우에만 설정 해야 하는 경우가 많습니다. .NET SDK의 경우 반대의 경우도 마찬가지입니다. 명시적으로 설정 하지 않은 모든 속성에서 기본값은 명시적으로 사용 하도록 설정 하지 않은 경우 해당 검색 동작을 사용 하지 않도록 설정 하는 것입니다.

## `analyzers`

분석기 요소는 필드에 사용할 언어 분석기의 이름을 설정합니다. 사용할 수 있는 분석기 범위에 대 한 자세한 내용은 [Azure Cognitive Search 인덱스에 분석기 추가](search-analyzers.md)를 참조 하세요. 분석기는 검색 가능한 필드에만 사용할 수 있습니다. 분석기가 필드에 할당되면 인덱스를 다시 작성하기 전에는 분석기를 변경할 수 없습니다.

## `suggesters`

제안기는 검색에서 자동 완성 또는 자동 완성 쿼리를 지원하는 데 사용되는 인덱스의 필드를 정의하는 스키마 섹션입니다. 일반적으로 사용자가 검색 쿼리를 입력 하는 동안 부분 검색 문자열이 [제안 (REST API)](/rest/api/searchservice/suggestions) 으로 전송 되 고 API는 제안 된 문서 또는 구의 집합을 반환 합니다. 

제안기에 추가된 필드는 검색어 미리 입력 기능을 빌드하는 데 사용됩니다. 모든 검색어는 인덱싱 중에 생성되어 별도로 저장됩니다. 제안기 구조체를 만드는 방법에 대한 자세한 내용은 [제안기 추가](index-add-suggesters.md)를 참조하세요.

## `corsOptions`

브라우저에서는 모든 원본 간 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 원본 간 쿼리를 허용하려면 **corsOptions** 특성을 설정하여 CORS(원본 간 리소스 공유)를 사용하도록 설정합니다. 보안상의 이유로 쿼리 API에서만 CORS가 지원됩니다. 

CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

+ **allowedorigins** (필수): 인덱스에 대 한 액세스 권한이 부여 되는 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://<fully-qualified-domain-name>:<port>` 형식이지만 `<port>`는 대개 생략됩니다. 자세한 내용은 [원본 간 리소스 공유(위키백과)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 참조하세요.

  모든 원본에 대한 액세스를 허용하려면 **allowedOrigins** 배열에서 `*`를 단일 항목으로 포함합니다. *프로덕션 검색 서비스에는 권장되지 않지만* 개발 및 디버깅에 유용한 경우가 많습니다.

+ **maxAgeInSeconds** (선택 사항): 브라우저는이 값을 사용 하 여 CORS 실행 전 응답을 캐시할 기간 (초)을 결정 합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

## `scoringProfiles`

[점수 매기기 프로필](index-add-scoring-profiles.md)은 검색 결과에서 더 위쪽에 표시할 항목을 제어할 수 있는 사용자 지정 채점 동작을 정의하는 스키마 섹션입니다. 점수 매기기 프로필은 필드 가중치와 함수로 구성됩니다. 사용하려면 쿼리 문자열에서 이름별로 프로필을 지정합니다.

기본 점수 매기기 프로필은 결과 집합에 있는 모든 항목에 대한 검색 점수를 계산하기 위해 백그라운드에서 작동합니다. 이름이 지정되지 않은 내부 점수 매기기 프로필을 사용할 수 있습니다. 또는 사용자 지정 프로필이 쿼리 문자열에 지정되지 않은 경우 항상 호출되는 기본값으로 사용자 지정 프로필을 사용하도록 **defaultScoringProfile**을 설정합니다.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>특성 및 인덱스 크기 (저장소 영향)

인덱스 크기는 업로드 하는 문서의 크기와 확인 기 포함 여부, 개별 필드에 대 한 특성 설정 방법 등의 인덱스 구성에 따라 결정 됩니다. 

다음 스크린샷은 다양한 특성을 조합한 인덱스 스토리지 패턴을 보여줍니다. 인덱스는 데이터 가져오기 마법사를 사용 하 여 쉽게 만들 수 있는 부동산 **샘플 인덱스**를 기반으로 합니다. 인덱스 스키마는 표시되지 않지만, 인덱스 이름을 보고 특성을 유추할 수 있습니다. 예를 들어 *검색 가능한* 인덱스에는 "검색 가능" 특성이 선택 되어 있고 다른 모든 항목이 없는 경우, realestate-us-sample 검색 가능한 인덱스에는 "검색 *가능* " 특성을 선택 하 고 다른 것은 realestate-us-sample 하지 않습니다.

![특성 선택에 따른 인덱스 크기](./media/search-what-is-an-index/realestate-index-size.png "특성 선택에 따른 인덱스 크기")

이러한 인덱스 변형은 인위적이지만, 특성이 스토리지에 미치는 영향을 광범위하게 비교하는 데 참조할 수 있습니다. "검색 가능"을 설정 하면 인덱스 크기가 증가 하나요? 아니요. **확인 기** 에 필드를 추가 하면 인덱스 크기가 증가 하나요? 예.

필터와 정렬을 지 원하는 인덱스는 전체 텍스트 검색을 지 원하는 인덱스 보다 비례 하 게 큽니다. 이는 필터 및 정렬 작업이 정확히 일치 하는 항목을 검색 하 여 약어 텍스트 문자열이 있어야 하기 때문입니다. 반면 전체 텍스트 쿼리를 지 원하는 검색 가능한 필드는 전체 문서 보다 더 작은 공간을 사용 하는 토큰화 된 용어로 채워지는 반전 된 인덱스를 사용 합니다. 

> [!Note]
> 저장소 아키텍처는 Azure Cognitive Search의 구현 세부 정보로 간주 되며, 예 고 없이 변경 될 수 있습니다. 현재 동작이 나중에도 유지된다는 보장은 없습니다.

## <a name="next-steps"></a>다음 단계

인덱스 작성을 파악했으면 포털에서 계속해서 첫 번째 인덱스를 만들 수 있습니다. **데이터 가져오기** 마법사를 시작 하 여 *realestate-us-sample* 또는 *호텔-샘플* 호스트 된 데이터 원본을 선택 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [데이터 가져오기 마법사 (포털)](search-get-started-portal.md)

두 데이터 집합 모두에 대해 마법사는 인덱스 스키마를 유추 하 고, 데이터를 가져오고, 검색 탐색기를 사용 하 여 쿼리할 수 있는 검색 가능한 인덱스를 출력할 수 있습니다. 데이터 **가져오기** 마법사의 **데이터에 연결** 페이지에서 이러한 데이터 원본을 찾습니다.

   ![샘플 인덱스 만들기](media/search-what-is-an-index//import-wizard-sample-data.png "샘플 인덱스 만들기")