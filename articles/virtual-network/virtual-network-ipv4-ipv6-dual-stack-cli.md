---
title: Azure 가상 네트워크-CLI에서에서 i p v 6 이중 스택 응용 프로그램 배포
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure 가상 네트워크 IPv6 이중 스택 응용 프로그램을 배포 하는 방법입니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131015"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Azure virtual network-CLI (미리 보기)에 IPv6 이중 스택 응용 프로그램 배포

이 문서에서는 이중 스택 서브넷, 이중 (i p v 4 + IPv6) 프런트 엔드 구성 된 이중 IP 구성이 nic가 있는 Vm 사용 하 여 부하 분산 장치를 사용 하 여 이중 스택 가상 네트워크를 포함 하는 Azure에서 이중 스택 (i p v 4 + IPv6) 응용 프로그램을 배포 하는 방법을 보여 줍니다. 이중 네트워크 보안 그룹 규칙 및 이중 공용 Ip입니다.

> [!Important]
> Azure Virtual Network에 대 한 이중 스택 IPv6는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 빠른 시작에서는 Azure CLI 버전 2.0.49 사용 하 여 설치 하 고 Azure CLI를 로컬로 사용 하려는 경우 이상. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Azure 가상 네트워크 기능에 대 한 IPv6를 사용 하려면 다음과 같이 Azure PowerShell을 사용 하 여 구독을 구성 해야 합니다.

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 Azure CLI 명령을 실행 하 여 등록 상태를 확인할 수 있습니다.

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
등록이 완료되면 다음 명령을 실행합니다.

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 사용 하 여 이중 스택 가상 네트워크를 만들려면 먼저 만들어야 [az 그룹 만들기](/cli/azure/group)합니다. 다음 예제에서는 명명 된 리소스 그룹을 만듭니다 *myRGDualStack* 에 *eastus* 위치:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>부하 분산 장치에 대 한 IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 IPv4 및 IPv6 끝점에 액세스 하려면 부하 분산 장치에 대 한 IPv4 및 IPv6 공용 IP 주소를 필요 합니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 IPv4 및 IPv6 라는 공용 IP 주소를 만듭니다 *dsPublicIP_v4* 하 고 *dsPublicIP_v6* 에 *myRGDualStack* 리소스 그룹:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Vm에 대 한 공용 IP 주소 만들기

인터넷에서 Vm에 원격으로 액세스 하는 경우 IPv4 공용 IP 주소를 Vm에 대 한 필요 합니다. [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다.

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 이중 프런트 엔드 IP (예: IPv4 및 IPv6) 및 부하 분산 장치의 백 엔드 주소 풀을 구성 하 고 기본 부하 분산 장치를 만듭니다.

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

기본 Load Balancer를 만듭니다 [az network lb 만듭니다](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 라는 **dsLB** 이라는 프런트 엔드 풀을 포함 하는 **dsLbFrontEnd_v4**, 이라는 백 엔드 풀을  **dsLbBackEndPool_v4** IPv4 공용 IP 주소와 연결 된 **dsPublicIP_v4** 이전 단계에서 만든 합니다. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6 프런트 엔드 만들기

사용 하 여 IPV6 프런트 엔드 IP를 만듭니다 [az network lb frontend-ip-만들](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)합니다. 다음 예제에서는 라는 프런트 엔드 IP 구성을 만듭니다 *dsLbFrontEnd_v6* 연결 된 *dsPublicIP_v6* 주소:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6 백 엔드 주소 풀 구성

IPv6 백 엔드 주소 풀 만들기 [az network lb address-pool-만들](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)합니다. 다음 예에서는 이라는 백 엔드 주소 풀을 만듭니다 *dsLbBackEndPool_v6* IPv6 NIC 구성으로 Vm을 포함 하려면:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 라는 부하 분산 장치 규칙을 만듭니다 *dsLBrule_v4* 하 고 *dsLBrule_v6* 에 트래픽을 분산 *TCP* 포트 *80* 를 IPv4 및 IPv6 프런트 엔드 IP 구성:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
지원 네트워크 리소스가-만들어야 일부 Vm을 배포 하기 전에 가용성 집합, 네트워크 보안 그룹, 가상 네트워크 및 가상 Nic입니다. 
### <a name="create-an-availability-set"></a>가용성 집합 만들기
앱의 가용성 향상을 위해 가용성 집합에 Vm을 배치 합니다.

[az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest)를 사용하여 가용성 집합을 만듭니다. 다음 예에서는 이라는 가용성 집합을 만듭니다 *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNET에서 인바운드 및 아웃 바운드 통신을 제어 하는 하는 규칙에 대 한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

사용 하 여 네트워크 보안 그룹 만들기 [az network nsg 만들기](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>인바운드 및 아웃 바운드 연결에 대 한 네트워크 보안 그룹 규칙 만들기

포트 80 통해 및 아웃 바운드 연결에 포트 3389를 인터넷 연결을 통해 RDP 연결을 허용 하도록 네트워크 보안 그룹 규칙을 만듭니다 [az network nsg 규칙 만들기](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)합니다.

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create)를 사용하여 가상 네트워크를 만듭니다. 다음 예에서는 이라는 가상 네트워크를 만듭니다 *dsVNET* 서브넷이 있는 *dsSubNET_v4* 하 고 *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC 만들기

각 VM에 대 한 가상 Nic를 만듭니다 [az network nic 만들기](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)합니다. 다음 예제에서는 각 VM에 대 한 가상 NIC를 만듭니다. 각 NIC에 (1 IPv4 구성, IPv6 구성 1)의 두 가지 IP 구성이 있습니다. IPV6 구성을 사용 하 여 만든 [az network nic ip 구성 만들기](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)합니다.
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)로 VM을 만듭니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 

가상 머신 만들기 *dsVM0* 다음과 같습니다.

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
가상 머신 만들기 *dsVM1* 다음과 같습니다.

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal에서 IPv6 이중 스택 가상 네트워크를 보려면
Azure portal에서 다음과 같이 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에 입력 *dsVnet*합니다.
2. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다. 그러면 합니다 **개요** 이중 스택 가상 네트워크의 페이지 *dsVnet*합니다. 이중 스택 가상 네트워크 IPv4 및 IPv6 둘 다 구성 이라는 이중 스택 서브넷에 있는 두 개의 Nic를 보여 줍니다 *dsSubnet*합니다.

  ![Azure에서 IPv6 이중 스택 virtual network](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure 가상 네트워크에 대 한 IPv6는 Azure portal에서 사용할 수 있는이 미리 보기 릴리스에 대 한 읽기 전용입니다.


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성 (IPv4 및 IPv6)를 사용 하 여 기본 부하 분산 장치를 만들었습니다. 또한 부하 분산 장치의 백 엔드 풀에 추가 된 이중 IP 구성 (i p v 4 + IPv6)를 사용 하 여 Nic를 포함 하는 두 개의 가상 머신을 만들었습니다. Azure 가상 네트워크에서 IPv6 지원에 대 한 자세한 내용은를 참조 하세요. [IPv6에 대 한 Azure Virtual Network 란?](ipv6-overview.md)