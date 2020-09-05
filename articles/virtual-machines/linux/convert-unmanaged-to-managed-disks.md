---
title: Linux VM을 비관리 디스크에서 managed disks로 변환
description: Azure CLI를 사용 하 여 관리 되지 않는 디스크에서 관리 디스크로 Linux VM을 변환 하는 방법입니다.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 89035d26e05ca4b93dc271f02fef1afea24b0f20
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815337"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux 가상 머신을 비관리 디스크에서 Managed Disks로 변환

비관리 디스크를 사용하는 기존 Linux VM(가상 머신)이 있는 경우 [Azure Managed Disks](../managed-disks-overview.md)를 사용하도록 VM을 변환할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

이 문서에서는 Azure CLI를 사용하여 VM을 변환하는 방법을 보여 줍니다. CLI를 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에
* [Managed Disks로의 마이그레이션에 대한 FAQ](../faq-for-disks.md#migrate-to-managed-disks)를 검토합니다.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* 원래 VHD와 변환 전 VM에서 사용된 스토리지 계정은 삭제되지 않습니다. 이들 작업은 요금이 계속 청구됩니다. 이러한 아티팩트에 대한 요금이 청구되지 않도록 하려면 변환이 완료되었는지 확인한 후 원래 VHD Blob을 삭제합니다. 이러한 연결 되지 않은 디스크를 삭제 하기 위해 확인 해야 하는 경우 연결 되지 않은 [Azure 관리 및 비관리 디스크 찾기 및 삭제](find-unattached-disks.md)문서를 참조 하세요.

## <a name="convert-single-instance-vms"></a>단일 인스턴스 VM 변환
이 섹션에서는 단일 인스턴스 Azure VM을 비관리 디스크에서 Managed Disks로 변환하는 방법을 설명합니다. Vm이 가용성 집합에 있는 경우 다음 섹션을 참조 하세요. 이 프로세스를 사용 하 여 프리미엄 (SSD) 관리 되지 않는 디스크에서 프리미엄 managed disks로 또는 표준 (HDD) 관리 되지 않는 디스크에서 표준 managed disks로 Vm을 변환할 수 있습니다.

1. [az vm deallocate](/cli/azure/vm)를 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm)를 사용하여 VM을 Managed Disks로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하는 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. [az vm start](/cli/azure/vm)를 사용하여 VM을 Managed Disks로 변환한 후 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>가용성 집합의 VM 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

가용성 집합을 변환하기 전에 가용성 집합에 있는 모든 VM의 할당을 취소해야 합니다. 가용성 집합이 관리되는 가용성 집합으로 변환된 후 모든 VM을 관리되는 디스크로 변환하려고 합니다. 그런 다음 모든 VM을 시작하고 정상적으로 운영을 계속합니다.

1. [az vm availability-set list](/cli/azure/vm/availability-set)를 사용하여 가용성 집합의 모든 VM을 나열합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`에 있는 모든 VM을 나열합니다.

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [az vm deallocate](/cli/azure/vm)를 사용하여 모든 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set)를 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 변환합니다.

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm)를 사용하여 모든 VM을 Managed Disks로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하는 `myVM`이라는 VM을 변환합니다.

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. [az vm start](/cli/azure/vm)를 사용하여 모든 VM을 Managed Disks로 변환한 후 시작합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Azure Portal을 사용하여 변환

Azure Portal을 사용하여 관리되지 않는 디스크에서 관리 디스크로 변환할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 VM 목록에서 VM을 선택합니다.
3. VM 블레이드의 메뉴에서 **디스크**를 선택합니다.
4. **디스크** 블레이드 상단에서 **관리 디스크로 마이그레이션**을 선택합니다.
5. VM이 가용성 집합에 있으면 **관리 디스크로 마이그레이션** 블레이드에 가용성 집합을 먼저 변환해야 한다는 경고가 표시됩니다. 경고에는 클릭하여 가용성 집합을 변환할 수 있는 링크가 있습니다. 가용성 집합이 변환되거나 VM이 가용성 집합에 없는 경우에는 **마이그레이션**을 클릭하여 디스크를 관리 디스크로 마이그레이션하는 프로세스를 시작합니다.

VM이 중지되고 마이그레이션이 완료된 후 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계

저장소 옵션에 대 한 자세한 내용은 [Azure Managed Disks 개요](../managed-disks-overview.md)를 참조 하세요.
