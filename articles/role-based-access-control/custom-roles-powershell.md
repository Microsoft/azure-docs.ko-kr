---
title: Azure PowerShell를 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할 만들기 또는 업데이트
description: Azure PowerShell를 사용 하 여 Azure 리소스에 대 한 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 지정 역할을 나열, 생성, 업데이트 또는 삭제 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 52057477fdba9757be2737c223d569b9e9a3e749
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137447"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할 만들기 또는 업데이트

[Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 사용자 지정 역할을 만들 수 있습니다. 이 문서에서는 Azure PowerShell를 사용 하 여 사용자 지정 역할을 나열, 생성, 업데이트 또는 삭제 하는 방법을 설명 합니다.

사용자 지정 역할을 만드는 방법에 대 한 단계별 자습서는 [자습서: Azure PowerShell 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)를 참조 하세요.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))
- [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

범위에서 할당에 사용할 수 있는 역할을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용합니다. 다음 예제에서는 선택한 구독에 할당할 수 있는 모든 역할을 나열합니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

다음 예제에서는 선택한 구독에 할당할 수 있는 사용자 지정 역할만을 나열합니다.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

선택한 구독이 역할의 `AssignableScopes`에 없는 경우 사용자 지정 역할은 나열되지 않습니다.

## <a name="list-a-custom-role-definition"></a>사용자 지정 역할 정의 나열

사용자 지정 역할 정의를 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)를 사용 합니다. 이는 기본 제공 역할에 대해를 사용 하는 것과 동일한 명령입니다.

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

다음 예에서는 역할의 작업만 나열 합니다.

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 명령을 사용합니다. 역할을 구조화하는 방법에는 `PSRoleDefinition` 개체를 사용하거나 JSON 템플릿을 사용하는 두 가지 방법이 있습니다. 

### <a name="get-operations-for-a-resource-provider"></a>리소스 공급자에 대한 작업 가져오기

사용자 지정 역할을 만드는 경우 리소스 공급자에서 가능한 모든 작업을 알고 있어야 합니다.
[리소스 공급 기업 작업](resource-provider-operations.md)의 목록을 보거나 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 명령을 사용하여 이 정보를 가져올 수 있습니다.
예를 들어 가상 머신에 사용 가능한 모든 작업을 확인하려는 경우 다음 명령을 사용합니다.

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition 개체를 사용하여 사용자 지정 역할 만들기

PowerShell을 사용하여 사용자 지정 역할을 만들 때는 [기본 제공 역할](built-in-roles.md) 중 하나를 출발점으로 사용하거나 처음부터 시작할 수 있습니다. 이 섹션의 첫 번째 예제에서는 기본 제공 역할로 시작한 다음, 추가 권한으로 사용자 지정합니다. 속성을 편집하여 원하는 `Actions`, `NotActions` 또는 `AssignableScopes`를 추가한 다음, 변경 내용을 새 역할로 저장합니다.

다음 예제에서는 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 기본 제공 역할로 시작하여 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 새 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 머신을 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 만드는 다른 방법을 보여줍니다. 먼저 새 `PSRoleDefinition` 개체를 만듭니다. 해당 작업은 `perms` 변수에서 지정되고 `Actions` 속성으로 설정됩니다. `NotActions` 속성은 `NotActions`Virtual Machine Contributor[ 기본 제공 역할에서 ](built-in-roles.md#virtual-machine-contributor)를 읽어 설정됩니다. [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor)는 `NotActions`가 없으므로 이 줄은 필요하지 않지만 다른 역할에서 정보를 검색할 수 있는 방법을 보여줍니다.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>JSON 템플릿을 사용하여 사용자 지정 역할 만들기

JSON 템플릿을 사용자 지정 역할의 원본 정의로 사용할 수 있습니다. 다음 예제에서는 스토리지 및 컴퓨팅 리소스에 대한 읽기 액세스, 지원 액세스를 허용하고 해당 역할을 두 개의 구독에 추가하는 사용자 지정 역할을 만듭니다. 다음 예제가 포함된 새 파일 `C:\CustomRoles\customrole1.json`을 만듭니다. 초기 역할 생성 시 새 ID가 자동 생성되므로 Id를 `null`로 설정해야 합니다. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

구독에 역할을 추가하려면 다음 PowerShell 명령을 실행합니다.

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 만들 때와 유사하게 `PSRoleDefinition` 개체 또는 JSON 템플릿을 사용하여 기존 사용자 지정 역할을 수정할 수 있습니다.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition 개체를 사용하여 사용자 지정 역할 업데이트

사용자 지정 역할을 수정하려면 먼저 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 명령을 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 명령을 사용하여 수정한 역할 정의를 저장합니다.

다음 예제에서는 `Microsoft.Insights/diagnosticSettings/*`Virtual Machine Operator*사용자 지정 역할에* 작업을 추가합니다.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할의 할당 가능한 범위에 Azure 구독을 추가합니다.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>JSON 템플릿을 사용하여 사용자 지정 역할 업데이트

이전 JSON 템플릿을 통해 기존 사용자 지정 역할을 손쉽게 수정하여 작업을 추가 또는 제거할 수 있습니다. 다음 예에 표시된 것처럼 JSON 템플릿을 업데이트하고 네트워킹에 대한 읽기 작업을 추가합니다. 템플릿에 나열된 정의는 기존 정의에 점증적으로 적용되지 않습니다. 즉, 템플릿에 지정한 것과 똑같이 역할이 표시됩니다. 또한 Id 필드를 역할의 ID로 업데이트해야 합니다. 이 값을 잘 모르는 경우 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) cmdlet을 사용하여 이 정보를 가져올 수 있습니다.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

기존 역할을 업데이트하려면 다음 PowerShell 명령을 실행합니다.

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) 명령을 사용합니다.

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 제거합니다.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure PowerShell을 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)
