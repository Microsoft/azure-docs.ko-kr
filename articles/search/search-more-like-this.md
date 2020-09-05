---
title: moreLikeThis (미리 보기) 쿼리 기능
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API의 미리 보기 버전에서 제공 되는 moreLikeThis (미리 보기) 기능에 대해 설명 합니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cd6b64f118460a115963ed0bf105641d80334348
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934993"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Cognitive Search의 moreLikeThis (미리 보기)

> [!IMPORTANT] 
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2020-06-30-미리 보기](search-api-preview.md) 에서이 기능을 제공 합니다. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

`moreLikeThis=[key]` 문서 키로 지정 된 문서와 유사한 문서를 찾는 [문서 검색 API](/rest/api/searchservice/search-documents) 의 쿼리 매개 변수입니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 매개 변수를 사용 하 여 지정한 제한 된 필드를 제외한 모든 검색 가능 필드의 내용이 고려 됩니다 `searchFields` . `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 수준 검색 가능 필드의 내용을 고려 합니다. 대신 특정 필드를 지정 하려면 매개 변수를 사용 하면 `searchFields` 됩니다. 

`MoreLikeThis` [복합 형식의](search-howto-complex-data-types.md)검색 가능한 하위 필드에는를 사용할 수 없습니다.

## <a name="examples"></a>예

다음 모든 예제에서는 [빠른 시작: Azure Portal에서 검색 인덱스 만들기](search-get-started-portal.md)의 호텔 샘플을 사용 합니다.

### <a name="simple-query"></a>단순 쿼리

다음 쿼리는 매개 변수로 지정 된 대로 원본 문서의 필드와 가장 유사한 설명 필드가 있는 문서를 찾습니다 `moreLikeThis` .

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

이 예제에서 요청은 29를 사용 하는 것과 유사한 호텔을 검색 합니다 `HotelId` .
Http GET을 사용 하는 대신 `MoreLikeThis` HTTP POST를 사용 하 여 호출할 수도 있습니다.

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>필터 적용

`MoreLikeThis` 는와 같은 다른 일반적인 쿼리 매개 변수와 함께 사용할 수 있습니다 `$filter` . 예를 들어, 범주가 ' r o o t '이 고 등급이 3.5 보다 높은 호텔 으로만 쿼리를 제한할 수 있습니다.

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>필드 선택 및 결과 제한

선택기를 사용 하 여 `$top` 쿼리에서 반환 되는 결과 수를 제한할 수 있습니다 `MoreLikeThis` . 또한를 사용 하 여 필드를 선택할 수 있습니다 `$select` . 여기에서 상위 3 개의 호텔은 해당 ID, 이름 및 등급과 함께 선택 됩니다. 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>다음 단계

모든 웹 테스트 도구를 사용 하 여이 기능을 시험해 볼 수 있습니다.  이 연습에서는 Postman을 사용 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Postman을 사용 하 여 Azure Cognitive Search REST Api 살펴보기](search-get-started-postman.md)