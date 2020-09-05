---
title: 규정 비준수 리소스 수정
description: 이 지침에서는 Azure Policy에서 정책을 준수하지 않는 리소스를 수정하는 과정을 안내합니다.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 1274b049d7ce19601968697b22da38f0eb2cb5ff
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958748"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure Policy를 사용하여 비준수 리소스 수정

**deployIfNotExists** 또는 **수정** 정책을 준수하지 않는 리소스는 **수정**을 통해 준수 상태로 설정할 수 있습니다. 관리 그룹, 구독, 리소스 그룹 또는 개별 리소스에 대 한 **할당과 같은 기존** 리소스에 대 한 할당 된 정책의 **수정 작업** 을 실행 하도록 Azure Policy를 지시 하 여 수정 작업을 수행 합니다. 이 문서에서는 Azure Policy를 통한 수정을 이해하고 수행하는 데 필요한 단계를 보여 줍니다.

## <a name="how-remediation-security-works"></a>수정 보안의 작동 방식

Azure Policy는 **deployIfNotExists** 정책 정의의 템플릿을 실행할 때 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 사용합니다.
Azure Policy는 각 할당용 관리 ID를 만듭니다. 단, 관리 ID를 부여할 역할 관련 세부 정보가 있어야 합니다. 관리 ID에서 역할이 누락된 경우 정책 또는 이니셔티브 할당 중에 이 오류가 표시됩니다. 포털 사용 시 Azure Policy는 할당이 시작되면 나열된 역할을 관리 ID에 자동으로 부여합니다. 관리 id의 _위치_ 는 Azure Policy 작업에 영향을 주지 않습니다.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="관리 ID - 역할 누락" border="false":::

> [!IMPORTANT]
> **deployIfNotExists** 또는 **수정**으로 수정된 리소스가 정책 할당의 범위를 벗어나거나, 템플릿이 정책 할당 범위를 벗어난 리소스의 속성에 액세스하는 경우에는 할당의 관리 ID에 [액세스 권한을 수동으로 부여](#manually-configure-the-managed-identity)해야 합니다. 이렇게 하지 않으면 수정 배포는 실패합니다.

## <a name="configure-policy-definition"></a>정책 정의 구성

첫 단계에서는 **deployIfNotExists** 또는 **수정**이 포함된 템플릿의 콘텐츠를 정상적으로 배포하기 위해 정책 정의에 포함되어 있어야 하는 역할을 정의합니다. 이렇게 하려면 **details** 속성 아래에 **roleDefinitionIds** 속성을 추가합니다. 이 속성은 환경의 역할과 일치하는 문자열 배열입니다. 전체 예제를 보려면 [deployIfNotExists 예제](../concepts/effects.md#deployifnotexists-example) 또는 [수정 예제](../concepts/effects.md#modify-examples)를 참조하세요.

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** 속성은 역할의 짧은 **roleName**을 가져오지 않으며 전체 리소스 식별자를 사용합니다. 환경의 ‘Contributor’ 역할 ID를 가져오려면 다음 코드를 사용합니다.

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>관리 ID 수동 구성

포털을 사용하여 할당을 만들 때 Azure Policy는 관리 ID를 생성하고 **roleDefinitionIds**에 정의된 역할을 부여합니다. 다음과 같은 상황에서는 관리 ID를 만들고 권한을 할당하는 단계를 수동으로 수행해야 합니다.

- Azure PowerShell 등의 SDK를 사용하는 경우
- 할당 범위 외부의 리소스를 템플릿이 수정하는 경우
- 할당 범위 외부의 리소스를 템플릿이 읽는 경우

### <a name="create-managed-identity-with-powershell"></a>PowerShell을 사용하여 관리 ID 만들기

정책 할당 중에 관리 ID를 만들려면 **Location**을 정의하고 **AssignIdentity**를 사용해야 합니다. 다음 예제에서는 기본 제공 정책 **SQL DB 투명 데이터 암호화 배포**의 정의를 가져오고 대상 리소스 그룹을 설정한 다음 할당을 만듭니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

이제 `$assignment` 변수에는 관리 ID의 보안 주체 ID와 정책 할당 생성 시에 반환된 표준 값이 포함되어 있습니다. `$assignment.Identity.PrincipalId`를 통해 이 변수에 액세스할 수 있습니다.

### <a name="grant-defined-roles-with-powershell"></a>PowerShell을 사용하여 정의된 역할 부여

Azure Active Directory를 통해 새 관리 ID 복제를 완료해야 필요한 역할을 해당 ID에 부여할 수 있습니다. 다음 예제에서는 복제가 완료된 후 **roleDefinitionIds**에 대해 `$policyDef`에서 정책 정의를 반복하며, [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 새 관리 ID에 역할을 부여합니다.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>포털을 통해 정의된 역할 부여

포털을 사용하여 할당의 관리 ID에 정의된 역할을 부여하는 방법에는 두 가지가 있습니다. 그중 하나는 **액세스 제어(IAM)** 를 사용하는 것이고, 다른 하나는 정책 또는 이니셔티브 할당을 편집한 후에 **저장**을 클릭하는 것입니다.

할당의 관리 ID에 역할을 추가하려면 다음 단계를 수행합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. Azure Policy 페이지의 왼쪽에서 **할당**을 선택합니다.

1. 관리 ID가 있는 할당을 찾아서 해당 이름을 클릭합니다.

1. 편집 페이지에서 **할당 ID** 속성을 찾습니다. 할당 ID는 다음과 같이 표시됩니다.

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   관리 ID의 이름은 할당 리소스 ID의 끝부분(이 예제에서는 `2802056bfc094dfb95d4d7a5`)입니다. 할당 리소스 ID의 이 부분을 복사합니다.

1. 역할 정의를 수동으로 추가해야 하는 리소스 또는 리소스 상위 컨테이너(리소스 그룹, 구독, 관리 그룹)로 이동합니다.

1. 리소스 페이지에서 **액세스 제어(IAM)** 링크를 클릭한 다음 액세스 제어 페이지 위쪽의 **+ 역할 할당 추가**를 클릭합니다.

1. 정책 정의에서 **roleDefinitionIds**와 일치하는 적절한 역할을 선택합니다.
   **다음에 대한 액세스 할당:** 은 기본값인 ‘Azure AD 사용자, 그룹 또는 애플리케이션’으로 설정해 둡니다. **선택** 상자에 앞에서 찾은 할당 리소스 ID 부분을 붙여넣거나 입력합니다. 검색이 완료되면 이름이 같은 개체를 클릭하여 ID를 선택하고 **저장**을 클릭합니다.

## <a name="create-a-remediation-task"></a>수정 작업 만들기

### <a name="create-a-remediation-task-through-portal"></a>포털을 통해 수정 작업 만들기

평가 중에는 **deployIfNotExists** 또는 **수정** 효과가 포함된 정책 할당이 비준수 리소스가 있는지를 확인합니다. 비준수 리소스가 있으면 **수정** 페이지에서 세부 정보가 제공됩니다. 비준수 리소스가 있는 정책 목록을 통해 **수정 작업**이 트리거됩니다. 이 옵션을 선택하면 **deployIfNotExists** 템플릿 또는 **수정** 작업에서 배포가 작성됩니다.

**수정 작업**을 만들려면 다음 작업을 수행합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="모든 서비스에서 정책 검색" border="false":::

1. Azure Policy 페이지의 왼쪽에서 **수정**을 선택합니다.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Policy 페이지의 수정 선택" border="false":::

1. 비준수 리소스가 있는 모든 **deployIfNotExists** 및 **수정** 정책 할당이 **수정할 정책** 탭과 데이터 테이블에 포함됩니다. 비준수 리소스가 있는 정책을 클릭합니다. **새 수정 작업** 페이지가 열립니다.

   > [!NOTE]
   > **규정 준수** 페이지에서 정책을 찾아서 클릭한 다음 **수정 작업 만들기** 단추를 클릭하여 **수정 작업** 페이지를 열 수도 있습니다.

1. **새 수정 작업** 페이지에서 **범위** 줄임표를 사용해 수정할 리소스를 필터링하여 정책이 할당된 하위 리소스를 선택합니다. 개별 리소스 개체까지 포함하여 선택해야 합니다. 또한 **위치** 드롭다운을 사용하여 리소스를 추가로 필터링합니다. 테이블에 나열된 리소스만 수정됩니다.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="수정 - 수정할 리소스 선택" border="false":::

1. 리소스를 필터링한 후에 **수정**을 클릭하여 수정 작업을 시작합니다. 정책 준수 페이지에서 **수정 작업** 탭이 열리고 작업 진행 상태가 표시됩니다. 수정 작업으로 만든 배포가 바로 시작됩니다.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="수정 - 수정 작업의 진행 상황" border="false":::

1. 정책 준수 페이지에서 **수정 작업**을 클릭하여 진행 상황 관련 세부 정보를 확인합니다. 작업에 사용된 필터링과 수정 중인 리소스 목록이 표시됩니다.

1. **수정 작업** 페이지에서 리소스를 마우스 오른쪽 단추로 클릭하여 수정 작업의 배포 또는 리소스를 확인합니다. 행 끝부분의 **관련 이벤트**를 클릭하여 오류 메시지와 같은 세부 정보를 확인합니다.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="수정 - 리소스 작업 상황에 맞는 메뉴" border="false":::

**수정 작업**을 통해 배포한 리소스는 정책 준수 페이지의 **배포된 리소스** 탭에 추가됩니다.

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLI를 통해 수정 작업 만들기

Azure CLI를 사용하여 **수정 작업**을 만들려면 `az policy remediation` 명령을 사용합니다. `{subscriptionId}`를 구독 ID로 바꾸고 `{myAssignmentId}`를 **deployIfNotExists** 또는 **수정** 정책 할당 ID로 바꿉니다.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

다른 수정 명령과 예제는 [az policy remediation](/cli/azure/policy/remediation) 명령을 참조하세요.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell을 통해 수정 작업 만들기

Azure PowerShell을 사용하여 **수정 작업**을 만들려면 `Start-AzPolicyRemediation` 명령을 사용합니다. `{subscriptionId}`를 구독 ID로 바꾸고 `{myAssignmentId}`를 **deployIfNotExists** 또는 **수정** 정책 할당 ID로 바꿉니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

다른 수정 cmdlet 및 예제는 [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) 모듈을 참조하세요.

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Azure Portal에서 정책 할당 중에 수정 작업 만들기

수정 작업을 만드는 간소화된 방법은 정책 할당 중에 Azure Portal에서 수행하는 것입니다. 할당할 정책 정의가 **Deployifnotexists** 또는 수정 효과 인 경우 수정 **탭의** 마법사는 **수정** _작업 만들기_ 옵션을 제공 합니다. 이 옵션을 선택 하면 정책 할당과 동시에 수정 작업이 생성 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](get-compliance-data.md) 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
