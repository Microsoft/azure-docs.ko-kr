---
title: Azure를 변환할 표준에서 프리미엄 또는 표준에는 프리미엄 디스크 저장소 관리 | Microsoft Docs
description: Azure PowerShell을 사용 하 여 프리미엄 또는 표준으로 프리미엄을 표준에서 디스크를 관리 하는 Azure를 변환 하는 방법.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd893c68f2e9cac713e09dd0bdafb7f277ae889
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766098"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>관리 디스크의 저장소 형식 업데이트

네 가지 방법으로 Azure에 대 한 관리 디스크: Ultra azure 디스크 저장소, 프리미엄 SSD, 표준 SSD 및 표준 HDD입니다. 가동 성능 요구 사항에 따라 이러한 저장소 유형 간에 전환할 수 있습니다. 이 기능은 관리 되지 않는 디스크에 대 한 지원 되지 않습니다. 쉽게 수행할 수 있습니다 하지만 [비관리 디스크를 관리 디스크로 변환](convert-unmanaged-to-managed-disks.md) 디스크 유형 간에 전환할 수 있습니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

* 변환에는 가상 머신 (VM)를 다시 시작 해야, 하므로 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약 해야 합니다.
* 먼저 디스크를 관리 되지 않는 경우 [관리 되는 디스크로 변환](convert-unmanaged-to-managed-disks.md) 저장소 옵션 간에 전환할 수 있도록 합니다.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Premium 및 Standard 간 VM의 모든 관리 디스크를 전환 합니다.

이 예제에서는 Premium storage로 표준 또는 Premium에서 Standard storage에서 모든 VM의 디스크를 변환 하는 방법을 보여 줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

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
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard와 Premium 간에 개별 관리 되는 디스크를 전환 합니다.

개발/테스트 워크 로드에 대 한 비용을 줄이기 위해 표준 및 프리미엄 디스크의 혼합이 좋습니다. 더 나은 성능이 필요로 하는 디스크에만 업그레이드를 선택할 수 있습니다. 이 예제에서는 Premium storage로 표준 또는 프리미엄 계층에서 표준 저장소 계층에서 단일 VM 디스크를 변환 하는 방법을 보여 줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 이 예제에는 Premium storage를 지 원하는 크기로 전환 하는 방법을 보여 줍니다.

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
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Azure portal에서 Premium으로 표준에서 관리 디스크 변환

다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. VM 목록에서 선택 **가상 머신** 포털에서 합니다.
3. VM 중지 되지 않는 경우 선택할 **중지** VM의 맨 위에 있는 **개요** 창 및 vm이 중지 대기 합니다.
3. VM에 대 한 창에서 선택 **디스크** 합니다.
4. 변환 하려는 디스크를 선택 합니다.
5. 선택 **구성** 합니다.
6. 변경 된 **계정 유형** 에서 **표준 HDD** 하 **Premium SSD**합니다.
7. 클릭 **저장할**, 디스크 창을 닫습니다.

디스크 형식을 변환 하 여 즉각적 이며 변환 후 VM을 다시 시작할 수 있습니다.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>간의 표준 HDD 및 SSD 표준 관리 디스크를 전환 합니다. 

이 예제에서는 표준 표준 SSD HDD 또는 표준 HDD로 SSD를 표준에서 단일 VM 디스크를 변환 하는 방법을 보여 줍니다.

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
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.
