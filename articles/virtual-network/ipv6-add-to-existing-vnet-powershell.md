---
title: Azure Virtual Network에서 IPv6로 IPv4 응용 프로그램 업그레이드-PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Powershell을 사용 하 여 Azure 가상 네트워크의 기존 응용 프로그램에 IPv6 주소를 배포 하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f2ff81c1f1989e28fa48e4307a13433a7b98e915
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051052"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Azure virtual network에서 IPv4 응용 프로그램을 IPv6로 업그레이드-PowerShell

이 문서에서는 표준 Load Balancer 및 공용 IP를 사용 하 여 Azure 가상 네트워크의 기존 IPv4 응용 프로그램에 IPv6 연결을 추가 하는 방법을 보여 줍니다. 전체 업그레이드에는 다음이 포함 됩니다.
- 가상 네트워크 및 서브넷에 대 한 IPv6 주소 공간
- IPv4 및 IPV6 프런트 엔드 구성을 모두 사용 하는 표준 Load Balancer
- IPv4 + IPv6 구성이 모두 포함 된 Nic가 있는 Vm
- 부하 분산 장치에서 인터넷 연결 IPv6 연결을 위한 IPv6 공용 IP



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [빠른 시작: 표준 Load Balancer Azure PowerShell 만들기](../load-balancer/quickstart-load-balancer-standard-public-powershell.md)에 설명 된 대로 표준 Load Balancer를 배포 했다고 가정 합니다.

## <a name="retrieve-the-resource-group"></a>리소스 그룹 검색

이중 스택 가상 네트워크를 만들려면 먼저 [AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)를 사용 하 여 리소스 그룹을 검색 해야 합니다.

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP 주소 만들기

표준 Load Balancer에 대 한 [AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 를 사용 하 여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *Myresourcegroupslb* 리소스 그룹에 *PublicIP_v6* 이라는 IPv6 공용 IP 주소를 만듭니다.

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>부하 분산 장치 프런트 엔드 구성

기존 부하 분산 장치 구성을 검색 한 후 다음과 같이 [AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) 를 사용 하 여 새 IPv6 IP 주소를 추가 합니다.

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀 구성

부하 분산 장치 구성의 로컬 복사본에 백 엔드 풀을 만들고 다음과 같이 새 백 엔드 풀 구성을 사용 하 여 실행 중인 부하 분산 장치를 업데이트 합니다.

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>부하 분산 장치 규칙 구성
기존 부하 분산 장치 프런트 엔드 및 백 엔드 풀 구성을 검색 한 다음 [AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)를 사용 하 여 새 부하 분산 규칙을 추가 합니다.

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 주소 범위 추가

다음과 같이 Vm을 호스트 하는 가상 네트워크 및 서브넷에 IPv6 주소 범위를 추가 합니다.

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>NIC에 IPv6 구성 추가

다음과 같이 [AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) 를 사용 하 여 IPv6 주소를 사용 하 여 모든 VM nic를 구성 합니다.

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기
다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에서 *Myvnet*을 입력 합니다.
2. 검색 결과에 **Myvnet** 이 표시 되 면 선택 합니다. 그러면 *Myvnet*이라는 이중 스택 가상 네트워크의 **개요** 페이지가 시작 됩니다. 이중 스택 가상 네트워크는 *Mysubnet*이라는 이중 스택 서브넷에 있는 IPv4 및 IPv6 구성을 모두 사용 하 여 세 개의 nic를 표시 합니다.

  ![Azure의 IPv6 이중 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용 하 여 기존 표준 Load Balancer를 이중 스택 (IPv4 및 IPv6) 구성으로 업데이트 했습니다. 또한 백 엔드 풀에 있는 Vm의 Nic와 이들을 호스트 하는 Virtual Network에 IPv6 구성을 추가 했습니다. Azure virtual network의 IPv6 지원에 대 한 자세한 내용은 [azure Virtual Network에 대 한](ipv6-overview.md) i p v 6을 참조 하세요.
