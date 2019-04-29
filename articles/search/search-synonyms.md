---
title: 검색 인덱스에 대한 쿼리 확장을 위한 동의어 - Azure Search
description: Azure Search 인덱스에 대한 검색 쿼리 범위를 확장하기 위한 동의어 맵을 만듭니다. 범위는 목록으로 제공하는 동급 용어를 포함하도록 확장됩니다.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4383cc327d8058ca44acd892f41a7a256e3b1727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281805"
---
# <a name="synonyms-in-azure-search"></a>Azure Search의 동의어

검색 엔진의 동의어는 사용자가 실제로 용어를 제공할 필요 없이 쿼리의 범위를 암시적으로 확장하는 동등한 용어를 연결합니다. 예를 들어 용어 "dog"와 "canine" 및 "puppy"의 동의어 연결을 지정하면 "dog", "canine" 또는 "puppy"를 포함하는 모든 문서는 쿼리의 범위에 속하게 됩니다.

Azure Search에서 동의어 확장은 쿼리 시에 수행됩니다. 기존 작업을 중단하지 않고 동의어 맵을 서비스에 추가할 수 있습니다. 인덱스를 다시 빌드할 필요 없이 **synonymMaps** 속성을 필드 정의에 추가할 수 있습니다.

## <a name="feature-availability"></a>기능 가용성

동의어 기능은 최신 api-버전에서 지원됩니다(api-버전=2017-11-11). 지금은 Azure Portal 지원이 없습니다.

## <a name="how-to-use-synonyms-in-azure-search"></a>Azure Search에서 동의어를 사용하는 방법

Azure Search에서 동의어 지원은 사용자가 정의하고 서비스에 업로드하는 동의어 맵을 기반으로 합니다. 이러한 맵은 독립적인 리소스(인덱스 또는 데이터 원본 등)를 구성하며 검색 서비스의 모든 인덱스에서 검색 가능한 필드에 의해 사용될 수 있습니다.

동의어 맵과 인덱스는 독립적으로 유지됩니다. 동의어 맵을 정의하고 서비스에 업로드하면 필드 정의에서 **synonymMaps**라는 새 속성을 추가하여 필드에서 동의어 기능을 사용할 수 있습니다. 동의어 맵 만들기, 업데이트 및 삭제는 항상 전체 문서 작업이므로 증분식으로 동의어 맵의 일부분을 만들거나 업데이트하거나 삭제할 수 없습니다. 단일 항목을 업데이트하는 경우에도 다시 로드해야 합니다.

동의어를 검색 애플리케이션에 통합하는 과정은 다음의 두 단계 프로세스로 이루어집니다.

1.  아래 API를 통해 동의어 맵을 검색 서비스에 추가합니다.  

2.  인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps 리소스 API

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>POST 또는 PUT을 사용하여 서비스 아래에 동의어 맵을 추가하거나 업데이트합니다.

동의어 맵은 POST 또는 PUT을 통해 서비스에 업로드됩니다. 각 규칙은 줄 바꿈 문자('\n')로 구분되어야 합니다. 무료 서비스에서는 동의어 맵당 최대 5,000개의 규칙을 정의하고 다른 모든 SKU에서는 10,000개의 규칙을 정의할 수 있습니다. 각 규칙에는 최대 20개의 확장이 있을 수 있습니다.

동의어 맵은 아래에 설명된 Apache Solr 형식이어야 합니다. 다른 형식의 기존 동의어 사전이 있어 이를 직접 사용하려는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에 알려 주세요.

다음 예제에서처럼 HTTP POST를 사용하여 새 동의어 맵을 만들 수 있습니다.

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

또는 PUT을 사용하여 URI에서 동의어 맵 이름을 지정할 수 있습니다. 동의어 맵이 없으면 생성됩니다.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr 동의어 형식

Solr 형식은 동등하고 명시적인 동의어 매핑을 지원합니다. 매핑 규칙은 이 문서 ([SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter))에 설명된 Apache Solr의 공개 소스 동의어 필터 사양을 준수합니다. 다음은 동등한 동의어에 대한 샘플 규칙입니다.
```
USA, United States, United States of America
```

위의 규칙을 사용하면 검색 쿼리 "USA"가 "USA" 또는 "United States" 또는 "United States of America"로 확장됩니다.

명시적 매핑은 "=>" 화살표로 표시됩니다. 지정되면 "=>" 왼쪽에 일치하는 검색 쿼리의 용어 시퀀스는 오른쪽에 대체 항목으로 바뀝니다. 아래 규칙이 지정되면 검색 쿼리 "Washington", "Wash" 또는 "WA"가 모두 "WA"로 다시 작성됩니다. 명시적 매핑은 지정된 방향으로만 적용돠며 이 경우 "WA" 쿼리를 "Washington"으로 다시 작성하지 않습니다.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>서비스 아래 동의어 맵을 나열합니다.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>서비스 아래 동의어 맵을 가져옵니다.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>서비스 아래 동의어 맵을 삭제합니다.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다.

새 필드 속성 **synonymMaps**는 검색 가능한 필드에 사용할 동의어 맵을 지정하는 데 사용될 수 있습니다. 동의어 맵은 서비스 수준 리소스이며 서비스 아래 인덱스의 모든 필드에서 참조할 수 있습니다.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps**은 'Edm.String' 또는 'Collection(Edm.String)' 형식의 검색 가능한 필드에 지정될 수 있습니다.

> [!NOTE]
> 필드당 하나의 동의어 맵만 있을 수 있습니다. 여러 동의어 맵을 사용하려면 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에 알려 주세요.

## <a name="impact-of-synonyms-on-other-search-features"></a>동의어가 다른 검색 기능에 미치는 영향

동의어 기능은 OR 연산자와 함께 동의어를 사용하여 원래 쿼리를 다시 작성합니다. 따라서 강조 표시된 적중 항목 및 점수 매기기 프로필에서는 원래 용어 및 동의어를 같은 것으로 취급합니다.

동의어 기능은 검색 쿼리에 적용되고 필터 또는 패싯에는 적용되지 않습니다. 마찬가지로 제안은 원래 용어만을 기반으로 하며 동의어 일치는 응답에 나타나지 않습니다.

동의어 확장은 와일드카드 검색 용어에는 적용되지 않으며 접두사, 유사 용어 및 정규식 용어는 확장되지 않습니다.

동의어 확장과 와일드카드, 정규식 또는 유사 항목 검색을 적용하는 단일 쿼리를 수행해야 하는 경우 OR 구문을 사용하여 쿼리를 결합할 수 있습니다. 예를 들어 간단한 쿼리 구문에서 동의어와 와일드카드를 결합하려면 용어는 `<query> | <query>*`가 됩니다.

## <a name="tips-for-building-a-synonym-map"></a>동의어 맵 빌드에 대한 팁

- 간결하고 잘 설계된 동의어 맵은 가능한 일치 항목의 철저한 목록보다 더 효율적입니다. 쿼리가 많은 동의어로 확장되는 경우 지나치게 크거나 복잡한 사전은 구문 분석하는 데 오래 걸리며 쿼리 대기 시간에 영향을 줍니다. 사용할 용어를 추측하지 않고 [검색 트래픽 분석 보고서](search-traffic-analytics.md)를 통해 실제 용어를 가져올 수 있습니다.

- 예비 연습 및 유효성 검사 연습 모두에서 이 보고서를 설정 후 사용하여 동의어 일치의 이점이 되는 용어를 정확하게 결정한 다음 동의어 맵이 더 나은 결과를 산출하는지에 대한 유효성 검증으로 계속 사용합니다. 미리 정의된 보고서에서 타일 "가장 일반적인 검색 쿼리" 및 "결과가 0인 검색 쿼리"는 필요한 정보를 제공합니다.

- 검색 애플리케이션에 대한 여러 동의어 맵(예: 애플리케이션이 다국어 고객 기반을 지원하는 언어별 맵)을 만들 수 있습니다. 현재 필드에서는 그중 하나만 사용할 수 있습니다. 언제든지 필드의 synonymMaps 속성을 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개발(비프로덕션) 환경에 기존 인덱스가 있는 경우 작은 사전을 사용하여 동의어 추가를 통해 점수 매기기 프로필, 강조 표시된 적중 항목 및 제안 사항을 비롯한 검색 환경을 변경하는 방법을 확인할 수 있습니다.

- [검색 트래픽 분석을 사용하도록 설정](search-traffic-analytics.md)하고 미리 정의된 Power BI 보고서를 사용하여 가장 많이 사용되는 용어 및 제로 문서를 반환하는 용어를 참조합니다. 이러한 통찰력을 바탕으로 인덱스에 있는 문서로 해석되어야 하는 비생산적인 쿼리에 대한 동의어를 포함하도록 사전을 수정합니다.
