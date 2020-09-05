---
title: 유사성 알고리즘 순위 지정
titleSuffix: Azure Cognitive Search
description: 유사성 알고리즘을 설정 하 여 순위를 지정 하는 새로운 유사성 알고리즘을 시도 하는 방법
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: ee788125a5710e5a8b9861c28c4af8e6d83d5d80
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924540"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure Cognitive Search의 순위 알고리즘

> [!IMPORTANT]
> 2020 년 7 월 15 일부 터 새로 생성 된 검색 서비스는 자동으로 BM25 순위 함수를 사용 합니다 .이 함수는 대부분의 경우에는 현재 기본 순위 보다 사용자 기대치에 맞게 더 잘 맞춘 검색 순위를 제공 합니다. BM25는 상위 순위를 넘어 문서 크기와 같은 요소에 따라 결과를 조정 하는 구성 옵션도 가능 합니다.  
>
> 이러한 변경으로 인해 검색 결과의 순서가 약간 변경 될 수 있습니다. 이러한 변경의 영향을 테스트 하려는 사용자의 경우 BM25 알고리즘은 api 버전 2019-05-06-미리 보기 및 2020-06-30에서 사용할 수 있습니다.  

이 문서에서는 미리 보기 API를 사용 하 여 만들고 쿼리 하는 새 인덱스에 대해 기존 검색 서비스에서 새로운 BM25 순위 알고리즘을 사용 하는 방법을 설명 합니다.

Azure Cognitive Search은 이전에 사용 된 *ClassicSimilarity* 구현을 대체 하는 OKAPI BM25 algorithm *BM25Similarity*의 공식 Lucene 구현을 채택 하는 과정에 있습니다. 이전 ClassicSimilarity 알고리즘과 마찬가지로 BM25Similarity는 용어 빈도 (TF) 및 역 문서 주기 (IDF)를 변수로 사용 하 여 각 문서-쿼리 쌍의 관련성 점수를 계산 하는 데 사용 하는 TF-IDF와 유사한 검색 함수 이며 순위에 사용 됩니다. 

이전 클래식 유사성 알고리즘과 개념적으로 유사 하지만 BM25는 확률 정보 검색에서 루트를 사용 하 여이를 개선 합니다. 또한 BM25는 사용자가 관련성 점수가 일치 조건에 따라 확장 되는 방식을 결정할 수 있도록 허용 하는 고급 사용자 지정 옵션을 제공 합니다.

## <a name="how-to-test-bm25-today"></a>지금 BM25을 테스트 하는 방법

새 인덱스를 만들 때 **유사성** 속성을 설정 하 여 알고리즘을 지정할 수 있습니다. 아래와 같이 또는를 사용할 수 있습니다 `api-version=2019-05-06-Preview` `api-version=2020-06-30` .

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

**유사성** 속성은이 중간 기간 동안 기존 서비스 에서만 두 알고리즘을 모두 사용할 수 있는 경우에 유용 합니다. 

| 속성 | Description |
|----------|-------------|
| 유사성 | 선택 사항입니다. 유효한 값에는 *#Microsoft "ClassicSimilarity"* 또는 *#Microsoft "BM25Similarity"* 가 포함 됩니다. <br/> `api-version=2019-05-06-Preview`2020 년 7 월 15 일 이전에 생성 된 검색 서비스에서 이상을 사용 해야 합니다. |

2020 년 7 월 15 일 이후에 만들어진 새 서비스의 경우 BM25이 자동으로 사용 되며 유일한 유사성 알고리즘입니다. 새 서비스에서 **유사성** 을로 설정 하려고 하면 `ClassicSimilarity` 새 서비스에서 해당 알고리즘이 지원 되지 않기 때문에 400 오류가 반환 됩니다.

2020 년 7 월 15 일 이전에 만든 기존 서비스의 경우 클래식 유사성은 기본 알고리즘으로 유지 됩니다. **유사성** 속성이 생략 되거나 null로 설정 된 경우 인덱스는 클래식 알고리즘을 사용 합니다. 새 알고리즘을 사용 하려는 경우 위에 설명 된 대로 **유사성** 을 설정 해야 합니다.

## <a name="bm25-similarity-parameters"></a>BM25 유사성 매개 변수

BM25 유사성은 계산 된 관련성 점수를 제어 하기 위해 두 개의 사용자 지정 가능 매개 변수를 추가 합니다.

### <a name="k1"></a>k1

*K1* 매개 변수는 각 일치 조건의 용어 빈도와 문서 쿼리 쌍의 최종 관련성 점수 간의 크기 조정 함수를 제어 합니다.

값이 0 인 "이진 모델"은 해당 용어가 텍스트에 표시 되는 횟수에 관계 없이 일치 하는 모든 문서에 대해 동일 하지만, 더 큰 k1 값을 사용 하면 동일한 용어의 인스턴스가 문서에 모두 있는 경우 점수를 계속 증가 시킬 수 있습니다. 기본적으로 Azure Cognitive Search는 k1 매개 변수에 1.2 값을 사용 합니다. 여러 용어가 검색 쿼리의 일부가 될 것으로 생각 되는 경우 더 높은 k1 값을 사용 하는 것이 중요할 수 있습니다. 이러한 경우에는 여러 다른 쿼리 용어와 일치 하는 문서를 한 번만 일치 하는 문서를 여러 번 검색 하는 것이 좋습니다. 예를 들어 "아폴로 Spaceflight" 라는 용어를 포함 하는 문서에 대 한 인덱스를 쿼리할 때 "아폴로" 및 "Spaceflight"를 모두 명확 하 게 설명 하는 다른 문서와 비교 하 여 "Spaceflight" 라는 용어를 포함 하지 않고 "아폴로" 라는 용어를 몇 번만 포함 하는 문서의 점수를 낮출 수 있습니다. 
 
### <a name="b"></a>b

*B* 매개 변수는 문서의 길이가 관련성 점수에 미치는 영향을 제어 합니다.

0.0 값은 문서의 길이가 점수에 영향을 주지 않음을 의미 하는 반면, 1.0 값은 문서 길이로 관련성 점수에 대 한 용어 빈도의 영향을 정규화 함을 의미 합니다. B 매개 변수에 대 한 Azure Cognitive Search에 사용 되는 기본값은 0.75입니다. 문서 길이의 용어 빈도를 정규화 하는 것은 긴 문서를 penalize 하는 경우에 유용 합니다. 경우에 따라 긴 문서에 비해 긴 문서 (예: 전체 novel)에 관련성이 많은 용어가 포함 될 가능성이 높습니다.

### <a name="setting-k1-and-b-parameters"></a>K1 및 b 매개 변수 설정

B 또는 k1 값을 사용자 지정 하려면 BM25를 사용할 때 유사성 개체에 속성으로 추가 하면 됩니다.

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

유사성 알고리즘은 인덱스를 만들 때에만 설정할 수 있습니다. 이는 기존 인덱스에 대해 사용 중인 유사성 알고리즘을 변경할 수 없음을 의미 합니다. *"B"* 및 *"k1"* 매개 변수는 BM25를 사용 하는 기존 인덱스 정의를 업데이트할 때 수정할 수 있습니다. 기존 인덱스에서 해당 값을 변경 하면 최소한 몇 초 동안 인덱스를 오프 라인 상태로 전환 하므로 인덱싱 및 쿼리 요청이 실패 합니다. 따라서 업데이트 요청의 쿼리 문자열에서 "allowIndexDowntime 중지 시간 = true" 매개 변수를 설정 해야 합니다.

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>참조  

+ [REST API 참조](/rest/api/searchservice/)   
+ [인덱스에 점수 매기기 프로필 추가](index-add-scoring-profiles.md)    
+ [인덱스 API 만들기](/rest/api/searchservice/create-index)   
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?view=azure-dotnet)