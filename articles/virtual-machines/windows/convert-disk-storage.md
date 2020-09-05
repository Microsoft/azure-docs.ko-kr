---
title: Standard 및 premium SSD 간에 관리 디스크 저장소 변환
description: Azure PowerShell를 사용 하 여 Azure managed disks를 Standard에서 Premium 또는 Premium으로 변환 하는 방법을 설명 합니다.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d81cd0ac57a5a18d90144584e8705cbffcba6f9b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871431"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>관리 디스크의 스토리지 형식 업데이트

Azure로 관리 되는 디스크에는 Azure ultra disks, 프리미엄 SSD, 표준 SSD 및 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구 사항에 따라 세 가지 GA 디스크 유형 (프리미엄 SSD, 표준 SSD 및 표준 HDD) 간을 전환할 수 있습니다. 아직 또는에서 ultra 디스크로 전환할 수 없습니다. 새 항목을 배포 해야 합니다.

이 기능은 관리 되지 않는 디스크에 대해 지원 되지 않습니다. 하지만 관리 [되지 않는 디스크를 관리 디스크로 쉽게 변환](convert-unmanaged-to-managed-disks.md) 하 여 디스크 유형 간에 전환할 수 있습니다.

 

## <a name="prerequisites"></a>전제 조건

* 변환 하려면 VM (가상 컴퓨터)을 다시 시작 해야 하므로 기존 유지 관리 기간 동안에는 디스크 저장소의 마이그레이션을 예약 해야 합니다.
* 디스크가 관리 되지 않는 경우 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md) 하 여 저장소 옵션 간을 전환할 수 있습니다.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>프리미엄 및 표준 간에 VM의 모든 관리 디스크 전환

이 예제에서는 모든 VM의 디스크를 표준에서 프리미엄 저장소로 또는 프리미엄에서 표준 저장소로 변환 하는 방법을 보여 줍니다. 프리미엄 managed disks를 사용 하려면 VM이 Premium storage를 지 원하는 [vm 크기](../sizes.md) 를 사용 해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard와 Premium 간에 개별 관리 디스크 전환

개발/테스트 워크 로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합 하 여 사용할 수 있습니다. 더 나은 성능이 필요한 디스크만 업그레이드 하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준에서 프리미엄 저장소로 또는 프리미엄에서 표준 저장소로 변환 하는 방법을 보여 줍니다. 프리미엄 managed disks를 사용 하려면 VM이 Premium storage를 지 원하는 [vm 크기](../sizes.md) 를 사용 해야 합니다. 또한이 예제에서는 Premium storage를 지 원하는 크기로 전환 하는 방법을 보여 줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Azure Portal에서 관리 되는 디스크를 표준에서 프리미엄으로 변환

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 **가상 머신** 목록에서 VM을 선택 합니다.
3. VM이 중지 되지 않은 경우 VM **개요** 창 맨 위에서 **중지** 를 선택 하 고 vm이 중지 될 때까지 기다립니다.
3. VM에 대 한 창의 메뉴에서 **디스크** 를 선택 합니다.
4. 변환 하려는 디스크를 선택 합니다.
5. 메뉴에서 **구성** 을 선택 합니다.
6. **계정 유형을** **표준 HDD** 에서 **프리미엄 SSD**로 변경 합니다.
7. **저장**을 클릭 하 고 디스크 창을 닫습니다.

디스크 유형 변환이 즉각적입니다. 변환 후 VM을 시작할 수 있습니다.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>표준 HDD와 표준 SSD 간에 관리 디스크 전환 

이 예제에서는 단일 VM 디스크를 표준 HDD에서 표준 SSD 또는 표준 SSD에서 표준 HDD로 변환 하는 방법을 보여 줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>다음 단계

[스냅샷](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.