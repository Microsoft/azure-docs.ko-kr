---
title: 자습서 - Azure PowerShell을 사용하여 확장 집합용 디스크 만들기 및 사용
description: Azure PowerShell을 사용하여 디스크를 추가, 준비, 나열 및 분리하는 방법을 포함하여, 가상 머신 확장 집합이 있는 Managed Disks를 만들고 사용하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 0334b13fa73eb2fd648184f44bf0856c0d2a9ed9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076806"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합이 있는 디스크 만들기 및 사용

가상 머신 확장 집합은 디스크를 사용하여 VM 인스턴스의 운영 체제, 애플리케이션 및 데이터를 저장합니다. 확장 집합을 만들고 관리할 때 예상 작업에 적합한 디스크 크기와 구성을 선택해야 합니다. 이 자습서에서는 VM 디스크를 만들고 관리하는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>기본 Azure 디스크
확장 집합을 만들거나 크기를 조정하면 두 개의 디스크가 각 VM 인스턴스에 자동으로 연결됩니다. 

**운영 체제 디스크** - 운영 체제 디스크는 최대 2TB까지 크기를 지정할 수 있으며, VM 인스턴스의 운영 체제를 호스팅합니다. OS 디스크는 기본적으로 */dev/sda*로 레이블이 지정됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. 이 구성으로 인해 OS 디스크는 애플리케이션 또는 데이터를 호스트해서는 **안 됩니다**. 애플리케이션 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 문서의 뒷부분에서 자세히 설명합니다. 

**임시 디스크** - 임시 디스크는 VM 인스턴스와 동일한 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 이러한 디스크는 고성능 디스크이며, 임시 데이터 처리와 같은 작업에 사용할 수 있습니다. 그러나 VM 인스턴스가 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터가 제거됩니다. 임시 디스크의 크기는 VM 인스턴스 크기에 따라 결정됩니다. 임시 디스크는 */dev/sdb*로 레이블이 지정되고 탑재 지점은 */mnt*입니다.

### <a name="temporary-disk-sizes"></a>임시 디스크 크기
| Type | 일반적인 크기 | 최대 임시 디스크 크기(GiB) |
|----|----|----|
| [범용](../virtual-machines/sizes-general.md) | A, B 및 D 시리즈 | 1600 |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md) | F 시리즈 | 576 |
| [메모리에 최적화](../virtual-machines/sizes-memory.md) | D, E, G 및 M 시리즈 | 6144 |
| [Storage에 최적화](../virtual-machines/sizes-storage.md) | L 시리즈 | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N 시리즈 | 1440 |
| [고성능](../virtual-machines/sizes-hpc.md) | A 및 H 시리즈 | 2000 |


## <a name="azure-data-disks"></a>Azure 데이터 디스크
애플리케이션을 설치하고 데이터를 저장해야 하는 경우 추가 데이터 디스크를 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 스토리지가 필요한 상황에 사용해야 합니다. 각 데이터 디스크의 최대 용량은 4TB입니다. VM 인스턴스의 크기에 따라 연결할 수 있는 데이터 디스크 수가 결정됩니다. 각 VM vCPU에 대해 두 개의 데이터 디스크를 연결할 수 있습니다.

### <a name="max-data-disks-per-vm"></a>VM당 최대 데이터 디스크 수
| Type | 일반적인 크기 | VM당 최대 데이터 디스크 수 |
|----|----|----|
| [범용](../virtual-machines/sizes-general.md) | A, B 및 D 시리즈 | 64 |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md) | F 시리즈 | 64 |
| [메모리에 최적화](../virtual-machines/sizes-memory.md) | D, E, G 및 M 시리즈 | 64 |
| [Storage에 최적화](../virtual-machines/sizes-storage.md) | L 시리즈 | 64 |
| [GPU](../virtual-machines/sizes-gpu.md) | N 시리즈 | 64 |
| [고성능](../virtual-machines/sizes-hpc.md) | A 및 H 시리즈 | 64 |


## <a name="vm-disk-types"></a>VM 디스크 유형
Azure는 두 가지 유형의 디스크를 제공합니다.

### <a name="standard-disk"></a>표준 디스크
Standard Storage는 HDD에서 지원되며, 비용 효율적인 스토리지 및 성능을 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

### <a name="premium-disk"></a>프리미엄 디스크
프리미엄 디스크는 SSD 기반의 대기 시간이 짧은 고성능 디스크로 지원됩니다. 이러한 디스크는 프로덕션 작업을 실행하는 VM에 권장됩니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM을 지원합니다. 디스크 크기를 선택하면 값이 다음 형식으로 반올림됩니다. 예를 들어 디스크 크기가 128GB 미만인 경우 디스크 유형은 P10입니다. 디스크 크기가 129GB에서 512GB 사이이면 크기는 P20입니다. 512GB 초과이면 크기는 P30입니다.

### <a name="premium-disk-performance"></a>프리미엄 디스크 성능
|Premium Storage 디스크 유형 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 디스크 크기(반올림) | 32GB | 64GB | 128GB | 512 GB | 1,024GB(1TB) | 2,048GB(2TB) | 4,095GB(4TB) |
| 디스크당 최대 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
디스크당 처리량 | 25MB/초 | 50MB/초 | 100MB/초 | 150MB/초 | 200MB/s | 250MB/초 | 250MB/초 |

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 Standard_GS5 VM은 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [Windows VM 크기](../virtual-machines/sizes.md)를 참조하세요.


## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결
확장 집합을 만들 때 또는 기존 확장 집합을 사용하여 디스크를 만들고 연결할 수 있습니다.

### <a name="attach-disks-at-scale-set-creation"></a>확장 집합을 만들 때 디스크 연결
[New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 메시지가 표시되면 VM 인스턴스에 대한 사용자 이름과 암호를 제공합니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치에는 80 TCP 포트에서 트래픽을 분산할 뿐만 아니라 3389 TCP 포트의 원격 데스크톱 트래픽 및 5985 TCP 포트의 PowerShell 원격을 허용하는 규칙이 포함되어 있습니다.

`-DataDiskSizeGb` 매개 변수를 사용하여 두 개의 디스크를 만듭니다. 첫 번째 디스크의 크기는 *64*GB이고, 두 번째 디스크의 크기는 *128*GB입니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

확장 집합 리소스와 VM 인스턴스를 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

### <a name="attach-a-disk-to-existing-scale-set"></a>기존 확장 집합에 디스크 연결
기존 확장 집합에 디스크를 연결할 수도 있습니다. [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk)를 사용하여 다른 디스크를 추가하기 위해 이전 단계에서 만든 확장 집합을 사용합니다. 다음 예제에서는 *128*GB 디스크를 기존 확장 집합에 추가로 연결합니다.

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>데이터 디스크 준비
확장 집합 VM 인스턴스에 만들어지고 연결된 디스크는 원시 디스크입니다. 데이터 및 애플리케이션과 함께 사용하려면 먼저 디스크를 준비해야 합니다. 디스크를 준비하려면 파티션을 만들고, 파일 시스템을 만들고, 디스크를 탑재합니다.

확장 집합의 여러 VM 인스턴스에 걸쳐 프로세스를 자동화하려면 Azure 사용자 지정 스크립트 확장을 사용할 수 있습니다. 이 확장은 연결된 데이터 디스크를 준비하는 것과 같이 각 VM 인스턴스에서 스크립트를 로컬로 실행할 수 있습니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../virtual-machines/extensions/custom-script-windows.md)를 참조하세요.


다음 예제에서는 연결된 모든 원시 데이터 디스크를 준비하는 [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension)을 사용하여 각 VM 인스턴스에서 GitHub 샘플 리포지토리의 스크립트를 실행합니다.


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

디스크가 올바르게 준비되었는지 확인하려면 RDP를 VM 인스턴스 중 하나에 연결합니다. 

먼저 [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer)를 사용하여 부하 분산 장치 개체를 가져옵니다. 그런 다음, [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig)를 사용하여 인바운드 NAT 규칙을 봅니다. NAT 규칙은 RDP에서 수신 대기하는 각 VM 인스턴스에 대한 *FrontendPort*를 나열합니다. 마지막으로 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

VM에 연결하려면 앞의 명령과 같이 필요한 VM 인스턴스의 고유한 공용 IP 주소와 포트 번호를 지정합니다. 메시지가 표시되면 확장 집합을 만들 때 사용한 자격 증명을 입력합니다. Azure Cloud Shell을 사용하는 경우 로컬 PowerShell 프롬프트 또는 원격 데스크톱 클라이언트에서 이 단계를 수행합니다. 다음 예제에서는 *1* VM 인스턴스에 연결합니다.

```powershell
mstsc /v 52.168.121.216:50001
```

VM 인스턴스에서 로컬 PowerShell 세션을 열고 [Get-Disk](/powershell/module/storage/get-disk)를 사용하여 연결된 디스크를 확인합니다.

```powershell
Get-Disk
```

다음 예제 출력에서는 세 개의 데이터 디스크가 VM 인스턴스에 연결되었음을 보여 줍니다.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

다음과 같이 VM 인스턴스의 파일 시스템과 탑재 지점을 검사합니다.

```powershell
Get-Partition
```

다음 예제 출력에서는 세 개의 데이터 디스크에 드라이브 문자가 할당되었음을 보여 줍니다.

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

확장 집합의 각 VM 인스턴스에 있는 디스크는 동일한 방식으로 자동으로 준비됩니다. 확장 집합이 강화되면 필요한 데이터 디스크가 새 VM 인스턴스에 연결됩니다. 또한 사용자 지정 스크립트 확장도 실행되어 디스크를 자동으로 준비합니다.

VM 인스턴스와의 원격 데스크톱 연결 세션을 닫습니다.


## <a name="list-attached-disks"></a>연결된 디스크 나열
확장 집합에 연결된 디스크에 대한 정보를 보려면 다음과 같이 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)를 사용합니다.

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

*VirtualMachineProfile.StorageProfile* 속성 아래에 *DataDisks*의 목록이 표시됩니다. 디스크 크기, 스토리지 계층 및 LUN(논리 단위 번호)에 대한 정보가 표시됩니다. 다음 예제 출력에서는 확장 집합에 연결된 세 개의 데이터 디스크에 대해 자세히 설명합니다.

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>디스크 분리
지정된 디스크가 더 이상 필요하지 않은 경우 확장 집합에서 디스크를 분리할 수 있습니다. 확장 집합의 모든 VM 인스턴스에서 디스크가 제거됩니다. 확장 집합에서 디스크를 분리하려면 [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk)를 사용하고 디스크의 LUN을 지정합니다. LUN은 이전 섹션의 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 출력에 표시되어 있습니다. 다음 예제에서는 확장 집합에서 LUN *3*을 분리합니다.

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 디스크를 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 확장 집합이 있는 디스크를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

확장 집합 VM 인스턴스에 대해 사용자 지정 이미지를 사용하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합 VM 인스턴스용 사용자 지정 이미지 사용](tutorial-use-custom-image-powershell.md)
