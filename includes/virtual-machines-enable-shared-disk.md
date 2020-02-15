---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4487beffd0e6a0643f4d3d88f5bb342f23eb848f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202256"
---
## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Azure 공유 디스크 배포

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 `maxShares` 새 속성을 사용 하 여 `>1`값을 정의 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> 모든 Vm에서 디스크가 분리 된 경우에만 `maxShares`의 값을 설정 하거나 변경할 수 있습니다. `maxShares`에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하세요.

다음 템플릿을 사용 하기 전에 `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`및 `[parameters('maxShares')]`를 사용자 고유의 값으로 바꿉니다.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Vm에서 Azure 공유 디스크 사용

`maxShares>1`를 사용 하 여 공유 디스크를 배포한 후에는 하나 이상의 Vm에 디스크를 탑재할 수 있습니다.

> [!IMPORTANT]
> 디스크를 공유 하는 모든 Vm은 동일한 [근접 배치 그룹](../articles/virtual-machines/windows/proximity-placement-groups.md)에 배포 되어야 합니다.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>지원 되는 SCSI PR 명령

공유 디스크를 클러스터의 Vm에 탑재 한 후에는 SCSI PR을 사용 하 여 쿼럼 및 디스크에 대 한 읽기/쓰기를 설정할 수 있습니다. Azure 공유 디스크를 사용 하는 경우 다음과 같은 PR 명령을 사용할 수 있습니다.

디스크와 상호 작용 하려면 영구 예약 작업 목록으로 시작 합니다.

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION 또는 PR_RELEASE_RESERVATION를 사용 하는 경우 다음의 영구 예약 형식 중 하나를 제공 합니다.

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION 또는 PR_RELEASE 예약을 사용 하는 경우에도 영구 예약 키를 제공 해야 합니다.


## <a name="next-steps"></a>다음 단계

공유 디스크를 사용해 보려는 경우 [미리 보기에 등록 하세요](https://aka.ms/shareddisksignup).