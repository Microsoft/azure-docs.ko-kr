---
title: Azure에서 표준 내부 Load Balancer를 사용 하 여 IPv6 이중 스택 응용 프로그램 배포-PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 azure Powershell을 사용 하 여 Azure virtual network에서 표준 내부 Load Balancer를 사용 하 여 IPv6 이중 스택 응용 프로그램을 배포 하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333366"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Azure에서 표준 내부 Load Balancer를 사용 하 여 IPv6 이중 스택 응용 프로그램 배포-PowerShell (미리 보기)

이 문서에서는 이중 스택을 포함 하는 이중 스택 (IPv4 + IPv6) 응용 프로그램을 Azure에 배포 하는 방법을 보여 줍니다. 이중 (IPv4 + IPv6) 프런트 엔드 구성을 사용 하는 표준 내부 Load Balancer, 이중 IP가 있는 Nic가 있는 Vm 구성, 네트워크 보안 그룹 및 공용 Ip를 구성 합니다.

> [!Important]
> Azure Virtual Network에 대 한 IPv6 지원은 현재 공개 미리 보기 상태입니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IPv6 지원 내부 Load Balancer를 만드는 절차는 [여기](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)에 설명 된 인터넷 연결 IPv6 Load Balancer를 만드는 프로세스와 거의 동일 합니다. 내부 부하 분산 장치를 만들기 위한 유일한 차이점은 아래 PowerShell 예제에 설명 된 대로 프런트 엔드 구성에 있습니다.

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

위에서 내부 부하 분산 장치 프런트 엔드 구성을 수행 하는 변경 내용은 다음과 같습니다.
- @No__t-0은 "IPv6"로 지정 됩니다.
- @No__t-0 인수가 생략 되었거나 `-PrivateIpAddress`로 대체 되었습니다. 개인 주소는 내부 부하 분산 장치가 배포 될 서브넷 IP 공간의 범위 내에 있어야 합니다. 정적 `-PrivateIpAddress`이 생략 된 경우 내부 부하 분산 장치가 배포 된 서브넷에서 다음 사용 가능한 IPv6 주소가 선택 됩니다.
- 내부 부하 분산 장치가 배포 되는 이중 스택 서브넷은 `-Subnet` 또는 `-SubnetId` 인수를 사용 하 여 지정 됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="prerequisites"></a>전제 조건
Azure에서 이중 스택 응용 프로그램을 배포 하기 전에 다음 Azure PowerShell를 사용 하 여이 미리 보기 기능에 대 한 구독을 구성 해야 합니다.

다음과 같이 등록 합니다.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 Azure PowerShell 명령을 실행 하 여 등록 상태를 확인할 수 있습니다. 다음과 같이 등록을 확인 합니다.
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
등록이 완료되면 다음 명령을 실행합니다.

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이중 스택 가상 네트워크를 만들려면 먼저 [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)를 사용 하 여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *미국 동부* 위치에 *dsStd_ILB_RG* 이라는 리소스 그룹을 만듭니다.

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 가상 컴퓨터에 액세스 하려면 Vm에 대 한 IPv4 및 IPv6 공용 IP 주소가 필요 합니다. [AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용 하 여 공용 IP 주소를 만듭니다. 다음 예제에서는 *dsStd_ILB_RG* 리소스 그룹에 *RdpPublicIP_1* 및 *RdpPublicIP_2* 라는 IPv4 및 IPv6 공용 IP 주소를 만듭니다.

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>가상 네트워크 및 서브넷 만들기

[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 를 사용 하 여 가상 네트워크 만들기- [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)을 사용 하 여 서브넷 구성 이중 스택 구성. 다음 예제에서는 *Dsvnet*을 사용 하 여 *dsvnet* 이라는 가상 네트워크를 만듭니다.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>표준 Load Balancer 만들기

이 섹션에서는 부하 분산 장치에 대 한 이중 프런트 엔드 IP (IPv4 및 IPv6) 및 백 엔드 주소 풀을 구성 하 고 표준 Load Balancer를 만듭니다.

### <a name="create-front-end-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 다음 예제에서는 *dsLbFrontEnd_v4* 및 *dsLbFrontEnd_v6*라는 IPv4 및 IPv6 프런트 엔드 IP 구성을 만듭니다.

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다. 다음 예제에서는 IPV4 및 IPv6 NIC 구성을 모두 사용 하 여 Vm을 포함 하는 *dsLbBackEndPool_v4* 및 *dsLbBackEndPool_v6* 라는 백 엔드 주소 풀을 만듭니다.

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 Vm만 트래픽을 수신할 수 있도록 하려면 상태 프로브를 선택적으로 정의할 수 있습니다. 기본 부하 분산 장치는 IPv4 프로브를 사용 하 여 Vm의 IPv4 및 IPv6 엔드포인트 모두에 대 한 상태를 평가 합니다. 표준 부하 분산 장치에는 명시적 IPv6 상태 프로브에 대 한 지원이 포함 됩니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 *dsLBrule_v4* 및 *dsLBrule_v6* 이라는 부하 분산 장치 규칙을 만들고 *TCP* 포트 *80* 의 트래픽을 IPv4 및 IPv6 프런트 엔드 IP 구성으로 분산 합니다.

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용 하 여 표준 Load Balancer를 만듭니다. 다음 예제에서는 이전 단계에서 만든 IPv4 및 IPv6 프런트 엔드 IP 구성, 백 엔드 풀 및 부하 분산 규칙을 사용 하 여 *Myinternalloadbalancer* 라는 공용 표준 Load Balancer를 만듭니다.

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
일부 Vm을 배포 하 고 부하 분산 장치를 테스트 하려면 먼저 지원 되는 네트워크 리소스-가용성 집합, 네트워크 보안 그룹 및 가상 Nic를 만들어야 합니다. 

### <a name="create-an-availability-set"></a>가용성 집합 만들기
응용 프로그램의 고가용성을 향상 시키려면 Vm을 가용성 집합에 저장 합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예에서는 *dsavset*이라는 가용성 집합을 만듭니다.

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNet에서 인바운드 및 아웃 바운드 통신을 제어 하는 규칙에 대 한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>포트 3389에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통해 RDP 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기

80 포트를 통한 인터넷 연결을 허용 하는 네트워크 보안 그룹 규칙을 만듭니다. [AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>NIC 만들기

[AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용 하 여 가상 nic를 만듭니다. 다음 예제에서는 IPv4 및 IPv6 구성을 사용 하 여 두 개의 가상 Nic를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩)

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기
다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에서 *Dsvnet*을 입력 합니다.
2. **Dsvnet** 이 검색 결과에 표시 되 면 선택 합니다. 그러면 *Dsvnet*이라는 이중 스택 가상 네트워크의 **개요** 페이지가 시작 됩니다. 이중 스택 가상 네트워크는 *Dssubnet*이라는 이중 스택 서브넷에 있는 IPv4 및 IPv6 구성을 모두 사용 하 여 두 개의 nic를 표시 합니다.

![표준 내부 Load Balancer를 사용 하는 IPv6 이중 스택 Virtual Network](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Azure virtual network에 대 한 IPv6은이 미리 보기 릴리스에 대 한 읽기 전용 Azure Portal에서 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성 (IPv4 및 IPv6)을 사용 하 여 표준 Load Balancer를 만들었습니다. 또한 부하 분산 장치의 백 엔드 풀에 추가 된 Nic를 포함 하는 두 개의 가상 머신 (IPV4 + IPv6)를 만들었습니다. Azure virtual network의 IPv6 지원에 대 한 자세한 내용은 [azure Virtual Network에 대 한](ipv6-overview.md) i p v 6을 참조 하세요.