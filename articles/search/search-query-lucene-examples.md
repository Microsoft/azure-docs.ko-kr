---
title: 전체 Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 및 와일드카드에 대한 Lucene 쿼리 구문을 나타내는 쿼리 예제는 Azure Cognitive Search 서비스에서 검색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693563"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"Full" Lucene 검색 구문 사용(Azure Cognitive Search의 고급 쿼리)

Azure Cognitive Search에 대한 쿼리를 생성하는 경우 기본 [단순 쿼리 파서](query-simple-syntax.md)를 좀 더 강력한 [Lucene 쿼리 파서](query-lucene-syntax.md)로 바꾸어 특수 및 고급 쿼리 식을 작성할 수 있습니다.

Lucene 쿼리 파서는 필드 범위 쿼리, 유사 항목 검색, 중위 및 접미사, 와일드카드 검색, 근접 검색, 용어 상승, 정규식 검색 등의 복잡한 쿼리 형식을 지원합니다. 성능이 늘어나면 처리 요구 사항도 늘어나므로 실행 시간이 약간 길어진다고 예상해야 합니다. 이 문서에서는 전체 구문을 기반으로 쿼리 작업을 보여 주는 예제를 단계별로 진행할 수 있습니다.

> [!Note]
> 전체 Lucene 쿼리 구문을 통해 사용하도록 설정된 특수화된 쿼리 구문 대부분이 [텍스트 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않으므로 형태소 분석 또는 기본형 분석을 예상한 경우에 당황할 수 있습니다. 어휘 분석은 완전한 용어(용어 쿼리 또는 구 쿼리)에만 수행됩니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 부분적인 쿼리 용어에서는 소문자 변환만 수행됩니다. 
>

## <a name="hotels-sample-index"></a>호텔 샘플 인덱스

다음 쿼리는 이 [빠른 시작](search-get-started-portal.md)의 지침에 따라 만들 수 있는 호텔 샘플 인덱스를 기반으로 합니다.

예제 쿼리는 REST API 및 POST 요청을 사용하여 설명됩니다. [Postman](search-get-started-rest.md)에서 또는 [Cognitive Search 확장을 사용한 Visual Studio Code](search-get-started-vs-code.md)에서 붙여넣거나 실행할 수 있습니다.

요청 헤더의 값은 다음과 같아야 합니다.

| 키 | 값 |
|-----|-------|
| 콘텐츠 형식 | application/json|
| api-key  | `<your-search-service-api-key>`, 쿼리 또는 관리자 키 중 하나 |

URI 매개 변수는 다음 예제와 유사하게 인덱스 이름, docs 컬렉션, 검색 명령 및 API 버전을 포함하는 검색 서비스 엔드포인트를 포함해야 합니다.

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

요청 본문은 유효한 JSON으로 구성되어야 합니다.

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ `*`로 설정된 “검색”은 지정되지 않은 쿼리로, null 또는 빈 검색에 해당합니다. 특별히 유용한 것은 아니지만 할 수 있는 것 중 가장 간단한 검색이며, 모든 값을 사용하여 인덱스에서 조회 가능 필드를 모두 표시합니다.

+ "full"로 설정된 "queryType"는 전체 Lucene 쿼리 파서를 호출하며 이 구문에 필요합니다.

+ 쉼표로 구분된 필드 목록으로 설정된 “select”는 검색 결과의 컨텍스트에서 유용한 필드만 포함하여 검색 결과 컴퍼지션에 사용됩니다.

+ “count”는 검색 조건과 일치하는 문서 수를 반환합니다. 빈 검색 문자열에서 개수는 인덱스의 모든 문서(호텔 샘플 인덱스의 경우 50개)가 됩니다.

## <a name="example-1-fielded-search"></a>예제 1: 필드 지정 검색

개별 필드 지정 검색 범위는 특정 필드에 대한 검색 식을 포함합니다. 이 예제에서는 "호텔"이라는 용어가 포함된 호텔 이름을 검색하지만 "모텔"은 검색하지 않습니다. AND를 사용하는 여러 필드 매핑을 지정할 수 있습니다. 

이 쿼리 구문을 사용하는 경우 쿼리하려는 필드가 검색 식 자체에 있는 경우 "searchFields" 매개 변수를 생략할 수 있습니다. 필드 지정 search를 사용하여 "searchFields"를 포함하는 경우는 `fieldName:searchExpression` 항상 "searchFields" 보다 우선적으로 적용됩니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

이 쿼리에 대한 응답은 "리조트 및 Spa"로 필터링된 다음 예제와 유사하게 이름에 "호텔" 또는 "모텔"을 포함하는 호텔을 반환합니다.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

검색 식은 단일 용어 또는 구 또는 괄호 안의 좀 더 복잡한 식일 수 있습니다. 선택적으로 부울 연산자를 사용할 수 있습니다. 몇 가지 예는 다음과 같습니다.

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

이 경우에 주소/StateProvince 필드에서 두 개의 다른 위치를 검색하고 있으므로 두 문자열이 단일 엔터티로 평가되길 원하는 경우 한 구문을 인용 부호로 묶어야 합니다. 클라이언트에 따라 (`\`) 큰따옴표를 이스케이프 해야 할 수도 있습니다.

`fieldName:searchExpression`지정한 필드는 검색 가능한 필드여야 합니다. 필드 정의의 특성을 지정하는 방법에 대한 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 참조하세요.

## <a name="example-2-fuzzy-search"></a>예제 2: 유사 항목 검색

유사 항목 검색은 철자가 잘못된 단어를 포함하여 비슷한 용어와 일치합니다. 유사 항목 검색을 수행하려면 편집 거리를 지정하는 0과 2 사이의 값을 선택적 매개 변수로 포함하여 단일 단어의 끝에 물결표`~` 기호를 추가합니다. 예를 들어, `blue~` 또는 `blue~1`은 blue, blues 및 glue를 반환합니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

이 쿼리에 대한 응답은 일치 문서에서 "컨시어지"로 확인되고, 간결하게 잘립니다.

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

구는 직접 지원되지 않지만 `search=Tags:landy~ AND sevic~`과 같이 여러 부분으로 구성된 구의 각 용어에 유사 항목 일치를 지정할 수 있습니다.  이 쿼리 식은 "laundry service"에서 15개의 일치 항목을 찾습니다.

> [!Note]
> 유사 항목 쿼리는 [분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-3-proximity-search"></a>예제 3: 근접 검색

근접 검색은 문서에서 서로 가까이 있는 용어를 찾습니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다.

이 쿼리는 문서에서 서로 5개의 단어 내에서 “호텔”과 “공항”이라는 용어를 찾게 됩니다. 따옴표는 구를 유지하기 위해 (`\"`)을 이스케이프합니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>예제 4: 용어 상승

용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿 `^` 기호를 사용합니다. 상승 계수 기본값은 1이고 양수여야 하지만 1보다 작은 값(예: 0.2)일 수 있습니다. 용어 상승은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다.

이 "before" 쿼리에서 "해변 access"를 검색하면 하나 또는 두 용어 모두와 일치하는 7 개의 문서가 있습니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

실제로는 "access"와 일치하는 문서가 하나 뿐이며 유일한 일치이기 때문에 문서에 "해변"이라는 용어가 없더라도 배치가 높습니다(두 번째 위치).

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

"after" 쿼리에서는 검색을 반복합니다. 이번에는 두 단어가 존재하지 않을 경우 용어 "access"보다 용어 "해변"이 포함된 결과를 상승시킵니다. 사람이 읽을 수 있는 쿼리 버전은 `search=Description:beach^2 access`입니다. 클라이언트에 따라, `^2`을 `%5E2`로 표현해야 할 수도 있습니다.

"해변"이라는 용어를 상승한 후에는 이전 Carrabelle 호텔의 일치 항목을 여섯 번째 위치로 이동합니다.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>예제 5: 정규식

정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> 정규식 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="example-6-wildcard-search"></a>예제 5: 와일드카드 검색

일반적으로 다중(`*`) 또는 단일(`?`) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

이 쿼리에서는 'sc' 접두사가 포함된 호텔 이름을 검색합니다. `*` 또는 `?` 기호를 검색의 첫 문자로 사용할 수 없습니다.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> 와일드카드 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 쿼리 용어에서는 소문자 변환만 수행됩니다.
>

## <a name="next-steps"></a>다음 단계

코드에서 쿼리를 지정해 보세요. 다음 링크에서는 Azure SDK를 사용하여 검색 쿼리를 설정하는 방법에 대해 설명합니다.

+ [.NET SDK를 사용하여 인덱스 쿼리](search-get-started-dotnet.md)
+ [Python SDK를 사용하여 인덱스 쿼리](search-get-started-python.md)
+ [JavaScript SDK를 사용하여 인덱스 쿼리](search-get-started-javascript.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](query-simple-syntax.md)
+ [전체 Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 구문](search-query-odata-filter.md)