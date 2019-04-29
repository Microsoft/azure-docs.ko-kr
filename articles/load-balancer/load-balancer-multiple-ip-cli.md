---
title: Azure CLI를 사용하여 여러 IP 구성의 부하 분산
titlesuffix: Azure Load Balancer
description: Azure CLI를 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1e8911847a555e3b6326f15d15a09344a4472f2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860558"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Azure CLI를 사용하여 여러 IP 구성의 부하 분산

이 문서에서는 보조 NIC(네트워크 인터페이스)에 여러 IP 주소가 있는 Azure Load Balancer를 사용하는 방법을 설명합니다. 이 시나리오에는 Windows를 실행하는 VM이 둘 있고 각각 기본 및 보조 NIC가 있습니다. 각 보조 NIC에는 두 가지 IP 구성이 있습니다. 각 VM은 contoso.com 및 fabrikam.com 웹 사이트를 둘 다 호스트합니다. 각 웹 사이트는 보조 NIC의 IP 구성 중 하나에 바인딩됩니다. Azure Load Balancer를 사용하여 웹 사이트의 각 IP 구성에 트래픽을 분산하기 위해 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출합니다. 이 시나리오는 양 쪽 프런트 엔드는 물론 양쪽 백 엔드 풀 IP 주소에 동일한 포트 번호를 사용합니다.

![LB 시나리오 이미지](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>여러 IP 구성의 부하를 분산하는 단계

이 문서에 설명된 시나리오를 수행하려면 다음 단계에 완료하세요.

1. 연결된 문서의 단계에 따라 [Azure CLI를 설치 및 구성](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)하고 Azure 계정에 로그인합니다.
2. 다음과 같이 *contosofabrikam*이라는 [리소스 그룹을 만듭니다](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group).

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. 두 개의 VM에 대한 [가용성 집합을 만듭니다](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set). 이 시나리오에서는 다음 명령을 사용합니다.

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. *myVNet*이라는 [가상 네트워크를 만들고](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) *mySubnet*이라는 서브넷을 만듭니다.

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. *mylb*라는 [부하 분산 장치를 만듭니다](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. 부하 분산 장치의 프런트 엔드 IP 구성에 대해 두 개의 동적 공용 IP 주소를 만듭니다.

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. 각각 *contosofe* 및 *fabrikamfe*라는 두 개의 프런트 엔드 IP 구성을 만듭니다.

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. *contosopool* 및 *fabrikampool*이라는 백 엔드 주소 풀과 [프로브](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*를 만들고, *HTTPc* 및 *HTTPf*라는 부하 분산 규칙을 만듭니다.

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. 다음 명령을 실행하고 출력을 확인하여 [Load Balancer가 제대로 만들어졌는지 확인](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다.

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. 다음과 같이 VM1이라는 첫 번째 가상 머신에 대해 *myPublicIp*라는 [공용 IP를 만들고](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *mystorageaccont1*이라는 [스토리지 계정을 만듭니다](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. 다음과 같이 VM1에 대해 [네트워크 인터페이스를 만들고](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) *VM1-ipconfig2*라는 두 번째 IP 구성을 추가하고 [VM을 만듭니다](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm).

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. 두 번째 VM에 대해 10 ~ 11단계를 반복합니다.

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. 마지막으로 DNS 리소스 레코드가 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리키도록 구성해야 합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure에서 부하 분산 서비스를 결합하는 방법에 대한 자세한 내용은 [Azure에서 부하 분산 서비스 사용](../traffic-manager/traffic-manager-load-balancing-azure.md)을 참조하세요.
- [Azure Load Balancer에 대한 로그 분석](../load-balancer/load-balancer-monitor-log.md)을 통해 Azure에서 부하 분산 장치를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용하는 방법에 대해 알아보세요.
