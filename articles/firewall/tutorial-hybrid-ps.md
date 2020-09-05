---
title: PowerShell을 사용 하 여 하이브리드 네트워크에서 Azure 방화벽 배포 & 구성
description: 이 문서에서는 Azure PowerShell를 사용 하 여 Azure 방화벽을 배포 하 고 구성 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: a91d0e11c44657a2d4cdd267ffa6490ca89532a9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069411"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Azure PowerShell을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성

온-프레미스 네트워크를 Azure Virtual Network에 연결하여 하이브리드 네트워크를 만들 경우 Azure 네트워크 리소스에 대한 액세스를 제어하는 기능은 전체 보안 계획의 중요한 부분입니다.

Azure Firewall을 사용하여 허용 및 거부된 네트워크 트래픽을 정의하는 규칙을 사용하여 하이브리드 네트워크에서 네트워크 액세스를 제어할 수 있습니다.

이 문서에서는 다음과 같은 3 개의 가상 네트워크를 만듭니다.

- **VNet-Hub** - 방화벽이 이 가상 네트워크에 있습니다.
- **VNet-Spoke** - 스포크 가상 네트워크는 Azure에 있는 워크로드를 나타냅니다.
- **VNet-Onprem** - 온-프레미스 가상 네트워크는 온-프레미스 네트워크를 나타냅니다. 실제 배포에서는 VPN 또는 ExpressRoute 연결을 통해 연결할 수 있습니다. 간단히 하기 위해이 문서에서는 VPN gateway 연결을 사용 하며, Azure에 배치 된 가상 네트워크는 온-프레미스 네트워크를 나타내는 데 사용 됩니다.

![하이브리드 네트워크의 방화벽](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

이 문서에서는 다음 방법을 설명합니다.

* 변수 선언
* 방화벽 허브 가상 네트워크 만들기
* 스포크 가상 네트워크 만들기
* 온-프레미스 가상 네트워크 만들기
* 방화벽 구성 및 배포
* VPN 게이트웨이 만들기 및 연결
* 허브 및 스포크 가상 네트워크 피어링
* 경로 만들기
* 가상 머신 만들기
* 방화벽 테스트

이 자습서를 완료 하는 대신 Azure Portal를 사용 하려는 경우 [자습서: Azure Portal를 사용 하 여 하이브리드 네트워크에서 Azure 방화벽 배포 및 구성](tutorial-hybrid-portal.md)을 참조 하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

이 문서에서는 PowerShell을 로컬로 실행 해야 합니다. Azure PowerShell 모듈을 설치해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

이 시나리오가 제대로 작동하기 위해서는 세 가지 주요 요구 사항이 있습니다.

- Azure Firewall IP 주소를 가리키는 스포크 서브넷의 UDR(사용자 정의 경로)이 기본 게이트웨이입니다. 이 경로 테이블에서 가상 네트워크 게이트웨이 경로 전파가 **사용 하지 않도록 설정** 되어 있어야 합니다.
- 허브 게이트웨이 서브넷의 UDR은 스포크 네트워크에 대한 다음 호프로 방화벽 IP 주소를 가리켜야 합니다.

   Azure Firewall 서브넷에서는 BGP로부터 경로를 학습하므로 UDR이 필요하지 않습니다.
- VNet-Hub와 VNet-Spoke를 피어링할 때는 **AllowGatewayTransit**, VNet-Spoke와 VNet-Hub를 피어링할 때는 **UseRemoteGateways**를 설정합니다.

이러한 경로를 만드는 방법을 보려면이 문서의 [경로 만들기](#create-the-routes) 섹션을 참조 하세요.

>[!NOTE]
>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet이 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습하는 경우 이 경로를 직접 인터넷 연결을 유지하기 위해 **Internet**으로 설정된 **NextHopType** 값을 통해 0.0.0.0/0 UDR로 재정의해야 합니다.
>
>Azure Firewall은 강제 터널링을 지원하도록 구성할 수 있습니다. 자세한 내용은 [Azure Firewall 강제 터널링](forced-tunneling.md)을 참조하세요.

>[!NOTE]
>직접 피어링된 VNet 사이의 트래픽은 UDR이 기본 게이트웨이로 Azure Firewall을 가리키는 경우에도 직접 라우팅됩니다. 이 시나리오에서 서브넷 트래픽에 대한 서브넷을 방화벽으로 보내려면 UDR에 두 가지 서브넷에 명시적으로 지정된 대상 서브넷 네트워크 접두사가 포함되어 있어야 합니다.

관련된 Azure PowerShell 참조 설명서를 검토하려면, [Azure PowerShell 참조](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)를 참조하세요.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="declare-the-variables"></a>변수 선언

다음 예제에서는이 문서의 값을 사용 하 여 변수를 선언 합니다. 경우에 따라 사용자 구독에서 작동하도록 하기 위해 일부 값을 사용자 고유의 값으로 바꾸어야 할 수 있습니다. 필요한 경우 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>방화벽 허브 가상 네트워크 만들기

먼저이 아티클에 대 한 리소스를 포함 하는 리소스 그룹을 만듭니다.

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

이제 방화벽 허브 가상 네트워크를 만듭니다.

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

가상 네트워크용으로 만들 VPN 게이트웨이에 할당할 공용 IP 주소를 요청합니다. *AllocationMethod*가 **동적**인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 VPN 게이트웨이에 동적으로 할당됩니다.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>스포크 가상 네트워크 만들기

스포크 가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

스포크 가상 네트워크를 만듭니다.

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>온-프레미스 가상 네트워크 만들기

가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

이제 온-프레미스 가상 네트워크를 만듭니다.

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

가상 네트워크용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. *AllocationMethod*가 **동적**인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>방화벽 구성 및 배포

이제 허브 가상 네트워크에 방화벽을 배포합니다.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>네트워크 규칙 구성

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>VPN 게이트웨이 만들기 및 연결

허브 및 온-프레미스 가상 네트워크는 VPN Gateway를 통해 연결됩니다.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>허브 가상 네트워크에 대한 VPN Gateway 만들기

VPN 게이트웨이 구성을 만듭니다. VPN 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

이제 허브 가상 네트워크에 대한 VPN Gateway를 만듭니다. 네트워크 간 구성에는 RouteBased VpnType이 필요합니다. 종종 선택한 VPN 게이트웨이 SKU에 따라 VPN 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>온-프레미스 가상 네트워크에 대한 VPN Gateway 만들기

VPN 게이트웨이 구성을 만듭니다. VPN 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

이제 온-프레미스 가상 네트워크에 대한 VPN Gateway를 만듭니다. 네트워크 간 구성에는 RouteBased VpnType이 필요합니다. 종종 선택한 VPN 게이트웨이 SKU에 따라 VPN 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>VPN 연결 만들기

이제 허브와 온-프레미스 게이트웨이 간의 VPN 연결을 만들 수 있습니다.

#### <a name="get-the-vpn-gateways"></a>VPN 게이트웨이 가져오기

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>연결 만들기

이 단계에서는 허브 가상 네트워크에서 온-프레미스 가상 네트워크로의 연결을 만듭니다. 예제에서 참조된 공유 키를 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
온-프레미스에서 허브 가상 네트워크로의 연결을 만듭니다. 이 단계는 VNet-Onprem에서 VNet-hub로의 연결을 만든다는 점을 제외하고는 이전 단계와 유사합니다. 공유된 키가 일치하는지 확인합니다. 몇 분 후 연결이 설정됩니다.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>연결 확인

*-Debug*를 포함하거나 포함하지 않은 상태에서 *Get-AzVirtualNetworkGatewayConnection* cmdlet을 사용하여 연결 성공을 확인할 수 있습니다. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용합니다. 메시지가 표시되면 **모두** 실행하기 위해 **A**를 선택합니다. 예제에서 *-Name*은 테스트하려는 연결의 이름을 나타냅니다.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

cmdlet이 완료되면 값을 봅니다. 다음 예제에서는 연결 상태가 *연결됨*으로 표시되고 송/수신 바이트를 볼 수 있습니다.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>허브 및 스포크 가상 네트워크 피어링

이제 허브 및 스포크 가상 네트워크를 피어링합니다.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>경로 만들기

다음으로 두 경로 만듭니다.

- 허브 게이트웨이 서브넷에서 방화벽 IP 주소를 통해 스포크 서브넷으로 가는 경로
- 방화벽 IP 주소를 통해 스포크 서브넷으로부터의 기본 경로

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

이제 스포크 워크로드 및 온-프레미스 가상 머신을 만들어 적절한 서브넷에 배치합니다.

### <a name="create-the-workload-virtual-machine"></a>워크로드 가상 머신 만들기

스포크 가상 네트워크에서 IIS를 실행하고 공용 IP 주소가 없으며 Ping 유입을 허용하는 가상 머신을 만듭니다.
메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>온-프레미스 가상 머신 만들기

원격 데스크톱을 사용하여 공용 IP 주소에 연결하는 간단한 가상 머신입니다. 여기에서 방화벽을 통해 온-프레미스 서버에 연결할 수 있습니다. 메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>방화벽 테스트

먼저 **VM-spoke-01** 가상 머신의 개인 IP 주소를 가져와 기록합니다.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Azure Portal에서 **VM-Onprem** 가상 머신에 연결합니다.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
**VM-Onprem**에서 웹 브라우저를 열고 http://\<VM-spoke-01 private IP\>로 이동합니다.

Internet Information Services 기본 페이지가 표시됩니다.

**VM-Onprem**에서 개인 IP 주소의 **VM-spoke-01**로 원격 데스크톱을 엽니다.

연결에 성공하며 선택한 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

<!---- You can ping the server on the spoke VNet.--->
- 스포크 가상 네트워크에서 웹 서버를 탐색할 수 있습니다.
- RDP를 사용하여 스포크 가상 네트워크에 있는 서버에 연결할 수 있습니다.

이제 방화벽 규칙이 예상대로 작동하는지 호가인하기 위해 방화벽 네트워크 규칙 수집 동작을 **거부**로 변경합니다. 다음 스크립트를 실행하여 규칙 수집 동작을 **거부**로 변경합니다.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

이제 테스트를 다시 실행합니다. 이번에는 모두 실패해야 합니다. 변경된 규칙을 테스트하려면 먼저 기존 원격 데스크톱을 모두 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에서 사용하기 위해 방화벽 리소스를 그대로 유지하거나, 더 이상 필요하지 않은 경우 **FW-Hybrid-Test** 리소스 그룹을 삭제하여 모든 방화벽 관련 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

그런 다음, Azure Firewall 로그를 모니터링할 수 있습니다.

[자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
