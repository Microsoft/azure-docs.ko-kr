---
title: 연결되지 않은 Azure 관리/비관리 디스크를 찾아서 삭제 | Microsoft Docs
description: Azure PowerShell을 사용하여 연결되지 않은 Azure 관리/비관리(VHD/페이지 Blob) 디스크를 찾아서 삭제하는 방법입니다.
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
ms.openlocfilehash: f4cd419e06ba44b16d24f36e937002dce5196622
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763335"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>연결되지 않은 Azure 관리/비관리 디스크 찾기 및 삭제

Azure에서 VM(가상 머신)을 삭제할 때 기본적으로 VM에 연결된 디스크는 삭제되지 않습니다. 이 기능은 의도하지 않은 VM 삭제로 인한 데이터 손실을 방지하는 데 도움이 됩니다. VM을 삭제한 후에도 연결되지 않은 디스크에 대한 요금을 계속 지불합니다. 이 문서에서는 연결되지 않은 디스크를 찾아서 삭제하여 불필요한 비용을 줄이는 방법을 보여줍니다.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>관리 디스크: 연결되지 않은 디스크 찾기 및 삭제

다음 스크립트는 **ManagedBy** 속성 값을 검사하여 연결되지 않은 [관리 디스크](managed-disks-overview.md)를 찾습니다. 관리 디스크가 VM에 연결되어 있으면 **ManagedBy** 속성에 해당 VM의 리소스 ID가 포함됩니다. 관리 디스크가 연결되어 있지 않으면 **ManagedBy** 속성이 null입니다. 스크립트는 Azure 구독의 모든 관리 디스크를 검사합니다. 스크립트에서 **ManagedBy** 속성이 null로 설정된 관리 디스크를 찾을 경우 디스크가 연결되어 있지 않다고 결정합니다.

>[!IMPORTANT]
>먼저 **deleteUnattachedDisks** 변수를 0으로 설정하여 스크립트를 실행합니다. 이 작업을 통해 연결되지 않은 모든 관리 디스크를 찾아서 볼 수 있습니다.
>
>연결되지 않은 모든 디스크를 검토한 후 스크립트를 다시 실행하고 **deleteUnattachedDisks** 변수를 1로 설정합니다. 이 작업을 통해 연결되지 않은 모든 관리 디스크를 삭제할 수 있습니다.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>비관리 디스크: 연결되지 않은 디스크 찾기 및 삭제

비관리 디스크는 [Azure Storage 계정](../../storage/common/storage-create-storage-account.md)에 [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs)으로 저장된 VHD 파일입니다. 다음 스크립트는 **LeaseStatus** 속성 값을 검사하여 연결되지 않은 비관리 디스크(페이지 Blob)를 찾습니다. 비관리 디스크가 VM에 연결되어 있으면 **LeaseStatus** 속성이 **Locked**로 설정됩니다. 비관리 디스크가 연결되어 있지 않으면 **LeaseStatus** 속성이 **Unlocked**로 설정됩니다. 스크립트는 Azure 구독의 모든 Azure Storage 계정에 있는 모든 비관리 디스크를 검사합니다. 스크립트에서 **LeaseStatus** 속성이 **Unlocked**로 설정된 비관리 디스크를 찾을 경우 디스크가 연결되어 있지 않다고 결정합니다.

>[!IMPORTANT]
>먼저 **deleteUnattachedVHDs** 변수를 0으로 설정하여 스크립트를 실행합니다. 이 작업을 통해 연결되지 않은 모든 비관리 VHD를 찾아서 볼 수 있습니다.
>
>연결되지 않은 모든 디스크를 검토한 후 스크립트를 다시 실행하고 **deleteUnattachedVHDs** 변수를 1로 설정합니다. 이 작업을 통해 연결되지 않은 모든 비관리 VHD를 삭제할 수 있습니다.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [저장소 계정 삭제](../../storage/common/storage-create-storage-account.md) 및 [PowerShell을 사용하여 분리된 디스크 확인](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)을 참조하세요.
