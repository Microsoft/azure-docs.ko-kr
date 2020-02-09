---
title: URL을 기반으로 웹 트래픽 라우팅 - Azure PowerShell
description: Azure PowerShell을 사용하여 URL을 기반으로 웹 트래픽을 확장 가능한 특정 서버 풀로 라우팅하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8ca5c411db5644182f7e87f1ee1c63f3cbc4d2e9
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200413"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Azure PowerShell을 사용하여 URL을 기반으로 웹 트래픽 라우팅

Azure PowerShell을 사용하여 애플리케이션 액세스에 사용되는 URL을 기반으로 확장 가능한 특정 서버 풀로 웹 트래픽 라우팅을 구성할 수 있습니다. 이 문서에서는 [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)를 사용 하 여 3 개의 백 엔드 풀이 있는 [Azure 애플리케이션 게이트웨이](application-gateway-introduction.md) 를 만듭니다. 각 백 엔드 풀은 공통 데이터, 이미지 및 비디오와 같은 특정 목적을 갖습니다.  트래픽을 별도의 풀에 라우팅하면 고객이 필요할 때 필요한 정보를 얻을 수 있습니다.

트래픽을 라우팅하려면 웹 트래픽이 풀의 올바른 서버에 도착하도록 특정 포트에서 수신 대기하는 수신기에 할당되는 [라우팅 규칙](application-gateway-url-route-overview.md)을 만듭니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 수신기, URL 경로 맵 및 규칙 만들기
> * 확장 가능한 백 엔드 풀 만들기

![URL 라우팅 예제](./media/tutorial-url-route-powershell/scenario.png)

원하는 경우 [Azure CLI](tutorial-url-route-cli.md) 또는 [Azure Portal](create-url-route-portal.md)를 사용 하 여이 절차를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요 합니다. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

리소스를 만드는 데 필요한 시간 때문에이 절차를 완료 하는 데 최대 90 분이 걸릴 수 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

응용 프로그램에 대한 모든 리소스를 포함 하는 리소스 그룹을 만듭니다. 

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

기존 가상 네트워크를 사용하거나 새로운 가상 네트워크를 만들지에 관계 없이 애플리케이션 게이트웨이에만 사용되는 서브넷이 포함되어 있는지 확인해야 합니다. 이 문서에서는 응용 프로그램 게이트웨이 및 확장 집합에 대한 서브넷에 대한 서브넷을 만듭니다. 애플리케이션 게이트웨이의 리소스에 액세스할 수 있도록 공용 IP 주소를 만들어야 합니다.

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)를 사용하여 *myAGSubnet* 및 *myBackendSubnet*이라는 서브넷 구성을 만듭니다. 서브넷 구성으로 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 *myVNet*이라는 가상 네트워크를 만듭니다. 마지막으로 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 *myAGPublicIPAddress*라는 공용 IP 주소를 만듭니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

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
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Application Gateway 만들기

이 섹션에서는 애플리케이션 게이트웨이를 지원하는 리소스를 만든 다음, 최종적으로 애플리케이션 게이트웨이를 만듭니다. 만든 리소스는 다음과 같습니다.

- *IP 구성 및 프런트 엔드 포트* -이전에 만든 서브넷을 application gateway에 연결 하 고 액세스 하는 데 사용할 포트를 할당 합니다.
- *기본 풀* - 모든 애플리케이션 게이트웨이에 서버 백 엔드 풀이 하나 이상 있어야 합니다.
- *기본 수신기 및 규칙* - 기본 수신기는 할당된 포트에서 트래픽을 수신 대기하고 기본 규칙은 기본 풀로 트래픽을 보냅니다.

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

[AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)를 사용 하 여 이전에 만든 *myagsubnet* 을 application gateway에 연결 합니다. [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)를 사용하여 애플리케이션 게이트웨이에 *myAGPublicIPAddress*를 할당합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>기본 풀 및 설정 만들기

[New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)을 사용하여 애플리케이션 게이트웨이에 대해 *appGatewayBackendPool*이라는 기본 백 엔드 풀을 만듭니다. [AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)를 사용 하 여 백 엔드 풀에 대한 설정을 구성 합니다.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>기본 수신기 및 규칙 만들기

애플리케이션 게이트웨이가 백엔드 풀로 트래픽을 적절히 라우팅하도록 설정하려면 수신기가 필요합니다. 이 문서에서는 두 개의 수신기를 만듭니다. 사용자가 만드는 첫 번째 기본 수신기는 루트 URL에서 트래픽을 수신 대기합니다. 사용자가 만드는 두 번째 수신기는 특정 URL에서 트래픽을 수신 대기합니다.

이전에 만든 프런트 엔드 구성 및 프런트 엔드 포트에서 [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener)를 사용하여 *myDefaultListener*라는 기본 수신기를 만듭니다. 

수신기에 들어오는 트래픽에 사용할 백 엔드 풀을 알려면 규칙이 필요합니다. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)을 사용하여 *rule1*이라는 기본 규칙을 만듭니다.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

필요한 지원 리소스를 만들었으므로 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)를 사용하여 *myAppGateway*라는 애플리케이션 게이트웨이에 대한 매개 변수를 지정한 다음, [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)를 사용하여 만듭니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

응용 프로그램 게이트웨이를 만드는 데 최대 30 분이 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다. 

이 시점에서 포트 80의 트래픽을 수신 대기 하 고 해당 트래픽을 기본 서버 풀로 보내는 응용 프로그램 게이트웨이가 있습니다.

### <a name="add-image-and-video-backend-pools-and-port"></a>이미지와 비디오 백 엔드 풀 및 포트 추가

애플리케이션 게이트웨이 [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool)에 *imagesBackendPool* 및 *videoBackendPool*이라는 백 엔드 풀을 추가합니다. [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport)를 사용하여 풀에 대한 프런트 엔드 포트를 추가합니다. [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway)를 사용하여 애플리케이션 게이트웨이에 변경 내용을 제출합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

애플리케이션 게이트웨이를 업데이트하면 완료하는 데 20분이 걸릴 수도 있습니다.

### <a name="add-backend-listener"></a>백 엔드 수신기 추가

[Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)를 사용하여 트래픽을 라우팅하는 데 필요한 *backendListener*라는 백 엔드 수신기를 추가합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>URL 경로 맵 추가

URL 경로 맵은 애플리케이션에 전송한 URL을 특정 백 엔드 풀로 라우팅하도록 합니다. [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) 및 [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig)를 사용하여 *imagePathRule* 및 *videoPathRule*이라는 URL 경로 맵을 만듭니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>라우팅 규칙 추가

라우팅 규칙은 URL 맵을 사용자가 만든 수신기에 연결합니다. [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)을 사용하여 *rule2*라는 규칙을 추가합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>가상 머신 확장 집합 만들기

이 예제에서는 사용자가 만든 세 개의 백 엔드 풀을 지원하는 세 개의 가상 머신 확장 집합을 만듭니다. 생성된 확장 집합의 이름은 *myvmss1*, *myvmss2* 및 *myvmss3*입니다. IP 설정을 구성할 때 확장 집합을 백 엔드 풀에 할당합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>IIS 설치

각 확장 집합에는 IIS를 설치하는 두 개의 가상 머신 인스턴스가 포함됩니다.  응용 프로그램 게이트웨이가 작동 하는지 테스트 하기 위해 샘플 페이지가 생성 됩니다.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. 공용 IP 주소를 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다. 예: `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm` 또는 `http://52.168.55.24:8080/video/test.htm`.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![애플리케이션 게이트웨이의 기준 URL 테스트](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

URL을 http://&lt;ip 주소&gt;: 8080/images/test.htm로 변경 합니다. ip 주소를 &lt;ip 주소&gt;로 바꾸면 다음 예제와 같은 내용이 표시 됩니다.

![애플리케이션 게이트웨이의 이미지 URL 테스트](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

URL을 http://&lt;ip 주소&gt;: 8080/video/test.htm로 변경 합니다. ip 주소를 &lt;ip 주소&gt;로 바꾸면 다음 예제와 같은 내용이 표시 됩니다.

![애플리케이션 게이트웨이의 비디오 URL 테스트](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>다음 단계

[URL을 기반으로 하는 웹 트래픽 리디렉션](./tutorial-url-redirect-powershell.md)
