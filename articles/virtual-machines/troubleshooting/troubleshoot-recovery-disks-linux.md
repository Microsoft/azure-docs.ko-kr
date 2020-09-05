---
title: Azure CLI를 통해 Linux 문제 해결 VM 사용 | Microsoft Docs
description: Azure CLI를 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제를 해결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: f05804785435824970d90410d9a1c9490a3d6c06
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654721"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Azure CLI를 사용하여 OS 디스크를 복구 VM에 연결하는 방식으로 Linux VM 문제 해결
Linux 가상 머신(VM)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목으로 인해 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Linux VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure CLI를 사용하는 방법을 자세히 설명합니다. 

## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 영향을 받는 VM을 중지합니다.
1. VM의 OS 디스크에서 스냅숏을 만듭니다.
1. OS 디스크 스냅샷에서 디스크를 만듭니다.
1. 문제 해결을 위해 새 OS 디스크를 다른 Linux VM에 연결 하 고 탑재 합니다.
1. 문제 해결 VM에 연결합니다. 새 OS 디스크의 문제를 해결 하려면 파일을 편집 하거나 도구를 실행 합니다.
1. 문제 해결 VM에서 새 OS 디스크를 분리 하 고 분리 합니다.
1. 영향을 받는 VM용 OS 디스크를 변경합니다.

이러한 문제 해결 단계를 수행하려면 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다.

> [!Important]
> 이 문서의 스크립트는 [관리 디스크](../managed-disks-overview.md)를 사용하는 VM에만 적용됩니다. 

다음 예제에서 매개 변수 이름을 사용자 고유의 값으로 바꿉니다 (예: `myResourceGroup` 및) `myVM` .

## <a name="determine-boot-issues"></a>부팅 문제 확인
VM이 올바르게 부팅할 수 없는 원인을 확인하려면 직렬 출력을 검사합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목 또는 삭제하거나 이동 중인 기본 가상 하드 디스크입니다.

[az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics)를 사용하여 부팅 로그를 가져옵니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에서 직렬 출력을 수신합니다.

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

직렬 출력을 검토하여 VM가 부팅되지 않는 원인을 확인합니다. 직렬 출력에 아무런 표시가 없는 경우, 가상 하드 디스크가 문제 해결 VM에 연결하고 `/var/log`에 로그 파일을 검토해야 할 수 있습니다.

## <a name="stop-the-vm"></a>VM을 중지합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 중지합니다.

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>영향을 받는 VM의 OS 디스크에서 스냅숏 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM에 연결할 수 없습니다. 다음 단계에서는 이 스냅샷에서 디스크를 만듭니다. 다음 예제에서는 `myVM'이라는 VM의 OS 디스크에서 `mySnapshot` 이름으로 스냅샷을 만듭니다. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>스냅샷에서 디스크 만들기

이 스크립트에서는 `mySnapshot`이라는 스냅샷에서 `myOSDisk` 이름으로 관리 디스크를 만듭니다.  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

리소스 그룹과 원본 스냅숏이 동일한 영역에 없는 경우를 실행할 때 "리소스를 찾을 수 없음" 오류가 표시 됩니다 `az disk create` . 이 경우를 지정 `--location <region>` 하 여 원본 스냅숏과 동일한 지역에 디스크를 만들어야 합니다.

이제 원본 OS 디스크의 복사본이 마련됐습니다. 문제 해결을 위해이 새 디스크를 다른 Windows VM에 탑재할 수 있습니다.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>새 가상 하드 디스크를 다른 VM에 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 디스크를이 문제 해결 VM에 연결 하 여 디스크의 콘텐츠를 찾아보고 편집 합니다. 이 프로세스를 통해 모든 구성 오류를 수정 하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다.

이 스크립트는 디스크를 `myNewOSDisk` VM에 연결 합니다 `MyTroubleshootVM` .

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>연결된 데이터 디스크 탑재

> [!NOTE]
> 다음 예제에서는 Ubuntu VM에 필요한 단계를 자세히 설명합니다. Red Hat Enterprise Linux 또는 SUSE와 같은 다른 Linux 배포판을 사용하는 경우 로그 파일 위치와 `mount` 명령을 약간 다를 수 있습니다. 명령의 적절한 변경에 대한 특정 배포에 대한 설명서를 참조하세요.

1. 적절한 자격 증명을 사용하여 문제 해결 VM에 대한 SSH입니다. 이 디스크가 문제 해결 VM에 연결된 첫 번째 데이터 디스크인 경우 디스크는 `/dev/sdc`에 연결할 수 있습니다. `dmesg`를 사용하여 연결된 디스크를 볼 수 있습니다.

    ```bash
    dmesg | grep SCSI
    ```

    다음 예제와 유사하게 출력됩니다.

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    앞의 예제에서 OS 디스크는 `/dev/sda`에 있으며 각 VM에 제공된 임시 디스크는 `/dev/sdb`에 있습니다. 여러 데이터 디스크가 있는 경우 `/dev/sdd`, `/dev/sde` 등에 있어야 합니다.

2. 디렉터리를 만들어 기존 가상 하드 디스크를 탑재합니다. 다음 예제는 디렉터리 `troubleshootingdisk`를 만듭니다.

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 기존 가상 하드 디스크에 여러 개의 파티션이 있는 경우 필수 파티션을 탑재합니다. 다음 예제에서는 `/dev/sdc1`에 첫 번째 주 파티션을 탑재합니다.

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 가상 하드 디스크의 UUID(Universally Unique Identifier)를 사용하여 Azure의 VM에 데이터 디스크를 마운트하는 것이 가장 좋습니다. 이 짧은 문제 해결 시나리오에서는 UUID를 사용하여 가상 하드 디스크를 탑재할 필요는 없습니다. 그러나 일반적인 사용 환경에서 UUID 대신 디바이스 이름을 사용하여 가상 하드 디스크를 탑재하기 위해 `/etc/fstab`을 편집하면 VM이 부팅되지 않을 수 있습니다.


## <a name="fix-issues-on-the-new-os-disk"></a>새 OS 디스크에서 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.


## <a name="unmount-and-detach-the-new-os-disk"></a>새 OS 디스크 분리 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 탑재 해제하고 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. SSH 세션에서 문제 해결 VM까지 기존 가상 하드 디스크를 탑재 해제합니다. 먼저 탑재 지점에 대한 상위 디렉터리에서 변경합니다.

    ```bash
    cd /
    ```

    이제 기존 가상 하드 디스크를 탑재 해제합니다. 다음 예제는 `/dev/sdc1`에 있는 디바이스를 탑재 해제합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

2. 이제 VM에서 가상 하드 디스크를 분리합니다. 문제 해결 VM에 대 한 SSH 세션을 종료 합니다.

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>영향을 받는 VM용 OS 디스크 변경

Azure CLI를 사용 하 여 OS 디스크를 교환할 수 있습니다. VM을 삭제하고 다시 만들 필요가 없습니다.

이 예제에서는 `myVM`이라는 VM을 중지하고 `myNewOSDisk`라는 디스크를 새 OS 디스크로 할당합니다.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md)을 참조하세요. VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Linux VM에서 애플리케이션 연결 문제 해결](troubleshoot-app-connection.md)을 참조하세요.

