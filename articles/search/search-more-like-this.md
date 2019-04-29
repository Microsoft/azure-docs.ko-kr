---
title: Azure Search의 moreLikeThis(미리 보기) - Azure Search
description: Azure Search REST API에서 노출된 moreLikeThis(미리 보기) 기능에 대한 예비 설명서입니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283359"
---
# <a name="morelikethis-in-azure-search-preview"></a>Azure Search의 moreLikeThis(미리 보기)

`moreLikeThis=[key]`는 [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents)의 쿼리 매개 변수입니다. 검색 쿼리에서 `moreLikeThis` 매개 변수를 지정하여 문서 키로 지정된 문서와 유사한 문서를 찾을 수 있습니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. `searchFields` 매개 변수가 필드를 제한하는 데 사용되지 않은 한 기본적으로 모든 `searchable` 필드의 내용이 고려됩니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

## <a name="examples"></a>예 

다음은 moreLikeThis 쿼리의 예입니다. 이 쿼리는 해당 설명 필드가 `moreLikeThis` 매개 변수로 지정된 대로 소스 문서의 필드에 가장 유사한 문서를 찾습니다.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>기능 가용성

moreLikeThis 기능은 현재 미리 보기에 있으며 미리 보기 api-version `2015-02-28-Preview` 및 `2016-09-01-Preview`에서만 지원됩니다. 요청 시 API 버전이 지정되므로 동일한 앱에서 일반적으로 사용할 수 있는 (GA) 및 미리 보기 API를 결합할 수 있습니다. 그러나 미리 보기 API는 SLA가 적용되지 않으며 기능이 변경될 수 있으므로 프로덕션 애플리케이션에서 사용하지 않는 것이 좋습니다.
