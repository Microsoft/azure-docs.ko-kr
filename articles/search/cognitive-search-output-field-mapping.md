---
title: 출력 필드에 보강된 입력 필드의 인식 검색 매핑 - Azure Search
description: 원본 데이터 필드를 보강하고 추출해 Azure Search 인덱스에서 출력 필드에 매핑합니다.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: dd62119b01465392a92c7e68231fed8027b04da2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342623"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>검색 가능한 인덱스에 보강된 필드를 매핑하는 방법

이 문서에서는 검색 가능한 인덱스에서 입력 필드에 보강된 출력 필드를 매핑하는 방법에 대해 알아봅니다. [기술 집합을 정의](cognitive-search-defining-skillset.md)했으면 검색 인덱스에서 지정된 필드에 값을 직접 기여하는 모든 기술의 출력 필드를 매핑해야 합니다. 필드 매핑은 보강된 문서에서 콘텐츠를 인덱스로 이동하는 데 필요합니다.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings 사용
필드를 매핑하려면 아래와 같이 `outputFieldMappings`을 인덱서 정의에 추가합니다.

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
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
            "targetFieldName": "descriptions"
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
각 출력 필드 매핑의 경우 인덱스(targetFieldName)에서 참조된 대로 필드 이름 및 보강된 필드 (sourceFieldName)의 이름을 설정합니다.

sourceFieldName에서 경로는 하나 또는 여러 요소를 나타낼 수 있습니다. 위의 예에서 ```/document/content/sentiment```은 단일 숫자 값을 나타내는 반면 ```/document/content/organizations/*/description```는 여러 조직 설명을 나타냅니다. 여러 요소가 있는 경우 이런 요소는 요소 각각을 포함한 배열로 “평면화”됩니다. 더 구체적으로 ```/document/content/organizations/*/description``` 예제의 경우 *설명* 필드의 데이터는 인덱스되기 전에 설명의 플랫 배열과 같습니다.

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>다음 단계
검색 가능한 필드에 보강된 필들을 매핑했으면 [인덱스 정의의 일부로서](search-what-is-an-index.md) 검색 가능한 필드 각각에 대해 필드 특성을 설정할 수 있습니다.

필드 매핑에 대한 자세한 내용은 [Azure Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.
