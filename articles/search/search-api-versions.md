---
title: .NET SDK 및 REST API에 대한 API 버전 관리 - Azure Search
description: Azure Search REST API의 버전 정책 및 .NET SDK의 클라이언트 라이브러리.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c07a0c8f5440033455c69fe40806adf9b548c16f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126962"
---
# <a name="api-versions-in-azure-search"></a>Azure Search의 API 버전
Azure Search는 정기적으로 기능 업데이트를 공개합니다. 항상 그렇지는 않지만 경우에 따라 이러한 업데이트에는 이전 버전과 호환성을 유지하기 위해 API의 새 버전이 필요하기도 합니다. 새 버전을 게시하면 코드에서 검색 서비스 업데이트를 통합 하는 시기와 방법을 제어할 수 있습니다.

일반적으로 새 API 버전을 사용하여 코드를 업그레이드하기 위해 다소의 작업이 필요하므로, Azure Search 팀은 필요한 경우에만 새 버전을 게시합니다. API의 일부를 변경하여 더 이상 이전 버전과 호환되지 않는 경우에만 새 버전이 필요합니다. 그러한 변경은 기존 기능의 수정 또는 기존 API 노출 영역을 변경하는 새로운 기능으로 인해 발생할 수 있습니다.

SDK 업데이트와 동일한 규칙이 적용됩니다. Azure Search SDK는 버전이 주, 부, 빌드 번호 등의 세 부분으로 구성되는 [의미 체계 버전 관리](https://semver.org/) 규칙을 따릅니다(예: 1.1.0). 이전 버전과 호환되지 않는 변경의 경우에만 SDK의 새로운 주 버전이 릴리스됩니다. 호환성을 유지하는 기능 업데이트의 경우 부 버전이, 버그 수정의 경우 빌드 버전이 증가합니다.

> [!NOTE]
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

## <a name="snapshot-of-current-versions"></a>현재 버전의 스냅숏
아래는 Azure Search에 대한 모든 프로그래밍 인터페이스의 현재 버전의 스냅숏입니다.

| 인터페이스 | 가장 최근의 주 버전 | 상태 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |일반 공급, 2018년 4월 릴리스됨 |
| [.NET SDK 미리 보기](https://aka.ms/search-sdk-preview) |4.0.1-preview |미리 보기, 2017년 5월 릴리스 |
| [서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |일반 공급 |
| [서비스 REST API 2017-11-11-Preview](search-api-2017-11-11-preview.md) |\2017-11-11-Preview |미리 보기 |
| [.NET 관리 SDK](https://aka.ms/search-mgmt-sdk) |2.0 |일반 공급 |
| [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |일반 공급 |

REST API의 경우 각 호출의 `api-version` 을 포함해야 합니다. `api-version`을 사용하면 API 미리 보기 등 특정 버전을 대상으로 하기 쉽습니다. 다음 예제에서는 `api-version` 매개 변수를 지정하는 방법을 보여 줍니다.

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> 각 요청에는 `api-version`이 있지만, 모든 API 요청에 대해 동일한 버전을 사용하는 것이 좋습니다. 새 API 버전이 이전 버전에서 인식 하지 못하는 특성 또는 작업을 도입하는 경우 특히 유용합니다. 혼합 API 버전에는 의도하지 않은 결과가 있을 수 있기 때문에 사용하지 말아야 합니다.
>
> 서비스 REST API 및 관리 REST API는 따로따로 버전이 지정됩니다. 버전 번호 체계는 동일합니다.

일반 공급(또는 GA) API는 프로덕션에서 사용할 수 있으며 Azure 서비스 수준 계약에 적용을 받습니다. 미리 보기 버전에는 GA 버전으로 항상 마이그레이션되지 않는 실험적 기능이 있습니다. **프로덕션 애플리케이션에서 미리 보기 API를 사용하지 않는 것이 좋습니다.**

## <a name="about-preview-and-generally-available-versions"></a>미리 보기 및 일반 공급 버전 정보
Azure Search는 항상 REST API를 통해 실험 기능을 미리 공개한 다음, .NET SDK의 시험판 버전을 통해 공개합니다.

미리 보기 기능은 기능 디자인 및 구현에 관한 사용자 의견을 수집하기 위해 테스트 및 실험에 사용할 수 있습니다. 이러한 이유로 미리 보기 기능은 시간에 따라 이전 버전과 호환되지 않는 방식으로 변경될 수 있습니다. 이는 이전 버전과 호환되는 일부 수정 및 향상된 기능 외에는 변경되지 않으며 안정적인 GA 버전의 기능과 대조됩니다. 또한 미리 보기 기능이 항상 GA로 릴리스되는 것은 아닙니다.

이러한 이유로 미리 보기 버전에 종속된 프로덕션 코드를 작성하지 않는 것이 좋습니다. 이전 미리 보기 버전을 사용하는 경우 일반 공급(GA) 버전으로 마이그레이션하는 것이 좋습니다.

.NET SDK: 코드 마이그레이션 지침은 [.NET SDK 업그레이드](search-dotnet-sdk-migration.md)에서 확인할 수 있습니다.

일반 공급은 Azure Search가 현재 SLA(서비스 수준 계약) 하에 있다는 의미입니다. SLA는 [Azure Search 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에서 찾을 수 있습니다.
