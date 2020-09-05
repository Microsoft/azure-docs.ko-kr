---
title: '빠른 시작: PowerShell을 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 비규격 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548415"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만듭니다. 완료되면 _비준수_ 가상 머신을 식별하게 됩니다.

명령줄 또는 스크립트에서 Azure 리소스를 관리하는 데 Azure PowerShell 모듈이 사용됩니다.
이 가이드에서는 Az 모듈을 사용하여 정책 할당을 만드는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 시작하기 전에, 최신 버전의 Azure PowerShell을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

- Azure PowerShell을 사용하여 Azure Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자와 함께 작동하는지 확인할 수 있습니다. 리소스 공급자를 등록하려면 리소스 공급자 등록 작업을 수행할 수 있는 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하는 다음 명령을 실행합니다.

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 _관리 디스크가 없는 VM 감사_ 정의를 할당합니다. 이 정책 정의는 관리 디스크를 사용하지 않는 가상 머신을 식별합니다.

새 정책 할당을 만들려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

이 명령은 다음 정보를 사용합니다.

- **이름** - 할당의 실제 이름입니다. 이 예제에서는 _audit-vm-manageddisks_가 사용되었습니다.
- **표시 이름** - 정책 할당에 대한 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다. 
- **정의** – 할당을 만드는 데 기준으로 사용되는 정책 정의입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다. 
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다. 리소스 그룹의 이름으로 &lt;범위&gt;를 바꿉니다.

이제 비규격 리소스를 식별하여 환경의 규정 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

만든 정책 할당을 준수하지 않는 리소스를 식별하기 위해 다음 정보를 사용합니다. 다음 명령을 실행합니다.

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

정책 상태 가져오기에 대한 자세한 내용은 [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState)를 참조하세요.

결과는 다음 예제와 유사합니다.

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

결과는 Azure Portal 보기에서 정책 할당의 **리소스 규정 준수** 탭에 보이는 것과 일치합니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 명령을 사용합니다.

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)