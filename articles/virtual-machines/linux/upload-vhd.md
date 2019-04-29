---
title: Azure CLI를 사용하여 사용자 지정 Linux VM 업로드 또는 복사 | Microsoft Docs
description: Resource Manager 배포 모델 및 Azure CLI를 사용하여 사용자 지정 가상 머신을 업로드하거나 복사합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 6483fa8737ee3de6a60c4e4646fefec30ae702b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473418"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기

<!-- rename to create-vm-specialized -->

이 문서에서는 사용자 지정 VHD(가상 하드 디스크)를 업로드하는 방법과 Azure에서 기존 VHD를 복사하는 방법을 보여 줍니다. 그런 다음, 새로 만든 VHD를 사용하여 새 Linux VM(가상 머신)을 만듭니다. 요구 사항에 맞게 Linux 배포판을 설치하여 구성한 다음, 해당 VHD를 사용하여 새 Azure 가상 머신을 만들 수 있습니다.

사용자 지정 디스크에서 여러 VM을 만들려면 먼저 VM 또는 VHD에서 이미지를 만듭니다. 자세한 내용은 [CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md)를 참조하세요.

사용자 지정 디스크를 만드는 두 가지 옵션이 있습니다.
* VHD 업로드
* 기존 Azure VM 복사

## <a name="quick-commands"></a>빠른 명령

사용자 지정 또는 특수 디스크에서 [az vm create](/cli/azure/vm#az-vm-create)를 사용하여 새 VM을 만들 때 사용자 지정 또는 마켓플레이스 이미지를 지정하는(--image) 대신 디스크를 **연결**합니다(--attach-os-disk). 다음 예제에서는 사용자 지정된 VHD에서 만들어진 *myManagedDisk*라는 관리 디스크를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>요구 사항
뒤에 나오는 단계를 완료하는 데 필요한 항목은 다음과 같습니다.

* Azure에서 사용하기 위해 준비된 Linux 가상 컴퓨터 이 문서의 [VM 준비](#prepare-the-vm) 섹션에서는 SSH를 통해 VM에 연결하는 데 필요한 Azure Linux 에이전트(waagent) 설치에 대한 배포판 관련 정보를 찾는 방법에 대해 설명합니다.
* 기존 [Azure 보증 Linux 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 VHD 형식으로 가상 디스크에 VHD 파일(또는 [보증되지 않는 배포에 대한 정보](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조) VM과 VHD를 만드는 도구는 여러 가지가 있습니다.
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 또는 [KVM](https://www.linux-kvm.org/page/RunningKVM)을 설치 및 구성하고 VHD를 이미지 형식으로 사용하도록 주의합니다. 필요한 경우 `qemu-img convert`를 사용하여 [이미지를 변환](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)할 수 있습니다.
  * 또한 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 또는 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)에서 Hyper-V를 사용할 수 있습니다.

> [!NOTE]
> 새 VHDX 형식은 Azure에서 지원되지 않습니다. VM을 만들 때 VHD를 형식으로 지정합니다. 필요한 경우 [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 또는 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet을 사용하여 VHDX 디스크를 VHD로 변환할 수 있습니다. Azure는 동적 VHD 업로드를 지원하지 않으므로 업로드하기 전에 먼저 이러한 디스크를 정적 VHD로 변환해야 합니다. Azure에 업로딩하는 과정 중에 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go) 와 같은 도구를 사용하여 동적 디스크를 변환할 수 있습니다.
> 
> 


* 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index#az-login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

다음 예제에서는 예제 매개 변수 이름(예: *myResourceGroup*, *mystorageaccount* 및 *mydisks*)을 사용자의 고유한 값으로 바꿉니다.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>VM 준비

Azure에서는 다양한 Linux 배포를 지원합니다( [보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법에 대해 설명합니다.

* [CentOS 기반 배포](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES 및 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [기타: 보증되지 않는 배포](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 일반적인 추가 팁은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

> [!NOTE]
> [Azure의 Linux 보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 "지원되는 버전"에 지정된 보증 배포 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 [Azure 플랫폼 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)가 Linux를 실행하는 VM에 적용됩니다.
> 
> 

## <a name="option-1-upload-a-vhd"></a>옵션 1: VHD 업로드

로컬 컴퓨터에서 실행 중이거나 다른 클라우드에서 내보낸 사용자 지정된 VHD를 업로드할 수 있습니다. VHD를 사용하여 새 Azure VM을 만들려면 저장소 계정에 VHD를 업로드하고 VHD에서 관리 디스크를 만들어야 합니다. 자세한 내용은 [Azure Managed Disks 개요](../windows/managed-disks-overview.md)를 참조하세요.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

사용자 지정 디스크를 업로드하고 VM을 만들기 전에 먼저 [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만들어야 합니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>저장소 계정 만들기

[az storage account create](/cli/azure/storage/account)를 사용하여 사용자 지정 디스크 및 VM에 대한 스토리지 계정을 만듭니다. 다음 예제에서는 이전에 만든 리소스 그룹에 *mystorageaccount*라는 스토리지 계정을 만듭니다.

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>저장소 계정 키 나열
Azure는 각 저장소 계정에 대해 두 개의 512 비트 선택키를 생성합니다. 이러한 액세스 키는 쓰기 작업을 수행할 때와 같이 저장소 계정에 인증할 때 사용됩니다. 자세한 내용은 [저장소에 대한 액세스 관리](../../storage/common/storage-account-manage.md#access-keys)를 참조하세요. 

[az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list)를 사용하여 액세스 키를 확인합니다. 예를 들어 만든 저장소 계정에 대한 액세스 키를 보려면 다음과 같이 합니다.

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

다음과 유사하게 출력됩니다.

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
다음 단계에서 저장소 계정과 상호 작용하는 데 사용할 수 있도록 **key1**을 기록해 둡니다.

### <a name="create-a-storage-container"></a>저장소 컨테이너 만들기
서로 다른 디렉터리를 만들어 로컬 파일 시스템을 논리적으로 구성하는 것과 같은 방식으로 저장소 계정 내에 컨테이너를 만들어 디스크를 구성합니다. 저장소 계정에는 많은 컨테이너가 포함될 수 있습니다. [az storage container create](/cli/azure/storage/container#az-storage-container-create)를 사용하여 컨테이너를 만듭니다.

다음 예제에서는 *mydisks*라는 컨테이너를 만듭니다.

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>VHD 업로드
[az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload)를 사용하여 사용자 지정 디스크 이미지를 업로드합니다. 사용자 지정 디스크를 페이지 Blob으로 업로드하고 저장합니다.

로컬 컴퓨터에 있는 사용자 지정 디스크에 선택키, 이전 단계에서 만든 컨테이너, 경로를 차례로 지정합니다.

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
VHD를 업로드하는 데 시간이 걸릴 수 있습니다.

### <a name="create-a-managed-disk"></a>관리 디스크 만들기


[az disk create](/cli/azure/disk#az-disk-create)를 사용하여 VHD에서 관리 디스크를 만듭니다. 다음 예제에서는 다음과 같은 이름을 지정한 저장소 계정 및 컨테이너에 업로드한 VHD에서 *myManagedDisk*라는 관리 디스크를 만듭니다.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>옵션 2: 기존 VM 복사

Azure에서 사용자 지정 VM을 만든 다음, OS 디스크를 복사하고 새 VM에 연결하여 다른 복사본을 만들 수도 있습니다. 테스트에는 적합하지만, 기존 Azure VM을 여러 개의 새 VM에 대한 모델로 사용하려는 경우 *이미지*를 대신 만듭니다. 기존 Azure VM에서 이미지를 만드는 방법에 대한 자세한 내용은 [CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md)를 참조하세요.

### <a name="create-a-snapshot"></a>스냅숏 만들기

이 예에서는 *myResourceGroup*이라는 리소스 그룹에 *myVM*이라는 VM의 스냅숏을 만들고 *osDiskSnapshot*이라는 스냅숏을 만듭니다.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>관리 디스크 만들기

스냅숏에서 새 관리 디스크를 만듭니다.

스냅숏의 ID를 가져옵니다. 이 예제에서 스냅숏 이름은 *osDiskSnapshot*이며 *myResourceGroup* 리소스 그룹에 있습니다.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

관리 디스크를 만듭니다. 이 예제에서는 스냅숏에서 *myManagedDisk*라는 관리 디스크를 만듭니다. 이 디스크는 표준 저장소에 있으며 크기는 128GB입니다.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 VM을 만들고, 관리 디스크를 OS 디스크로 연결합니다(--attach-os-disk). 다음 예제에서는 업로드한 VHD에서 만든 관리 디스크를 사용하여 *myNewVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

원본 VM의 자격 증명을 사용하여 SSH를 통해 VM에 연결할 수 있어야 합니다. 

## <a name="next-steps"></a>다음 단계
사용자 지정 가상 디스크를 준비하고 업로드한 후 [Resource Manager 및 템플릿 사용하기](../../azure-resource-manager/resource-group-overview.md)에 관해 자세히 알아볼 수 있습니다. 또한 새 Vm에 [데이터 디스크 추가](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 를 고려할 수도 있습니다. 애플리케이션이 액세스해야 할 Vm에서 실행되고 있다면 반드시 [포트 및 엔드포인트 열기](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 해야 합니다.
