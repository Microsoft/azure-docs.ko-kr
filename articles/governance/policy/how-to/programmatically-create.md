---
title: 프로그래밍 방식으로 정책 만들기
description: 이 문서에서는 Azure CLI, Azure PowerShell 및 REST API를 사용하여 Azure Policy에 대해 프로그래밍 방식으로 정책을 만들고 관리하는 방법을 설명합니다.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 9b0c2e50536a847555dfa5cc6b9c823cfc1a4cfa
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047058"
---
# <a name="programmatically-create-policies"></a>프로그래밍 방식으로 정책 만들기

이 문서는 프로그래밍 방식으로 정책을 만들고 관리하는 방법을 설명합니다. Azure Policy 정의는 리소스에 다양한 규칙과 효과를 적용합니다. 적용은 리소스가 회사 표준 및 서비스 수준 계약을 준수하는지 확인합니다.

규정 준수에 대한 내용은 [준수 데이터 가져오기](get-compliance-data.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 필수 조건을 충족시키는지 확인합니다.

1. [ARMClient](https://github.com/projectkudu/ARMClient)를 아직 설치하지 않은 경우 설치합니다. Azure Resource Manager 기반 API에 HTTP 요청을 전송하는 도구입니다.

1. Azure PowerShell 모듈을 최신 버전으로 업데이트합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 최신 버전에 대한 자세한 내용은 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 참조하세요.

1. 구독이 리소스 공급자와 함께 작동하도록 Azure PowerShell을 사용하여 Azure Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하려면 리소스 공급자에 대해 등록 작업을 실행할 수 있는 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하는 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../../../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

1. 아직 Azure CLI를 설치하지 않았다면 설치합니다. [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)에서 최신 버전을 얻을 수 있습니다.

## <a name="create-and-assign-a-policy-definition"></a>정책 정의 만들기 및 할당

리소스의 가시성을 향상시키는 첫 번째 단계는 리소스에 대한 정책을 만들어 할당하는 것입니다. 다음은 프로그래밍 방식으로 정책을 만들고 할당 하는 방법을 알아보는 단계입니다. 예제 정책은 PowerShell, Azure CLI 및 HTTP 요청을 사용하여 모든 공용 네트워크에 개방된 스토리지 계정을 감사합니다.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>PowerShell을 사용하여 정책 정의 만들기 및 할당

1. 다음 JSON 코드 조각을 사용하여 AuditStorageAccounts.json 이름의 JSON 파일을 만듭니다.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   정책 정의 작성에 대한 자세한 내용은 [Azure Policy 정의 구조](../concepts/definition-structure.md)를 참조합니다.

1. AuditStorageAccounts.json 파일을 사용하여 정책 정의를 만들려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   이 명령은 _공용 네트워크에 대해 열린 스토리지 계정 감사_라는 정책 정의를 작성합니다.
   사용할 수 있는 다른 매개 변수에 대한 자세한 내용은 [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)을 참조하세요.

   위치 매개 변수 없이 호출할 경우 `New-AzPolicyDefinition`은 기본적으로 선택한 세션 컨텍스트 구독에 정책 정의를 저장하도록 지정됩니다. 정의를 다른 위치에 저장하려면 다음 매개 변수를 사용합니다.

   - **SubscriptionId** - 다른 구독에 저장합니다. _GUID_ 값이 필요합니다.
   - **ManagementGroupName** - 관리 그룹에 저장합니다. _문자열_ 값이 필요합니다.

1. 정책 정의를 만든 후 다음 명령을 실행하여 정책 할당을 만들 수 있습니다.

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   _ContosoRG_를 원하는 리소스 그룹의 이름으로 바꿉니다.

   `New-AzPolicyAssignment`의 **Scope** 매개 변수는 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 사용하여 작동합니다. 이 매개 변수는 `Get-AzResourceGroup`의 **ResourceId** 속성이 반환하는 전체 리소스 경로를 사용합니다. 각 컨테이너에 대한 **Scope** 패턴은 다음과 같습니다. `{rName}`, `{rgName}`, `{subId}` 및 `{mgName}`을 각각 리소스 이름, 리소스 그룹 이름, 구독 ID 및 관리 그룹 이름으로 바꿉니다.
   `{rType}`은 리소스의 **리소스 종류**로 바꿉니다(예: VM에 대한 `Microsoft.Compute/virtualMachines`).

   - 리소스 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - 리소스 그룹 - `/subscriptions/{subId}/resourceGroups/{rgName}`
   - 구독 - `/subscriptions/{subId}/`
   - 관리 그룹 - `/providers/Microsoft.Management/managementGroups/{mgName}`

리소스 관리자 PowerShell 모듈을 사용 하 여 리소스 정책을 관리 하는 방법에 대 한 자세한 내용은 [Az. Resources](/powershell/module/az.resources/#policies)를 참조 하세요.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>ARMClient를 사용하여 정책 정의 만들기 및 할당

다음 절차에 따라 정책 정의를 만듭니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 파일을 만듭니다. 다음 단계에서 파일을 호출합니다.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. 다음 호출 중 하나를 사용하여 정책 정의 만들기:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   이전 {subscriptionId}를 구독의 ID로 또는 {managementGroupId}를 [관리 그룹](../../management-groups/overview.md)의 ID로 바꿉니다.

   쿼리 구조에 대한 자세한 내용은 [Azure Policy 정의 - 만들기 또는 업데이트](/rest/api/resources/policydefinitions/createorupdate) 및 [정책 정의 - 관리 그룹에서 만들기 또는 업데이트](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)를 참조하세요.

다음 절차를 사용하여 정책 할당을 만들고 리소스 그룹 수준에서 정책 정의를 할당합니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 정책 할당 파일을 만듭니다. &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. 다음 호출을 사용하여 정책 할당을 만듭니다.

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

   REST API에 HTTP 호출하는 방법에 대한 자세한 내용은 [Azure REST API 리소스](/rest/api/resources/)를 참조합니다.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Azure CLI를 사용해 정책 정의 만들기 및 할당

정책 정의를 만들려면 다음 절차를 사용합니다.

1. 다음 JSON 코드 조각을 복사하여 JSON 정책 할당 파일을 만듭니다.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   정책 정의 작성에 대한 자세한 내용은 [Azure Policy 정의 구조](../concepts/definition-structure.md)를 참조합니다.

1. 정책 정의를 만들려면 다음 명령을 실행합니다.

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   이 명령은 _공용 네트워크에 대해 열린 스토리지 계정 감사_라는 정책 정의를 작성합니다.
   사용할 수 있는 다른 매개 변수에 대한 자세한 내용은 [az policy definition create](/cli/azure/policy/definition#az-policy-definition-create)를 참조하세요.

   위치 매개 변수 없이 호출할 경우 `az policy definition creation`은 기본적으로 선택한 세션 컨텍스트 구독에 정책 정의를 저장하도록 지정됩니다. 정의를 다른 위치에 저장하려면 다음 매개 변수를 사용합니다.

   - **subscription** - 다른 구독에 저장합니다. 구독 ID에 대해 _GUID_ 값 또는 구독 이름에 대해 _문자열_ 값이 필요합니다.
   - **management-group** - 관리 그룹에 저장합니다. _문자열_ 값이 필요합니다.

1. 정책 할당을 만들려면 다음 명령을 사용합니다. &lt;&gt; 기호의 예제 정보를 자신의 고유 값으로 바꿉니다.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   `az policy assignment create`의 **scope** 매개 변수는 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 사용하여 작동합니다. 매개 변수는 전체 리소스 경로를 사용합니다. 각 컨테이너에 대한 **scope** 패턴은 다음과 같습니다. `{rName}`, `{rgName}`, `{subId}` 및 `{mgName}`을 각각 리소스 이름, 리소스 그룹 이름, 구독 ID 및 관리 그룹 이름으로 바꿉니다. `{rType}`은 리소스의 **리소스 종류**로 바꿉니다(예: VM에 대한 `Microsoft.Compute/virtualMachines`).

   - 리소스 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - 리소스 그룹 - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - 구독 - `/subscriptions/{subID}`
   - 관리 그룹 - `/providers/Microsoft.Management/managementGroups/{mgName}`

다음 명령으로 PowerShell을 사용하여 Azure Policy 정의 ID를 가져올 수 있습니다.

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

만든 정책 정의에 대한 정책 정의 ID는 다음 예제와 유사해야 합니다.

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Azure CLI를 사용하여 리소스 정책을 관리하는 방법에 대한 자세한 내용은 [Azure CLI 리소스 정책](/cli/azure/policy)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 제시된 명령 및 쿼리에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure REST API 리소스](/rest/api/resources/)
- [Azure PowerShell 모듈](/powershell/module/az.resources/#policies)
- [Azure CLI 정책 명령](/cli/azure/policy)
- [Azure Policy Insights 리소스 공급자 REST API 참조](/rest/api/policy-insights)
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md).