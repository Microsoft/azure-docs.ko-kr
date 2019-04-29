---
title: 가속화된 네트워킹을 사용하는 Azure 가상 머신 만들기 | Microsoft Docs
description: 가속화된 네트워킹을 사용하는 Linux 가상 머신을 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 8ea17e5615c0256c084b0745a392fb49f8873f99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713747"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>가속 네트워킹을 사용하는 Linux 가상 머신 만들기

이 자습서에서는 가속 네트워킹을 사용하는 Linux VM(가상 머신)을 만드는 방법에 대해 알아봅니다. 가속 네트워킹을 사용하여 Windows VM을 만들려면 [가속 네트워킹을 사용하여 Windows VM 만들기](create-vm-accelerated-networking-powershell.md)를 참조하세요. 가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다. 다음 그림에서는 가속 네트워킹을 사용할 때와 사용하지 않을 때 두 개의 VM 간의 통신을 보여줍니다.

![비교](./media/create-vm-accelerated-networking/accelerated-networking.png)

가속화된 네트워킹을 사용하지 않는 경우 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 통과해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다. 가상 스위치에 대한 자세한 내용은 [Hyper-V 네트워크 가상화 및 가상 스위치](https://technet.microsoft.com/library/jj945275.aspx) 문서를 참조하세요.

가속화된 네트워킹을 사용하는 경우 네트워크 트래픽이 가상 머신의 NIC(네트워크 인터페이스)에 도착한 다음 VM으로 전달됩니다. 이제 가상 스위치가 적용되는 모든 네트워크 정책은 오프로드되어 하드웨어에 적용됩니다. 하드웨어에서 정책을 적용하면 NIC는 호스트에 적용된 모든 정책을 유지하면서 VM에 직접 네트워크 트래픽을 전달할 수 있으므로 호스트 및 가상 스위치를 우회할 수 있습니다.

가속화된 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 Azure VNet(Virtual Network)에 연결된 둘 이상의 VM에서 이 기능을 사용하도록 설정하는 것이 좋습니다. VNet에서 통신하거나 온-프레미스에 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향을 최소화할 수 있습니다.

## <a name="benefits"></a>이점
* **더 짧은 대기 시간/더 많은 초당 패킷 수(pps):** 데이터 경로에서 가상 스위치를 제거하면 정책 처리를 위해 패킷이 호스트에 머무는 시간이 없어지며 VM 내에서 처리할 수 있는 패킷 수가 늘어납니다.
* **지터 감소:** 적용해야 하는 정책의 수와 처리를 수행하는 CPU의 워크로드에 따라 가상 스위치 처리 성능이 달라집니다. 정책 적용을 하드웨어로 오프로드하면 패킷이 VM으로 직접 전달되고, 호스트-VM 통신과 모든 소프트웨어 인터럽트 및 컨텍스트 전환이 제거되어 이러한 가변성이 해소됩니다.
* **CPU 사용률 감소:** 이 기능을 사용할 때는 호스트에서 가상 스위치를 바이패스하므로 네트워크 트래픽 처리에 CPU를 더 적게 사용하게 됩니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 배포는 즉시 Azure Gallery에서 기본으로 지원됩니다. 
* **Azure linux 커널로 Ubuntu 14.04**
* **Ubuntu 16.04 이상** 
* **SLES12 SP3 이상** 
* **RHEL 7.4 이상**
* **CentOS 7.4 이상**
* **CoreOS Linux**
* **Debian “Stretch”(백포트 커널 포함)**
* **Oracle Linux 7.4 이상 사용 하 여 Red Hat 호환 커널 (RHCK)**
* **UEK 버전 5 사용 하 여 oracle Linux 7.5 이상**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>제한 및 제약 조건

### <a name="supported-vm-instances"></a>지원되는 VM 인스턴스
가속 네트워킹은 가장 일반적인 용도로 2개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다.  지원되는 이러한 계열은 D/DSv2 및 F/Fs입니다.

하이퍼스레딩을 지원하는 인스턴스에서 가속 네트워킹은 4개 이상의 vCPU가 포함된 VM 인스턴스에서 지원됩니다. 지원되는 계열은 D/dsv3, e/esv3, Fsv2, Lsv2, Ms/Mms 및 Ms/Mmsv2 합니다.

VM 인스턴스에 대한 자세한 내용은 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="regions"></a>영역
모든 공용 Azure 지역 및 Azure Government 클라우드에서 사용할 수 있습니다.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>실행 중인 VM에서 가속 네트워킹 사용
가속 네트워킹을 사용하지 않고 지원되는 VM 크기에서는 기능이 중지되고 할당이 취소된 경우에만 사용하도록 설정할 수 있습니다.  
### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager를 통한 배포
가속 네트워킹을 사용하여 가상 머신(클래식)을 배포할 수 없습니다.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Azure 가속 네트워킹을 사용하여 Linux VM 만들기
## <a name="portal-creation"></a>포털 만들기
이 문서에서는 Azure CLI를 통해 가속 네트워킹을 사용하는 가상 머신을 만드는 단계를 안내하지만, [Azure Portal을 통해 가속 네트워킹을 사용하는 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)도 가능합니다. 포털에서 가상 컴퓨터를 만들 때를 **가상 머신 만들기** 블레이드를 선택 합니다 **네트워킹** 탭 합니다.  이 탭에는 대 한 옵션이 **가속 네트워킹**합니다.  선택한 경우에 [지원 되는 운영 체제](#supported-operating-systems) 및 [VM 크기](#supported-vm-instances),이 옵션을 "On"입니다. 자동으로 채워집니다  그러지 않으면 가속 네트워킹에 대해 "Off" 옵션을 채우는 하 고 사용자에 게 왜 사용 되지 않는 수 하는 이유를 제공 합니다.   

* *참고:* 포털을 통해 지원 되는 운영 체제 에서만 사용할 수 있습니다.  사용자 지정 이미지를 사용 하는 경우 이미지는 가속화 된 네트워킹을 지원 하세요. CLI 또는 Powershell을 사용 하 여 VM을 만듭니다. 

가상 머신을 만든 후 가속 네트워킹을 사용 하도록 설정할지의 지침을 수행 하 여 확인할 수 있습니다 합니다 [가속화 된 네트워킹을 사용할 수 있는지 확인](#confirm-that-accelerated-networking-is-enabled)합니다.

## <a name="cli-creation"></a>CLI 만들기
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

최신 [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myNic*, *myVm*이 포함됩니다.

[az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *centralus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location centralus
```

[Linux 가속 네트워킹](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)에 나열된 지원되는 Linux 지역을 선택합니다.

[az network vnet create](/cli/azure/network/vnet)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 하나의 서브넷을 포함한 *myVnet*이라는 가상 네트워크를 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기
[az network nsg create](/cli/azure/network/nsg)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

네트워크 보안 그룹에는 여러 가지 기본 규칙이 포함됩니다. 그 중 하나는 인터넷의 모든 인바운드 액세스를 사용하지 않도록 설정합니다. [az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 가상 머신에 대한 SSH 액세스를 허용하도록 포트를 엽니다.

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>가속 네트워킹을 사용하여 네트워크 인터페이스 만들기

[az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다. 인터넷에서 가상 머신에 액세스하려는 계획이 없다면 공용 IP 주소가 필요하지 않지만 이 문서의 단계를 완료하려면 필요합니다.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

가속 네트워킹을 사용하여 [az network nic create](/cli/azure/network/nic)로 네트워크 인터페이스를 만듭니다. 다음 예제에서는 *myVnet* 가상 네트워크의 *mySubnet*이라는 서브넷에서 *myNic*이라는 네트워크 인터페이스를 만들고, *myNetworkSecurityGroup* 네트워크 보안 그룹을 네트워크 인터페이스에 연결합니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>VM 만들기 및 NIC 연결
VM을 만들 때 `--nics`로 만든 NIC를 지정합니다. [Linux 가속 네트워킹](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)에 나열된 크기 및 배포를 선택합니다. 

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 다음 예제에서는 가속 네트워킹(*Standard_DS4_v2*)을 지원하는 UbuntuLTS 이미지 및 크기를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

모든 VM 크기 및 특성 목록은 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

VM을 만들면 다음 예제 출력과 유사한 출력이 반환됩니다. **publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>가속 네트워킹을 사용할 수 있는지 확인합니다.

다음 명령을 사용하여 VM으로 SSH 세션을 만듭니다. `<your-public-ip-address>`를 만든 가상 머신에 할당된 공용 IP 주소와 바꾸고, VM을 만들 때 `--admin-username`에 다른 값을 사용한 경우 *azureuser*를 바꿉니다.

```bash
ssh azureuser@<your-public-ip-address>
```

Bash 셸에서 `uname -r`을 입력하고, 커널 버전이 다음 버전 이상인지 확인합니다.

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


`lspci` 명령을 사용하여 Mellanox VF 디바이스를 VM에 노출했는지 확인합니다. 반환되는 출력은 다음 출력과 유사합니다.

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

`ethtool -S eth0 | grep vf_` 명령을 사용하여 VF(가상 함수)에서 작업을 확인합니다. 다음 샘플 출력과 비슷한 출력을 수신하면 가속 네트워킹을 사용하고 작동시킬 수 있습니다.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
이제 가속화된 네트워킹을 VM에 사용할 수 있습니다.

## <a name="enable-accelerated-networking-on-existing-vms"></a>기존 VM에서 가속화된 네트워킹을 사용하도록 설정
가속 네트워킹을 사용하지 않고 VM을 만든 경우 기존 VM에서 이 기능을 사용하도록 설정할 수 있습니다.  VM이 위에 설명된 다음 필수 조건을 충족하여 가속 네트워킹을 지원해야 합니다.

* VM은 가속화된 네트워킹에 대해 지원되는 크기이어야 함
* VM은 지원되는 Azure Gallery 이미지(및 Linux용 커널 버전)이어야 함
* 가용성 집합의 모든 VM 또는 VMSS는 모든 NIC에서 가속화된 네트워킹을 사용하기 전에 중지/할당 취소되어야 함

### <a name="individual-vms--vms-in-an-availability-set"></a>개별 VM 및 가용성 집합의 VM
먼저 VM을 중지/할당 취소하거나, 가용성 집합인 경우 집합에 포함된 모든 VM을 중지/할당 취소합니다.

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

중요한 사항이니 유의하세요. VM이 가용성 집합 없이 개별적으로 만들어진 경우 개별 VM을 중지/할당 취소하기만 하면 가속 네트워킹을 사용하도록 설정할 수 있습니다.  VM이 가용성 집합으로 만들어진 경우 가용성 집합에 포함된 모든 VM을 중지/할당 취소해야 NIC에서 가속 네트워킹을 사용하도록 설정할 수 있습니다. 

VM이 중지되면 해당 VM의 NIC에서 가속 네트워킹을 사용하도록 설정합니다.

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

VM을 다시 시작하거나, 가용성 집합인 경우 집합에 포함된 모든 VM을 다시 시작하고 가속 네트워킹이 사용되는지 확인합니다. 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS는 약간 차이는 있지만 동일한 워크플로를 따릅니다.  먼저, VM을 중지합니다.

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

VM이 중지되면 네트워크 인터페이스 아래의 가속화된 네트워킹 속성을 업데이트합니다.

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

VMSS에는 자동, 롤링, 수동의 세 가지 다른 설정으로 업데이트를 적용하는 VM 업그레이드가 있습니다.  이 지침에서는 다시 시작한 후 VMSS에서 변경 내용을 즉시 픽업하도록 정책이 자동으로 설정되었습니다.  변경 내용이 즉시 픽업되도록 정책을 자동으로 설정하려면: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

마지막으로, VMSS를 다시 시작합니다.

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

다시 시작하고 업그레이드가 완료될 때까지 기다린 후 완료되면 VF가 VM 내부에 나타납니다.  지원되는 OS 및 VM 크기를 사용 중인지 확인합니다.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>가속 네트워킹을 사용하는 기존 VM 크기 조정

가속화된 네트워킹을 사용하는 VM은 가속화된 네트워킹을 지원하는 VM으로만 크기 조정할 수 있습니다.  

크기 조정 작업을 통해 가속화된 네트워킹을 사용하는 VM을 가속화된 네트워킹이 지원되지 않는 VM 인스턴스로 크기 조정할 수 없습니다.  이러한 VM 중 하나의 크기를 조정하려면 다음을 수행합니다. 

* VM을 중지/할당 취소하거나, 가용성 집합/VMSS인 경우 집합/VMSS에 포함된 모든 VM을 중지/할당 취소합니다.
* VM 또는 가용성 집합/VMSS인 경우, 집합/VMSS에 포함된 모든 VM의 NIC에서 가속 네트워킹을 사용하지 않도록 설정해야 합니다.
* 가속화된 네트워킹이 사용되지 않으면 VM/가용성 집합/VMSS를 가속화된 네트워킹이 지원되지 않는 새 크기로 이동하고 다시 시작할 수 있습니다.  

