---
title: PowerShell을 사용 하 여 Azure Vm에 대 한 유지 관리 알림 받기
description: Azure에서 실행 되는 가상 머신에 대 한 유지 관리 알림을 확인 하 고 PowerShell을 사용 하 여 셀프 서비스 유지 관리를 시작 합니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: c23a06c502bd43b52cb35220b711084c7c8c1c7a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121845"
---
# <a name="handling-planned-maintenance-using-powershell"></a>PowerShell을 사용 하 여 계획 된 유지 관리 처리

**이 문서는 Linux 및 Windows를 실행 하는 가상 컴퓨터에 적용 됩니다.**

Azure Powershell을 사용 하 여 Vm이 [유지 관리](maintenance-notifications.md)되도록 예약 된 시기를 확인할 수 있습니다. 계획된 유지 관리 정보는 [ 매개 변수를 사용하는 경우 ](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)Get-AzVM`-status` cmdlet에서 확인할 수 있습니다.
  
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. VM에 영향을 미치는 유지 관리가 예약되지 않은 경우 cmdlet은 유지 관리 정보를 반환하지 않습니다. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

MaintenanceRedeployStatus의 다음과 같은 속성이 반환됩니다. 

| 값 | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 이번에 VM에서 유지 관리를 시작할 수 있는지 여부를 나타냅니다. |
| PreMaintenanceWindowStartTime         | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 시작 시간입니다. |
| PreMaintenanceWindowEndTime           | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 종료 시간입니다. |
| MaintenanceWindowStartTime            | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 시작 시간입니다. |
| MaintenanceWindowEndTime              | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 종료 시간입니다. |
| LastOperationResultCode               | VM에서 유지 관리를 시작하는 마지막 시도의 결과입니다. |



또한 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)을 사용하고 VM을 지정하지 않고 리소스 그룹의 모든 VM에 대한 유지 관리 상태를 가져올 수 있습니다.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

다음 PowerShell 예제에서는 구독 ID를 사용 하 고 유지 관리를 위해 예약 된 Vm의 목록을 반환 합니다.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell을 사용하여 VM에서 유지 관리 시작

이전 섹션의 함수 정보를 사용하여 **IsCustomerInitiatedMaintenanceAllowed**가 true로 설정되어 있는 경우 VM에서 유지 관리를 시작합니다.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>클래식 배포

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 PowerShell을 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

클래식 VM에서 유지 관리를 시작하려면 다음을 입력합니다.

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](maintenance-notifications-cli.md) 또는 [포털](maintenance-notifications-portal.md)을 사용 하 여 계획 된 유지 관리를 처리할 수도 있습니다.
