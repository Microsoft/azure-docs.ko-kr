---
title: Azure에서 관리형 이미지 만들기
description: Azure에서 일반화된 VM 또는 VHD의 관리 이미지를 만듭니다. 이미지를 사용하여 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 751fa9f9fe2ba17a982b71a6332be302804f0dcc
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047295"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지 만들기

스토리지 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM(가상 머신)에서 관리 이미지 리소스를 만들 수 있습니다. 여러 VM을 만드는 데 이미지를 사용할 수 있습니다. 관리형 이미지의 청구 방법에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 

관리형 이미지 하나는 최대 20개의 동시 배포를 지원합니다. 동일한 관리형 이미지에서 20개가 넘는 VM을 동시에 만들려고 하면 단일 VHD의 스토리지 성능 제한으로 인해 프로비저닝 시간이 초과될 수 있습니다. 20개가 넘는 VM을 동시에 만들려면 20개의 동시 VM 배포마다 복제본 1개로 구성된 [Shared Image Gallery](shared-image-galleries.md) 이미지를 사용합니다.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 모든 개인 계정 및 보안 정보를 제거한 다음 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 개요](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) 및 [지원되지 않는 시나리오](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)를 참조하세요. 

> [!IMPORTANT]
> VM에서 Sysprep을 실행하고 나면 해당 VM은 *일반화*된 것으로 간주되므로 다시 시작할 수 없습니다. VM 일반화 프로세스는 되돌릴 수 없습니다. 원래 VM을 작동하는 상태로 유지해야 하는 경우에는 [VM의 복사본](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)을 만들고 복사본을 일반화해야 합니다. 
>
>Sysprep을 사용 하려면 드라이브의 암호를 완전히 해독 해야 합니다. VM에서 암호화를 사용 하도록 설정한 경우 Sysprep를 실행 하기 전에 암호화를 사용 하지 않도록 설정 합니다.
>
> Azure에 VHD(가상 하드 디스크)를 처음으로 업로드하기 전에 Sysprep을 실행하려는 경우 [VM을 준비](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)해야 합니다.  
> 
> 

Windows VM을 일반화하려면 다음 단계를 수행합니다.

1. Windows VM에 로그인합니다.
   
2. 관리자로 명령 프롬프트 창을 엽니다. 

3. Panther 디렉터리 (C:\Windows\Panther)를 삭제 합니다. 그런 다음 디렉터리 를%windir%\system32\sysprep로 변경 하 고를 실행 `sysprep.exe` 합니다.
   
4. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 시작**을 선택하고 **일반화** 확인란을 선택합니다.
   
5. **종료 옵션**에서 **종료**를 선택합니다.
   
6. **확인**을 선택합니다.
   
    ![Sysprep 시작](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep은 작업을 완료하면 VM을 종료합니다. VM을 다시 시작하지 않습니다.

> [!TIP]
> **선택적** [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options)을 사용하여 이미지를 최적화하고 VM의 첫 번째 부팅 시간을 줄입니다.
>
> 이미지를 최적화하려면 Windows 탐색기에서 VHD를 두 번 클릭하여 탑재한 다음 `/optimize-image` 매개 변수를 사용하여 DISM을 실행합니다.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> 여기서 D:는 탑재된 VHD의 경로입니다.
>
> `DISM /optimize-image`를 실행하는 경우 VHD를 마지막으로 수정해야 합니다. 배포하기 전에 VHD를 변경하는 경우 `DISM /optimize-image`를 다시 실행해야 합니다.

## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. VM 이미지를 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신**을 검색하여 선택합니다.

2. 목록에서 VM을 선택합니다.

3. VM의 **가상 머신** 페이지 위쪽 메뉴에서 **캡처**를 선택합니다.

   **이미지 만들기** 페이지가 나타납니다.

4. **이름**에서 미리 입력된 이름을 적용하거나 이미지에 사용할 이름을 입력합니다.

5. **리소스 그룹**에서 **새로 만들기**를 선택하고 이름을 입력하거나, 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.

6. 이미지가 만들어진 후 원본 VM을 삭제하려면 **이미지를 만든 후 이 가상 머신을 자동으로 삭제**를 선택합니다.

7. 모든 [가용성 영역](../../availability-zones/az-overview.md)에서 이미지를 사용하려면 **영역 복원력**에서 **설정**을 선택합니다.

8. **만들기**를 선택하여 이미지를 만듭니다.

생성된 이미지는 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시됩니다.



## <a name="create-an-image-of-a-vm-using-powershell"></a>PowerShell을 사용 하 여 VM 이미지 만들기

 

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다. 이 예제에서는 VM에서 관리되는 디스크를 사용하는 관리되는 이미지를 만드는 방법을 보여 줍니다.

시작하기 전에 Azure PowerShell 모듈이 최신 버전인지 확인합니다. 버전을 확인하려면 PowerShell에서 `Get-Module -ListAvailable Az`를 실행합니다. 버전을 업그레이드해야 하는 경우 [PowerShellGet을 사용하여 Windows에서 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결합니다.


> [!NOTE]
> 이미지를 영역 중복 스토리지에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 이미지 구성에 `-ZoneResilient` 매개 변수(`New-AzImageConfig` 명령)를 포함하는 지역에 이미지를 만들어야 합니다.

VM 이미지를 만들려면 다음 단계를 수행합니다.

1. 일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 가상 머신의 상태를 **일반화됨**으로 설정합니다. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 가상 머신을 가져옵니다. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell을 사용하여 관리되는 디스크에서 이미지 만들기

OS 디스크의 이미지만 만들려면 관리 디스크 ID를 OS 디스크로 지정합니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 관리되는 디스크의 ID를 가져옵니다.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>PowerShell을 사용 하 여 스냅숏에서 이미지 만들기

다음 단계를 수행하면 일반화된 VM의 스냅샷에서 관리되는 이미지를 만들 수 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 스냅샷을 가져옵니다.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>스토리지 계정을 사용하는 VM에서 이미지 만들기

관리 디스크를 사용하지 않는 VM에서 관리형 이미지를 만들려면 스토리지 계정에 있는 OS VHD의 URI가 필요합니다. 이 URI는 https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd* 형식입니다. 이 예제의 VHD는 *vhdcontainer* 컨테이너의 *mystorageaccount*에 있으며 VHD 파일 이름은 *vhdfilename.vhd*입니다.


1.  일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM을 중지하고 할당을 취소합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM을 일반화됨으로 표시합니다.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>다음 단계
- [관리되는 이미지에서 VM 만들기](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 수행합니다.    
