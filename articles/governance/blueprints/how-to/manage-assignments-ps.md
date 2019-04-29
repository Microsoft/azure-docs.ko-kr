---
title: PowerShell 사용 하 여 할당을 관리 하는 방법
description: 공식 Azure 청사진 PowerShell 모듈을 Az.Blueprint blueprint 할당을 관리 하는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682846"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell 사용 하 여 할당을 관리 하는 방법

Blueprint 할당을 사용 하 여 관리할 수 있습니다 합니다 **Az.Blueprint** Azure PowerShell 모듈. 모듈 가져오기, 만들기, 업데이트 및 할당을 제거를 지원 합니다. 또한 모듈 기존 blueprint 정의에 세부 정보를 가져올 수 있습니다. 이 문서에서는 모듈을 설치 및 사용을 시작 하는 방법을 설명 합니다.

## <a name="add-the-azblueprint-module"></a>Az.Blueprint 모듈 추가

Blueprint 할당을 관리 하는 Azure PowerShell을 사용 하려면 모듈을 추가 해야 합니다. [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell/)와 함께 로컬로 설치된 PowerShell로 이 모듈을 사용할 수 있습니다.

### <a name="base-requirements"></a>기본 요구 사항

Azure 청사진 모듈에는 다음 소프트웨어가 필요합니다.

- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
- PowerShellGet 2.0.1 이상 설치되거나 업데이트되지 않은 경우 [다음 지침](/powershell/gallery/installing-psget)을 따릅니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에 대 한 청사진 모듈이 **Az.Blueprint**합니다.

1. **관리자** PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 하는 경우 **Az.Accounts** 가 이미 설치는 것을 사용 하는 데 필요한 `-AllowClobber` 강제로 설치 하려면.

1. 모듈을 가져왔는지와 올바른 버전(0.1.0)인지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Blueprint 정의 가져옵니다.

종종 할당을 사용 하려면 먼저 blueprint 정의에 대 한 참조를 가져옵니다.
`Get-AzBlueprint` cmdlet은 하나 이상의 blueprint 정의 가져옵니다. Cmdlet을 사용 하 여 관리 그룹에서 blueprint 정의 가져올 수 있습니다 `-ManagementGroupId {mgId}` 또는 사용 하 여 구독 `-SubscriptionId {subId}`합니다. **이름** 매개 변수 blueprint 정의 가져옵니다. 하지만 함께 사용 해야 **ManagementGroupId** 하거나 **SubscriptionId**합니다. **버전** 사용할 수 있습니다 **이름을** blueprint 정의 사용 하는 반환 하는 방법에 대 한 더 명확히 지정할 수 있습니다. 대신 **버전**, 스위치 `-LatestPublished` grabs 가장 최근에 게시 된 버전입니다.

다음 예제에서는 `Get-AzBlueprint` 명명 된 blueprint 정의의 모든 버전을 가져오는 ' 101-청사진-정의-구독 ' 라고 하는 특정 구독에서 `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Blueprint 정의 여러 버전에 대 한 출력 예는 다음과 같습니다.

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

합니다 [매개 변수를 blueprint](../concepts/parameters.md#blueprint-parameters) 청사진에 정의 하도록 확장할 수 있습니다 자세한 정보를 제공 합니다.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Blueprint 할당 가져오기

Blueprint 할당을 이미 있는 경우 사용 하 여에 대 한 참조를 가져올 수 있습니다는 `Get-AzBlueprintAssignment` cmdlet. Cmdlet은 취합니다 **SubscriptionId** 하 고 **이름** 선택적 매개 변수로. 하는 경우 **SubscriptionId** 지정 하지 않으면 현재 구독 컨텍스트를 사용 합니다.

다음 예제에서는 `Get-AzBlueprintAssignment` 명명 된 ' 할당-잠금-리소스-그룹 '으로 표시 하는 특정 구독에서 단일 blueprint 할당을 가져오려면 `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Blueprint 할당에 대 한 출력 예는 다음과 같습니다.

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Blueprint 할당을 만들려면

Blueprint 할당 아직 존재 하지 않는 경우 사용 하 여 만들 수 있습니다는 `New-AzBlueprintAssignment` cmdlet. 이 cmdlet은 다음 매개 변수를 사용 합니다.

- **이름** [필수]
  - Blueprint 할당의 이름을 지정합니다.
  - 고유 해야 하며에 존재 하지 **SubscriptionId**
- **Blueprint** [필수]
  - 할당할 blueprint 정의 지정 합니다.
  - 사용 하 여 `Get-AzBlueprint` 참조 개체를 가져오려면
- **위치** [필수]
  - 시스템 할당 관리 되는 id 및 구독 배포 개체에 만들 지역을 지정합니다
- **구독** (선택 사항)
  - 할당에 배포 된 구독을 지정 합니다.
  - 지정 하지 않으면 기본값은 현재 구독 컨텍스트
- **잠금** (선택 사항)
  - 정의 된 [리소스 잠금 blueprint](../concepts/resource-locking.md) 배포 된 리소스에 사용할
  - 지원 되는 옵션: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - 지정 하지 않으면 기본값은 _없음_
- **SystemAssignedIdentity** (선택 사항)
  - 할당에 대 한 시스템에서 지정한 관리 되는 id를 만들고 리소스를 배포 하려면를 선택 합니다.
  - "Identity" 매개 변수 집합에 대 한 기본값
  - 함께 사용할 수 없습니다 **UserAssignedIdentity**
- **UserAssignedIdentity** (선택 사항)
  - 할당을 위해 사용 하 고 리소스를 배포 하는 사용자 할당 관리 되는 id를 지정 합니다.
  - "Identity" 매개 변수 집합의 일부
  - 함께 사용할 수 없습니다 **SystemAssignedIdentity**
- **매개 변수** (선택 사항)
  - A [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 설정에 대 한 키/값 쌍의 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters) blueprint 할당
  - 동적 매개 변수는에 대 한 기본 합니다 **defaultValue** 정의
  - 매개 변수가 제공 되지 않는 없는 경우 **defaultValue**, 옵션이 아닌 매개 변수

    > [!NOTE]
    > **매개 변수** secureStrings를 지원 하지 않습니다.

- **ResourceGroupParameter** (선택 사항)
  - A [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 리소스 그룹 아티팩트
  - 각 리소스 그룹 아티팩트 자리 표시자 동적으로 설정 하는 것에 대 한 키/값 쌍을 갖습니다 **이름을** 및/또는 **위치** 해당 리소스 그룹 아티팩트
  - 리소스 그룹 매개 변수 및 없음 제공 되지 않았습니다 **defaultValue**, 리소스 그룹 매개 변수를 선택적 없습니다.

다음 예제에서는 사용 하 여 인출 ' 내 blueprint' blueprint 정의 '1.1' 버전의 새 할당을 만듭니다 `Get-AzBlueprint`'westus2'를 관리 되는 id 및 할당 개체 위치를 설정, 사용 하 여 리소스를 잠급니다  _AllResourcesReadOnly_를 둘 다에 대 한 해시 테이블을 설정 하 고 **매개 변수** 하 고 **ResourceGroupParameter** 라고 하는 특정 구독에 `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Blueprint 할당을 만들기 위한 출력 예는 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Blueprint 할당을 업데이트 합니다.

경우에 따라 이미 만들어져 있는 blueprint 할당을 업데이트 해야 합니다. `Set-AzBlueprintAssignment` cmdlet이이 작업을 처리 합니다. Cmdlet은 대부분의 동일한 매개 변수를 사용 하는 `New-AzBlueprintAssignment` 업데이트할 할당에 설정 된 아무 것도 허용 하는 cmdlet가 않습니다. 이에 대 한 예외는 _이름을_를 _Blueprint_, 및 _SubscriptionId_합니다. 제공 된 값만 업데이트 됩니다.

Blueprint 할당을 업데이트 하는 경우 어떻게 되는지 이해를 참조 하세요 [할당을 업데이트 하는 것에 대 한 규칙](./update-existing-assignments.md#rules-for-updating-assignments)합니다.

- **이름** [필수]
  - 업데이트에 대 한 blueprint 할당의 이름을 지정 합니다.
  - 업데이트를 하지 할당을 변경 하려면 할당을 찾는 데 사용
- **Blueprint** [필수]
  - Blueprint 할당 blueprint 정의 지정합니다.
  - 사용 하 여 `Get-AzBlueprint` 참조 개체를 가져오려면
  - 업데이트를 하지 할당을 변경 하려면 할당을 찾는 데 사용
- **위치** (선택 사항)
  - 시스템 할당 관리 되는 id 및 구독 배포 개체에 만들 지역을 지정합니다
- **구독** (선택 사항)
  - 할당에 배포 된 구독을 지정 합니다.
  - 지정 하지 않으면 기본값은 현재 구독 컨텍스트
  - 업데이트를 하지 할당을 변경 하려면 할당을 찾는 데 사용
- **잠금** (선택 사항)
  - 정의 된 [리소스 잠금 blueprint](../concepts/resource-locking.md) 배포 된 리소스에 사용할
  - 지원 되는 옵션: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (선택 사항)
  - 할당에 대 한 시스템에서 지정한 관리 되는 id를 만들고 리소스를 배포 하려면를 선택 합니다.
  - "Identity" 매개 변수 집합에 대 한 기본값
  - 함께 사용할 수 없습니다 **UserAssignedIdentity**
- **UserAssignedIdentity** (선택 사항)
  - 할당을 위해 사용 하 고 리소스를 배포 하는 사용자 할당 관리 되는 id를 지정 합니다.
  - "Identity" 매개 변수 집합의 일부
  - 함께 사용할 수 없습니다 **SystemAssignedIdentity**
- **매개 변수** (선택 사항)
  - A [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 설정에 대 한 키/값 쌍의 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters) blueprint 할당
  - 동적 매개 변수는에 대 한 기본 합니다 **defaultValue** 정의
  - 매개 변수가 제공 되지 않는 없는 경우 **defaultValue**, 옵션이 아닌 매개 변수

    > [!NOTE]
    > **매개 변수** secureStrings를 지원 하지 않습니다.

- **ResourceGroupParameter** (선택 사항)
  - A [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 리소스 그룹 아티팩트
  - 각 리소스 그룹 아티팩트 자리 표시자 동적으로 설정 하는 것에 대 한 키/값 쌍을 갖습니다 **이름을** 및/또는 **위치** 해당 리소스 그룹 아티팩트
  - 리소스 그룹 매개 변수 및 없음 제공 되지 않았습니다 **defaultValue**, 리소스 그룹 매개 변수를 선택적 없습니다.

다음 예제에서는 업데이트 된 인출 ' 내 blueprint' blueprint 정의 '1.1' 버전의 할당 `Get-AzBlueprint` 잠금 모드를 변경 하 여:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Blueprint 할당을 만들기 위한 출력 예는 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Blueprint 할당을 제거 합니다.

Blueprint 할당을 제거할 수에 대 한 때를 `Remove-AzBlueprintAssignment` cmdlet이이 작업을 처리 합니다. Cmdlet에는 **이름** 또는 **InputObject** 지정할 blueprint 할당을 제거 합니다. **SubscriptionId** 됩니다 _필요한_ 모든 경우에 제공 해야 합니다.

다음 예제에서는 사용 하 여 기존 blueprint 할당을 인출 `Get-AzBlueprintAssignment` 로 표시 하는 특정 구독에서 제거 합니다 `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>종단 간 예제

모든 단계를 한 데 모아, 다음 예제에서는 blueprint 정의 가져옵니다, 한 다음, 업데이트를 만들고 라고 하는 특정 구독에 blueprint 할당을 제거 `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결