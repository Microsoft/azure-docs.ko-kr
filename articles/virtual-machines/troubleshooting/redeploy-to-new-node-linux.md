---
title: Azure에서 Linux Virtual Machines 다시 배포 | Microsoft Docs
description: Azure에서 가상 머신을 다시 배포하여 SSH 연결 문제를 완화하는 방법
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116950"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>새 Azure 노드로 Linux 가상 머신 다시 배포
Azure에서 Linux VM(가상 머신)에 대한 SSH 또는 애플리케이션 액세스 문제를 해결하는 데 어려움이 있는 경우 VM을 다시 배포하는 것이 도움이 될 수 있습니다. VM을 다시 배포하면 VM이 Azure 인프라 내의 새 노드로 이동된 다음 전원이 다시 켜집니다. 모든 구성 옵션 및 관련 리소스는 그대로 유지됩니다. 이 문서에서는 Azure CLI 또는 Azure 포털을 사용하여 VM을 다시 배포하는 방법을 보여 줍니다.

> [!NOTE]
> VM을 다시 배포한 후에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다. 


## <a name="use-the-azure-cli"></a>Azure CLI 사용
최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.

[az vm redeploy](/cli/azure/vm)를 사용하여 VM을 다시 배포합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 다시 배포합니다.

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Azure 클래식 CLI 사용
최신 [Azure 클래식 CLI](../../cli-install-nodejs.md)를 설치하고 Azure 계정에 로그인합니다. Resource Manager 모드(`azure config mode arm`)에 있는지 확인합니다.

다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 다시 배포합니다.

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [자세한 SSH 문제 해결 단계](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 특정 도움말을 찾을 수 있습니다. VM에서 실행 중인 애플리케이션에 액세스할 수 없는 경우 [애플리케이션 문제 해결](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 읽어볼 수도 있습니다.


