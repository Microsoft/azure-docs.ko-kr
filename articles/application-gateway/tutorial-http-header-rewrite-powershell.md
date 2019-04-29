---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는 Azure PowerShell을 사용하여 Azure Application Gateway를 만들고 HTTP 헤더를 다시 쓰는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715099"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>자습서: 애플리케이션 게이트웨이를 만들고 HTTP 헤더 다시 쓰기

새 [자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)를 만들 때 Azure PowerShell을 사용하여 [HTTP 요청 및 응답 헤더를 다시 쓰는 규칙](rewrite-http-headers.md)을 구성할 수 있습니다.

> [!IMPORTANT] 
> 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 자동 크기 조정 가상 네트워크 만들기
> * 예약된 공용 IP 만들기
> * 애플리케이션 게이트웨이 인프라 설정
> * Http 헤더 다시 쓰기 규칙 구성 지정
> * 자동 크기 조정 지정
> * Application Gateway 만들기
> * 애플리케이션 게이트웨이 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 Azure PowerShell을 로컬로 실행해야 합니다. Az 모듈 버전 1.0.0 이상을 설치해야 합니다. `Import-Module Az`를 실행한 후 `Get-Module Az`를 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
사용 가능한 위치 중 하나에 리소스 그룹을 만듭니다.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

자동 크기 조정 애플리케이션 게이트웨이에 대한 하나의 전용 서브넷이 있는 가상 네트워크를 만듭니다. 현재 각 전용 서브넷에는 하나의 자동 크기 조정 애플리케이션 게이트웨이만 배포할 수 있습니다.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>예약된 공용 IP 만들기

PublicIPAddress의 할당 메서드를 **Static**(고정)으로 지정합니다. 자동 크기 조정 애플리케이션 게이트웨이 VIP는 정적일 수만 있습니다. 동적 IP는 지원되지 않습니다. 표준 PublicIpAddress SKU만 지원됩니다.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>세부 정보 검색

애플리케이션 게이트웨이에 대한 IP 구성 세부 정보를 만들기 위해 로컬 개체에서 리소스 그룹, 서브넷 및 IP에 대한 세부 정보를 검색합니다.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>인프라 구성

IP 구성, 프런트 엔드 IP 구성, 백 엔드 풀, HTTP 설정, 인증서, 포트 및 수신기를 기존 표준 애플리케이션 게이트웨이와 동일한 형식으로 구성합니다. 새 SKU는 표준 SKU와 동일한 개체 모델을 따릅니다.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>HTTP 헤더 다시 쓰기 규칙 구성 지정

Http 헤더를 다시 쓰는 데 필요한 새 개체를 구성합니다.

- **RequestHeaderConfiguration**:이 개체는 다시 쓰려는 요청 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
- **ResponseHeaderConfiguration**:이 개체는 다시 쓰려는 응답 헤더 필드와 원본 헤더를 다시 써야 하는 새 값을 지정하는 데 사용합니다.
- **ActionSet**: 이 개체는 위에 지정된 요청 및 응답 헤더의 구성을 포함합니다. 
- **RewriteRule**: 이 개체는 위에 지정된 모든 *actionSets*를 포함합니다. 
- **RewriteRuleSet**: 이 개체는 모든 *rewriteRules*를 포함하며 요청 회람 규칙인 기본 또는 경로 기반에 연결해야 합니다.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>회람 규칙 지정

요청 회람 규칙을 만듭니다. 규칙을 만들면 이 다시 쓰기 구성이 회람 규칙을 통해 원본 수신기에 첨부됩니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다. 아래에서는 기본 회람 규칙이 만들어지고 다시 쓰기 규칙 세트가 첨부됩니다.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>자동 크기 조정 지정

이제 애플리케이션 게이트웨이에 대한 자동 크기 조정 구성을 지정할 수 있습니다. 지원되는 두 가지 자동 크기 조정 구성 유형은 다음과 같습니다.

* **고정 용량 모드**. 이 모드에서는 애플리케이션 게이트웨이가 자동으로 크기 조정되지 않고 고정된 배율 단위 용량에서 작동합니다.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **자동 크기 조정 모드**. 이 모드에서는 애플리케이션 게이트웨이가 애플리케이션 트래픽 패턴에 따라 자동으로 크기 조정됩니다.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

애플리케이션 게이트웨이를 만들고, 중복 영역 및 자동 크기 조정 구성을 포함합니다.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

Get-AzPublicIPAddress를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. 공용 IP 주소 또는 DNS 이름을 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>리소스 정리

먼저 애플리케이션 게이트웨이를 통해 만들어진 리소스를 검색합니다. 그런 다음, 더 이상 필요하지 않은 경우 `Remove-AzResourceGroup` 명령을 사용하여 리소스 그룹, 애플리케이션 게이트웨이 및 관련된 모든 리소스를 제거할 수 있습니다.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [URL 경로 기반 회람 규칙을 사용하여 애플리케이션 게이트웨이 만들기](./tutorial-url-route-powershell.md)
