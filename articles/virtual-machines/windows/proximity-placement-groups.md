---
title: 'PowerShell: 근접 배치 그룹 사용'
description: Azure PowerShell를 사용 하 여 근접 배치 그룹을 만들고 사용 하는 방법에 대해 알아봅니다.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: c1c144ac9db040bfac45ecc7838401ae09c9e2c4
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137982"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>PowerShell을 사용 하 여 근접 배치 그룹에 Vm 배포


가능한 한 가까운 시간 내에 Vm을 가져오기 위해 가장 낮은 대기 시간을 달성 하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포 해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까운 위치에 있는지 확인 하는 데 사용 되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구 하는 작업에 유용 합니다.


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용 하 여 근접 배치 그룹을 만듭니다. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용 하 여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>VM 만들기

[New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 를 사용 하 여 vm을 만들 때 근접 배치 그룹 ID를 참조 하도록 `-ProximityPlacementGroup $ppg.Id`를 사용 하 여 근접 배치 그룹에 vm을 만듭니다.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)를 사용 하 여 배치 그룹에서 VM을 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>근접 배치 그룹으로 기존 VM 이동

또한 근접 배치 그룹에 기존 VM을 추가할 수 있습니다. 먼저 VM의 할당을 취소 한 다음 VM을 업데이트 하 고 다시 시작 해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Restart-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>근접 배치 그룹에서 기존 VM 이동

근접 배치 그룹에서 VM을 제거 하려면 먼저 VM의 할당을 취소 한 다음 VM을 업데이트 하 고 다시 시작 해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroupId = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Restart-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>가용성 집합
또한 근접 배치 그룹에 가용성 집합을 만들 수 있습니다. [AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) cmdlet과 동일한 `-ProximityPlacementGroup` 매개 변수를 사용 하 여 가용성 집합을 만들고 가용성 집합에서 만든 모든 vm이 동일한 근접 배치 그룹에도 만들어집니다.

근접 배치 그룹에 기존 가용성 집합을 추가 하거나 제거 하려면 먼저 가용성 집합의 모든 Vm을 중지 해야 합니다. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>기존 가용성 집합을 근접 배치 그룹으로 이동

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>근접 배치 그룹에서 기존 가용성 집합을 이동 합니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>확장 집합

또한 근접 배치 그룹에 확장 집합을 만들 수 있습니다. [AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 와 동일한 `-ProximityPlacementGroup` 매개 변수를 사용 하 여 확장 집합을 만들고 모든 인스턴스가 동일한 근접 배치 그룹에 생성 됩니다.


근접 배치 그룹에 기존 확장 집합을 추가 하거나 제거 하려면 먼저 확장 집합을 중지 해야 합니다. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>기존 확장 집합을 근접 배치 그룹으로 이동

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Restart-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>근접 배치 그룹에서 기존 확장 집합을 이동 합니다.

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Restart-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](../linux/proximity-placement-groups.md) 를 사용 하 여 근접 배치 그룹을 만들 수도 있습니다.
