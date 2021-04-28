---
title: '자습서: 웹 애플리케이션 액세스 향상 - Azure Application Gateway'
description: 이 자습서에서는 Azure PowerShell을 사용하여 예약된 IP 주소로 자동 크기 조정, 영역 중복 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8196267ff7a71fb3910848fd0fef11a40a3c1c32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869944"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>자습서: 웹 애플리케이션 액세스를 향상시키는 애플리케이션 게이트웨이 만들기

웹 애플리케이션 액세스 향상과 관련된 IT 관리자인 경우 고객의 요구 사항에 따라 크기를 조정하고 여러 가용성 영역을 확장할 수 있도록 애플리케이션 게이트웨이를 최적화할 수 있습니다. 이 자습서는 자동 크기 조정, 영역 중복 및 예약된 VIP(고정 IP)와 같은 Azure Application Gateway 기능을 구성하는 데 도움이 됩니다. Azure PowerShell cmdlet과 Azure Resource Manager 배포 모델을 사용하여 문제를 해결합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 자체 서명된 인증서 만들기
> * 자동 크기 조정 가상 네트워크 만들기
> * 예약된 공용 IP 만들기
> * 애플리케이션 게이트웨이 인프라 설정
> * 자동 크기 조정 지정
> * Application Gateway 만들기
> * 애플리케이션 게이트웨이 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서에서는 관리 Azure PowerShell 세션을 로컬로 실행해야 합니다. Azure PowerShell 모듈 버전 1.0.0 이상이 설치되어 있어야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Connect-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
사용 가능한 위치 중 하나에 리소스 그룹을 만듭니다.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

프로덕션에 사용하려면 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서에서는 [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate)를 사용하여 자체 서명된 인증서를 만듭니다. [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate)을 인증서에서 pfx 파일을 내보내도록 반환된 지문과 함께 사용할 수 있습니다.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

다음과 같은 결과가 표시됩니다.

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

지문을 사용하여 pfx 파일을 만듭니다. *\<password>* 를 선택한 암호로 바꿉니다.

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
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
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>세부 정보 검색

애플리케이션 게이트웨이에 대한 IP 구성 세부 정보를 만들기 위해 로컬 개체에서 리소스 그룹, 서브넷 및 IP에 대한 세부 정보를 검색합니다.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>웹앱 만들기

백 엔드 풀에 대해 두 개의 웹앱을 구성합니다. *\<site1-name>* 및 *\<site-2-name>* 을 `azurewebsites.net` 도메인의 고유한 이름으로 바꿉니다.

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>인프라 구성

IP 구성, 프런트 엔드 IP 구성, 백 엔드 풀, HTTP 설정, 인증서, 포트, 수신기 및 규칙을 기존 표준 Application Gateway와 동일한 형식으로 구성합니다. 새 SKU는 표준 SKU와 동일한 개체 모델을 따릅니다.

$pool 변수 정의에서 두 개의 웹앱 FQDN(예: `mywebapp.azurewebsites.net`)을 바꿉니다.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>자동 크기 조정 지정

이제 애플리케이션 게이트웨이에 대한 자동 크기 조정 구성을 지정할 수 있습니다. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
이 모드에서는 애플리케이션 게이트웨이가 애플리케이션 트래픽 패턴에 따라 자동으로 크기 조정됩니다.

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

애플리케이션 게이트웨이를 만들고, 중복 영역 및 자동 크기 조정 구성을 포함합니다.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

Get-AzPublicIPAddress를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. 공용 IP 주소 또는 DNS 이름을 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>리소스 정리

먼저 애플리케이션 게이트웨이를 통해 만들어진 리소스를 검색합니다. 그런 다음, 더 이상 필요하지 않은 경우 `Remove-AzResourceGroup` 명령을 사용하여 리소스 그룹, 애플리케이션 게이트웨이 및 관련된 모든 리소스를 제거할 수 있습니다.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [URL 경로 기반 회람 규칙을 사용하여 애플리케이션 게이트웨이 만들기](./tutorial-url-route-powershell.md)
