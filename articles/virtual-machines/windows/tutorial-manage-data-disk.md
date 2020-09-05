---
title: 자습서- Azure PowerShell을 사용하여 Azure 디스크 관리
description: 이 자습서에서는 Azure PowerShell은 사용하여 가상 머신용 Azure 디스크를 만들고 관리하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 528fe5dea533faf9447e03dd901568d783891ce9
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718937"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>자습서- Azure PowerShell을 사용하여 Azure 디스크 관리

Azure 가상 머신은 디스크를 사용하여 VM 운영 체제, 애플리케이션 및 데이터를 저장합니다. VM을 만들 때 예상되는 워크로드에 적합한 디스크 크기와 구성을 선택하는 것이 중요합니다. 이 자습서에서는 VM 디스크의 배포 및 관리에 대해 다룹니다. 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="default-azure-disks"></a>기본 Azure 디스크

Azure Virtual Machine을 만들면 두 개의 디스크가 자동으로 가상 머신에 연결됩니다. 

**운영 체제 디스크** - 운영 체제 디스크는 최대 4TB까지 가능하며 VM 운영 체제를 호스트합니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/) 이미지에서 새 VM(가상 머신)을 만드는 경우 일반적으로 127GB(하지만 일부 이미지는 더 작은 OS 디스크 크기임)입니다. OS 디스크는 기본적으로 *C:* 드라이브 문자가 할당됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. OS 디스크는 애플리케이션 또는 데이터를 호스트해서는 **안 됩니다**. 애플리케이션 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 문서의 뒷부분에서 자세히 설명합니다.

**임시 디스크** - 임시 디스크는 VM과 같은 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 임시 디스크는 성능이 높고 임시 데이터 처리 등의 작업에 사용할 수 있습니다. 그러나 VM이 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터는 제거됩니다. 임시 디스크의 크기는 [VM 크기](../sizes.md)에 따라 결정됩니다. 임시 디스크는 기본적으로 *D:* 드라이브 문자가 할당됩니다.

## <a name="azure-data-disks"></a>Azure 데이터 디스크

애플리케이션을 설치하고 데이터를 저장하기 위해 데이터 디스크를 더 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 스토리지가 필요한 경우에 사용해야 합니다. 가상 컴퓨터의 크기에 따라 VM에 연결할 수 있는 데이터 디스크 수가 결정됩니다.

## <a name="vm-disk-types"></a>VM 디스크 유형

Azure는 두 가지 형식의 디스크를 제공합니다.

**표준 디스크**는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 스토리지를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

**프리미엄 디스크** - SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. [크기 이름](../vm-naming-conventions.md)에 **S**가 있는 VM 크기는 일반적으로 Premium Storage를 지원합니다. 예를 들어 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM은 Premium Storage를 지원합니다. 디스크 크기를 선택하면 값이 다음 형식으로 반올림됩니다. 예를 들어 디스크 크기가 64GB 이상 128GB 미만인 경우 디스크 유형은 P10입니다. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Premium Storage 디스크를 프로비전하면 표준 스토리지와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 스토리지 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 애플리케이션에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다. 프리미엄 SSD 디스크는 한자리 밀리초 미만의 낮은 지연 시간과 시간의 99.9% 동안 이전 표에서 설명한 목표 IOPS 및 처리량을 제공하도록 설계되었습니다.

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 64 데이터 디스크는 Standard_GS5 VM에 연결할 수 있습니다. 이러한 각 디스크는 P30에 해당하는 크기이며 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [VM 유형 및 크기](../sizes.md)를 참조하세요.

## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 다음 명령을 사용하여 가상 머신을 만듭니다.

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1)을 사용하여 가상 머신의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.


[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 가상 머신을 만듭니다. VM에서 관리자 계정의 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


[New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig)를 사용하여 초기 구성을 만듭니다. 다음 예제에서는 크기가 128GB인 디스크를 구성합니다.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

[New-AzDisk](/powershell/module/az.compute/new-azdisk) 명령을 사용하여 데이터 디스크를 만듭니다.

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

[Get-AzVM](/powershell/module/az.compute/get-azvm) 명령을 사용하여 데이터 디스크를 추가할 가상 머신을 가져옵니다.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

[Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) 명령을 사용하여 데이터 디스크를 가상 머신 구성에 추가합니다.

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

[Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) 명령을 사용하여 가상 머신을 업데이트합니다.

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>데이터 디스크 준비

디스크가 가상 머신에 연결된 후 디스크를 사용하도록 운영 체제를 구성해야 합니다. 다음 예제에서는 VM에 추가된 첫 번째 디스크를 수동으로 구성하는 방법을 보여 줍니다. [사용자 지정 스크립트 확장](./tutorial-automate-vm-deployment.md)을 사용하여 이 프로세스를 자동화할 수도 있습니다.

### <a name="manual-configuration"></a>수동 구성

가상 컴퓨터와 RDP 연결 만들기 PowerShell을 열고 이 스크립트를 실행합니다.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>데이터 디스크 확인

데이터 디스크가 연결되어 있는지를 확인하려면 연결된 `DataDisks`에 대한 `StorageProfile`을 확인합니다.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

출력은 다음 예제와 비슷합니다.

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 VM 디스크 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

VM 구성 자동화에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM 구성 자동화](./tutorial-automate-vm-deployment.md)
