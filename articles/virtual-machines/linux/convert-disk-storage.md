---
title: Standard 및 premium SSD 간에 관리 디스크 저장소 변환
description: Azure CLI를 사용 하 여 Azure managed disks 저장소를 standard에서 premium 또는 premium으로 표준으로 변환 하는 방법입니다.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 10674d15a7515b01b0df6cf37bce89f153cb9b0b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870688"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure 관리 디스크 저장소를 표준에서 프리미엄 또는 프리미엄으로 표준으로 변환

Azure로 관리 되는 디스크에는 Azure ultra disks, 프리미엄 SSD, 표준 SSD 및 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구 사항에 따라 세 가지 GA 디스크 유형 (프리미엄 SSD, 표준 SSD 및 표준 HDD) 간을 전환할 수 있습니다. 아직 또는에서 ultra 디스크로 전환할 수 없습니다. 새 항목을 배포 해야 합니다.

이 기능은 관리 되지 않는 디스크에 대해 지원 되지 않습니다. 하지만 관리 [되지 않는 디스크를 관리 디스크로 쉽게 변환](convert-unmanaged-to-managed-disks.md) 하 여 디스크 유형 간에 전환할 수 있습니다.

이 문서에서는 Azure CLI를 사용 하 여 관리 디스크를 표준에서 프리미엄 또는 프리미엄으로 변환 하는 방법을 보여 줍니다. 도구를 설치 하거나 업그레이드 하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

* 디스크를 변환 하려면 VM (가상 컴퓨터)을 다시 시작 해야 하므로 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약 합니다.
* 관리 되지 않는 디스크의 경우 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md) 하 여 저장소 옵션 간을 전환할 수 있습니다.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>프리미엄 및 표준 간에 VM의 모든 관리 디스크 전환

이 예제에서는 모든 VM의 디스크를 표준에서 프리미엄 저장소로 또는 프리미엄에서 표준 저장소로 변환 하는 방법을 보여 줍니다. 프리미엄 managed disks를 사용 하려면 VM이 Premium storage를 지 원하는 [vm 크기](../sizes.md) 를 사용 해야 합니다. 또한이 예제에서는 Premium storage를 지 원하는 크기로 전환 합니다.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard와 Premium 간에 개별 관리 디스크 전환

개발/테스트 워크 로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합 하 여 사용할 수 있습니다. 더 나은 성능이 필요한 디스크만 업그레이드 하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준에서 프리미엄 저장소로 또는 프리미엄에서 표준 저장소로 변환 하는 방법을 보여 줍니다. 프리미엄 managed disks를 사용 하려면 VM이 Premium storage를 지 원하는 [vm 크기](../sizes.md) 를 사용 해야 합니다. 또한이 예제에서는 Premium storage를 지 원하는 크기로 전환 합니다.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>표준 HDD와 표준 SSD 간에 관리 디스크 전환

이 예제에서는 단일 VM 디스크를 표준 HDD에서 표준 SSD 또는 표준 SSD에서 표준 HDD로 변환 하는 방법을 보여 줍니다.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Azure Portal에서 Standard와 Premium 간에 관리 디스크 전환

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **가상 머신**목록에서 VM을 선택 합니다.
3. VM이 중지 되지 않은 경우 VM **개요** 창 맨 위에서 **중지** 를 선택 하 고 vm이 중지 될 때까지 기다립니다.
4. VM에 대 한 창의 메뉴에서 **디스크** 를 선택 합니다.
5. 변환 하려는 디스크를 선택 합니다.
6. 메뉴에서 **구성** 을 선택 합니다.
7. **계정 유형을** **표준 HDD** 에서 **프리미엄 SSD** 또는 **프리미엄 SSD** 에서 **표준 HDD**로 변경 합니다.
8. **저장**을 선택 하 고 디스크 창을 닫습니다.

디스크 형식의 업데이트가 즉각적입니다. 변환 후 VM을 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[스냅숏을](snapshot-copy-managed-disk.md)사용 하 여 VM의 읽기 전용 복사본을 만듭니다.