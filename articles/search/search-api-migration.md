---
title: 최신 Azure Search Service REST API 버전으로 업그레이드 - Azure Search
description: API 버전의 차이를 검토하고 기존 코드를 최신 Azure Search Service REST API 버전으로 마이그레이션하는 데 필요한 작업을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126994"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>최신 Azure Search Service REST API 버전으로 업그레이드
[Azure Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/)의 이전 버전을 사용하는 경우 이 문서를 통해 최신 일반 공급 API 버전, 2017-11-11을 사용하기 위해 애플리케이션을 업그레이드할 수 있습니다.

REST API의 2017-11-11 버전에는 이전 버전에서 변경된 몇 가지가 포함되어 있습니다. 이는 대부분 이전 버전과 호환되기 때문에 이전에 사용하던 버전에 따라 간단히 코드를 변경할 수 있습니다. 새 API 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>버전 2017-11-11의 새로운 기능
2017-11-11 버전은 Azure Search 서비스 REST API의 최신 일반 공급 릴리스입니다. 이 API 버전의 새로운 기능은 다음과 같습니다.

* [동의어](search-synonyms.md) 새 동의어 기능을 사용하면 동의어를 정의하고 쿼리의 범위를 확장할 수 있습니다.
* [텍스트 Blob의 효율적인 인덱싱을 위한 지원](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText) Azure Blob 인덱서에 대한 새 `text` 구문 분석 모드는 인덱싱 성능을 크게 향상시킵니다.
* [서비스 통계 API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics) 이 새로운 API를 사용하여 Azure Search의 현재 사용량 및 리소스의 한도를 봅니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
GA 버전, 2015-02-28 또는 2016-09-01에서 업그레이드하는 경우 버전 번호를 제외하고 코드를 변경하지 않아도 됩니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.
* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

이러한 경우에 해당하는 경우 코드를 적절하게 변경해야 합니다. 2017-11-11 버전의 [새로운 기능](#WhatsNew)을 사용하여 시작하지 않으면 코드를 변경할 필요가 없습니다.

미리 보기 API 버전에서 업그레이드하는 경우, 위 내용이 적용되지만 일부 미리 보기 기능은 2017-11-11 버전에서 사용할 수 없다는 점을 알아야 합니다.

* Azure Blob Storage 인덱서는 CSV 파일과 JSON 배열을 포함하는 Blob을 지원합니다.
* "이보다 더 나은" 쿼리

코드가 이러한 기능을 사용하는 경우 기능을 사용하지 않고는 2017-11-11로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요.
> 
> 

## <a name="conclusion"></a>결론
Azure Search 서비스 REST API 사용에 대한 자세한 정보가 필요한 경우 MSDN의 최근에 업데이트된 [API 참조](https://docs.microsoft.com/rest/api/searchservice/)를 참조하세요.

Azure Search에 대한 귀하의 피드백을 환영합니다. 문제가 발생하면 [Azure Search MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) 또는 [StackOverflow](https://stackoverflow.com/)를 통해 자유롭게 도움을 요청하세요. StackOverflow에서 Azure Search에 대한 질문이 있는 경우, `azure-search`를 사용하여 태그하세요.

Azure Search를 이용해 주셔서 감사합니다!

