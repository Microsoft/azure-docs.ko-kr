---
title: Az. 검색 모듈을 사용 하는 PowerShell 스크립트
titleSuffix: Azure Cognitive Search
description: PowerShell을 사용 하 여 Azure Cognitive Search 서비스를 만들고 구성 합니다. 서비스의 규모를 확장 또는 축소 하 고, 관리 및 쿼리 api 키를 관리 하 고, 시스템 정보를 쿼리할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209299"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>PowerShell을 사용 하 여 Azure Cognitive Search 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Windows, Linux 또는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 에서 PowerShell cmdlet 및 스크립트를 실행 하 여 Azure Cognitive Search를 만들고 구성할 수 있습니다. **Az. search** 모듈은 [검색 관리 REST api](https://docs.microsoft.com/rest/api/searchmanagement) 에 대 한 전체 패리티가 있는 [Azure PowerShell](https://docs.microsoft.com/powershell/) 를 확장 하 고 다음 작업을 수행 하는 기능을 제공 합니다.

> [!div class="checklist"]
> * [구독에서 search 서비스 나열](#list-search-services)
> * [서비스 정보 반환](#get-search-service-information)
> * [서비스 만들기 또는 삭제](#create-or-delete-a-service)
> * [관리 API 다시 생성-키](#regenerate-admin-keys)
> * [쿼리 api 만들기 또는 삭제-키](#create-or-delete-query-keys)
> * [복제본 및 파티션을 사용 하 여 확장 또는 축소](#scale-replicas-and-partitions)

경우에 따라 위의 목록에 *없는* 작업에 대 한 질문을 받게 됩니다. 현재는 **Az. Search** 모듈이 나 management REST API를 사용 하 여 서버 이름, 지역 또는 계층을 변경할 수 없습니다. 서비스를 만들 때 전용 리소스가 할당 됩니다. 따라서 기본 하드웨어 (위치 또는 노드 형식)를 변경 하려면 새 서비스가 필요 합니다. 마찬가지로 한 서비스에서 다른 서비스로 콘텐츠를 전송 하기 위한 도구나 Api는 없습니다.

서비스 내에서 콘텐츠 작성 및 관리는 [Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/) 또는 [.net SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)를 통해 진행 됩니다. 콘텐츠에 대 한 전용 PowerShell 명령은 없지만 REST 또는 .NET Api를 호출 하는 PowerShell 스크립트를 작성 하 여 인덱스를 만들고 로드할 수 있습니다.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>버전 확인 및 모듈 로드

이 문서의 예는 대화형 이며 높은 권한이 필요 합니다. Azure PowerShell ( **Az** module)이 설치 되어 있어야 합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/overview)를 참조하세요.

### <a name="powershell-version-check-51-or-later"></a>PowerShell 버전 확인 (5.1 이상)

지원 되는 모든 운영 체제에서 로컬 PowerShell은 5.1 이상 이어야 합니다.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>로드 Azure PowerShell

**Az** 가 설치 되어 있는지 확실 하지 않은 경우 확인 단계로 다음 명령을 실행 합니다. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

일부 시스템은 모듈을 자동으로 로드 하지 않습니다. 이전 명령에서 오류가 발생 하는 경우 모듈을 로드 하 고, 실패 하면 설치 지침으로 돌아가서 단계가 누락 되었는지 확인 합니다.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>브라우저 로그인 토큰을 사용 하 여 Azure에 연결

포털 로그인 자격 증명을 사용 하 여 PowerShell에서 구독에 연결할 수 있습니다. 또는 [서비스 주체를 사용 하 여 비 대화형으로 인증할](../active-directory/develop/howto-authenticate-service-principal-powershell.md)수 있습니다.

```azurepowershell-interactive
Connect-AzAccount
```

여러 Azure 구독을 보유 하는 경우 Azure 구독을 설정 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>구독의 서비스 나열

다음 명령은 [**Az. resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)에서 구독에 이미 프로 비전 된 기존 리소스 및 서비스에 대 한 정보를 반환 합니다. 이미 생성 된 검색 서비스 수를 모르는 경우 이러한 명령은 해당 정보를 반환 하 여 포털에 대 한 여행 정보를 저장 합니다.

첫 번째 명령은 모든 검색 서비스를 반환 합니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

서비스 목록에서 특정 리소스에 대 한 정보를 반환 합니다.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

결과는 다음 출력과 비슷합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>가져오기 Az. Search

[**Az. Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) 는 모듈을 로드할 때까지 사용할 수 없습니다.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>모두 나열 Az. Search 명령

확인 단계로 모듈에서 제공 하는 명령 목록을 반환 합니다.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

결과는 다음 출력과 비슷합니다.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>검색 서비스 정보 가져오기

**Az. 검색** 을 가져오고 검색 서비스를 포함 하는 리소스 그룹을 알고 있는 경우 [AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) 를 실행 하 여 이름, 지역, 계층, 복제본 및 파티션 수를 비롯 한 서비스 정의를 반환 합니다.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

결과는 다음 출력과 비슷합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>서비스 만들기 또는 삭제

[**AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 는 [새 검색 서비스를 만드는](search-create-service-portal.md)데 사용 됩니다.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
결과는 다음 출력과 비슷합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

[**AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 는 관리 [API 키](search-security-api-keys.md)를 롤오버 하는 데 사용 됩니다. 각 서비스에서 인증 된 액세스에 대해 두 개의 관리 키가 생성 됩니다. 모든 요청에 키가 필요 합니다. 두 관리 키는 기능적으로 동일 하며, 모든 정보를 검색 하거나 개체를 만들고 삭제할 수 있는 기능을 사용 하 여 검색 서비스에 대 한 모든 쓰기 권한을 부여 합니다. 다른 키를 대체할 때 사용할 수 있도록 두 개의 키가 있습니다. 

`primary` 또는 `secondary` 키로 지정 된 한 번에 하나씩만 다시 생성할 수 있습니다. 중단 없는 서비스의 경우 기본 키를 롤링 하는 동안 보조 키를 사용 하도록 모든 클라이언트 코드를 업데이트 해야 합니다. 작업이 진행 되는 동안에는 키를 변경 하지 마세요.

짐작할 수 있듯이 클라이언트 코드를 업데이트 하지 않고 키를 다시 생성 하면 이전 키를 사용 하는 요청이 실패 합니다. 모든 새 키를 다시 생성 해도 서비스에서 영구적으로 잠기지 않으며 포털을 통해 서비스에 계속 액세스할 수 있습니다. 기본 및 보조 키를 다시 생성 한 후 새 키를 사용 하도록 클라이언트 코드를 업데이트할 수 있으며 그에 따라 작업이 다시 시작 됩니다.

API 키에 대 한 값은 서비스에 의해 생성 됩니다. 사용할 Azure Cognitive Search에 대 한 사용자 지정 키를 제공할 수 없습니다. 마찬가지로, 관리 API 키에 대 한 사용자 정의 이름이 없습니다. 키에 대 한 참조는 `primary` 또는 `secondary`고정 문자열입니다. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

결과는 다음 출력과 비슷합니다. 두 키는 한 번에 하나만 변경 하더라도 반환 됩니다.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>쿼리 키 만들기 또는 삭제

[**AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) 은 클라이언트 앱에서 Azure Cognitive Search 인덱스에 대 한 읽기 전용 액세스를 위한 쿼리 [API 키](search-security-api-keys.md) 를 만드는 데 사용 됩니다. 쿼리 키는 검색 결과를 검색 하기 위해 특정 인덱스에 인증 하는 데 사용 됩니다. 쿼리 키는 인덱스, 데이터 원본 또는 인덱서와 같은 서비스의 다른 항목에 대 한 읽기 전용 액세스 권한을 부여 하지 않습니다.

사용할 Azure Cognitive Search에 대 한 키를 제공할 수 없습니다. API 키는 서비스에 의해 생성 됩니다.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>복제본 및 파티션 크기 조정

[**AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) 은 서비스 내에서 청구 가능한 리소스를 다시 조정 [복제본과 파티션을 늘리거나 줄이는](search-capacity-planning.md) 데 사용 됩니다. 복제본 또는 파티션을 증가 시키면 청구에 추가 되 고,이에 따라 고정 및 가변 요금이 청구 됩니다. 추가 처리 성능이 일시적으로 필요한 경우에는 워크 로드를 처리 하기 위해 복제본과 파티션을 늘릴 수 있습니다. 개요 포털 페이지의 모니터링 영역에는 쿼리 대기 시간, 초당 쿼리 수 및 제한 (현재 용량이 충분 한지 여부를 나타냄)에 대 한 타일이 있습니다.

높아지면를 추가 하거나 제거 하는 데 시간이 걸릴 수 있습니다. 용량에 대 한 조정 작업은 백그라운드에서 발생 하므로 기존 작업을 계속할 수 있습니다. 추가 구성은 필요 없이 들어오는 요청에 대해 준비 되는 즉시 추가 용량이 사용 됩니다. 

용량 제거는 방해가 될 수 있습니다. 삭제 된 요청을 방지 하기 위해 용량을 줄이기 전에 모든 인덱싱 및 인덱서 작업을 중지 하는 것이 좋습니다. 가능 하지 않은 경우 새 대상 수준에 도달할 때까지 한 번에 하나의 복제본과 파티션을 증분 방식으로 줄이는 것이 좋습니다.

명령을 제출한 후에는 중간에 종료할 수 있는 방법이 없습니다. 개수를 수정 하기 전에 명령이 완료 될 때까지 기다려야 합니다.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

결과는 다음 출력과 비슷합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>다음 단계

[인덱스](search-what-is-an-index.md)를 작성 하 고 포털, REST api 또는 .net SDK를 사용 하 여 [인덱스를 쿼리](search-query-overview.md) 합니다.

* [Azure Portal에서 Azure Cognitive Search 인덱스 만들기](search-create-index-portal.md)
* [다른 서비스에서 데이터를 로드 하는 인덱서 설정](search-indexer-overview.md)
* [Azure Portal에서 검색 탐색기를 사용 하 여 Azure Cognitive Search 인덱스 쿼리](search-explorer.md)
* [.NET에서 Azure Cognitive Search를 사용 하는 방법](search-howto-dotnet-sdk.md)