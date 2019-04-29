---
title: Azure AI Gallery에서 데이터 관리
titleSuffix: Azure Machine Learning Studio
description: 인터페이스 또는 AI Gallery 카탈로그 API를 사용하여 Azure AI Gallery에서 제품 내 사용자 데이터를 내보내고 삭제할 수 있습니다. 이 문서는 방법을 안내합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 44ff2a5b723c086604acf39e9f975deb53759ae1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752048"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Azure AI Gallery에서 제품 내 사용자 데이터 보기 및 삭제

인터페이스 또는 AI Gallery 카탈로그 API를 사용하여 Azure AI Gallery에서 제품 내 사용자 데이터를 보고 삭제할 수 있습니다. 이 문서에서는 이러한 작업을 수행하는 방법에 대해 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>UI를 사용하여 AI Gallery에서 데이터 보기

Azure AI Gallery 웹 사이트 UI를 통해 게시한 항목을 볼 수 있습니다. 사용자는 공개 솔루션과 목록에 없는 솔루션, 프로젝트, 실험 및 게시된 다른 항목을 볼 수 있습니다.

1.  [Azure AI Gallery](https://gallery.azure.ai/)에 로그인합니다.
2.  오른쪽 위 모서리에서 프로필 사진을 클릭한 다음, 계정 이름을 클릭하여 프로필 페이지를 로드합니다.
3.  프로필 페이지에는 목록에 없는 항목을 포함하여 갤러리에 게시된 모든 항목이 표시됩니다.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>AI Gallery 카탈로그 API를 사용하여 데이터 보기

https://catalog.cortanaanalytics.com/entities에서 액세스할 수 있는 AI Gallery 카탈로그 API를 통해 수집된 데이터를 프로그래밍 방식으로 볼 수 있습니다. 데이터를 보려면 작성자 ID가 필요합니다. 카탈로그 API를 통해 목록에 없는 엔터티를 보려면 액세스 토큰이 필요합니다.

카탈로그 응답은 JSON 형식으로 반환됩니다.

### <a name="get-an-author-id"></a>작성자 ID 가져오기
작성자 ID는 Azure AI Gallery에 게시할 때 사용한 이메일 주소를 기반으로 합니다. 다음 절차는 변경되지 않습니다.

1.  [Azure AI Gallery](https://gallery.azure.ai/)에 로그인합니다.
2.  오른쪽 위 모서리에서 프로필 사진을 클릭한 다음, 계정 이름을 클릭하여 프로필 페이지를 로드합니다.
3.  주소 표시줄의 URL은 `authorId=` 뒤에 나오는 영숫자 ID를 표시합니다. 예를 들어 다음과 같습니다. URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    작성자 ID: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>액세스 토큰 가져오기

카탈로그 API를 통해 목록에 없는 엔터티를 보려면 액세스 토큰이 필요합니다. 액세스 토큰이 없어도 사용자는 여전히 공용 엔터티 및 다른 사용자 정보를 볼 수 있습니다.

액세스 토큰을 가져오려면 로그인하는 동안 브라우저에서 카탈로그 API에 보내는 HTTP 요청의 `DataLabAccessToken` 헤더를 검사해야 합니다.

1.  [Azure AI Gallery](https://gallery.azure.ai/)에 로그인합니다.
2.  오른쪽 위 모서리에서 프로필 사진을 클릭한 다음, 계정 이름을 클릭하여 프로필 페이지를 로드합니다.
3.  F12 키를 눌러 브라우저 [개발자 도구] 창을 열고, [네트워크] 탭을 선택한 다음, 페이지를 새로 고칩니다. 
4. 필터 텍스트 상자에서 *catalog* 문자열을 입력하여 해당 문자열에 대한 요청을 필터링합니다.
5.  `https://catalog.cortanaanalytics.com/entities` URL에 대한 요청에서 GET 요청을 찾아 *헤더* 탭을 선택합니다. *요청 헤더* 섹션까지 아래로 스크롤합니다.
6.  `DataLabAccessToken` 헤더 아래에 영숫자 토큰이 있습니다. 데이터 보안을 유지하려면 이 토큰을 공유하지 마세요.

### <a name="view-user-information"></a>사용자 정보 보기
이전 단계에서 가져온 작성자 ID를 사용하고 다음 URL에서 `[AuthorId]`를 대체하여 사용자의 프로필 정보를 봅니다.

    https://catalog.cortanaanalytics.com/users/[AuthorID]

예를 들어 이 URL 요청은 다음과 같습니다.
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

다음과 같은 응답을 반환합니다.

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>공용 엔터티 보기

카탈로그 API는 [AI Gallery 웹 사이트](https://gallery.azure.ai/)에서도 직접 볼 수 있도록 게시된 엔터티에 대한 정보를 Azure AI Gallery에 저장합니다. 

게시된 엔터티를 보려면 다음 URL을 방문하여 `[AuthorId]`를 위의 [작성자 ID 가져오기](#get-an-author-id)에서 가져온 작성자 ID로 바꿉니다.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

예를 들면 다음과 같습니다.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>목록에 없는 엔터티 및 공용 엔터티 보기

여기서의 쿼리는 공용 엔터티만 표시합니다. 목록에 없는 항목을 포함하여 모든 항목을 보려면 이전 섹션에서 가져온 액세스 토큰을 제공합니다.

1.  [Postman](https://www.getpostman.com)과 같은 도구를 사용하여 [액세스 토큰 가져오기](#get-your-access-token)에서 설명한 대로 카탈로그 URL에 HTTP GET 요청을 만듭니다.
2.  값이 액세스 토큰으로 설정된 `DataLabAccessToken`이라는 HTTP 요청 헤더를 만듭니다.
3.  HTTP 요청을 제출합니다.

> [!TIP]
> 목록에 없는 엔터티가 카탈로그 API의 응답에 표시되지 않으면 사용자가 올바르지 않거나 만료된 액세스 토큰이 있을 수 있습니다. Azure AI Gallery에서 로그아웃한 다음, [액세스 토큰 가져오기](#get-your-access-token)의 단계를 반복하여 토큰을 갱신하세요. 
