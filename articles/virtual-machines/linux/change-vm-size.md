---
title: Azure CLI를 사용한 Linux VM 크기 조정 방법 | Microsoft Docs
description: VM 크기를 변경하여 Linux 가상 머신의 규모를 확장하거나 축소하는 방법
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bd8136c61a277e730c07c8789afd1bf3214bd7c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127075"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Azure CLI를 사용하여 Linux 가상 머신 크기 조정 

VM(가상 머신)을 프로비전한 후 [VM 크기][vm-sizes]를 변경하여 VM의 크기를 확장 또는 축소할 수 있습니다. 경우에 따라 먼저 VM의 할당을 취소해야 합니다. VM을 호스트하는 하드웨어 클러스터에서 원하는 크기를 사용할 수 없는 경우 VM 할당을 취소해야 합니다. 이 문서에서는 Azure CLI에서 Linux VM 크기를 조정하는 방법을 자세히 설명합니다. 

## <a name="resize-a-vm"></a>VM 크기 조정
VM의 크기를 조정하려면 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다.

1. VM이 [az vm list-vm-resize-options](/cli/azure/vm)로 호스트된 하드웨어 클러스터에서 사용 가능한 VM 크기의 목록을 봅니다. 다음 예제에서는 리소스 그룹 `myResourceGroup` 지역의 VM `myVM`에 대한 VM 크기를 나열합니다.
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 원하는 VM 크기가 나열되는 경우 [az vm resize](/cli/azure/vm)로 VM의 크기를 조정합니다. 다음 예제에서는 VM `myVM`을 `Standard_DS3_v2` 크기로 조정합니다.
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    이 과정에서 VM이 다시 시작됩니다. 다시 시작한 후 기존의 OS 및 데이터 디스크는 다시 매핑됩니다. 임시 디스크에 있는 모든 내용이 손실됩니다.

3. 원하는 VM 크기가 목록에 없는 경우 먼저 [az vm deallocate](/cli/azure/vm)로 VM 할당을 취소해야 합니다. 이 프로세스를 통해 VM은 해당 지역에서 지원하는 사용 가능한 크기로 조정된 다음 시작될 수 있습니다. 다음 단계에서는 할당을 취소하고 크기를 조정한 다음 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 시작합니다.
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소도 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계
확장성을 높이기 위해서는 여러 VM 인스턴스를 실행하고 규모를 확장합니다. 자세한 내용은 [Virtual Machine Scale Set에서 Linux 컴퓨터 자동 확장][scale-set]을 참조하세요. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
