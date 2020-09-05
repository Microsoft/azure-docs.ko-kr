---
title: Azure PowerShell |를 사용 하 여 Azure 개인 링크 서비스 만들기 Microsoft Docs
description: Azure PowerShell를 사용 하 여 Azure 개인 링크 서비스를 만드는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505635"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 링크 서비스 만들기
이 문서에서는 Azure PowerShell를 사용 하 여 Azure에서 개인 링크 서비스를 만드는 방법을 보여 줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에 최신 Azure PowerShell 모듈 버전이 필요 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

개인 링크를 만들려면 먼저 [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)를 사용 하 여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *WestCentralUS* 위치에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용 하 여 개인 링크에 대 한 가상 네트워크를 만듭니다. 다음 예제에서는 프런트 엔드 (*frontendSubnet*), 백 엔드 (*backendSubnet*), 개인 링크 (*othersubnet*) 용 서브넷을 사용 하 여 *myvnet* 이라는 가상 네트워크를 만듭니다.

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>내부 Load Balancer 만들기
[AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용 하 여 내부 표준 Load Balancer를 만듭니다. 다음 예제에서는 이전 단계에서 만든 프런트 엔드 IP 구성, 프로브, 규칙 및 백 엔드 풀을 사용 하 여 내부 표준 Load Balancer를 만듭니다.

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기
[AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer)를 사용 하 여 개인 링크 서비스를 만듭니다.  이 예제에서는 *Myresourcegroup*이라는 리소스 그룹의 표준 Load Balancer을 사용 하 여 *myPLS* 이라는 개인 링크 서비스를 만듭니다.
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>개인 링크 서비스 가져오기
다음과 같이 [AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) 를 사용 하 여 개인 링크 서비스에 대 한 세부 정보를 가져옵니다.

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

이 단계에서는 개인 링크 서비스가 성공적으로 생성 되 고 트래픽을 받을 준비가 된 것입니다. 위의 예제는 PowerShell을 사용 하 여 개인 링크 서비스를 만드는 방법을 보여 주기 위한 것입니다.  트래픽 수신 대기를 위해 부하 분산 장치 백 엔드 풀 또는 백 엔드 풀의 응용 프로그램을 구성 하지 않았습니다. 종단 간 트래픽 흐름을 확인 하려면 표준 부하 분산 장치 뒤에 응용 프로그램을 구성 하는 것이 좋습니다.

다음으로 PowerShell을 사용 하 여이 서비스를 다른 VNet의 개인 끝점에 매핑하는 방법을 설명 합니다. 이 예제는 개인 끝점을 만들고 위에서 만든 개인 링크 서비스에 연결 하는 것으로 제한 됩니다. Virtual Network에서 Virtual Machines를 만들어 시나리오를 빌드하기 위한 개인 끝점으로 트래픽을 보내고 받을 수 있습니다.

## <a name="create-a-private-endpoint"></a>Private Endpoint 만들기
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용 하 여 개인 끝점에 대 한 가상 네트워크를 만듭니다. 이 예제에서는 *vnetPE*   *myresourcegroup*이라는 리소스 그룹에 vnetpe 라는 가상 네트워크를 만듭니다.

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
가상 네트워크에서 위에서 만든 개인 링크 서비스를 사용 하기 위한 개인 끝점을 만듭니다.

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>개인 끝점 가져오기
다음과 같이를 사용 하 여 개인 끝점의 IP 주소를 가져옵니다 `Get-AzPrivateEndpoint` .

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>개인 끝점 연결 승인
' AzPrivateEndpointConnection '를 사용 하 여 개인 연결 서비스에 대 한 개인 끝점 연결을 승인 합니다.

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>다음 단계
- [Azure 개인 링크](private-link-overview.md) 에 대 한 자세한 정보

