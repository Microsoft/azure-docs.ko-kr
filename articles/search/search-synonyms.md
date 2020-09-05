---
title: 검색 인덱스에 대 한 쿼리 확장의 동의어
titleSuffix: Azure Cognitive Search
description: 동의어 맵을 만들어 Azure Cognitive Search 인덱스에서 검색 쿼리의 범위를 확장 합니다. 범위는 목록으로 제공하는 동급 용어를 포함하도록 확장됩니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: aad953483749d676844221f7e519f50c50b63ad4
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948643"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Cognitive Search의 동의어

검색 엔진의 동의어는 사용자가 실제로 용어를 제공할 필요 없이 쿼리의 범위를 암시적으로 확장하는 동등한 용어를 연결합니다. 예를 들어 용어 "dog"와 "canine" 및 "puppy"의 동의어 연결을 지정하면 "dog", "canine" 또는 "puppy"를 포함하는 모든 문서는 쿼리의 범위에 속하게 됩니다.

Azure Cognitive Search에서 동의어 확장은 쿼리 시에 수행 됩니다. 기존 작업을 중단하지 않고 동의어 맵을 서비스에 추가할 수 있습니다. 인덱스를 다시 빌드할 필요 없이 **synonymMaps** 속성을 필드 정의에 추가할 수 있습니다.

## <a name="create-synonyms"></a>동의어 만들기

동의어를 만들 수 있는 포털 지원은 없지만 REST API 또는 .NET SDK를 사용할 수 있습니다. REST를 시작 하려면이 API를 사용 하 여 Postman 및 공식화 요청을 [사용 하](search-get-started-postman.md) 는 것이 좋습니다. [동의어 맵 만들기](/rest/api/searchservice/create-synonym-map)를 사용 하는 것이 좋습니다. C # 개발자의 경우 [c #을 사용 하 여 Azure 인식 검색에서 동의어 추가](search-synonyms-tutorial-sdk.md)를 시작할 수 있습니다.

선택적으로 서비스 쪽 암호화를 위해 [고객 관리 키](search-security-manage-encryption-keys.md) 를 사용 하는 경우 해당 보호를 동의어 맵의 내용에 적용할 수 있습니다.

## <a name="use-synonyms"></a>동의어 사용

Azure Cognitive Search에서 동의어 지원은 사용자가 정의 하 고 서비스에 업로드 하는 동의어 맵을 기반으로 합니다. 이러한 맵은 독립적인 리소스(인덱스 또는 데이터 원본 등)를 구성하며 검색 서비스의 모든 인덱스에서 검색 가능한 필드에 의해 사용될 수 있습니다.

동의어 맵과 인덱스는 독립적으로 유지됩니다. 동의어 맵을 정의하고 서비스에 업로드하면 필드 정의에서 **synonymMaps**라는 새 속성을 추가하여 필드에서 동의어 기능을 사용할 수 있습니다. 동의어 맵 만들기, 업데이트 및 삭제는 항상 전체 문서 작업이므로 증분식으로 동의어 맵의 일부분을 만들거나 업데이트하거나 삭제할 수 없습니다. 단일 항목을 업데이트하는 경우에도 다시 로드해야 합니다.

동의어를 검색 애플리케이션에 통합하는 과정은 다음의 두 단계 프로세스로 이루어집니다.

1.  아래 API를 통해 동의어 맵을 검색 서비스에 추가합니다.  

2.  인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다.

검색 애플리케이션에 대한 여러 동의어 맵(예: 애플리케이션이 다국어 고객 기반을 지원하는 언어별 맵)을 만들 수 있습니다. 현재 필드에서는 그중 하나만 사용할 수 있습니다. 언제든지 필드의 synonymMaps 속성을 업데이트할 수 있습니다.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps 리소스 API

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>POST 또는 PUT을 사용하여 서비스 아래에 동의어 맵을 추가하거나 업데이트합니다.

동의어 맵은 POST 또는 PUT을 통해 서비스에 업로드됩니다. 각 규칙은 줄 바꿈 문자('\n')로 구분되어야 합니다. 무료 서비스에서 동의어 맵 당 최대 5000 개의 규칙을 정의 하 고 다른 모든 Sku에서 맵 당 2만 규칙을 정의할 수 있습니다. 각 규칙에는 최대 20개의 확장이 있을 수 있습니다.

동의어 맵은 아래에 설명된 Apache Solr 형식이어야 합니다. 다른 형식의 기존 동의어 사전이 있어 이를 직접 사용하려는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에 알려 주세요.

다음 예제에서처럼 HTTP POST를 사용하여 새 동의어 맵을 만들 수 있습니다.

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

또는 PUT을 사용하여 URI에서 동의어 맵 이름을 지정할 수 있습니다. 동의어 맵이 없으면 생성됩니다.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Apache Solr 동의어 형식

Solr 형식은 동등하고 명시적인 동의어 매핑을 지원합니다. 매핑 규칙은이 문서에서 설명 하는 Apache [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)r의 오픈 소스 동의어 필터 사양을 따릅니다. 다음은 동등한 동의어에 대한 샘플 규칙입니다.

```
USA, United States, United States of America
```

위의 규칙을 사용하면 검색 쿼리 "USA"가 "USA" 또는 "United States" 또는 "United States of America"로 확장됩니다.

명시적 매핑은 "=>" 화살표로 표시됩니다. 이 지정 된 경우 "=>"의 왼쪽과 일치 하는 검색 쿼리의 용어 순서가 오른쪽의 대체 항목으로 바뀝니다. 아래 규칙이 지정되면 검색 쿼리 "Washington", "Wash" 또는 "WA"가 모두 "WA"로 다시 작성됩니다. 명시적 매핑은 지정된 방향으로만 적용돠며 이 경우 "WA" 쿼리를 "Washington"으로 다시 작성하지 않습니다.

```
Washington, Wash., WA => WA
```

쉼표가 포함 된 동의어를 정의 해야 하는 경우 다음 예제와 같이 백슬래시로 이스케이프할 수 있습니다.

```
WA\, USA, WA, Washington
```

백슬래시는 JSON 및 c #과 같은 다른 언어에서 특수 문자 이므로 두 번 이스케이프 해야 할 수도 있습니다. 예를 들어 위의 동의어 맵에 대 한 REST API으로 전송 되는 JSON은 다음과 같습니다.

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>서비스 아래 동의어 맵을 나열합니다.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>서비스 아래 동의어 맵을 가져옵니다.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>서비스 아래 동의어 맵을 삭제합니다.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>인덱스 정의에서 동의어 맵을 사용하도록 검색 가능한 필드를 구성합니다.

새 필드 속성 **synonymMaps**는 검색 가능한 필드에 사용할 동의어 맵을 지정하는 데 사용될 수 있습니다. 동의어 맵은 서비스 수준 리소스이며 서비스 아래 인덱스의 모든 필드에서 참조할 수 있습니다.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
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
```

**synonymMaps**은 'Edm.String' 또는 'Collection(Edm.String)' 형식의 검색 가능한 필드에 지정될 수 있습니다.

> [!NOTE]
> 필드당 하나의 동의어 맵만 있을 수 있습니다. 여러 동의어 맵을 사용하려면 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에 알려 주세요.

## <a name="impact-of-synonyms-on-other-search-features"></a>동의어가 다른 검색 기능에 미치는 영향

동의어 기능은 OR 연산자와 함께 동의어를 사용하여 원래 쿼리를 다시 작성합니다. 따라서 강조 표시된 적중 항목 및 점수 매기기 프로필에서는 원래 용어 및 동의어를 같은 것으로 취급합니다.

동의어 기능은 검색 쿼리에 적용되고 필터 또는 패싯에는 적용되지 않습니다. 마찬가지로 제안은 원래 용어만을 기반으로 하며 동의어 일치는 응답에 나타나지 않습니다.

동의어 확장은 와일드카드 검색 용어에는 적용되지 않으며 접두사, 유사 용어 및 정규식 용어는 확장되지 않습니다.

동의어 확장과 와일드카드, 정규식 또는 유사 항목 검색을 적용하는 단일 쿼리를 수행해야 하는 경우 OR 구문을 사용하여 쿼리를 결합할 수 있습니다. 예를 들어 간단한 쿼리 구문에서 동의어와 와일드카드를 결합하려면 용어는 `<query> | <query>*`가 됩니다.

개발(비프로덕션) 환경에 기존 인덱스가 있는 경우 작은 사전을 사용하여 동의어 추가를 통해 점수 매기기 프로필, 강조 표시된 적중 항목 및 제안 사항을 비롯한 검색 환경을 변경하는 방법을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [동의어 맵 만들기](/rest/api/searchservice/create-synonym-map)