---
title: Azure PowerShell를 사용 하 여 Azure 전용 호스트 배포
description: Azure PowerShell를 사용 하 여 전용 호스트에 Vm을 배포 합니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 5cd82635f3aec2cca251e122aadf96f70d377c8a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190513"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Azure PowerShell를 사용 하 여 전용 호스트에 Vm 배포

이 문서에서는 Vm (가상 머신)을 호스트 하는 Azure [전용 호스트](dedicated-hosts.md) 를 만드는 방법을 안내 합니다. 

Azure PowerShell 버전 2.8.0 이상을 설치 했는지 확인 하 고 `Connect-AzAccount`사용 하 여의 Azure 계정에 로그인 했는지 확인 합니다. 

## <a name="limitations"></a>제한 사항

- 가상 머신 확장 집합은 현재 전용 호스트에서 지원 되지 않습니다.
- DSv3, ESv3 및 Fsv2 VM 시리즈는 다음과 같이 지원 됩니다. 

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹** 은 전용 호스트의 컬렉션을 나타내는 리소스입니다. 지역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가 합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다. 
- 여러 가용성 영역에 걸쳐 있습니다. 이 경우에는 사용 하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑되는 여러 장애 도메인에 걸쳐 있습니다. 
 
두 경우 모두 호스트 그룹의 장애 도메인 수를 제공 해야 합니다. 그룹의 장애 도메인을 확장 하지 않으려면 장애 도메인 수 1을 사용 합니다. 

가용성 영역 및 장애 도메인을 모두 사용 하도록 결정할 수도 있습니다. 이 예제에서는 2 개 장애 도메인을 사용 하 여 영역 1에 호스트 그룹을 만듭니다. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>호스트 만들기

이제 호스트 그룹에서 전용 호스트를 만들어 보겠습니다. 호스트의 이름 외에 호스트의 SKU를 제공 해야 합니다. 호스트 SKU는 지원 되는 VM 시리즈 뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.


호스트 Sku 및 가격 책정에 대 한 자세한 내용은 [Azure 전용 호스트 가격](https://aka.ms/ADHPricing)을 참조 하세요.

호스트 그룹에 대 한 장애 도메인 수를 설정 하는 경우 호스트에 대 한 장애 도메인을 지정 하 라는 메시지가 표시 됩니다. 이 예에서는 호스트의 장애 도메인을 1로 설정 합니다.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>VM 만들기

전용 호스트에서 가상 컴퓨터를 만듭니다. 

호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 머신을 만들 때 동일한 영역을 사용 해야 합니다. 이 예에서는 호스트 그룹이 영역 1에 있기 때문에 영역 1에서 VM을 만들어야 합니다.  


```powershell
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 리소스가 부족 한 호스트에서 가상 컴퓨터를 만드는 경우 가상 컴퓨터는 실패 상태로 생성 됩니다. 

## <a name="check-the-status-of-the-host"></a>호스트의 상태를 확인 합니다.

`-InstanceView` 매개 변수와 함께 [GetAzHost](/powershell/module/az.compute/get-azhost) 를 사용 하 여 호스트에 배포할 수 있는 가상 컴퓨터 수와 호스트 상태를 확인할 수 있습니다.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

출력은 다음과 유사합니다.

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="clean-up"></a>정리

가상 컴퓨터가 배포 되지 않은 경우에도 전용 호스트에 대 한 요금이 청구 됩니다. 비용을 절약 하기 위해 현재 사용 하지 않는 호스트를 모두 삭제 해야 합니다.  

호스트를 사용 하는 가상 컴퓨터가 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [New-azvm](/powershell/module/az.compute/remove-azvm)를 사용 하 여 vm을 삭제 합니다.

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Vm을 삭제 한 후 [AzHost](/powershell/module/az.compute/remove-azhost)를 사용 하 여 호스트를 삭제할 수 있습니다.

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

모든 호스트를 삭제 한 후에는 [AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)를 사용 하 여 호스트 그룹을 삭제할 수 있습니다. 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

[AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)를 사용 하 여 단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 Vm, 호스트 및 호스트 그룹을 포함 하 여 그룹에 생성 된 모든 리소스가 삭제 됩니다.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>다음 단계

- 영역에서 최대 복원 력을 위해 영역 및 장애 도메인을 모두 사용 하는 샘플 템플릿은 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에 있습니다.

- [Azure Portal](dedicated-hosts-portal.md)를 사용 하 여 전용 호스트를 배포할 수도 있습니다.
