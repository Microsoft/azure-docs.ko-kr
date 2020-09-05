---
title: 유사성 및 점수 매기기 개요
titleSuffix: Azure Cognitive Search
description: 유사성 및 점수 매기기의 개념과 개발자가 점수 매기기 결과를 사용자 지정하기 위해 수행할 수 있는 작업을 설명합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 300da87ecff13fc160ec08684cf1d032f9a19f71
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924489"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure Cognitive Search의 유사성 및 점수 매기기

점수 매기기는 전체 텍스트 검색 쿼리에 대해 검색 결과에 반환된 모든 항목의 검색 점수를 계산하는 것입니다. 점수는 현재 검색 작업의 컨텍스트에서 항목의 관련성을 나타내는 표시기입니다. 점수가 높을수록 항목의 관련성도 높습니다. 검색 결과에서 항목은 각 항목에 대해 계산된 검색 점수를 기준으로 높은 순위부터 낮은 순위로 정렬됩니다. 

기본적으로 응답에 상위 50개 항목이 반환되지만 **$top** 매개 변수를 사용하여 반환되는 항목 수를 늘리거나 줄일 수 있으며(최대 1,000개 항목) **$skip** 매개 변수를 사용하여 다음 결과 세트를 가져올 수 있습니다.

검색 점수는 쿼리와 데이터의 통계 속성에 따라 계산됩니다. Azure Cognitive Search에서는 검색 용어와 일치하는 문서를 찾은 다음([searchMode](/rest/api/searchservice/search-documents#searchmodeany--all-optional)에 따라 일부 또는 전체), 검색 용어가 많이 포함된 문서에 높은 점수를 할당합니다. 해당 용어가 데이터 인덱스에는 거의 없지만 해당 문서 내에서는 자주 나오는 경우 검색 점수가 더 높아집니다. 이와 같은 관련성 계산 방식의 기준을 *TF-IDF*, 즉 용어 빈도와 문서 빈도 반비례라고 합니다.

전체 결과 집합에서 검색 점수 값이 반복될 수 있습니다. 여러 적중 항목의 검색 점수가 같은 경우 동일 점수의 항목 순서는 정의되지 않았으므로 항목이 안정적으로 정렬되지 않습니다. 쿼리를 다시 실행하면, 특히 무료 서비스를 사용하거나 여러 복제본이 청구 가능한 서비스를 사용하는 경우 항목 이동 위치가 표시될 수 있습니다. 즉, 두 항목의 점수가 같은 경우 어떤 항목이 먼저 표시되는지 보장되지 않습니다.

반복 점수 간에 연결을 끊으려는 경우 **$orderby** 절을 먼저 점수를 기준으로 정렬한 다음, 정렬 가능한 다른 필드를 기준으로 정렬합니다(예: `$orderby=search.score() desc,Rating desc`). 자세한 내용은 [$orderby](./search-query-odata-orderby.md)를 참조하세요.

> [!NOTE]
> `@search.score = 1.00`은 점수가 매겨지지 않거나 순위가 지정되지 않은 결과 세트를 나타냅니다. 점수는 모든 결과에서 균일합니다. 쿼리 양식이 유사 항목 검색, 와일드카드 또는 regex 쿼리, **$filter** 식인 경우 점수가 없는 결과가 나타납니다. 

## <a name="scoring-profiles"></a>점수 매기기 프로필

사용자 지정 *점수 매기기 프로필*을 정의하여 다양한 필드의 순위 지정 방식을 사용자 지정할 수 있습니다. 점수 매기기 프로필을 사용하면 검색 결과에서 항목의 순위를 보다 강력하게 제어할 수 있습니다. 예를 들어 잠재 수익을 기준으로 하여 특정 항목을 상승시키거나, 새 항목을 프로모션하거나, 너무 오랫동안 재고에 포함되어 있던 항목을 상승시킬 수 있습니다. 

점수 매기기 프로필은 가중 필드, 함수 및 매개 변수로 구성된 인덱스 정의의 일부입니다. 정의 방법에 대한 자세한 내용은 [점수 매기기 프로필](index-add-scoring-profiles.md)을 참조하세요.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>통계 및 고정 세션 점수 매기기

확장성을 위해 Azure Cognitive Search는 분할 프로세스를 통해 각 인덱스를 수평으로 분산합니다. 즉, 인덱스 부분이 물리적으로 분리됩니다.

기본적으로 문서 점수는 *분할된 데이터베이스 내* 데이터의 통계 속성에 따라 계산됩니다. 이 방법은 일반적으로 많은 양의 데이터 모음이 있을 때는 문제가 되지 않으며, 모든 분할된 데이터베이스의 정보를 기준으로 점수를 계산해야 하는 경우보다 더 나은 성능을 제공합니다. 즉, 이러한 성능 최적화를 사용하면 매우 유사한 두 개의 문서(또는 동일한 문서)가 서로 다른 분할된 데이터베이스에 속하게 될 때 서로 다른 관련성 점수를 갖게 될 수 있습니다.

모든 분할된 데이터베이스의 통계 속성에 따라 점수를 계산하려면 [쿼리 매개 변수](/rest/api/searchservice/search-documents)로 *scoringStatistics=global*을 추가하여(또는 [쿼리 요청](/rest/api/searchservice/search-documents)의 본문 매개 변수로 *"scoringStatistics": "global"* 추가) 이렇게 할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
scoringStatistics를 사용하면 동일한 복제본의 모든 분할된 데이터베이스가 동일한 결과를 제공합니다. 즉, 항상 인덱스에 대한 최신 변경 내용으로 업데이트되므로 복제본마다 서로 약간씩 다를 수 있습니다. 일부 시나리오에서는 사용자가 "쿼리 세션" 중에 보다 일관된 결과를 얻게 하려고 할 수 있습니다. 이러한 시나리오에서는 쿼리의 일부로 `sessionId`를 제공할 수 있습니다. `sessionId`는 고유한 사용자 세션을 참조하기 위해 만드는 고유 문자열입니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
동일한 `sessionId`를 사용한다면 동일한 복제본을 대상으로 최상의 시도를 진행하여 사용자에게 표시되는 결과의 일관성이 높일 수 있습니다. 

> [!NOTE]
> 동일한 `sessionId` 값을 반복해서 다시 사용하면 복제본 간에 요청 부하가 분산되지 못하며 검색 서비스의 성능에 부정적인 영향을 미칠 수 있습니다. sessionId로 사용된 값은 '_' 문자로 시작할 수 없습니다.

## <a name="similarity-ranking-algorithms"></a>유사성 순위 알고리즘

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘인 *클래식 유사성* 알고리즘과 *Okapi BM25* 알고리즘(현재 미리 보기로 제공됨)의 공식 구현을 지원합니다. 클래식 유사성 알고리즘은 기본 알고리즘이지만 7월 15일부터 해당 날짜 이후에 만들어진 모든 새 서비스는 새 BM25 알고리즘을 사용합니다. 이 알고리즘은 새 서비스에서 유일하게 사용할 수 있습니다.

지금은 사용할 유사성 순위 알고리즘을 지정할 수 있습니다. 자세한 내용은 [순위 알고리즘](index-ranking-similarity.md)을 참조하세요.

다음 비디오 세그먼트는 Azure Cognitive Search에서 사용되는 순위 알고리즘에 대한 설명을 빠르게 제공합니다. 자세한 배경 정보는 전체 비디오를 시청하여 확인할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode 매개 변수 (미리 보기)

[문서 검색](/rest/api/searchservice/preview-api/search-documents) 요청에는 필드 수준에서 관련성에 대 한 추가 세부 정보를 제공할 수 있는 새 [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) 매개 변수가 있습니다. 는 `@searchScore` 문서에 대해 모두 계산 되는 반면 (이 쿼리의 컨텍스트에서이 문서는 어떻게 관련이 있는지) featuresMode를 통해 구조에 표시 된 대로 개별 필드에 대 한 정보를 가져올 수 있습니다 `@search.features` . 구조에는 쿼리에서 사용 되는 모든 필드 (쿼리에서 **Searchfields** 를 통한 특정 필드 또는 인덱스에서 **검색 가능한** 것으로 특성이 지정 된 모든 필드)가 포함 됩니다. 각 필드에 대해 다음 값을 얻습니다.

+ 필드에 있는 고유한 토큰 수
+ 유사성 점수 또는 쿼리 용어에 상대적인 필드의 내용과 유사한 측정값
+ 용어 빈도 또는 필드에서 쿼리 용어를 찾은 횟수

"Description" 및 "title" 필드를 대상으로 하는 쿼리의 경우를 포함 하는 응답은 `@search.features` 다음과 같습니다.

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

[사용자 지정 점수 매기기 솔루션](https://github.com/Azure-Samples/search-ranking-tutorial) 에서 이러한 데이터 요소를 사용 하거나 정보를 사용 하 여 검색 관련성 문제를 디버그할 수 있습니다.


## <a name="see-also"></a>참고 항목

 [점수 매기기 프로필](index-add-scoring-profiles.md) [REST API 참조](/rest/api/searchservice/)   
 [문서 검색 API](/rest/api/searchservice/search-documents)   
 [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?view=azure-dotnet)