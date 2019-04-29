---
title: Azure를 변환할 표준에서 프리미엄 또는 표준에는 프리미엄 디스크 저장소 관리 | Microsoft Docs
description: Azure CLI를 사용 하 여 프리미엄 또는 표준으로 프리미엄을 표준에서 디스크 저장소를 관리 하는 Azure를 변환 하는 방법.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2e7eb455a53abbe2df6ff72f091a599665732429
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765730"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure를 변환할 표준에서 프리미엄 또는 표준에는 프리미엄 디스크 저장소 관리

네 가지 [디스크 유형](disks-types.md) Azure에 대 한 관리 디스크: Ultra azure 디스크 저장소, 프리미엄 SSD, 표준 SSD 및 표준 HDD입니다. Premium SSD, 표준 SSD 및 가동 성능 요구 사항에 따라 표준 HDD 간에 쉽게 전환할 수 있습니다. 이 기능은 관리 되지 않는 디스크 또는 Ultra 디스크 저장소에 대 한 지원 되지 않습니다. 쉽게 수행할 수 있습니다 하지만 [managed disks로 변환할 관리 되지 않는](convert-unmanaged-to-managed-disks.md) 디스크 유형 간에 전환할 수 있습니다.

이 문서에서는 Azure CLI를 사용 하 여을 프리미엄 또는 표준으로 프리미엄 관리 디스크를 표준 변환 하는 방법을 보여 줍니다. 를 설치 하거나 업그레이드 도구 참조 [Azure CLI 설치](/cli/azure/install-azure-cli)합니다.

## <a name="before-you-begin"></a>시작하기 전에

* 디스크 변환에는 가상 머신 (VM)를 다시 시작 해야, 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약 하도록 합니다.
* 관리 되지 않는 디스크에 대 한 첫 번째 [managed disks로 변환](convert-unmanaged-to-managed-disks.md) 저장소 옵션 간에 전환할 수 있도록 합니다.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Premium 및 Standard 간 VM의 모든 관리 디스크를 전환 합니다.

이 예제에서는 Premium storage로 표준 또는 Premium에서 Standard storage에서 모든 VM의 디스크를 변환 하는 방법을 보여 줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한이 예제에서는 Premium storage를 지 원하는 크기로 전환 합니다.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard와 Premium 간에 개별 관리 되는 디스크를 전환 합니다.

개발/테스트 워크 로드에 대 한 비용을 줄이기 위해 표준 및 프리미엄 디스크의 혼합 하는 것이 좋습니다. 더 나은 성능이 필요로 하는 디스크에만 업그레이드를 선택할 수 있습니다. 이 예제에서는 Premium storage로 표준 또는 프리미엄 계층에서 표준 저장소 계층에서 단일 VM 디스크를 변환 하는 방법을 보여 줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한이 예제에서는 Premium storage를 지 원하는 크기로 전환 합니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>간의 표준 HDD 및 SSD 표준 관리 디스크를 전환 합니다.

이 예제에서는 표준 표준 SSD HDD 또는 표준 HDD로 SSD를 표준에서 단일 VM 디스크를 변환 하는 방법을 보여 줍니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Azure portal에서 Standard와 Premium 간에 관리 되는 디스크 교체

다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. VM 목록에서 선택 **가상 머신**합니다.
3. VM 중지 되지 않는 경우 선택할 **중지** VM의 맨 위에 있는 **개요** 창 및 vm이 중지 대기 합니다.
4. VM에 대 한 창에서 선택 **디스크** 합니다.
5. 변환 하려는 디스크를 선택 합니다.
6. 선택 **구성** 합니다.
7. 변경 된 **계정 유형** 에서 **표준 HDD** 에 **Premium SSD** 또는 **Premium SSD** 를 **표준 HDD**.
8. 선택 **저장할**, 디스크 창을 닫습니다.

디스크 유형 업데이트는 즉각적입니다. 변환 후 VM을 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

사용 하 여 VM의 읽기 전용 복사본을 만듭니다 [스냅숏을](snapshot-copy-managed-disk.md)합니다.
