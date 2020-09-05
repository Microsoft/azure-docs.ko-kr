---
title: Azure PowerShell를 사용 하 여 Azure 방화벽 배포 및 구성
description: 이 문서에서는 Azure PowerShell를 사용 하 여 Azure 방화벽을 배포 하 고 구성 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071706"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 방화벽 배포 및 구성

아웃바운드 네트워크 액세스 제어는 전체 네트워크 보안 계획에서 중요한 부분입니다. 예를 들어 웹 사이트에 대한 액세스를 제한할 수 있습니다. 또는 액세스 가능한 아웃바운드 IP 주소 및 포트를 제한할 수 있습니다.

Azure 서브넷에서 아웃바운드 네트워크로의 액세스를 제어하는 한 가지 방법은 Azure Firewall입니다. Azure Firewall을 사용하면 다음을 구성할 수 있습니다.

* 서브넷에서 액세스할 수 있는 FQDN(정규화된 도메인 이름)을 정의하는 애플리케이션 규칙.
* 원본 주소, 프로토콜, 대상 포트 및 대상 주소를 정의하는 네트워크 규칙.

네트워크 트래픽은 서브넷 기본 게이트웨이처럼 방화벽에 네트워크 트래픽을 라우팅할 경우 구성된 방화벽 규칙에 종속됩니다.

이 문서에서는 쉽게 배포할 수 있도록 세 개의 서브넷이 있는 간소화 된 단일 VNet을 만듭니다. 프로덕션 배포의 경우 [허브 및 스포크 모델](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)이 권장되며 방화벽은 자체 VNet에 있습니다. 워크로드 서버는 하나 이상의 서브넷이 있는 동일한 지역에서 피어링된 VNet에 있습니다.

* **AzureFirewallSubnet** - 방화벽은 이 서브넷에 있습니다.
* **워크로드-SN** - 워크로드 서버는 이 서브넷에 있습니다. 이 서브넷의 네트워크 트래픽은 방화벽을 통해 이동합니다.
* **점프-SN** - The "점프" 서버는 이 서브넷에 있습니다. 점프 서버에는 원격 데스크톱을 사용하여 연결할 수 있는 공용 IP 주소가 있습니다. 여기에서 다음 워크로드 서버에 연결할 수 있습니다(다른 원격 데스크톱을 사용하여).

![자습서 네트워크 인프라](media/tutorial-firewall-rules-portal/Tutorial_network.png)

이 문서에서는 다음 방법을 설명합니다.


* 테스트 네트워크 환경 설정
* 방화벽 배포
* 기본 경로 만들기
* [www.google.com]\(www.google.com) 액세스를 허용하도록 애플리케이션 규칙 구성
* 외부 DNS 서버 액세스를 허용하도록 네트워크 규칙 구성
* 방화벽 테스트

원하는 경우 [Azure Portal](tutorial-firewall-deploy-portal.md)를 사용 하 여이 절차를 완료할 수 있습니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 절차를 수행 하려면 PowerShell을 로컬로 실행 해야 합니다. Azure PowerShell 모듈을 설치해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Connect-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="set-up-the-network"></a>네트워크 설정

먼저 방화벽 배포에 필요한 리소스를 포함하는 리소스 그룹을 만듭니다. 그런 다음, VNet, 서브넷 및 테스트 서버를 만듭니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹에는 배포에 대 한 모든 리소스가 포함 됩니다.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>VNet 만들기

이 가상 네트워크에는 세 개의 서브넷이 있습니다.

> [!NOTE]
> AzureFirewallSubnet 서브넷의 크기는 /26입니다. 서브넷 크기에 대한 자세한 내용은 [Azure Firewall FAQ](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)를 참조하세요.

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
이제 가상 네트워크를 만듭니다.

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

이제 점프 및 워크로드 가상 머신을 만들어 적절한 서브넷에 배치합니다.
메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

Srv 점프 가상 머신을 만듭니다.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

공용 IP 주소가 없는 워크 로드 가상 머신을 만듭니다.
메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>방화벽 배포

이제 가상 네트워크에 방화벽을 배포 합니다.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

개인 IP 주소를 참고합니다. 기본 경로를 만들 때 나중에 사용할 수 있습니다.

## <a name="create-a-default-route"></a>기본 경로 만들기

BGP 경로 전파를 사용 하지 않도록 설정 하 여 테이블을 만듭니다.

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>애플리케이션 규칙 구성

응용 프로그램 규칙은 www.google.com에 대 한 아웃 바운드 액세스를 허용 합니다.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall은 기본적으로 허용되는 인프라 FQDN에 대한 기본 제공 규칙 컬렉션을 포함합니다. 이러한 FQDN은 플랫폼에 대해 특정적이며 다른 용도로 사용할 수 없습니다. 자세한 내용은 [인프라 FQDN](infrastructure-fqdns.md)을 참조하세요.

## <a name="configure-a-network-rule"></a>네트워크 규칙 구성

네트워크 규칙은 포트 53 (DNS)에서 두 개의 IP 주소에 대 한 아웃 바운드 액세스를 허용 합니다.

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>**Srv-Work** 네트워크 인터페이스에 대해 기본 및 보조 DNS 주소 변경

이 절차에서 테스트를 위해 서버의 기본 및 보조 DNS 주소를 구성 합니다. 일반적인 Azure Firewall 요구 사항이 아닙니다.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>방화벽 테스트

이제 방화벽이 예상대로 작동하는지 테스트합니다.

1. **Srv 작업** 가상 머신의 개인 IP 주소를 적어둡니다.

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. 원격 데스크톱을 **Srv-Jump** 가상 머신과 연결하고 로그인합니다. 여기에서 **Srv-Work** 개인 IP 주소에 대 한 원격 데스크톱 연결을 열고 로그인 합니다.

3. **SRV 작업**에서 PowerShell 창을 열고 다음 명령을 실행 합니다.

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   두 명령 모두 응답을 반환 해야 DNS 쿼리가 방화벽을 통해 수신 하 고 있음을 보여 줍니다.

1. 다음 명령을 실행합니다.

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com`요청이 성공 하 고 `www.microsoft.com` 요청이 실패 합니다. 이는 방화벽 규칙이 예상 대로 작동 하 고 있음을 보여 줍니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

* 구성된 외부 DNS 서버를 사용하여 DNS 이름을 확인할 수 있습니다.
* 다른 모든 FQDN이 아닌 허용된 FQDN 하나만 찾아볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에 대 한 방화벽 리소스를 유지할 수 있습니다. 또는 더 이상 필요 하지 않은 경우에는 모든 방화벽 관련 리소스를 삭제 하는 **테스트-FW-RG** 리소스 그룹을 삭제 합니다.

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
