---
title: Azure managed disks에 대 한 공유 디스크 사용
description: 여러 Vm에서 공유할 수 있도록 공유 디스크를 사용 하 여 Azure 관리 디스크 구성
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/21/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ad70aa1d044649a0ca61060fff9880d4ef1e34c1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752022"
---
# <a name="enable-shared-disk"></a>공유 디스크 사용

이 문서에서는 Azure managed disks에 대 한 공유 디스크 기능을 사용 하도록 설정 하는 방법을 설명 합니다. Azure 공유 디스크는 여러 Vm (가상 컴퓨터)에 관리 디스크를 동시에 연결할 수 있도록 하는 Azure managed disks에 대 한 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 클러스터된 애플리케이션을 배포하거나 기존 클러스터된 애플리케이션을 Azure로 마이그레이션할 수 있습니다. 

공유 디스크가 사용 하도록 설정 된 관리 디스크에 대 한 개념 정보를 찾고 있는 경우 다음을 참조 하세요.

* Linux: [Azure 공유 디스크](linux/disks-shared.md)

* Windows: [Azure 공유 디스크](windows/disks-shared.md)

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

공유 디스크는 여러 운영 체제를 지원 합니다. 지원 되는 운영 체제에 대 한 개념 문서의 [Windows](windows/disks-shared.md#windows) 및 [Linux](linux/disks-shared.md#linux) 섹션을 참조 하세요.

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>공유 디스크 배포

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>프리미엄 SSD를 공유 디스크로 배포

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 새 속성을 사용 하 `maxShares` 고 1 보다 큰 값을 정의 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> `maxShares`모든 vm에서 디스크를 분리 하는 경우에만 값을 설정 하거나 변경할 수 있습니다. 에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하십시오 `maxShares` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

다음 템플릿을 사용 하기 전에,, `[parameters('dataDiskName')]` `[resourceGroup().location]` 및를 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` 사용자 고유의 값으로 바꿉니다.

[프리미엄 SSD 공유 디스크 템플릿](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>공유 디스크로 ultra disk 배포

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 `maxShares` 매개 변수를 1 보다 큰 값으로 변경 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> `maxShares`모든 vm에서 디스크를 분리 하는 경우에만 값을 설정 하거나 변경할 수 있습니다. 에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하십시오 `maxShares` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>지역 디스크 예

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>영역 디스크 예

이 예제는 가용성 영역 1에서 디스크를 만드는 것을 제외 하 고 이전 예제와 거의 동일 합니다.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>지역 디스크 예

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>영역 디스크 예

이 예제는 가용성 영역 1에서 디스크를 만드는 것을 제외 하 고 이전 예제와 거의 동일 합니다.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>지역 디스크 예

다음 템플릿을 사용 하기 전에,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` 및를 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 사용자 고유의 값으로 바꿉니다.

[지역 공유 ultra disks 템플릿](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>영역 디스크 예

다음 템플릿을 사용 하기 전에,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` 및를 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 사용자 고유의 값으로 바꿉니다.

[영역 공유 ultra disks 템플릿](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Vm에서 Azure 공유 디스크 사용

를 사용 하 여 공유 디스크를 배포한 후에 `maxShares>1` 는 하나 이상의 vm에 디스크를 탑재할 수 있습니다.

> [!NOTE]
> 울트라 디스크를 배포 하는 경우 필요한 요구 사항과 일치 하는지 확인 합니다. 자세한 내용은 ultra disk 문서의 [PowerShell](disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm-1) 또는 [CLI](disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm) 섹션을 참조 하세요.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

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

Azure Resource Manager 템플릿을 사용 하 여 디스크를 배포 하려는 경우 다음 샘플 템플릿을 사용할 수 있습니다.
- [프리미엄 SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [지역 ultra 디스크](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [영역 ultra 디스크](https://aka.ms/SharedUltraDiskARMtemplateZonal)