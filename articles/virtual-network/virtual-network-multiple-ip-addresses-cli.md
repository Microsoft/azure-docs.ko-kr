---
title: Azure CLI를 사용해 여러 IP 주소가 있는 VM
titlesuffix: Azure Virtual Network
description: Azure CLI(명령줄 인터페이스)를 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: b693500e785d41b2ad3339e26dd9fd3505891bc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742285"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Azure CLI를 사용하여 가상 머신에 여러 IP 주소 할당

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

이 문서는 Azure CLI를 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 컴퓨터)을 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

다음 단계는 시나리오에 설명된 대로 여러 IP 주소를 가진 예시 가상 머신을 만드는 방법을 설명합니다. ""의 변수 값과 IP 주소 유형을 구현에 필요한 대로 변경합니다. 

1. [Azure CLI](/cli/azure/install-azure-cli)를 아직 설치하지 않은 경우 설치합니다.
2. [Linux VM에 SSH 공용 및 개인 키 쌍 만들기](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 완료하여 Linux VM에 SSH 공용 및 개인 키 쌍을 만듭니다.
3. 명령 셸에서 `az login` 명령을 사용하고 사용 중인 구독을 선택합니다.
4. Linux 또는 Mac 컴퓨터에서 다음에 나오는 스크립트를 실행하여 VM을 만듭니다. 스크립트는 리소스 그룹, 하나의 VNet(가상 네트워크), 세 개의 IP 구성이 있는 하나의 NIC 및 연결된 두 개의 NIC가 있는 VM을 만듭니다. NIC, 공용 IP 주소, 가상 네트워크 및 VM 리소스는 모두 동일한 위치 및 구독에 있어야 합니다. 리소스가 모두 동일한 리소스 그룹에 위치할 필요는 없습니다. 하지만 다음 스크립트에서는 모두 동일한 리소스 그룹에 위치합니다.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

스크립트는 세 가지 IP 구성의 NIC로 VM을 만드는 것 외에 다음 항목을 만듭니다.

- 기본적으로 단일 프리미엄이 디스크를 관리했지만 만들 수 있는 디스크 유형에 대한 다른 옵션을 사용할 수 있습니다. 자세한 내용은 [Azure CLI를 사용하여 Linux VM 만들기](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 하나의 서브넷 및 두 개의 공용 IP 주소를 가진 가상 네트워크. 또는 *기존* 가상 네트워크, 서브넷, NIC 또는 공용 IP 주소 리소스를 사용할 수 있습니다. 추가 리소스를 만드는 것이 아니라 기존 네트워크 리소스를 사용하는 방법을 알아보려면 `az vm create -h`을 입력합니다.

공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

VM을 만든 후에 `az network nic show --name MyNic1 --resource-group myResourceGroup` 명령을 입력하여 NIC 구성을 봅니다. `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table`을 입력하여 NIC에 연결된 IP 구성의 목록을 봅니다.

이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다.

## <a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 기존 Azure 네트워크 인터페이스에 더 추가할 수 있습니다. 예제는 이 문서에서 설명된 [시나리오](#scenario)를 기반으로 합니다.

1. 명령 셸을 열고 단일 세션 내에서 이 섹션의 나머지 단계를 완료합니다. 아직 Azure CLI를 설치 및 구성하지 않은 경우 [Azure CLI 설치](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서에 나오는 단계를 완료하고 `az-login` 명령을 사용하여 Azure 계정에 로그인합니다.

2. 요구 사항에 따라 다음 섹션 중 하나의 단계를 완료합니다.

    **개인 IP 주소 추가**
    
    NIC에 개인 IP 주소를 추가하려면 다음 명령을 사용하여 IP 구성을 만들어야 합니다. 고정 IP 주소는 서브넷에 사용되지 않는 주소여야 합니다.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    고유한 구성 이름과 개인 IP 주소를 사용하여 필요한 만큼 구성을 만듭니다(정적 IP 주소를 가진 구성의 경우).

    **공용 IP 주소 추가**
    
    공용 IP 주소는 새 IP 구성 또는 기존 IP 구성에 연결하면 해당 주소가 추가됩니다. 필요에 따라 이후 섹션 중 하나에 나와 있는 단계를 완료합니다.

    공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

    - **새 IP 구성에 리소스 연결**
    
        새 IP 구성의 공용 IP 주소를 추가할 때마다 모든 IP 구성 시 개인 IP 주소가 있어야 하기 때문에 개인 IP 주소도 추가해야 합니다. 기존 공용 IP 주소 리소스를 추가하거나 새로 만들 수 있습니다. 새 파일을 만들려면 다음 명령을 입력합니다.
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        고정 개인 IP 주소 및 여기에 연결된 *myPublicP3* 공용 IP 주소 리소스가 있는 새 IP 구성을 만들려면 다음 명령을 입력합니다.

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **기존 IP 구성에 리소스 연결** 공용 IP 주소 리소스는 아직 연결한 리소스가 없는 IP 구성에만 연결될 수 있습니다. 다음 명령을 입력하면 IP 구성에 연결된 공용 IP 주소가 있는지 여부를 확인할 수 있습니다.

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        반환되는 출력:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        *IpConfig-3*에 대한 **PublicIpAddressId** 열이 출력에서 비어 있기 때문에 현재 공용 IP 주소 리소스가 여기에 연결되어 있지 않습니다. IpConfig-3에 기존 공용 IP 주소 리소스를 추가하거나 다음 명령을 입력하여 새로 만들 수 있습니다.

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        *IPConfig 3*이라는 기존 IP 구성에 공용 IP 주소 리소스를 연결하려면 다음 명령을 입력합니다.
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스 ID를 보려면 다음 명령을 입력합니다.

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    반환되는 출력: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 지침에 따라 NIC에 추가한 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마십시오.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
