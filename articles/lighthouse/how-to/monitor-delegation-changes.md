---
title: 관리 테 넌 트의 위임 변경 내용 모니터링
description: 고객 테 넌 트의 위임 작업을 관리 하는 테 넌 트로 모니터링 하는 방법에 대해 알아봅니다.
ms.date: 08/18/2020
ms.topic: how-to
ms.openlocfilehash: 4d9d8b18634f94c355ea7fc0b5c125d631ec419c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589744"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>관리 테 넌 트의 위임 변경 내용 모니터링

서비스 공급자는 사용자 구독 또는 리소스 그룹이 [Azure Lighthouse](../overview.md)을 통해 테 넌 트에 위임 되거나 이전에 위임 된 리소스가 제거 되는 경우를 인식 하고자 할 수 있습니다.

테 넌 트 관리에서 [Azure 활동 로그](../../azure-monitor/platform/platform-logs-overview.md) 는 테 넌 트 수준에서 위임 작업을 추적 합니다. 이 기록 된 활동에는 모든 고객 테 넌 트에서 추가 되거나 제거 된 위임이 포함 됩니다.

이 항목에서는 모든 고객에 대 한 위임 작업을 테 넌 트에 모니터링 하는 데 필요한 사용 권한 및이를 위한 모범 사례에 대해 설명 합니다. 또한이 데이터를 쿼리하고 보고 하는 한 가지 방법을 보여 주는 예제 스크립트도 제공 합니다.

> [!IMPORTANT]
> 이러한 모든 단계는 고객 테 넌 트가 아닌 관리 테 넌 트에서 수행 해야 합니다.

## <a name="enable-access-to-tenant-level-data"></a>테 넌 트 수준 데이터에 대 한 액세스 사용

테 넌 트 수준 활동 로그 데이터에 액세스 하려면 계정에 루트 범위 (/)의 [모니터링 판독기](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 기본 제공 역할을 할당 해야 합니다. 이 할당은 강화 된 추가 액세스 권한이 있는 전역 관리자 역할이 있는 사용자가 수행 해야 합니다.

### <a name="elevate-access-for-a-global-administrator-account"></a>전역 관리자 계정에 대 한 액세스 권한 상승

루트 범위 (/)에서 역할을 할당 하려면 관리자 권한으로 전역 관리자 역할이 있어야 합니다. 이 승격 된 액세스는 역할 할당을 수행 해야 하는 경우에만 추가 하 고 완료 되 면 제거 해야 합니다.

권한 상승을 추가 및 제거 하는 방법에 대 한 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)을 참조 하세요.

액세스 권한을 상승 한 후에는 사용자 계정에 Azure의 루트 범위에 대 한 사용자 액세스 관리자 역할이 있습니다. 이 역할 할당을 통해 모든 리소스를 보고, 디렉터리의 모든 구독 또는 관리 그룹에 대 한 액세스 권한을 할당 하 고, 루트 범위에서 역할을 할당 하는 작업을 수행할 수 있습니다.

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>새 서비스 사용자 계정을 만들어 테 넌 트 수준 데이터에 액세스

액세스 권한을 상승 시킨 후에는 테 넌 트 수준 활동 로그 데이터를 쿼리할 수 있도록 계정에 적절 한 사용 권한을 할당할 수 있습니다. 이 계정에는 관리 테 넌 트의 루트 범위에서 할당 된 [모니터링 판독기](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 기본 제공 역할이 있어야 합니다.

> [!IMPORTANT]
> 루트 범위에서 역할 할당을 부여 하는 것은 테 넌 트의 모든 리소스에 동일한 권한이 적용 됨을 의미 합니다.

이는 광범위 한 액세스 이기 때문에 개별 사용자나 그룹이 아닌 서비스 주체 계정에이 역할을 할당 하는 것이 좋습니다.

 또한 다음과 같은 모범 사례를 따르는 것이 좋습니다.

- 다른 자동화에 사용 되는 기존 서비스 사용자에 게이 역할을 할당 하는 대신이 기능에만 사용할 [새 서비스 사용자 계정을 만듭니다](../../active-directory/develop/howto-create-service-principal-portal.md) .
- 이 서비스 주체에 게 위임 된 고객 리소스에 대 한 액세스 권한이 없어야 합니다.
- [인증서를 사용 하 여 인증](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in) 하 고 [Azure Key Vault에 안전](../../key-vault/general/best-practices.md)하 게 저장 합니다.
- 서비스 사용자를 대신 하 여 작업할 수 있는 액세스 권한이 있는 사용자를 제한 합니다.

다음 방법 중 하나를 사용 하 여 루트 범위를 할당 합니다.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>전역 관리자 계정에 대 한 관리자 권한 액세스 제거

서비스 주체 계정을 만들고 루트 범위에서 모니터링 독자 역할을 할당 한 후에는이 액세스 수준이 더 이상 필요 하지 않으므로 전역 관리자 계정에 대 한 [관리자 권한 액세스를 제거](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) 해야 합니다.

## <a name="query-the-activity-log"></a>활동 로그 쿼리

관리 테 넌 트의 루트 범위에 대 한 액세스를 모니터링 하는 새 서비스 사용자 계정을 만든 후 테 넌 트의 위임 작업을 쿼리하고 보고 하는 데 사용할 수 있습니다.

[이 Azure PowerShell 스크립트](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) 를 사용 하 여 추가 되거나 제거 된 위임 (또는 성공 하지 않은 시도)에 대해 지난 1 일간의 활동 및 보고서를 쿼리할 수 있습니다. [테 넌 트 활동 로그](/rest/api/monitor/TenantActivityLogs/List) 데이터를 쿼리 한 다음 추가 또는 제거 된 위임에 대해 보고 하는 다음 값을 생성 합니다.

- **DelegatedResourceId**: 위임 된 구독 또는 리소스 그룹의 ID입니다.
- **Customertenantid**: 고객 테 넌 트 ID
- **Customersubscriptionid**: 위임 된 구독 ID 이거나 위임 된 리소스 그룹을 포함 하는 구독 ID입니다.
- **CustomerDelegationStatus**: 위임 된 리소스의 상태 변경 (성공 또는 실패)
- **Eventtimestamp**: 위임 변경이 기록 된 날짜 및 시간입니다.

이 데이터를 쿼리할 때는 다음 사항을 염두에 두어야 합니다.

- 단일 배포에서 여러 리소스 그룹을 위임 하는 경우 각 리소스 그룹에 대해 별도의 항목이 반환 됩니다.
- 이전 위임에 대 한 변경 내용 (예: 권한 구조 업데이트)은 추가 된 위임으로 기록 됩니다.
- 위에서 언급 한 것 처럼이 테 넌 트 수준 데이터에 액세스 하려면 계정에 루트 범위 (/)에 모니터링 판독기 Azure 기본 제공 역할이 있어야 합니다.
- 사용자 고유의 워크플로 및 보고에서이 데이터를 사용할 수 있습니다. 예를 들어 [HTTP 데이터 수집기 API (공개 미리 보기)](../../azure-monitor/platform/data-collector-api.md) 를 사용 하 여 REST API 클라이언트에서 Azure Monitor 데이터를 로그 한 다음, [작업 그룹](../../azure-monitor/platform/action-groups.md) 을 사용 하 여 알림 또는 경고를 만들 수 있습니다.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만 [여러 테 넌 트를 관리](../concepts/enterprise.md) 하는 기업은 동일한 프로세스를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Lighthouse](../concepts/azure-delegated-resource-management.md)에 고객을 등록 하는 방법에 대해 알아봅니다.
- [Azure Monitor](../../azure-monitor/index.yml) 및 [Azure 활동 로그](../../azure-monitor/platform/platform-logs-overview.md)에 대해 알아봅니다.
