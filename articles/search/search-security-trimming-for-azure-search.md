---
title: 결과를 한정하기 위한 보안 필터 - Azure Search
description: 보안 필터 및 사용자 ID를 사용하여 Azure Search 콘텐츠에 대한 액세스 제어
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: brjohnstmsft
ms.author: brjohnst
manager: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 326a449d3992d22a4be2d365061c99ef8b13aef9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282387"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Azure Search의 결과를 한정하기 위한 보안 필터

사용자 ID에 따라 Azure Search에서 검색 결과를 한정하기 위해 보안 필터를 적용할 수 있습니다. 이 검색 환경에서는 일반적으로 검색을 요청하는 사람의 ID를 누가 문서에 대한 권한을 갖는지에 대한 원칙을 포함하는 필드와 비교해 보는 것이 필요합니다. 일치하는 항목이 발견되면, 사용자 또는 보안 주체(예: 그룹 또는 역할)는 해당 문서에 액세스할 수 있습니다.

보안 필터링을 달성할 한 가지 방법은 예를 들어 `Id eq 'id1' or Id eq 'id2'`와 같은 같음 표현의 복잡한 분리를 통해서입니다. 이 방법은 오류가 발생하기 쉽고, 유지 관리가 어려우며, 목록에 수백 수천 개의 값이 있는 경우 쿼리 응답 시간(초)이 느려집니다. 

더 간단하고 더 신속한 방법은 `search.in` 함수를 통하는 것입니다. 등식 대신에 `search.in(Id, 'id1, id2, ...')`을 사용한다면 1초 미만의 응답 시간을 기대할 수 있습니다.

이 문서는 다음 단계를 사용하여 보안 필터링을 수행하는 방법을 보여줍니다.
> [!div class="checklist"]
> * 보안 주체 식별자를 포함하는 필드 만들기 
> * 관련 보안 주체 식별자로 기존 문서를 푸시 또는 업데이트합니다.
> * `search.in` `filter`로 검색 요청 발급

>[!NOTE]
> 보안 주체 식별자를 검색하는 프로세스를 이 문서에서 다루지 않습니다. Id 서비스 공급자에게서 가져와야 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search 서비스](https://docs.microsoft.com/azure/search/search-create-service-portal) 및 [Azure Search 인덱스](https://docs.microsoft.com/azure/search/search-create-index-portal)가 있다고 가정합니다.  

## <a name="create-security-field"></a>보안 필드 만들기

문서에는 어떤 그룹에 액세스 권한이 있는지 지정하는 필드가 있어야 합니다. 이 정보는 발급자에 반환되는 결과 집합에서 어떤 문서가 선택되거나 거부되는지에 대한 필터 조건이 됩니다.
보안된 파일의 인덱스가 있고 각각의 파일을 다른 사용자가 액세스할 수 있다고 가정해 보겠습니다.
1. 필드 `group_ids`(어떤 이름이든 임의로 선택할 수 있습니다)를 `Collection(Edm.String)`로 추가합니다. 검색 결과가 사용자의 액세스 권한에 따라 필터링되도록 필드에 `true`에 설정된 `filterable` 특성이 있는지 확인합니다. 예를 들어, `file_name` "secured_file_b"를 사용해 문서에 대해 `group_ids` 필드를 `["group_id1, group_id2"]`에 설정할 경우, 그룹 Id가 "group_id1" 또는 "group_id2"에 속한 사용자만이 파일 읽기 권한이 있습니다.
   필드의 `retrievable` 특성이 `false`로 설정되었는지 확인하여 검색 요청의 일부로 반환되지 않게 합니다.
2. 또한 이 예제를 위해 `file_id`과 `file_name` 필드를 추가합니다.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>REST API를 사용하여 인덱스에 데이터 푸시
  
인덱스의 URL 엔드포인트에 HTTP POST 요청을 발급합니다. HTTP 요청의 본문은 추가될 문서를 포함하는 JSON 개체입니다.

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

요청 본문에 문서 콘텐츠를 지정합니다.

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

그룹 목록으로 기존 문서를 업데이트해야 하는 경우 `merge` 또는 `mergeOrUpload` 동작을 사용할 수 있습니다.

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

문서 추가 또는 업데이트에 대한 자세한 내용은 [문서 편집](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)에서 확인할 수 있습니다.
   
## <a name="apply-the-security-filter"></a>보안 필터를 적용합니다.

`group_ids` 액세스에 기초해 문서를 다듬으려면 `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` 필터를 사용해 검색 쿼리를 발급해야 합니다. 여기서 'group_id1 group_id2...'는 검색 요청 발급자가 속해 있는 그룹입니다.
이 필터는 주어진 식별자 중 하나가 `group_ids` 필드에 들어 있는 모든 문서와 일치합니다.
Azure Search을 사용한 문서 검색에 대한 상세한 내용은 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)에서 확인할 수 있습니다.
이 샘플은 POST 요청을 사용하여 문서를 검색하는 방법을 보여 줍니다.

HTTP POST 요청을 발급합니다.

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

요청 본문에 필터를 지정합니다.

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

`group_ids`에 "group_id1" 또는 "group_id2"가 들어 있는 곳에 문서를 되돌려 두어야 합니다. 즉, 사용자는 요청 발급자에게 읽기 권한이 있는 문서를 가져옵니다.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>결론

사용자 Id와 Azure Search `search.in()` 함수에 따라 결과를 필터링할 수 있는 방법입니다. 각 대상 문서와 연결 된 보안 주체 식별자에 대 한 일치 하도록 요청 하는 사용자에 대 한 사용자 식별자를 전달 하려면이 함수를 사용할 수 있습니다. 검색 요청이 처리될 때, `search.in` 함수는 사용자의 보안 주체 중 아무도 읽기 권한이 없는 검색 결과를 필터링합니다. 보안 주체 식별자는 보안 그룹, 역할 또는 심지어 사용자 본인 ID와 같은 것을 나타낼 수 있습니다.
 
## <a name="see-also"></a>참고 항목

+ [Azure Search 필터를 사용하여 Active Directory ID 기반 액세스 제어](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure Search의 필터](search-filters.md)
+ [Azure Search 작업의 데이터 보안 및 액세스 제어](search-security-overview.md)