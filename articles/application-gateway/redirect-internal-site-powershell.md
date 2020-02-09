---
title: PowerShell을 사용 하 여 내부 리디렉션
titleSuffix: Azure Application Gateway
description: Azure Powershell을 사용하여 내부 웹 트래픽을 적절한 백 엔드 서버 풀로 리디렉션하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 4e0c7707066028996e360a342db9f8dc0fd87599
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075118"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-azure-powershell"></a>Azure PowerShell을 사용하여 내부 리디렉션으로 애플리케이션 게이트웨이 만들기

Azure PowerShell을 사용하여 [애플리케이션 게이트웨이](multiple-site-overview.md)를 만들 때 [웹 트래픽 리디렉션](overview.md)을 구성할 수 있습니다. 이 자습서에서는 가상 머신 확장 집합을 사용하여 백 엔드 풀을 정의합니다. 그런 다음, 웹 트래픽이 적절한 풀에 도착하도록 소유한 도메인을 기준으로 수신기와 규칙을 구성합니다. 이 자습서에서는 사용자가 여러 도메인을 소유 하 고 *www.contoso.com* 및 *www\.contoso.org*의 예제를 사용 한다고 가정 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 애플리케이션 게이트웨이 만들기
> * 수신기 및 리디렉션 규칙 추가
> * 백 엔드 풀로 가상 머신 확장 집합 만들기
> * 도메인에서 CNAME 레코드 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬에 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

*New-AzVirtualNetworkSubnetConfig*를 사용하여 *myBackendSubnet* 및 [myAGSubnet](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)에 대한 서브넷 구성을 만듭니다. 서브넷 구성으로 *New-AzVirtualNetwork*를 사용하여 [myVNet](/powershell/module/az.network/new-azvirtualnetwork)이라는 가상 네트워크를 만듭니다. 마지막으로 *New-AzPublicIpAddress*를 사용하여 [myAGPublicIPAddress](/powershell/module/az.network/new-azpublicipaddress)라는 공용 IP 주소를 만듭니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

*New-AzApplicationGatewayIPConfiguration*을 사용하여 이전에 애플리케이션 게이트웨이에 만든 [myAGSubnet](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)을 연결합니다. *New-AzApplicationGatewayFrontendIPConfig*를 사용하여 [myAGPublicIPAddress](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)를 애플리케이션 게이트웨이에 할당합니다. 그런 후 [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)를 사용하여 HTTP 포트를 만들 수 있습니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>백 엔드 풀 및 설정 만들기

[AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)를 사용 하 여 application gateway에 대한 *contosoPool* 라는 백 엔드 풀을 만듭니다. [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)를 사용하여 백 엔드 풀에 대한 설정을 구성합니다.

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-first-listener-and-rule"></a>첫 번째 수신기 및 규칙 만들기

애플리케이션 게이트웨이에서 트래픽을 백 엔드 풀로 적절히 라우팅할 수 있는 수신기가 필요합니다. 이 자습서에서는 두 도메인에 대해 두 개의 수신기를 만듭니다. 이 예제에서는 *www.contoso.com* 및 *www\.contoso.org*의 도메인에 대해 수신기가 생성 됩니다.

이전에 만든 프런트 엔드 구성 및 프런트 엔드 포트와 함께 [AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 를 사용 하 여 *contosoComListener* 라는 첫 번째 수신기를 만듭니다. 수신기에서 들어오는 트래픽에 사용할 백 엔드 풀을 인식할 수 있는 규칙이 필요합니다. [AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)를 사용 하 여 *contosoComRule* 이라는 기본 규칙을 만듭니다.

```azurepowershell-interactive
$contosoComlistener = New-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.com"
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoComRule `
  -RuleType Basic `
  -HttpListener $contosoComListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

필요한 지원 리소스를 만들었으므로 *New-AzApplicationGatewaySku*를 사용하여 [myAppGateway](/powershell/module/az.network/new-azapplicationgatewaysku)라는 애플리케이션 게이트웨이에 대한 매개 변수를 지정한 다음, [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)를 사용하여 만듭니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $contosoComListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-the-second-listener"></a>두 번째 수신기 추가

[AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)를 사용 하 여 트래픽을 리디렉션하는 데 필요한 *contosoOrgListener* 이라는 수신기를 추가 합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$frontendPort = Get-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -ApplicationGateway $appgw
$ipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name contosoOrgListener `
  -Protocol Http `
  -FrontendIPConfiguration $ipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.org"
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>리디렉션 구성 추가

[Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)을 사용하여 수신기에 대해 리디렉션을 구성할 수 있습니다. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoComlistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -ApplicationGateway $appgw
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectOrgtoCom `
  -RedirectType Found `
  -TargetListener $contosoComListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-second-routing-rule"></a>두 번째 라우팅 규칙 추가

그런 다음 [추가-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)을 사용 하 여 *contosoOrgRule* 라는 새 규칙에 리디렉션 구성을 연결할 수 있습니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name redirectOrgtoCom `
  -ApplicationGateway $appgw   
Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name contosoOrgRule `
  -RuleType Basic `
  -HttpListener $contosoOrgListener `
  -RedirectConfiguration $redirectConfig
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

이 예제에서는 생성한 백 엔드 풀을 지원하는 가상 머신 확장 집합을 만듭니다. 만든 확장 집합에는 *myvmss*라는 이름이 지정되며 IIS가 설치되는 두 개의 가상 머신 인스턴스를 포함합니다. IP 설정을 구성할 때 확장 집합을 백 엔드 풀에 할당합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
  -OsDiskCreateOption FromImage
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>IIS 설치

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-cname-record-in-your-domain"></a>도메인에서 CNAME 레코드 만들기

애플리케이션 게이트웨이가 해당 공용 IP 주소로 생성된 후 DNS 주소를 가져와 도메인에서 CNAME 레코드를 만드는 데 사용할 수 있습니다. [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 애플리케이션 게이트웨이의 DNS 주소를 가져올 수 있습니다. DNSSettings의 *fqdn* 값을 복사하여 만드는 CNAME 레코드의 값으로 사용합니다. A 레코드를 사용할 경우 애플리케이션 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

브라우저의 주소 표시줄에 도메인 이름을 입력합니다. 예: [https://www.contoso.com](https://www.contoso.com).

![애플리케이션 게이트웨이에서 contoso 사이트 테스트](./media/redirect-internal-site-powershell/application-gateway-iistest.png)

주소를 다른 도메인으로 변경 합니다. 예를 들어 https://www.contoso.org 트래픽이 `www.contoso.com`의 수신기로 다시 리디렉션되는 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 애플리케이션 게이트웨이 만들기
> * 수신기 및 리디렉션 규칙 추가
> * 백 엔드 풀로 가상 머신 확장 집합 만들기
> * 도메인에서 CNAME 레코드 만들기
