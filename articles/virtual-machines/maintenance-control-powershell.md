---
title: PowerShell을 사용 하 여 Azure 가상 머신에 대 한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용 하 여 Azure Vm에 유지 관리를 적용 하는 시기를 제어 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 7e4586a5fba91fbc7432aa352b9608be728e8654
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251943"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>미리 보기: 유지 관리 제어 및 Azure PowerShell를 사용 하 여 업데이트 제어

유지 관리 제어를 사용 하 여 다시 부팅이 필요 하지 않은 플랫폼 업데이트를 관리 합니다. Azure는 안정성, 성능, 보안을 개선 하거나 새로운 기능을 시작 하기 위해 인프라를 자주 업데이트 합니다. 대부분의 업데이트는 사용자에 게 투명 합니다. 게임, 미디어 스트리밍 및 금융 트랜잭션과 같은 일부 중요 한 워크 로드는 유지 관리를 위해 VM을 고정 하거나 연결을 끊는 경우에도 몇 초 정도 허용할 수 없습니다. 유지 관리 제어는 플랫폼 업데이트를 대기 하는 옵션을 제공 하 고 35 일의 롤링 창 내에서 적용 합니다. 

유지 관리 제어를 통해 격리 된 Vm에 업데이트를 적용할 시기를 결정할 수 있습니다.

유지 관리 제어를 사용 하 여 다음을 수행할 수 있습니다.
- 업데이트를 하나의 업데이트 패키지로 일괄 처리 합니다.
- 업데이트를 적용 하려면 최대 35 일 동안 기다립니다. 
- Azure Functions를 사용 하 여 유지 관리 기간에 대 한 플랫폼 업데이트를 자동화 합니다.
- 유지 관리 구성은 구독 및 리소스 그룹에 걸쳐 작동 합니다. 

> [!IMPORTANT]
> 유지 관리 제어는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 

## <a name="limitations"></a>제한 사항

- Vm은 [전용 호스트](./linux/dedicated-hosts.md)에 있거나 [격리 된 VM 크기](./linux/isolation.md)를 사용 하 여 만들어야 합니다.
- 35 일 후 업데이트가 자동으로 적용 됩니다.
- 사용자에 게 **리소스 참여자** 액세스 권한이 있어야 합니다.


## <a name="enable-the-powershell-module"></a>PowerShell 모듈 사용

`PowerShellGet` 최신 인지 확인 합니다.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az. Maintenance PowerShell cmdlet은 미리 보기 상태 이므로 Cloud Shell 또는 로컬 PowerShell 설치에서 `AllowPrerelease` 매개 변수를 사용 하 여 모듈을 설치 해야 합니다.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

로컬로 설치 하는 경우 관리자 권한으로 PowerShell 프롬프트를 열어야 합니다.

신뢰할 수 없는 *리포지토리에서*설치할지를 확인 하는 메시지가 표시 될 수도 있습니다. `Y`를 입력 하거나 **모두 예를** 선택 하 여 모듈을 설치 합니다.



## <a name="create-a-maintenance-configuration"></a>유지 관리 구성 만들기

구성에 대 한 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *myMaintenanceRG* 이라는 리소스 그룹을 *에서는 eastus*에 만듭니다. 사용 하려는 리소스 그룹이 이미 있는 경우이 부분을 건너뛰고 나머지 예에서는 리소스 그룹 이름을 자신의 이름으로 바꿀 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 를 사용 하 여 유지 관리 구성을 만듭니다. 이 예제에서는 호스트로 범위가 지정 된 *Myconfig* 라는 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

`-MaintenanceScope host`를 사용 하면 호스트에 대 한 업데이트를 제어 하는 데 유지 관리 구성이 사용 됩니다.

이름이 같은 구성을 만들려고 하지만 다른 위치에 있는 경우 오류가 발생 합니다. 구성 이름은 구독에 대해 고유 해야 합니다.

[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)를 사용 하 여 사용 가능한 유지 관리 구성에 대해 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>구성 할당

[AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) 를 사용 하 여 격리 된 VM 또는 Azure 전용 호스트에 구성을 할당 합니다.

### <a name="isolated-vm"></a>격리 된 VM

구성의 ID를 사용 하 여 VM에 구성을 적용 합니다. `-ResourceType VirtualMachines`를 지정 하 고 `-ResourceName`VM의 이름과 `-ResourceGroupName`VM의 리소스 그룹을 제공 합니다. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 구성을 적용 하려면 호스트 그룹의 이름과 함께 `-ResourceType hosts``-ResourceParentName`를 포함 하 고 `-ResourceParentType hostGroups`해야 합니다. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

[AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) 를 사용 하 여 보류 중인 업데이트가 있는지 확인 합니다. `-subscription`를 사용 하 여 로그인 한 VM과 다른 VM의 Azure 구독을 지정 합니다.

표시할 업데이트가 없는 경우이 명령은 아무것도 반환 하지 않습니다. 그렇지 않으면 PSApplyUpdate 개체를 반환 합니다.

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>격리 된 VM

격리 된 VM에 대 한 보류 중인 업데이트를 확인 합니다. 이 예제에서 출력은 가독성을 위해 테이블 형식으로 지정 됩니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트의 보류 중인 업데이트를 확인 합니다. 이 예제에서 출력은 가독성을 위해 테이블 형식으로 지정 됩니다. 리소스의 값을 고유한 값으로 바꿉니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>업데이트 적용

[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) 를 사용 하 여 보류 중인 업데이트를 적용 합니다.

### <a name="isolated-vm"></a>격리 된 VM

격리 된 VM에 업데이트를 적용 하는 요청을 만듭니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

성공 하면이 명령은 `PSApplyUpdate` 개체를 반환 합니다. `Get-AzApplyUpdate` 명령의 Name 특성을 사용 하 여 업데이트 상태를 확인할 수 있습니다. [업데이트 상태 확인](#check-update-status)을 참조 하세요.

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용 합니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>업데이트 상태 확인
[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) 를 사용 하 여 업데이트 상태를 확인 합니다. 아래에 표시 된 명령은 `-ApplyUpdateName` 매개 변수에 대 한 `default`를 사용 하 여 최신 업데이트의 상태를 보여 줍니다. 업데이트 이름 ( [AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) 명령에 의해 반환 됨)을 대체 하 여 특정 업데이트의 상태를 가져올 수 있습니다.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime은 자동 유지 관리 기간을 사용 하지 않는 경우 사용자 또는 플랫폼에 의해 시작 된 업데이트가 완료 된 시간입니다. 유지 관리 제어를 통해 업데이트를 적용 한 적이 없는 경우 기본값을 표시 합니다.

### <a name="isolated-vm"></a>격리 된 VM

특정 가상 컴퓨터에 대 한 업데이트를 확인 합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대 한 업데이트를 확인 합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>유지 관리 구성 제거

[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) 를 사용 하 여 유지 관리 구성을 삭제 합니다.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조 하세요.
