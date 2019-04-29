---
title: Azure 가상 네트워크-PowerShell에서에서 IPv6 이중 스택 응용 프로그램 배포
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Powershell을 사용 하 여 Azure 가상 네트워크 IPv6 이중 스택 응용 프로그램을 배포 하는 방법입니다.
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
ms.openlocfilehash: f26391e36e3208996160fffad01e39ec2f182318
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130973"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Azure PowerShell (미리 보기)에서 IPv6 이중 스택 응용 프로그램 배포

이 문서는 이중 스택 가상 네트워크 및 서브넷, 이중 (i p v 4 + IPv6) 프런트 엔드 구성 된 이중 IP 구성이 nic가 있는 Vm 사용 하 여 부하 분산 장치를 포함 하는 Azure에서 이중 스택 (i p v 4 + IPv6) 응용 프로그램을 배포 하는 방법을 보여 줍니다. 네트워크 보안 그룹 및 공용 Ip입니다.

> [!Important]
> IPv6 Azure Virtual Network에 대 한 지원은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

이 문서에는 Azure PowerShell 모듈 버전 6.9.0 설치 하 고 PowerShell을 로컬로 사용 하려는 경우 이상. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="prerequisites"></a>필수 조건
이중 스택 응용 프로그램을 Azure에 배포 하기 전에 다음 Azure PowerShell을 사용 하 여이 미리 보기 기능에 대 한 구독을 구성 해야 합니다.

다음과 같이 등록 합니다.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 Azure PowerShell 명령을 실행 하 여 등록 상태를 확인할 수 있습니다. 등록 시 다음과 같이 확인 합니다.
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
등록이 완료되면 다음 명령을 실행합니다.

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 사용 하 여 이중 스택 가상 네트워크를 만들려면 먼저 만들어야 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다. 다음 예제에서는 명명 된 리소스 그룹을 만듭니다 *myRGDualStack* 에 *동부 미국* 위치:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 가상 컴퓨터에 액세스 하려면 부하 분산 장치에 대 한 IPv4 및 IPv6 공용 IP 주소를 필요 합니다. 사용 하 여 공용 IP 주소 만들기 [새로 만들기-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)합니다. 다음 예제에서는 IPv4 및 IPv6 라는 공용 IP 주소를 만듭니다 *dsPublicIP_v4* 하 고 *dsPublicIP_v6* 에 *dsRG1* 리소스 그룹:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
RDP 연결을 사용 하 여 가상 컴퓨터에 액세스 하려면 사용 하 여 가상 머신에 대 한 IPV4 공용 IP 주소를 만듭니다 [새로 만들기-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)합니다.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 이중 프런트 엔드 IP (예: IPv4 및 IPv6) 및 부하 분산 장치의 백 엔드 주소 풀을 구성 하 고 기본 부하 분산 장치를 만듭니다.

### <a name="create-front-end-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 다음 예제에서는 IPv4 및 IPv6 프런트 엔드 IP 구성 *dsLbFrontEnd_v4* 하 고 *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다. 다음 예에서는 이라는 백 엔드 주소 풀을 만듭니다 *dsLbBackEndPool_v4* 하 고 *dsLbBackEndPool_v6* IPV4와 IPv6 NIC 구성으로 Vm을 포함 하려면:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 되었는지 확인 하려면 Vm 트래픽을 수신, 필요에 따라 상태 프로브를 정의할 수 있습니다. 기본 load balancer는 IPv4 프로브를 사용 하 여 Vm에서 IPv4 및 IPv6 끝점에 대 한 상태를 평가 합니다. 표준 load balancer에는 명시적으로 IPv6 상태 프로브에 대 한 지원이 포함 됩니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 라는 부하 분산 장치 규칙을 만듭니다 *dsLBrule_v4* 하 고 *dsLBrule_v6* 에 트래픽을 분산 *TCP* 포트 *80* 를 IPv4 및 IPv6 프런트 엔드 IP 구성:

```azurepowershell-interactive
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

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 기본 Load Balancer를 만듭니다. 다음 예제에서는 공용 기본 부하 분산 장치를 명명 된 *myLoadBalancer* 에서 만든 규칙을 IPv4 및 IPv6 프런트 엔드 IP 구성, 백 엔드 풀, 상태 프로브, 부하 분산 규칙 및 NAT를 사용 하 여 이전 단계:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
일부 Vm을 배포 하 여 분산을 테스트할 수 만들어야 지원 네트워크 리소스가-가용성 집합, 네트워크 보안 그룹, 가상 네트워크 및 가상 Nic입니다. 
### <a name="create-an-availability-set"></a>가용성 집합 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet*이라는 가용성 집합을 만듭니다.

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNET에서 인바운드 및 아웃 바운드 통신을 제어 하는 하는 규칙에 대 한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>포트 3389에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통해 RDP 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
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

사용 하 여 포트 80 통해 인터넷 연결을 허용 하도록 네트워크 보안 그룹 규칙을 만듭니다 [새로 만들기-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)합니다.

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*을 만듭니다.

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC 만들기

사용 하 여 가상 Nic를 만듭니다 [새로 만들기-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)합니다. 다음 예제에서는 IPv4 및 IPv6 구성을 사용 하 여 두 개의 가상 nic가 둘 다를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩)

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IPv4 및 IPv6 끝점의 IP 주소를 확인 합니다.
사용 하 여이 배포에 사용 되는 IP의 요약을 리소스 그룹에서 네트워크 인터페이스 개체를 모두 가져옵니다 `get-AzNetworkInterface`합니다. 또한 IPv4 및 IPv6 끝점이 부하 분산 장치의 프런트 엔드 주소를 가져올 `get-AzpublicIpAddress`합니다.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
다음 그림에는 두 Vm의 개인 IPv4 및 IPv6 주소 및 부하 분산 장치의 프런트 엔드 IPv4 및 IPv6 IP 주소를 나열 하는 샘플 출력을 보여 줍니다.

![Azure에서 이중 스택 (IPv4/i p v 6) 응용 프로그램 배포의 IP 요약](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal에서 IPv6 이중 스택 가상 네트워크를 보려면
Azure portal에서 다음과 같이 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 표시줄에 입력 *dsVnet*합니다.
2. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다. 그러면 합니다 **개요** 이중 스택 가상 네트워크의 페이지 *dsVnet*합니다. 이중 스택 가상 네트워크 IPv4 및 IPv6 둘 다 구성 이라는 이중 스택 서브넷에 있는 두 개의 Nic를 보여 줍니다 *dsSubnet*합니다.

  ![Azure에서 IPv6 이중 스택 virtual network](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure 가상 네트워크에 대 한 IPv6는 Azure portal에서 사용할 수 있는이 미리 보기 릴리스에 대 한 읽기 전용입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성 (IPv4 및 IPv6)를 사용 하 여 기본 부하 분산 장치를 만들었습니다. 또한 부하 분산 장치의 백 엔드 풀에 추가 된 이중 IP 구성 (i p v 4 + IPv6)를 사용 하 여 Nic를 포함 하는 두 개의 가상 머신을 만들었습니다. Azure 가상 네트워크에서 IPv6 지원에 대 한 자세한 내용은를 참조 하세요. [IPv6에 대 한 Azure Virtual Network 란?](ipv6-overview.md)