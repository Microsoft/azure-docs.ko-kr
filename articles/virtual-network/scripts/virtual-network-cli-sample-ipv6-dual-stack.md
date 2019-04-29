---
title: Azure CLI 스크립트 샘플-IPv6 구성 virtual network 끝점 (미리 보기)
titlesuffix: Azure Virtual Network
description: Azure CLI를 사용 하 여 Azure 가상 네트워크에서 IPv6 끝점을 사용 하도록 설정
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/23/2019
ms.author: kumud
ms.openlocfilehash: a1c8f151a4c6459064d92ff2efb44fa8b74f8fc1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761453"
---
# <a name="configure-ipv6-endpoints-in-virtual-network-script-sample-preview"></a>가상 네트워크 스크립트 예제 (미리 보기)에 있는 IPv6 끝점을 구성 합니다.

이 문서에서는 이중 스택 서브넷, 이중 (i p v 4 + IPv6) 프런트 엔드 구성 된 이중 IP 구성이 nic가 있는 Vm 사용 하 여 부하 분산 장치를 사용 하 여 이중 스택 가상 네트워크를 포함 하는 Azure에서 이중 스택 (i p v 4 + IPv6) 응용 프로그램을 배포 하는 방법을 보여 줍니다. 이중 네트워크 보안 그룹 규칙 및 이중 공용 Ip입니다.

Azure [Cloud Shell](https://shell.azure.com/bash) 또는 로컬 Azure CLI 설치에서 스크립트를 실행할 수 있습니다. CLI를 로컬로 사용하는 경우 이 스크립트는 2.0.28 이상 버전을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건
Azure 가상 네트워크 기능에 대 한 IPv6를 사용 하려면 다음과 같은 구독에 한 번만 구성 해야 합니다.

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

## <a name="sample-script"></a>샘플 스크립트


```azurecli
# Create a resource group
az group create \
--name DsResourceGroup01 \
--location eastus

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

# Create public IP addresses for remote access to VMs
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create load balancer
az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4

# Create IPv6 front-end
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

# Configure IPv6 back-end address pool
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01

# Create a load balancer rule

az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

# Create an availability set
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2

# Create network security group

az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

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
--source-port-ranges 3389  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"

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
--address-prefix 10.0.0.0/24 \
--address-prefix "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1

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
--lb-address-pools dsLbBackEndPool_v6 
--lb-name dsLB

# Create virtual machines
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  

az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest

```
## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name <resourcegroupname> --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | 고정 IP 주소 및 연결된 DNS 이름을 사용하여 공용 IP 주소를 만듭니다. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Azure Load Balancer를 만듭니다. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | 부하 분산 장치 프로브를 만듭니다. 부하 분산 장치 프로브는 부하 분산 장치 집합의 각 VM을 모니터링하는 데 사용됩니다. 모든 VM이 액세스할 수 없게 되면 트래픽은 VM에 라우팅되지 않습니다. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | 부하 분산 장치 규칙을 만듭니다. 이 샘플에서는 포트 80에 대한 규칙을 만듭니다. HTTP 트래픽이 부하 분산 장치에 도착하면 LB 집합에서 VM 중 하나인 포트 80에 라우팅됩니다. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | 부하 분산 장치 NAT(네트워크 주소 변환) 규칙을 만듭니다.  NAT 규칙은 VM에 있는 포트에 부하 분산 장치의 포트를 매핑합니다. 이 샘플에서는 부하 분산 장치 집합의 각 VM에 SSH 트래픽에 대한 NAT 규칙을 만듭니다.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | 인터넷과 가상 컴퓨터 간에 보안 경계인 NSG(네트워크 보안 그룹)을 만듭니다. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | 인바운드 트래픽을 허용하도록 NSG 규칙을 만듭니다. 이 샘플에서 SSH 트래픽에 대해 포트 22가 열립니다. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | 가상 네트워크 카드를 만들고 가상 네트워크, 서브넷 및 NSG에 연결합니다. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | 가용성 집합을 만듭니다. 가용성 집합을 사용하면 오류가 발생하는 경우 물리적 리소스 간에 가상 머신을 분산하여 애플리케이션 가동 시간을 확인합니다. 전체 집합은 영향을 받지 않습니다. |
| [az vm create](/cli/azure/vm#az-vm-create) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure 네트워킹 CLI 스크립트 샘플은 [Azure 네트워킹 설명서](../cli-samples.md)에서 찾을 수 있습니다.
