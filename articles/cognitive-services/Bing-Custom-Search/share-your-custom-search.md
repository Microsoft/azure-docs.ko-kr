---
title: 사용자 지정 검색 공유 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: 팀 구성원들과 인스턴스를 공유하여 인스턴스 공동 편집 및 테스트를 쉽게 허용할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6141bd67681df757536792981d47756a20ed33db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353326"
---
# <a name="share-your-custom-search-instance"></a>Custom Search 인스턴스 공유

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

팀 구성원들과 인스턴스를 공유하여 인스턴스 공동 편집 및 테스트를 쉽게 허용할 수 있습니다. 전자 메일 주소만 사용하면 모든 사용자와 인스턴스를 공유할 수 있습니다. 인스턴스를 공유하려면 다음을 수행합니다.

- [Custom Search](https://customsearch.ai)에 로그인합니다.
- Custom Search 인스턴스를 선택합니다.
- 기어로 표시되는 설정 아이콘을 클릭합니다. 
- **인스턴스 공유** 아래에 인스턴스를 공유할 사용자의 전자 메일 주소를 입력하고 **공유** 를 클릭합니다. 

추가한 전자 메일 주소는 **인스턴스 공유 대상** 목록에 추가됩니다. 인스턴스를 공유하려는 각 사용자에 대해 이 프로세스를 반복합니다. 

Custom Search 계정이 없는 사용자의 메일도 목록에 추가할 수 있습니다. 하지만 해당 사용자가 구성을 변경하려면 먼저 Custom Search에 등록해야 합니다. 다른 사용자와 인스턴스를 공유하면 해당 Custom Search 인스턴스 목록에 표시됩니다. 한 번에 한 사용자만 인스턴스를 수정할 수 있습니다. 다른 사용자가 편집 중인 인스턴스를 수정하려고 하면 경고가 표시됩니다. 최대 10명의 사용자와 인스턴스를 공유할 수 있습니다.

## <a name="stop-sharing"></a>공유 중지

다른 사용자와 인스턴스 공유를 중지하려면 제거 아이콘을 사용하여 해당 메일 주소를 목록에서 제거합니다. 이렇게 하면 인스턴스 목록에서도 인스턴스가 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [Custom Autosuggest 환경 구성](define-custom-suggestions.md)