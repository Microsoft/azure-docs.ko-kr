---
title: Azure Search .NET Management SDK로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Search .NET Management SDK로 업그레이드 합니다. 새 기능 및 마이그레이션에 필요한 코드 변경에 대해 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936710"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Azure Search .NET Management SDK 버전 업그레이드

이 문서에서는 검색 서비스를 프로 비전 또는 프로 비전 해제 하 고, 용량을 조정 하 고, API 키를 관리 하는 데 사용 되는 Azure Search .NET 관리 SDK의 후속 버전으로 마이그레이션하는 방법을 설명 합니다

관리 Sdk는 특정 버전의 관리 REST API를 대상으로 합니다. 개념 및 작업에 대 한 자세한 내용은 [검색 관리 (REST)](/rest/api/searchmanagement/)를 참조 하세요.

## <a name="versions"></a>버전

| SDK 버전 | 해당 REST API 버전 | 기능 추가 또는 동작 변경 |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-version = 2020-30-20 | 끝점 보안 (IP 방화벽 및 [Azure 개인 링크](../private-link/private-endpoint-overview.md)와의 통합)을 추가 합니다. |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version = 2019-10-01 | 유용성이 개선되었습니다. [목록 쿼리 키](/rest/api/searchmanagement/querykeys/listbysearchservice) 에 대 한 주요 변경 내용 (GET은 중단 됨). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version = 2015-08-19  | 첫 번째 버전 |

## <a name="how-to-upgrade"></a>업그레이드하는 방법

1. `Microsoft.Azure.Management.Search`Nuget 패키지 관리자 콘솔을 사용 하거나 프로젝트 참조를 마우스 오른쪽 단추로 클릭 하 고 "Nuget 패키지 관리 ..."를 선택 하 여에 대 한 nuget 참조를 업데이트 합니다. Visual Studio에서

1. NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드를 구조화 하는 방법에 따라 성공적으로 다시 빌드할 수 있으며,이 경우 완료 됩니다.

1. 빌드가 실패하는 경우 변경된 일부 SDK 인터페이스를 구현했기 때문일 수 있습니다(예: 단위 테스트를 위해). 이 문제를 해결 하려면와 같은 최신 메서드를 구현 해야 `BeginCreateOrUpdateWithHttpMessagesAsync` 합니다.

1. 빌드 오류를 수정한 후 응용 프로그램을 변경 하 여 새로운 기능을 활용할 수 있습니다. 

## <a name="upgrade-to-30"></a>3.0로 업그레이드

버전 3.0에서는 IP 범위에 대 한 액세스를 제한 하 고, 공용 인터넷에 표시 되지 않아야 하는 검색 서비스에 대해 Azure 개인 링크를 선택적으로 통합 하 여 개인 끝점 보호를 추가 합니다.

### <a name="new-apis"></a>새로운 API

| API | 범주| 세부 정보 |
|-----|--------|------------------|
| [NetworkRuleSet 집합](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP 방화벽 | 허용 되는 IP 주소 목록에 대해 서비스 끝점에 대 한 액세스를 제한 합니다. 개념 및 포털 지침은 [IP 방화벽 구성](service-configure-firewall.md) 을 참조 하세요. |
| [공유 개인 링크 리소스](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | 검색 서비스에서 사용할 공유 개인 링크 리소스를 만듭니다.  |
| [개인 끝점 연결](/rest/api/searchmanagement/privateendpointconnections) | Private Link | 개인 끝점을 통해 검색 서비스에 대 한 연결을 설정 하 고 관리 합니다. 개념 및 포털 지침은 [개인 끝점 만들기](service-create-private-endpoint.md) 를 참조 하세요.|
| [개인 링크 리소스](/rest/api/searchmanagement/privatelinkresources/) | Private Link | 개인 끝점 연결이 있는 검색 서비스의 경우 동일한 가상 네트워크에서 사용 되는 모든 서비스 목록을 가져옵니다. Azure 데이터 원본 (Azure Storage, Cosmos DB, Azure SQL)에서 끌어오거나 Cognitive Services 또는 Key Vault를 사용 하는 인덱서를 검색 솔루션에 포함 하는 경우 해당 리소스는 모두 가상 네트워크에서 끝점을 가져야 하며,이 API는 목록을 반환 해야 합니다. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | 이 속성은 서비스 만들기 또는 업데이트 요청에 대 한 속성입니다. 사용 하지 않도록 설정 된 경우 개인 링크는 유일한 액세스 모달입니다. |

### <a name="breaking-changes"></a>호환성이 손상되는 변경

[목록 쿼리 키](/rest/api/searchmanagement/querykeys/listbysearchservice) 요청에서 GET을 더 이상 사용할 수 없습니다. 이전 릴리스에서는이 릴리스에서 GET 또는 POST를 사용할 수 있으며, 앞으로 이동 하는 모든 릴리스에서는 POST만 지원 됩니다. 

## <a name="upgrade-to-20"></a>2.0로 업그레이드

Azure Search .NET Management SDK 버전 2는 약간의 업그레이드 이므로 코드를 변경 하려면 최소한의 작업만 필요 합니다. Sdk를 변경 하는 것은 SDK 자체의 유용성을 향상 시키기 위한 엄격 하 게 클라이언트 쪽 변경 내용입니다. 변경 내용은 다음과 같습니다.

* 이제 `Services.CreateOrUpdate` 및 해당 비동기 버전은 프로비저닝 `SearchService`를 자동으로 폴링하며, 서비스 프로비저닝이 완료될 때까지 반환되지 않습니다. 따라서 이러한 폴링 코드를 직접 작성하지 않아도 됩니다.

* 서비스 프로비저닝을 여전히 수동으로 폴링하려는 경우 새로운 `Services.BeginCreateOrUpdate` 메서드 또는 비동기 버전 중 하나를 사용할 수 있습니다.

* 새 메서드 `Services.Update` 및 해당 비동기 버전이 SDK에 추가되었습니다. 이러한 메서드는 HTTP PATCH를 사용하여 서비스의 증분 업데이트를 지원합니다. 예를 들어, 이제 원하는 `partitionCount` 및 `replicaCount` 속성만 포함하는 `SearchService` 인스턴스를 이러한 메서드에 전달하여 서비스를 크기를 조정할 수 있습니다. `Services.Get`을 호출하고, 반환된 `SearchService`를 수정한 후 `Services.CreateOrUpdate`에 전달하는 이전 방식은 계속 지원되지만 더 이상 필요하지 않습니다. 

## <a name="next-steps"></a>다음 단계

문제가 발생 하는 경우 질문을 게시 하는 가장 좋은 포럼은 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest)입니다. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[검색]"으로 레이블을 지정 해야 합니다.