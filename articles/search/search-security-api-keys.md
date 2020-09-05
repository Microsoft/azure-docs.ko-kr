---
title: 관리 및 쿼리 api 키를 만들고 관리 하 고 보호 합니다.
titleSuffix: Azure Cognitive Search
description: Api 키는 서비스 끝점에 대 한 액세스를 제어 합니다. 관리자 키는 쓰기 액세스를 부여합니다. 쿼리 키는 읽기 전용 액세스에 대해서만 만들 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cd6e61b8614e4c8ff5d54232972865c81cbb3ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928873"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스에 대 한 api 키 만들기 및 관리

검색 서비스에 대한 모든 요청에는 해당 서비스용으로 특별히 생성된 읽기 전용 api-key가 필요합니다. 이 api-key는 검색 서비스 엔드포인트에 대한 액세스를 인증하는 유일한 방법으로, 모든 요청에 포함되어야 합니다. [REST 솔루션](search-get-started-postman.md)에서 api-key는 일반적으로 요청 헤더에 지정됩니다. [.NET 솔루션](search-howto-dotnet-sdk.md#core-scenarios)에서 키는 종종 구성 설정으로 지정된 후 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient)에서 [자격 증명](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)(관리자 키) 또는 [SearchCredentials](/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials)(쿼리 키)로 전달됩니다.

키는 서비스를 프로비전하는 동안 검색 서비스를 사용하여 생성됩니다. [Azure Portal](https://portal.azure.com)에서 키 값을 살펴보고 얻을 수 있습니다.

![포털 페이지, 설정, 키 섹션](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>api-key란?

api-key는 임의로 생성된 숫자 및 문자로 구성된 문자열입니다. [역할 기반 권한](search-security-rbac.md)을 통해 키를 삭제하거나 읽을 수 있지만 사용자 정의 암호로 키를 대체하거나 검색 작업에 액세스하기 위한 기본 인증 방법으로 Active Directory를 사용할 수 없습니다. 

검색 서비스에 액세스하는 데 사용되는 두 가지 키 유형은 다음과 같습니다. 관리자(읽기-쓰기) 및 쿼리(읽기 전용)

|키|Description|제한|  
|---------|-----------------|------------|  
|관리자|서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다.<br /><br /> 포털에서 *기본* 및 *보조* 키라고 하는 두 개의 관리자 키는 서비스를 만들 때 생성되고 요청 시 개별적으로 다시 생성할 수 있습니다. 키가 두 개이면 서비스에 대해 액세스를 지속하는 데 하나의 키를 사용하는 동안 다른 키를 롤오버할 수 있습니다.<br /><br /> 관리자 키는 HTTP 요청 헤더에서만 지정됩니다. URL에 관리자 api-key를 배치할 수 없습니다.|서비스당 최대 2개|  
|쿼리|인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 수행하는 클라이언트 애플리케이션에 배포됩니다.<br /><br /> 쿼리 키는 요청 시 생성됩니다. 포털에서 수동으로 만들거나 [관리 REST API](/rest/api/searchmanagement/)를 통해 프로그래밍 방식으로 만들 수 있습니다.<br /><br /> 검색, 제안 또는 조회 작업의 HTTP 요청 헤더에서 쿼리 키를 지정할 수 있습니다. 또는 쿼리 키를 URL에 매개 변수로 전달할 수 있습니다. 클라이언트 애플리케이션이 요청을 생성하는 방법에 따라 키를 쿼리 매개 변수로 전달하는 것이 쉬울 수 있습니다.<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|서비스당 50개|  

 시각적으로는 관리자 키 및 쿼리 키 간의 구분이 없습니다. 두 키는 임의로 생성된 32개의 영숫자 문자로 구성된 문자열입니다. 애플리케이션에서 지정된 키의 형식을 잃어버린 경우 [포털에서 키 값을 확인](https://portal.azure.com)하거나 [REST API](/rest/api/searchmanagement/)를 사용하여 값 및 키 형식을 반환할 수 있습니다.  

> [!NOTE]  
>  요청 URI에서 `api-key`와 같은 중요한 데이터를 전달하는 낮은 수준의 보안 사례로 간주됩니다. 이러한 이유로 Azure Cognitive Search는 쿼리 문자열의로 쿼리 키만 허용 `api-key` 하므로 인덱스의 내용을 공개적으로 사용할 수 있어야 하는 경우가 아니면이를 방지 해야 합니다. 일반적으로 `api-key`를 요청 헤더로 전달하는 것이 좋습니다.  

## <a name="find-existing-keys"></a>기존 키 찾기

포털에서 또는 [관리 REST API](/rest/api/searchmanagement/)를 통해 액세스 키를 가져올 수 있습니다. 자세한 내용은 [관리자 및 쿼리 api-key 관리](search-security-api-keys.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
3. 서비스를 선택 하 고 개요 페이지에서 **설정**  > **키** 를 클릭 하 여 관리자 및 쿼리 키를 봅니다.

   ![포털 페이지, 설정, 키 섹션](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>쿼리 키 만들기

쿼리 키는 문서 컬렉션을 대상으로 하는 작업의 인덱스 내 문서에 대 한 읽기 전용 액세스에 사용 됩니다. 검색, 필터 및 제안 쿼리는 쿼리 키를 사용 하는 모든 작업입니다. 인덱스 정의 나 인덱서 상태와 같은 시스템 데이터 또는 개체 정의를 반환 하는 모든 읽기 전용 작업에는 관리자 키가 필요 합니다.

클라이언트 앱에서 액세스 및 작업을 제한 하는 것은 서비스에서 검색 자산을 보호 하는 데 필수적입니다. 클라이언트 앱에서 시작 하는 모든 쿼리에 대해 항상 관리자 키 대신 쿼리 키를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
3. 서비스를 선택 하 고 개요 페이지에서 **설정**  > **키**를 클릭 합니다.
4. **쿼리 키 관리**를 클릭 합니다.
5. 서비스에 대해 이미 생성 된 쿼리 키를 사용 하거나 최대 50 개의 새 쿼리 키를 만듭니다. 기본 쿼리 키의 이름은 지정 되지 않지만 관리를 위해 추가 쿼리 키의 이름을 지정할 수 있습니다.

   ![쿼리 키 만들기 또는 사용](media/search-security-overview/create-query-key.png) 

> [!Note]
> 쿼리 키 사용을 보여 주는 코드 예제는 [c #에서 Azure Cognitive Search 인덱스 쿼리](./search-get-started-dotnet.md)에서 찾을 수 있습니다.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

비즈니스 연속성을 위한 보조 키를 사용 하 여 기본 키를 회전할 수 있도록 각 서비스에 대해 두 개의 관리 키가 생성 됩니다.

1. **설정**  > **키** 페이지에서 보조 키를 복사 합니다.
2. 모든 애플리케이션의 경우 보조 키를 사용하도록 api-key 설정을 업데이트합니다.
3. 기본 키를 다시 생성합니다.
4. 새 기본 키를 사용하도록 모든 애플리케이션을 업데이트합니다.

동시에 두 키를 다시 생성 하면 해당 키를 사용 하는 모든 클라이언트 요청이 실패 하 고 HTTP 403이 금지 됩니다. 그러나 콘텐츠는 삭제 되지 않으며 영구적으로 잠기지 않습니다. 

포털 또는 관리 계층 ([REST API](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)또는 Azure Resource Manager)을 통해 서비스에 계속 액세스할 수 있습니다. 관리 기능은 서비스 api 키가 아닌 구독 ID를 통해 작동 가능한 api 키가 없는 경우에도 계속 사용할 수 있습니다. 

포털 또는 관리 계층을 통해 새 키를 만든 후 새 키가 있고 요청에 이러한 키를 제공 하면 콘텐츠 (인덱스, 인덱서, 데이터 원본, 동의어 맵)로 액세스가 복원 됩니다.

## <a name="secure-api-keys"></a>api-key 보안
키 보안은 포털 또는 Resource Manager 인터페이스를 통해 액세스를 제한하여 보장됩니다(PowerShell 또는 명령줄 인터페이스). 설명한 것처럼 구독 관리자는 모든 api-key를 보고 다시 생성할 수 있습니다. 예방 조치로 역할 할당을 검토하여 관리 키에 대한 액세스 권한이 있는 사용자를 파악할 수 있습니다.

+ 서비스 대시보드에서 **액세스 제어(IAM)**, **역할 할당** 탭을 차례로 클릭하여 서비스에 대한 역할 할당을 봅니다.

다음 역할의 멤버는 키를 보고 다시 생성할 수 있습니다. 소유자, 참가자 [Search Service 참가자](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> 검색 결과에 대한 ID 기반 액세스의 경우 보안 필터를 만들어서 ID를 기준으로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md) 및 [Active Directory로 보안](search-security-trimming-for-azure-search-with-aad.md)을 참조하세요.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search의 역할 기반 액세스 제어](search-security-rbac.md)
+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [성능 및 최적화 문서](search-performance-optimization.md)