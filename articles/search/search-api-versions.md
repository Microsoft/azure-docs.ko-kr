---
title: API 버전
titleSuffix: Azure Cognitive Search
description: .NET SDK의 Azure Cognitive Search REST Api 및 클라이언트 라이브러리에 대 한 버전 정책입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 73037ac3725ad1e7e9dd84597936c47406244630
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949884"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search의 API 버전

Azure Cognitive Search는 정기적으로 기능 업데이트를 롤업 합니다. 항상 그렇지는 않지만 경우에 따라 이러한 업데이트에는 이전 버전과 호환성을 유지하기 위해 API의 새 버전이 필요하기도 합니다. 새 버전을 게시하면 코드에서 검색 서비스 업데이트를 통합 하는 시기와 방법을 제어할 수 있습니다.

규칙에 따라 Azure Cognitive Search 팀은 새 API 버전을 사용 하도록 코드를 업그레이드 하는 데 몇 가지 노력이 수반 될 수 있으므로 필요한 경우에만 새 버전을 게시 합니다. API의 일부를 변경하여 더 이상 이전 버전과 호환되지 않는 경우에만 새 버전이 필요합니다. 그러한 변경은 기존 기능의 수정 또는 기존 API 노출 영역을 변경하는 새로운 기능으로 인해 발생할 수 있습니다.

SDK 업데이트와 동일한 규칙이 적용됩니다. Azure Cognitive Search SDK는 해당 버전의 세 부분 (주, 부 및 빌드 번호 (예: 1.1.0))을 포함 하는 [의미 체계 버전 관리](https://semver.org/) 규칙을 따릅니다. 이전 버전과 호환되지 않는 변경의 경우에만 SDK의 새로운 주 버전이 릴리스됩니다. 호환성을 유지하는 기능 업데이트의 경우 부 버전이, 버그 수정의 경우 빌드 버전이 증가합니다.

> [!Important]
> .NET, Java, Python 및 JavaScript 용 Azure Sdk는 Azure Cognitive Search에 대 한 새 클라이언트 라이브러리를 배포 합니다. 현재 Azure SDK 라이브러리는 최신 검색 REST Api (2020-06-30) 또는 관리 REST Api (2020-03-13)를 완전히 지원 하지 않지만 시간이 지남에 따라 변경 됩니다. 이 페이지 또는 기능 향상에 대 한 알림의 [새로운](whats-new.md) 기능을 정기적으로 확인할 수 있습니다.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>지원 되지 않는 버전

2020 년 10 월 15 일까 지 최신 버전의 REST API에 대 한 기존 검색 솔루션을 업그레이드 합니다. 이 시점에서 Azure Cognitive Search REST API의 다음 버전은 사용 중지 되며 더 이상 지원 되지 않습니다.

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-미리 보기**
+ **2014-10-20-미리 보기**

또한 3.0.0 보다 오래 된 Azure Cognitive Search .NET SDK 버전은 이러한 REST API 버전 중 하나를 대상으로 하기 때문에 [**사용**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) 이 중지 됩니다. 

이 날짜 이후에는 더 이상 사용 되지 않는 REST API 또는 SDK 버전 중 하나를 사용 하는 응용 프로그램이 더 이상 작동 하지 않으므로 업그레이드 해야 합니다. 이 유형을 변경 하는 경우와 마찬가지로 12 개월의 통지를 제공 하므로 적절 한 시간을 조정할 수 있습니다.

Azure Cognitive Search을 계속 사용 하려면 [REST API](search-api-migration.md) 를 대상으로 하는 기존 코드를 [REST API 버전 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 또는 2020 년 10 월 15 일까 지 최신 SDK로 마이그레이션 하세요.  최신 버전으로 업데이트 하는 방법에 대 한 질문이 있는 경우 azuresearch_contact@microsoft.com 코드를 업데이트 하는 데 충분 한 시간을 갖도록 2020 년 5 월 15 일에 메일을 보내 주시기 바랍니다.

## <a name="rest-apis"></a>REST API

Azure Cognitive Search 서비스 인스턴스는 최신 버전을 포함 하 여 여러 REST API 버전을 지원 합니다. 더 이상 최신 버전이 아닌 경우 버전을 계속 사용할 수 있지만 최신 버전을 사용 하도록 [코드를 마이그레이션하](search-api-migration.md) 는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

다음 표에서는 Search Service REST API의 현재 버전과 이전에 릴리스된 버전의 버전 기록을 제공 합니다. 설명서는 가장 최근의 안정적인 미리 보기 버전에만 게시 됩니다.

### <a name="search-service-apis"></a>Search Service Api

검색 서비스에서 콘텐츠를 만들고 관리 합니다.

| 버전&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 상태 | 이전 버전과의 호환성 문제 |
|-------------------------|--------|------------------------------|
| [검색 2020-06-30](/rest/api/searchservice/index)| Stable | 최신 버전의 검색 REST Api를 제공 하며, 관련 점수 매기기 및 일반적으로 기술 자료 저장소에 대 한 가용성을 제공 합니다.|
| [검색 2020-06-30-Preview](/rest/api/searchservice/index-preview)| 미리 보기 | 안정적인 버전과 연결 된 미리 보기 버전입니다. 에는 여러 [미리 보기 기능이](search-api-preview.md)포함 되어 있습니다. |
| 검색 2019-05-06 | Stable  | [복합 형식을](search-howto-complex-data-types.md)추가 합니다. |
| 검색 2019-05-06-미리 보기 | 미리 보기 | 안정적인 버전과 연결 된 미리 보기 버전입니다. |
| 검색 2017-11-11 | Stable | 기술력과 및 [AI 보강](cognitive-search-concept-intro.md)를 추가 합니다. |
| 검색 2017-11-11-미리 보기 | 미리 보기 | 안정적인 버전과 연결 된 미리 보기 버전입니다. |
| 검색 2016-09-01 |Stable | [인덱서](search-indexer-overview.md)를 추가 합니다. |
| 검색 2016-09-01-미리 보기 | 미리 보기 | 안정적인 버전과 연결 된 미리 보기 버전입니다.|
| 검색 2015-02-28 | 10-10-2020 이후 지원 되지 않음   | 첫 번째 일반 공급 릴리스.  |
| 검색 2015-02-28-미리 보기 | 10-10-2020 이후 지원 되지 않음  | 안정적인 버전과 연결 된 미리 보기 버전입니다. |
| 검색 2014-10-20-미리 보기 | 10-10-2020 이후 지원 되지 않음 | 두 번째 공개 미리 보기입니다. |
| 검색 2014-07-31-미리 보기 | 10-10-2020 이후 지원 되지 않음  | 첫 번째 공개 미리 보기입니다. |

### <a name="management-rest-apis"></a>관리 REST API

검색 서비스를 만들고 구성 하며 API 키를 관리 합니다.

| 버전&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 상태 | 이전 버전과의 호환성 문제 |
|-------------------------|--------|------------------------------|
| [관리 2020-03-13](/rest/api/searchmanagement/) | Stable | Endpoint protection의 고급 기능을 통해 안정적인 최신 릴리스의 관리 REST Api 개인 링크를 통해 [개인 끝점](service-create-private-endpoint.md) 을 추가 하 고 새 서비스에 대 한 [네트워크 IP 규칙](service-configure-firewall.md) 을 추가 합니다. |
| [관리 2019-10-01-미리 보기](/rest/api/searchmanagement/index-2019-10-01-preview) | 미리 보기  | 버전 번호에도 불구 하 고 여전히 관리 REST Api의 현재 미리 보기 버전입니다. 지금은 미리 보기 기능이 없습니다. 모든 미리 보기 기능은 최근에 일반 공급으로 전환 되었습니다. |
| 관리 2015-08-19  | Stable | 관리 REST Api의 첫 번째 일반적으로 사용할 수 있는 버전입니다. 서비스 프로 비전, 확장 및 api 키 관리를 제공 합니다. |
| 관리 2015-08-19-미리 보기 | 미리 보기 | 관리 REST Api의 첫 번째 미리 보기 버전입니다. |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

다음 표에서는 최신 SDK 버전에 대 한 링크를 제공 합니다. 

| SDK 버전 | 상태 | 설명 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11.0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | 7 월 2020에 출시 된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. 검색 REST api-version = 2020-06-30 REST API를 대상으로 하지만 아직 지원 하지는 [않습니다.](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet) |
| [10.0 검색](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | 출시 5 월 2019. 검색 REST api-version = 2019-05-06을 대상으로 합니다.|
| [8.0-미리 보기](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | 미리 보기 | 4 월 2019에 출시 되었습니다. 검색 REST api-version = 2019-05 -06-Preview를 대상으로 합니다.|
| [3.0.0를 검색 합니다.](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | 관리 REST api-version = 2015-08-19를 대상으로 합니다.  |

이전 버전의 Microsoft Azure에 대 한 자세한 내용은를 검색 하 고, 모든 릴리스에 대 한 [NuGet 패키지 페이지로](https://www.nuget.org/packages/Microsoft.Azure.Search/) 이동 하 고, **버전 기록**으로 스크롤하고, 섹션을 확장 하 여 릴리스 전체 목록을 확인 하세요.

## <a name="azure-sdk-for-java"></a>Java용 Azure SDK

| SDK 버전 | 상태 | 설명  |
|-------------|--------|------------------------------|
| [Java azure-검색-문서 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | 7 월 2020에 출시 된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. 검색 REST api-version = 2019-05-06을 대상으로 합니다. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | 관리 REST api-version = 2015-08-19를 대상으로 합니다. |

## <a name="azure-sdk-for-javascript"></a>JavaScript용 Azure SDK

| SDK 버전 | 상태 | 설명  |
|-------------|--------|------------------------------|
| [JavaScript azure-검색 11.0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | 7 월 2020에 출시 된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. 검색 REST api-version = 2016-09-01을 대상으로 합니다. |
| [JavaScript azure-arm-검색](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | 관리 REST api-version = 2015-08-19를 대상으로 합니다. |

## <a name="azure-sdk-for-python"></a>Python용 Azure SDK

| SDK 버전 | 상태 | 설명  |
|-------------|--------|------------------------------|
| [Python azure-검색-문서 11.0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | 7 월 2020에 출시 된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. 검색 REST api-version = 2019-05-06을 대상으로 합니다. |
| [Python azure-관리-검색 1.0](/python/api/overview/azure/search?view=azure-python) | Stable | 관리 REST api-version = 2015-08-19를 대상으로 합니다. |