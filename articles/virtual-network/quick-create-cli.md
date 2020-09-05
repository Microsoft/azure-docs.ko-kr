---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure CLI
titlesuffix: Azure Virtual Network
description: 이 빠른 시작에서는 Azure CLI를 사용하여 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 통해 Azure 리소스가 서로 통신하고 인터넷과 통신할 수 있습니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056791"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 가상 네트워크 만들기

가상 네트워크를 사용하면 VM(가상 머신)과 같은 Azure 리소스가 서로 인터넷을 통해 비공개로 통신할 수 있습니다. 이 빠른 시작에서는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 만든 후에 두 개의 VM을 가상 네트워크에 배포합니다. 그런 다음, 인터넷에서 VM에 연결하고 새 가상 네트워크를 통해 비공개로 통신합니다.
## <a name="prerequisites"></a>필수 구성 요소
Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group-and-a-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *default*라는 하나의 서브넷이 있는 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 기본 키 위치에 SSH 키가 없는 경우 이 명령에서 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. `--no-wait` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다. 다음 예제에서는 *myVm1*이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

이전 단계에서 `--no-wait` 옵션을 사용했으므로 계속해서 *myVm2*라는 두 번째 VM을 만들 수 있습니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI 출력 메시지

VM을 만드는 데 몇 분이 걸릴 수 있습니다. Azure에서 VM을 만든 후 Azure CLI는 다음과 같은 출력을 반환합니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

**publicIpAddress**를 기록해 둡니다. 이 주소를 사용하여 다음 단계에서 인터넷을 통해 VM에 연결합니다.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음 명령에서 `<publicIpAddress>`를 *myVm2* VM의 공용 IP 주소로 바꿉니다.

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 간 통신

*myVm2* 및 *myVm1* VM 간의 프라이빗 통신을 확인하려면 다음 명령을 입력합니다.

```bash
ping myVm1 -c 4
```

*10.0.0.4*에서 4개의 응답을 받게 됩니다.

*myVm2* VM을 사용하여 SSH 세션을 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 두 VM 간에 비공개로 통신했습니다.
Azure는 VM 간에 무제한 프라이빗 통신을 허용합니다. 기본적으로 Azure는 인터넷에서 Windows VM으로의 인바운드 원격 데스크톱 연결만 허용합니다. 다양한 유형의 VM 네트워크 통신 구성에 대해 자세히 알아보려면 다음 문서로 이동하세요.
> [!div class="nextstepaction"]
> [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)
