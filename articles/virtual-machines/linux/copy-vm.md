---
title: Azure CLI를 사용 하 여 Linux VM 복사
description: Azure CLI 및 Managed Disks를 사용하여 Azure Linux VM의 복사본을 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 4612d15dd5d3ab6e814f09210d5ff3991fef0328
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121883"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Azure CLI 및 Managed Disks를 사용하여 Linux VM의 복사본 만들기

이 문서에서는 Azure CLI 및 Azure Resource Manager 배포 모델을 사용하여 Linux를 실행하는 Azure VM(가상 머신)의 복사본을 만드는 방법에 대해 설명합니다. 

[VHD에서 VM을 업로드하고 만들](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

-   [Azure CLI](/cli/azure/install-az-cli2)를 설치합니다.

-   [az login](/cli/azure/reference-index#az-login)을 사용하여 Azure 계정으로 로그인

-   복사본에 대한 원본으로 사용할 Azure VM 보유

## <a name="stop-the-source-vm"></a>원본 VM 중지

[az vm deallocate](/cli/azure/vm#az-vm-deallocate)를 사용하여 원본 VM의 할당을 취소합니다.
다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>원본 VM 복사

VM을 복사하려면 기본 가상 하드 디스크의 복사본을 만듭니다. 이 프로세스를 통해 원본 VM과 동일한 구성 및 설정을 포함하는 특수한 VHD(가상 하드 디스크) 및 관리 디스크를 만들 수 있습니다.

Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../windows/managed-disks-overview.md)를 참조하세요. 

1.  [az vm list](/cli/azure/vm#az-vm-list)를 사용하여 각 VM 및 해당 OS 디스크의 이름을 나열합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*의 모든 VM을 나열합니다.
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  [az disk create](/cli/azure/disk#az-disk-create)를 사용하여 새로운 관리 디스크를 만들어 디스크를 복사합니다. 다음 예제에서는 관리되는 디스크 *myDisk*에서 디스크 *myCopiedDisk*를 만듭니다.

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  이제 [az disk list](/cli/azure/disk#az-disk-list)를 사용하여 리소스 그룹의 Managed Disks를 확인합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*의 관리되는 디스크를 나열합니다.

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>가상 네트워크 설정

다음 선택적 단계는 새 가상 네트워크, 서브넷, 공용 IP 주소 및 가상 NIC(네트워크 인터페이스 카드)를 만듭니다.

문제 해결 목적 또는 추가 배포를 위해 VM을 복사하는 경우 기존 가상 네트워크의 VM을 사용하지 않으려 할 수 있습니다.

복사된 VM에 대한 가상 네트워크 인프라를 만들려는 경우 다음 몇 단계를 수행합니다. 가상 네트워크를 만들지 않으려면 [VM 만들기](#create-a-vm)로 건너뜁니다.

1.  [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*이라는 서브넷을 만듭니다.

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)를 사용하여 공용 IP를 만듭니다. 다음 예제는 *mypublicdns*라는 DNS 이름으로 *myPublicIP*라는 공용 IP를 만듭니다. (DNS 이름은 고유해야 하므로 고유한 이름을 입력합니다.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  [az network nic create](/cli/azure/network/nic#az-network-nic-create)를 사용하여 NIC를 만듭니다.
    다음 예제에서는 *mySubnet* 서브넷에 연결된 *myNic*라는 NIC를 만듭니다.

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 VM을 만듭니다.

다음과 같이 복사된 관리되는 디스크를 OS 디스크(`--attach-os-disk`)로 사용하도록 지정합니다.

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>다음 단계

[공유 이미지 갤러리](shared-images.md) 를 사용 하 여 VM 이미지를 관리 하는 방법을 알아봅니다.
