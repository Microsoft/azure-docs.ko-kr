---
title: Azure PowerShell에서 Windows 문제 해결 VM 사용 | Microsoft Docs
description: Azure PowerShell을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Azure에서 Windows VM 문제를 해결하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 63d5e4bcc57a734fc5ea455da7c2db940a4b8ec3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654687"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Azure PowerShell을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결
Azure에서 Windows 가상 머신(VM)에 부팅 또는 디스크 오류가 발생하는 경우 디스크 자체에서 문제 해결 단계를 수행해야 할 수 있습니다. 일반적인 예로는 애플리케이션 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에서는 디스크를 다른 Windows VM에 연결하여 모든 오류를 수정한 다음, 원래 VM을 복구하기 위해 Azure PowerShell을 사용하는 방법을 자세히 설명합니다. 

> [!Important]
> 이 문서의 스크립트는 [관리 디스크](../managed-disks-overview.md)를 사용하는 VM에만 적용됩니다. 

 

## <a name="recovery-process-overview"></a>복구 프로세스 개요
이제 VM용 OS 디스크를 변경하려면 Azure PowerShell을 사용할 수 있습니다. 더 이상 VM을 삭제하고 다시 만들 필요가 없습니다.

문제 해결 프로세스는 다음과 같습니다.

1. 영향을 받는 VM을 중지합니다.
2. VM의 OS 디스크에서 스냅샷을 만듭니다.
3. OS 디스크 스냅샷에서 디스크를 만듭니다.
4. 복구 VM에 디스크를 데이터 디스크로 연결합니다.
5. 복구 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 복사된 OS 디스크에서 문제를 수정합니다.
6. 복구 VM에서 디스크를 탑재 해제하고 분리합니다.
7. 영향을 받는 VM용 OS 디스크를 변경합니다.

VM 복구 명령을 사용 하 여 1, 2, 3, 4, 6, 7 단계를 자동화할 수 있습니다. 자세한 설명서 및 지침은 [Azure 가상 컴퓨터 복구 명령을 사용 하 여 WINDOWS VM 복구](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.

먼저 [최신 Azure PowerShell](/powershell/azure/)을 설치하고 구독에 로그인했는지 확인합니다.

```powershell
Connect-AzAccount
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 

## <a name="determine-boot-issues"></a>부팅 문제 확인
Azure에서 VM의 스크린샷을 보고 부팅 문제를 해결할 수 있습니다. 이 스크린샷은 VM이 부팅되지 않는 이유를 확인하는 데 도움이 됩니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 Windows VM `myVM`에서 스크린샷을 가져옵니다.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

스크린샷을 검토하여 VM이 부팅되지 않는 원인을 확인합니다. 제공된 특정 오류 메시지 또는 오류 코드를 기록해 둡니다.

## <a name="stop-the-vm"></a>VM을 중지합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 중지합니다.

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

다음 단계를 처리하기 전에 VM에서 삭제가 완료될 때까지 기다립니다.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>VM의 OS 디스크에서 스냅샷을 만들기

다음 예제에서는 `myVM'이라는 VM의 OS 디스크에서 `mySnapshot` 이름으로 스냅샷을 만듭니다. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM에 연결할 수 없습니다. 다음 단계에서는 이 스냅샷에서 디스크를 만듭니다.

## <a name="create-a-disk-from-the-snapshot"></a>스냅샷에서 디스크 만들기

이 스크립트에서는 `mysnapshot`이라는 스냅샷에서 `newOSDisk` 이름으로 관리 디스크를 만듭니다.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
이제 원본 OS 디스크의 복사본이 마련됐습니다. 이 디스크를 다른 Windows VM에 탑재하여 문제를 해결합니다.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>디스크를 다른 Windows VM에 연결하여 문제 해결

이제 원본 OS 디스크의 복사본을 데이터 디스크로 VM에 연결합니다. 이 프로세스를 사용하면 구성 오류를 수정하거나 추가 애플리케이션 또는 디스크의 시스템 로그 파일을 검토할 수 있습니다. 다음 예제에서는 `RecoveryVM`이라는 VM에 `newOSDisk`라는 디스크를 연결합니다.

> [!NOTE]
> 디스크를 연결하려면 원본 OS 디스크의 복사본 및 복구 VM이 동일한 위치에 있어야 합니다.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>복구 VM에 연결 및 연결된 디스크의 문제 해결

1. 적절한 자격증명을 사용하여 사용자 복구 VM에 대한 RDP입니다. 다음 예에서는 `myResourceGroup`이라는 리소스 그룹에서 `RecoveryVM`라는 VM에 대한 RDP 연결 파일을 `C:\Users\ops\Documents`에 다운로드합니다

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. 데이터 디스크가 자동으로 감지되고 연결돼야 합니다. 다음과 같이 연결된 볼륨 목록을 보고 드라이브 문자를 확인합니다.

    ```powershell
    Get-Disk
    ```

    다음 출력 예에서는 디스크 **2**에 연결된 디스크를 보여줍니다. (`Get-Volume`을 사용하여 드라이브 문자를 볼 수도 있습니다.)

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

원본 OS 디스크의 복제본이 탑재되면 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.

## <a name="unmount-and-detach-original-os-disk"></a>원본 OS 디스크의 탑재 해제 및 분리
오류가 해결되면 사용자 복구 VM에서 기존 디스크를 탑재 해제하고 분리합니다. 디스크를 복구 VM에 연결하는 임대가 해제될 때까지 다른 VM을 통해 디스크를 사용할 수 없습니다.

1. RDP 세션의 복구 VM에서 데이터 디스크를 분리합니다. 이전 `Get-Disk` cmdlet의 디스크 번호가 필요합니다. 그런 다음 `Set-Disk`를 사용하여 디스크를 오프라인으로 설정합니다.

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    이제 `Get-Disk`를 다시 사용하여 디스크가 오프라인으로 설정되었는지 확인합니다. 다음 출력 예에서는 디스크가 오프라인으로 설정되었다는 것을 보여 줍니다.

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. RDP 세션을 종료합니다. Azure PowerShell 세션에서는 'RecoveryVM'이라는 VM에서 `newOSDisk`라는 디스크를 제거합니다.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>영향을 받는 VM용 OS 디스크 변경

Azure PowerShell을 사용하여 OS 디스크를 교환할 수 있습니다. VM을 삭제하고 다시 만들 필요가 없습니다.

이 예제에서는 `myVM`이라는 VM을 중지하고 `newOSDisk`라는 디스크를 새 OS 디스크로 할당합니다. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>부팅 진단 확인 및 사용하도록 설정

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVMDeployed`에서 진단 확장을 사용할 수 있습니다.

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 RDP 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Windows VM에서 애플리케이션 연결 문제 해결](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
