---
title: Azure에서 PowerShell을 사용하여 Windows VM에 데이터 디스크 연결 | Microsoft Docs
description: Resource Manager 배포 모델에서 PowerShell을 사용하여 새롭거나 기존의 데이터 디스크를 Windows VM에 연결하는 방법입니다.
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
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1abc3fc18de3e9c1751c01c984e15ae44f25d5af
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766138"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>PowerShell을 사용하여 Windows VM에 데이터 디스크 연결

이 문서에서는 PowerShell을 사용하여 새 디스크와 기존 디스크를 Windows 가상 머신에 연결하는 방법을 보여 줍니다. 

먼저 다음 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 프리미엄 SSD를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 머신과 같은 [Premium Storage 지원 VM 유형](sizes-memory.md)이 필요합니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>가상 머신에 빈 데이터 디스크 추가

이 예에서는 기존 가상 머신에 빈 데이터 디스크를 추가하는 방법을 보여줍니다.

### <a name="using-managed-disks"></a>관리 디스크 사용

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>가용성 영역에서 Managed Disks 사용

가용성 영역에서 디스크를 만들려면 `-Zone` 매개 변수와 함께 [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig)를 사용합니다. 다음 예제에서는 영역 *1*에서 디스크를 만듭니다.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>디스크 초기화

빈 디스크가 추가되면 해당 디스크를 초기화해야 합니다. 디스크를 초기화하려면 VM에 로그인하여 디스크 관리를 사용합니다. VM을 만들 때 [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) 및 인증서를 사용하도록 설정한 경우 원격 PowerShell을 사용하여 디스크를 초기화할 수 있습니다. 또한 사용자 지정 스크립트 확장을 사용할 수 있습니다.

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

스크립트 파일에는 다음과 같이 디스크를 초기화하는 코드가 포함될 수 있습니다.

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>VM에 기존 데이터 디스크 추가

기존의 관리되는 디스크를 데이터 디스크로 VM에 연결할 수 있습니다.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 만듭니다.
