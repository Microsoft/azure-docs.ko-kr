---
title: '빠른 시작: 공용 부하 분산 장치 만들기 - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 부하 분산 장치를 만드는 방법을 보여 줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 3589aeb21053525e481f3448270d236265dd698e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052037"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기

Azure PowerShell에서 Azure Load Balancer를 시작하여 공용 부하 분산 장치와 세 개의 가상 머신을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

* 이름: **myResourceGroupLB**
* 위치: **eastus**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 다음을 수행합니다.

* **myPublicIP**라고 명명된 표준 영역 중복 공용 IP 주소를 만듭니다.
* 위치: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

영역 1에서 공용 IP 주소를 만들려면 다음 명령을 사용합니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-frontend-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 프런트 엔드 IP를 만듭니다.

* 이름: **myFrontEnd**
* 공용 IP **myPublicIP**에 연결됨

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 다음과 같은 백 엔드 주소 풀을 만듭니다. 

* 이름: **myBackEndPool**
* VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 다음과 같은 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 다음과 같은 부하 분산 장치 규칙을 만듭니다. 

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd**의 **포트 80**에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool**에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe**를 사용합니다.
* 프로토콜: **TCP**

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```

### <a name="create-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 다음과 같은 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* 위치: **eastus**
* 리소스 그룹: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 다음과 같은 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* 리소스 그룹: **myResourceGroupLB**
* 서브넷: **myBackendSubnet**
* 가상 네트워크: **10.0.0.0/16**
* 서브넷: **10.0.0.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기
가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 다음과 같은 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* **HTTP 허용**에 대한 설명
* 액세스 **허용**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 우선 순위 **2000**
* **인터넷** 원본
* **(*)** 의 원본 포트 범위
* **(*)** 의 대상 주소 접두사
* 대상 **포트 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 다음과 같은 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 이전 단계에서 생성된 보안 규칙을 변수에 저장합니다.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>네트워크 인터페이스 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 다음과 같은 세 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm-1"></a>VM 1

* 이름: **myNicVM1**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 이름: **myNicVM2**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 이름: **myNicVM3**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = Get-Credential
```

다음과 같은 가상 머신을 만듭니다.

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 이름: **myVM1**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* **영역 1**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* 이름: **myVM2**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* **영역 2**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* 이름: **myVM3**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM3**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* **영역 3**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>아웃바운드 규칙 구성 만들기
부하 분산 장치 아웃바운드 규칙은 백 엔드 풀의 VM에 대한 아웃바운드 SNAT(Source Network Address Translation)를 구성합니다. 

아웃바운드 연결에 대한 자세한 내용은 [Azure의 아웃바운드 연결](load-balancer-outbound-connections.md)을 참조하세요.

### <a name="create-outbound-public-ip-address"></a>아웃바운드 공용 IP 주소 만들기

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 다음을 수행합니다.

* **myPublicIPOutbound**라고 명명된 표준 영역 중복 공용 IP 주소를 만듭니다.
* 위치: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

영역 1에서 공용 IP 주소를 만들려면 다음 명령을 사용합니다.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>아웃바운드 프런트 엔드 IP 구성 만들기

[Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 새 프런트 엔드 IP 구성을 만듭니다.

* 이름: **myFrontEndOutbound**
* 공용 IP 주소 **myPublicIPOutbound**에 연결됩니다.

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'
$lbn = 'myLoadBalancer'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>아웃바운드 풀 만들기

[Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig)를 사용하여 새 아웃바운드 풀을 만듭니다. 

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)를 사용하여 부하 분산 장치에 다음과 같은 풀 및 프런트 엔드 IP 주소를 적용합니다.

* 이름: **myBackEndPoolOutbound**

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>아웃바운드 규칙을 만들고 부하 분산 장치에 적용

[Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig)를 사용하여 아웃바운드 백 엔드 풀에 대한 새 아웃바운드 규칙을 만듭니다. 

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)를 사용하여 부하 분산 장치에 다음 규칙을 적용합니다.

* 이름: **myOutboundRule**
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨
* 프런트 엔드 **myFrontEndOutbound**에 연결됨
* 프로토콜: **모두**
* 유휴 시간 제한: **15**
* 아웃바운드 포트: **10000**
* 백 엔드 풀 **myBackEndPoolOutbound**에 연결됨
* 리소스 그룹: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>아웃바운드 풀에 가상 머신 추가

[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)를 사용하여 부하 분산 장치의 아웃바운드 풀에 다음과 같은 가상 머신 네트워크 인터페이스를 추가합니다.


#### <a name="vm1"></a>VM1
* 백 엔드 주소 풀: **myBackEndPoolOutbound**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* 백 엔드 주소 풀: **myBackEndPoolOutbound**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* 백 엔드 주소 풀: **myBackEndPoolOutbound**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM3** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

인터넷에서 웹앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. 

[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 다음을 수행합니다.

* **myPublicIP**라고 명명된 표준 영역 중복 공용 IP 주소를 만듭니다.
* 위치: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-frontend-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 프런트 엔드 IP를 만듭니다.

* 이름: **myFrontEnd**
* 공용 IP **myPublicIP**에 연결됨

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 다음과 같은 백 엔드 주소 풀을 만듭니다. 

* 이름: **myBackEndPool**
* VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 다음과 같은 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 다음과 같은 부하 분산 장치 규칙을 만듭니다. 

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd**의 **포트 80**에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool**에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe**를 사용합니다.
* 프로토콜: **TCP**
* 프런트 엔드 IP 주소를 사용하여 아웃바운드 SNAT(Source Network Address Translation)을 사용하도록 설정합니다.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 다음과 같은 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* 위치: **eastus**
* 리소스 그룹: **myResourceGroupLB**

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 가상 네트워크 리소스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 다음과 같은 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* 리소스 그룹: **myResourceGroupLB**
* 서브넷: **myBackendSubnet**
* 가상 네트워크: **10.0.0.0/16**
* 서브넷: **10.0.0.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기
가상 네트워크에 대한 인바운드 연결을 정의하는 네트워크 보안 그룹을 만듭니다. 네트워크 보안 그룹을 만들어 가상 네트워크에 대한 인바운드 연결을 정의합니다.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 다음과 같은 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* **HTTP 허용**에 대한 설명
* 액세스 **허용**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 우선 순위 **2000**
* **인터넷** 원본
* **(*)** 의 원본 포트 범위
* **(*)** 의 대상 주소 접두사
* 대상 **포트 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 다음과 같은 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 이전 단계에서 생성된 보안 규칙을 변수에 저장합니다.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>네트워크 인터페이스 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 다음과 같은 세 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm-1"></a>VM 1

* 이름: **myNicVM1**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 이름: **myNicVM2**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 이름: **myNicVM3**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**
* **myBackEndPool**의 **myLoadBalancer** 부하 분산 장치에 연결됨

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>가상 머신에 대한 가용성 집합 만들기

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm)를 사용하여 다음과 같은 가상 머신에 대한 가용성 집합을 만듭니다.

* 이름: **myAvSet**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell
$cred = Get-Credential
```

다음과 같은 가상 머신을 만듭니다.

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 이름: **myVM1**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* 위치: **eastus**
* 가용성 집합: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* 이름: **myVM2**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* 위치: **eastus**
* 가용성 집합: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* 이름: **myVM3**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM3**에 연결됨
* 부하 분산 장치 **myLoadBalancer**에 연결됨
* 위치: **eastus**
* 가용성 집합: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

세 VM을 만들고 구성하는 데 몇 분 정도 소요됩니다.

---

## <a name="install-iis"></a>IIS 설치

[Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 

확장은 PowerShell Add-WindowsFeature Web-Server를 실행하여 IIS 웹 서버를 설치한 다음, Default.htm 페이지를 업데이트하여 VM의 호스트 이름을 표시합니다.

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm3"></a>VM3

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM3'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
  ## Variables for command. ##
  $rg = 'myResourceGroupLB'
  $ipn = 'myPublicIP'
    
  Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

   ![IIS 웹 서버](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

세 개의 VM 모두에서 부하 분산 장치 분산 트래픽을 확인하려면 각 VM의 IIS 웹 서버의 기본 페이지를 사용자 지정한 다음, 클라이언트 컴퓨터에서 웹 브라우저를 강제로 새로 고칠 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 나머지 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 수행한 작업

* 표준 또는 기본 공용 부하 분산 장치를 만들었습니다.
* 가상 머신을 연결했습니다. 
* 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성했습니다.
* 부하 분산 장치를 테스트했습니다.

Azure Load Balancer에 대해 자세히 알아보려면 [Azure Load Balancer란?](load-balancer-overview.md) 및 [Load Balancer 질문과 대답](load-balancer-faqs.md)으로 계속 진행하세요.

* [Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)에 대해 자세히 알아봅니다.


