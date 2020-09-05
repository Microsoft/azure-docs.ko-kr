---
title: 출력 필드에 입력 매핑
titleSuffix: Azure Cognitive Search
description: 원본 데이터 필드를 추출 및 보강 하 고 Azure Cognitive Search 인덱스의 출력 필드에 매핑합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935367"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-보강 필드를 검색 가능한 인덱스에 매핑하는 방법

![인덱서 단계](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "인덱서 단계")

이 문서에서는 검색 가능한 인덱스에서 입력 필드에 보강된 출력 필드를 매핑하는 방법에 대해 알아봅니다. [기술 집합을 정의](cognitive-search-defining-skillset.md)했으면 검색 인덱스에서 지정된 필드에 값을 직접 기여하는 모든 기술의 출력 필드를 매핑해야 합니다.

보강 문서에서 인덱스로 콘텐츠를 이동 하려면 출력 필드 매핑이 필요 합니다.  보강 문서는 사실 정보의 트리입니다. 인덱스에 복합 형식을 지 원하는 경우에도 보강 트리의 정보를 보다 단순한 형식 (예를 들어 문자열 배열)으로 변환 하는 것이 좋습니다. 출력 필드 매핑을 사용 하면 정보를 평면화 하 여 데이터 셰이프 변환을 수행할 수 있습니다. 출력 필드 매핑은 기술 실행 후 항상 발생 하지만 기술가 정의 되지 않은 경우에도이 단계를 실행할 수 있습니다.

출력 필드 매핑의 예:

* 기술의 일환으로 문서의 각 페이지에 언급 된 조직의 이름을 추출 했습니다. 이제 이러한 각 조직 이름을 Edm. Collection 형식의 인덱스에 있는 필드 (Edm. String)에 매핑해야 합니다.

* 기술의 일부로 "document/translated_text" 라는 새 노드를 생성 했습니다. 이 노드의 정보를 인덱스의 특정 필드에 매핑해야 합니다.

* 기술 있지만 Cosmos DB 데이터베이스에서 복합 형식을 인덱싱하는 경우 해당 복합 형식의 노드로 이동 하 여 인덱스의 필드에 매핑해야 합니다.

> [!NOTE]
> 최근 출력 필드 매핑에서 매핑 함수 기능을 사용 하도록 설정 했습니다. 매핑 함수에 대 한 자세한 내용은 [필드 매핑 함수](./search-indexer-field-mappings.md#field-mapping-functions) 를 참조 하세요.

## <a name="use-outputfieldmappings"></a>OutputFieldMappings 사용

필드를 매핑하려면 아래와 같이 `outputFieldMappings`을 인덱서 정의에 추가합니다.

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

요청 본문은 다음과 같이 구성되어 있습니다.

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

각 출력 필드 매핑에 대해 보강 문서 트리 (sourceFieldName)에서 데이터의 위치를 설정 하 고 인덱스 (targetFieldName)에서 참조 되는 필드의 이름을 설정 합니다.

## <a name="flattening-information-from-complex-types"></a>복합 형식의 정보 평면화 

sourceFieldName에서 경로는 하나 또는 여러 요소를 나타낼 수 있습니다. 위의 예에서 ```/document/content/sentiment```은 단일 숫자 값을 나타내는 반면 ```/document/content/organizations/*/description```는 여러 조직 설명을 나타냅니다. 

여러 요소가 있는 경우 이런 요소는 요소 각각을 포함한 배열로 “평면화”됩니다. 

더 구체적으로 ```/document/content/organizations/*/description``` 예제의 경우 *설명* 필드의 데이터는 인덱스되기 전에 설명의 플랫 배열과 같습니다.

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

이는 중요 한 원칙 이므로 다른 예제를 제공 합니다. 복합 형식의 배열이 보강 트리의 일부로 있는 것으로 가정 합니다. 아래에서 설명한 것과 같은 복합 형식의 배열이 있는 customEntities 라는 멤버가 있다고 가정해 보겠습니다.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

인덱스에 각 엔터티의 이름을 저장할 형식 컬렉션 (질병)의 ' n a m e ' 이라는 필드가 있다고 가정해 보겠습니다. 

이러한 작업은 다음과 같이 "" 기호를 사용 하 여 쉽게 수행할 수 있습니다 \* .

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

이 작업을 수행 하면 다음과 같이 customEntities 요소의 각 이름이 문자열의 단일 배열로 "평면화" 됩니다.

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>다음 단계
검색 가능한 필드에 보강된 필들을 매핑했으면 [인덱스 정의의 일부로서](search-what-is-an-index.md) 검색 가능한 필드 각각에 대해 필드 특성을 설정할 수 있습니다.

필드 매핑에 대 한 자세한 내용은 [Azure Cognitive Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조 하세요.