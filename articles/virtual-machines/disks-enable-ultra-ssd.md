---
title: Vm 용 Ultra disks-Azure managed disks
description: Azure Vm의 ultra disks에 대해 알아보기
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4c005bc49780edcb7f322455e37163e78d87619f
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852682"
---
# <a name="using-azure-ultra-disks"></a>Azure ultra disks 사용

이 문서에서는 ultra disk를 배포 하 고 사용 하는 방법을 설명 합니다. ultra disks에 대 한 개념 정보는 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md#ultra-disk)을 참조 하세요.

Azure ultra disks는 Azure IaaS Vm (가상 머신)에 대 한 높은 처리량, 높은 IOPS 및 일관 된 짧은 대기 시간 디스크 저장소를 제공 합니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. 울트라 디스크의 주요 장점 중 하나는 Vm을 다시 시작할 필요 없이 워크 로드와 함께 SSD의 성능을 동적으로 변경 하는 기능입니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다.

## <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM 크기 및 지역 가용성 확인

### <a name="vms-using-availability-zones"></a>가용성 영역을 사용 하는 Vm

Ultra disks를 활용 하려면 사용 중인 가용성 영역을 확인 해야 합니다. 모든 지역이 ultra disks를 사용 하는 모든 VM 크기를 지 원하는 것은 아닙니다. 영역, 영역 및 VM 크기가 ultra disks를 지원 하는지 확인 하려면 다음 명령 중 하나를 실행 하 여 **지역**, **vmsize**및 **구독** 값을 먼저 바꾸어야 합니다.

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

응답은 아래와 유사 합니다. 여기서 X는 선택한 지역에서 배포 하는 데 사용 하는 영역입니다. X는 1, 2 또는 3이 될 수 있습니다.

**영역** 값을 유지 하 고, 가용성 영역을 나타내며, Ultra disk를 배포 하기 위해이 값이 필요 합니다.

|ResourceType  |이름  |위치  |영역  |제한 사항  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 명령에서 응답이 없는 경우 선택한 VM 크기는 선택한 지역의 ultra disks에서 지원 되지 않습니다.

이제 배포할 영역을 알고 있으므로이 문서의 배포 단계에 따라 ultra 디스크가 연결 된 VM을 배포 하거나 기존 VM에 울트라 디스크를 연결 합니다.

### <a name="vms-with-no-redundancy-options"></a>중복 옵션이 없는 Vm

현재 미국 서 부에 배포 된 Ultra 디스크는 중복성 옵션 없이 배포 해야 합니다. 그러나 ultra disks를 지 원하는 모든 디스크 크기가이 영역에 있을 수는 없습니다. 미국 서 부에서 ultra disks를 지 원하는 항목을 확인 하기 위해 다음 코드 조각 중 하나를 사용할 수 있습니다. `vmSize`먼저 및 값을 바꾸어야 합니다 `subscription` .

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

응답은 다음 양식과 유사 하며, `UltraSSDAvailable   True` VM 크기가이 지역에서 ultra disks를 지원 하는지 여부를 나타냅니다.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요.

여러 개의 ultra disks를 사용 하 여 VM을 만들려면 샘플 [단일 ultra disks를 사용 하 여 Vm 만들기](https://aka.ms/ultradiskArmTemplate)를 참조 하세요.

사용자 고유의 템플릿을 사용 하려는 경우 및에 대 한 **apiVersion** `Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks` 이 (이상)로 설정 되어 있는지 확인 `2018-06-01` 합니다.

디스크 sku를 **UltraSSD_LRS**설정 하 고 디스크 용량, IOPS, 가용성 영역 및 처리량을 MBps 단위로 설정 하 여 울트라 디스크를 만듭니다.

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Azure Portal를 사용 하 여 ultra disk 배포

이 섹션에서는 데이터 디스크로 ultra 디스크를 사용 하는 가상 컴퓨터를 배포 하는 방법을 설명 합니다. 가상 컴퓨터를 배포 하는 방법을 잘 알고 있다고 가정 합니다. 그렇지 않은 경우 빠른 시작 [: Azure Portal에서 Windows 가상 컴퓨터 만들기](./windows/quick-create-portal.md)를 참조 하세요.

- [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 가상 머신 (VM) 배포로 이동 합니다.
- [지원 되는 VM 크기 및 지역을](#ga-scope-and-limitations)선택 해야 합니다.
- **가용성 옵션**에서 **가용성 영역** 을 선택 합니다.
- 선택한 항목을 선택 하 여 나머지 항목을 입력 합니다.
- **디스크**를 선택합니다.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 디스크 블레이드에서 **Ultra Disk 호환성 사용**에 대해 **예** 를 선택 합니다.
- **새 디스크 만들기 및 연결** 을 선택 하 여 지금 울트라 디스크를 연결 합니다.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- **새 디스크 만들기** 블레이드에서 이름을 입력 하 고 **크기 변경**을 선택 합니다.
- **계정 유형을** **Ultra Disk**로 변경 합니다.
- **사용자 지정 디스크 크기 (GiB)**, **디스크 IOPS**및 **디스크 처리량** 을 원하는 값으로 변경 합니다.
- 블레이드에서 **확인을** 선택 합니다.
- VM 배포를 계속 하면 다른 VM을 배포할 때와 동일 하 게 유지 됩니다.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Azure Portal를 사용 하 여 ultra disk 연결

또는 ultra disks를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 ultra disks를 사용할 수 있습니다. 기존 VM에서 ultra disks를 사용 하도록 설정 하 고 데이터 디스크에 연결 합니다.

- VM으로 이동 하 여 **디스크**를 선택 합니다.
- **편집**을 선택합니다.

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- **Ultra Disk 호환성 사용**에 대해 **예** 를 선택 합니다.

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- **저장**을 선택합니다.
- **데이터 디스크 추가** 를 선택한 다음 **이름** 드롭다운 목록에서 **디스크 만들기**를 선택 합니다.

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 새 디스크의 이름을 입력 한 다음 **크기 변경**을 선택 합니다.
- **계정 유형을** **Ultra Disk**로 변경 합니다.
- **사용자 지정 디스크 크기 (GiB)**, **디스크 IOPS**및 **디스크 처리량** 을 원하는 값으로 변경 합니다.
- **확인을** 선택 하 고 **만들기**를 선택 합니다.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 디스크의 블레이드에 반환 되 면 **저장**을 선택 합니다.

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Azure Portal를 사용 하 여 ultra disk 성능 조정

Ultra disks는 성능을 조정할 수 있는 고유한 기능을 제공 합니다. 디스크 자체의 Azure Portal에서 이러한 조정을 수행할 수 있습니다.

- VM으로 이동 하 여 **디스크**를 선택 합니다.
- 성능을 수정할 ultra disk를 선택 합니다.

![selecting-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- **구성** 을 선택한 다음 수정 합니다.
- **저장**을 선택합니다.

![configuring-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요.

울트라 디스크를 연결 하기 위해 ultra disks를 사용할 수 있는 VM을 만들어야 합니다.

**$Vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** 변수를 고유한 값으로 바꾸거나 설정 합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 설정 합니다. 그런 다음, 다음 CLI 명령을 실행 하 여 ultra enabled VM을 만듭니다.

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>기존 VM에서 ultra disk 호환성 사용

VM이 [GA 범위 및 제한](#ga-scope-and-limitations) 사항에 설명 된 요구 사항을 충족 하 고 [해당 계정에 대 한 적절 한 영역](#determine-vm-size-and-region-availability)에 있는 경우 vm에서 ultra disk 호환성을 사용 하도록 설정할 수 있습니다.

Ultra disk 호환성을 사용 하려면 VM을 중지 해야 합니다. VM을 중지 한 후에는 호환성을 사용 하도록 설정 하 고, 울트라 디스크를 연결한 다음, VM을 다시 시작할 수 있습니다.

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk 만들기

이제 ultra disks를 연결할 수 있는 VM이 있으므로 ultra 디스크를 만들어 연결할 수 있습니다.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>CLI를 사용 하 여 VM에 ultra disk 연결

또는 ultra disks를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 ultra disks를 사용할 수 있습니다.

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk의 성능 조정

울트라 디스크는 성능을 조정 하는 데 사용할 수 있는 고유한 기능을 제공 합니다. 다음 명령에서는이 기능을 사용 하는 방법을 보여 줍니다.

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요.

Ultra disks를 사용 하려면 ultra disks를 사용할 수 있는 VM을 만들어야 합니다. **$Resourcegroup** 및 **$vmName** 변수를 고유한 값으로 바꾸거나 설정 합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 설정 합니다. 그런 후에 다음 [new-azvm](/powershell/module/az.compute/new-azvm) 명령을 실행 하 여 ULTRA enabled VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>기존 VM에서 ultra disk 호환성 사용

VM이 [GA 범위 및 제한](#ga-scope-and-limitations) 사항에 설명 된 요구 사항을 충족 하 고 [해당 계정에 대 한 적절 한 영역](#determine-vm-size-and-region-availability)에 있는 경우 vm에서 ultra disk 호환성을 사용 하도록 설정할 수 있습니다.

Ultra disk 호환성을 사용 하려면 VM을 중지 해야 합니다. VM을 중지 한 후에는 호환성을 사용 하도록 설정 하 고, 울트라 디스크를 연결한 다음, VM을 다시 시작할 수 있습니다.

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk 만들기

이제 ultra disks를 사용할 수 있는 VM이 있으므로 ultra 디스크를 만들어 연결할 수 있습니다.

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>PowerShell을 사용 하 여 VM에 ultra disk 연결

또는 ultra disks를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 ultra disks를 사용할 수 있습니다.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk의 성능 조정

Ultra disks에는 성능을 조정할 수 있는 고유한 기능이 있습니다. 다음 명령은 디스크를 분리 하지 않고도 성능을 조정 하는 예제입니다.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
## <a name="next-steps"></a>다음 단계

[Azure Kubernetes 서비스 (미리 보기)에서 azure ultra Disks 사용](../aks/use-ultra-disks.md)을 참조 하세요.
