---
title: 여러 NIC를 사용하여 Azure에서 Linux VM 만들기 | Microsoft Docs
description: Azure CLI 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 Linux VM을 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b77ed879375cff8d45f7d532283647e70252bdab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772436"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>여러 네트워크 인터페이스 카드를 사용하여 Azure에서 Linux 가상 머신을 만드는 방법


이 문서에서는 Azure CLI를 사용하여 여러 NIC가 있는 VM을 만드는 방법을 설명합니다.

## <a name="create-supporting-resources"></a>지원 리소스 만들기
최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *mystorageaccount* 및 *myVM*이 포함됩니다.

먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnetFrontEnd*이라는 서브넷을 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet)를 사용하여 백 엔드 트래픽에 대한 서브넷을 만듭니다. 다음 예제에서는 *mySubnetBackEnd*이라는 서브넷을 만듭니다.

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

[az network nsg create](/cli/azure/network/nsg)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>여러 NIC 만들기 및 구성
[az network nic create](/cli/azure/network/nic)를 사용하여 두 개의 NIC를 만듭니다. 다음 예제는 네트워크 보안 그룹에 연결된 *myNic1* 및 *myNic2*라는 2개의 NIC를 만들며 하나의 NIC가 각 서브넷에 연결됩니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM 만들기 및 NIC 연결
VM을 만들 때 `--nics`로 만든 NIC를 지정합니다. 또한 VM 크기를 선택할 때도 주의해야 합니다. VM에 추가할 수 있는 NIC 총수가 제한되어 있습니다. [Linux VM 크기](sizes.md)에 대해 자세히 읽어보세요.

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

[여러 Nic에 대 한 게스트 OS 구성](#configure-guest-os-for- multiple-nics)의 단계를 완료하여 라우팅 테이블을 게스트 OS에 추가합니다.

## <a name="add-a-nic-to-a-vm"></a>VM에 NIC 추가
이전 단계에서는 여러 NIC가 있는 VM을 만들었습니다. Azure CLI를 사용해서 기존 VM에 NIC를 추가할 수도 있습니다. [VM 크기](sizes.md) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다. 필요한 경우 [VM의 크기를 조정](change-vm-size.md)할 수 있습니다.

[az network nic create](/cli/azure/network/nic)를 사용하여 다른 NIC를 만듭니다. 다음 예제에서는 이전 단계에서 만든 백 엔드 서브넷 및 네트워크 보안 그룹에 연결된 *myNic3*라는 NIC를 만듭니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

기존 VM에 NIC를 추가하려면 먼저 [az vm deallocate](/cli/azure/vm)를 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myVM*이라는 VM을 할당 취소합니다.


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic add](/cli/azure/vm/nic)를 사용하여 NIC를 추가합니다. 다음 예제에서는 *myNic3*를 *myVM*에 추가합니다.

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm)를 사용하여 VM을 시작합니다.

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

[여러 Nic에 대 한 게스트 OS 구성](#configure-guest-os-for- multiple-nics)의 단계를 완료하여 라우팅 테이블을 게스트 OS에 추가합니다.

## <a name="remove-a-nic-from-a-vm"></a>VM에서 NIC 제거
기존 VM에사 NIC를 제거하려면 먼저 [az vm deallocate](/cli/azure/vm)를 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myVM*이라는 VM을 할당 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic remove](/cli/azure/vm/nic)를 사용하여 NIC를 제거합니다. 다음 예제에서는 *myVM*에서 *myNic3*를 제거합니다.

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm)를 사용하여 VM을 시작합니다.

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)에 대해 읽어볼 수 있습니다. Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. *복사* 를 사용하여 만들 인스턴스 수를 지정합니다.

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[*복사*를 사용하여 여러 인스턴스 만들기](../../resource-group-create-multiple.md)에 대해 자세히 읽어보세요. 

`copyIndex()`를 사용하여 리소스 이름에 번호를 추가할 수도 있습니다. 이와 같이 `myNic1`, `myNic2` 등을 만들 수 있습니다. 다음은 인덱스 값을 추가하는 예를 보여 줍니다.

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../../virtual-network/template-samples.md)의 전체 예제를 읽어볼 수 있습니다.

[여러 Nic에 대 한 게스트 OS 구성](#configure-guest-os-for- multiple-nics)의 단계를 완료하여 라우팅 테이블을 게스트 OS에 추가합니다.

## <a name="configure-guest-os-for-multiple-nics"></a>여러 NIC에 대한 게스트 OS 구성

이전 단계에서는 가상 네트워크 및 서브넷을 만들고, NIC를 연결한 후 VM을 만들었습니다. SSH 트래픽을 허용하는 공용 IP 주소 및 네트워크 보안 그룹 규칙은 만들어지지 않았습니다. 여러 NIC에 대한 게스트 OS를 구성하려면 원격 연결을 허용하고 VM에서 로컬로 명령을 실행해야 합니다.

SSH 트래픽을 허용하려면 다음과 같이 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)를 사용하여 공용 IP 주소를 만들고 [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)를 사용하여 첫 번째 NIC에 할당합니다.

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

VM의 공용 IP 주소를 보려면 다음과 같이 [az vm show](/cli/azure/vm#az-vm-show)를 사용합니다.

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

이제 VM의 공용 IP 주소에 대해 SSH를 수행합니다. 이전 단계에서 제공된 기본 사용자 이름은 *azureuser*입니다. 고유한 사용자 이름 및 공용 IP 주소를 제공합니다.

```bash
ssh azureuser@137.117.58.232
```

보조 네트워크 인터페이스와 송수신하려면 각 보조 네트워크 인터페이스에 대한 운영 체제에 영구 경로 수동으로 추가해야 합니다. 이 문서에서 *eth1*은 보조 인터페이스입니다. 운영 체제에 영구 경로를 추가하기 위한 지침은 배포판에 따라 다릅니다. 이러한 지침에 대해서는 배포판에 대한 설명서를 참조하세요.

운영 체제에 경로를 추가할 때 게이트웨이 주소는 네트워크 인터페이스가 있는 서브넷에 대해 *.1*입니다. 예를 들어, 네트워크 인터페이스에 주소 *10.0.2.4*가 할당되면 경로에 대해 지정하는 게이트웨이는 *10.0.2.1*입니다. 경로의 대상에 대해 특정 네트워크를 정의할 수도 있고, 인터페이스에 대한 모든 트래픽이 지정된 게이트웨이를 통과하도록 하려면 대상으로 *0.0.0.0*을 지정할 수 있습니다. 각 서브넷에 대한 게이트웨이는 가상 네트워크에서 관리됩니다.

보조 인터페이스에 대한 경로를 추가한 경우, `route -n`을 사용하여 경로가 경로 테이블에 있는지 확인합니다. 다음 예제 출력은 이 문서의 VM에 두 개의 네트워크 인터페이스가 추가된 경로 테이블에 대한 것입니다.

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

재부팅 후에 경로 테이블을 다시 확인하여 추가한 경로가 재부팅 이후에도 유지되는지 확인합니다. 예를 들어 연결을 테스트하려면 다음 명령을 입력합니다. 여기서 *eth1*은 보조 네트워크 인터페이스의 이름입니다.

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>다음 단계
여러 NIC를 사용하여 VM을 만들려고 할 때 [Linux VM 크기](sizes.md)를 검토합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다.

VM을 추가로 보호하려면 Just-In-Time VM 액세스를 사용합니다. 이 기능은 필요할 때 및 지정된 기간 동안 SSH 트래픽에 대한 네트워크 보안 그룹 규칙을 엽니다. 자세한 내용은 [Just-In-Time를 사용하여 가상 머신 액세스 관리](../../security-center/security-center-just-in-time.md)를 참조하세요.
