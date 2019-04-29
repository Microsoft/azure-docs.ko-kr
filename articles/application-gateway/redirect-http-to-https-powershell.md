---
title: HTTP 및 HTTPS 간의 리디렉션으로 애플리케이션 게이트웨이 만들기 - Azure PowerShell | Microsoft Docs
description: Azure PowerShell을 사용하여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/13/2018
ms.author: victorh
ms.openlocfilehash: a9bff2ab8569a6e070ce5ea4c02b022a172450a3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098016"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Azure PowerShell을 사용하여 HTTP 및 HTTPS 간의 리디렉션으로 애플리케이션 게이트웨이 만들기

Azure PowerShell을 사용하여 SSL 종료를 위한 인증서로 [애플리케이션 게이트웨이](overview.md)를 만들 수 있습니다. 라우팅 규칙은 애플리케이션 게이트웨이에서 HTTP 트래픽을 HTTPS 포트로 리디렉션하는 데 사용됩니다. 이 예제에서는 두 개의 가상 머신 인스턴스를 포함하는 애플리케이션 게이트웨이의 백 엔드 풀에 대한 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)도 만듭니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 자체 서명된 인증서 만들기
> * 네트워크 설정
> * 인증서가 있는 애플리케이션 게이트웨이 만들기
> * 수신기 및 리디렉션 규칙 추가
> * 기본 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 이 자습서의 명령을 실행하려면 `Login-AzAccount`를 실행하여 Azure에 연결해야 합니다.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

프로덕션에 사용하려면 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서에서는 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)를 사용하여 자체 서명된 인증서를 만듭니다. [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate)을 인증서에서 pfx 파일을 내보내도록 반환된 지문과 함께 사용할 수 있습니다.

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

지문을 사용하여 pfx 파일을 만듭니다.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 라는 Azure 리소스 그룹을 만듭니다 *myResourceGroupAG* 사용 하 여 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다. 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)를 사용하여 *myBackendSubnet* 및 *myAGSubnet*에 대한 서브넷 구성을 만듭니다. 서브넷 구성으로 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 *myVNet*이라는 가상 네트워크를 만듭니다. 마지막으로 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 *myAGPublicIPAddress*라는 공용 IP 주소를 만듭니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```powershell
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

[New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)을 사용하여 이전에 애플리케이션 게이트웨이에 만든 *myAGSubnet*을 연결합니다. [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)를 사용하여 *myAGPublicIPAddress*를 애플리케이션 게이트웨이에 할당합니다. 사용 하 여 HTTPS 포트를 만들 수 있습니다 [새로 만들기-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)합니다.

```powershell
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
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>백 엔드 풀 및 설정 만들기

[New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)을 사용하여 애플리케이션 게이트웨이에 대한 *appGatewayBackendPool*이라는 백 엔드 풀을 만듭니다. [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings)를 사용하여 백 엔드 풀에 대한 설정을 구성합니다.

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>기본 수신기 및 규칙 만들기

애플리케이션 게이트웨이에서 트래픽을 백 엔드 풀로 적절히 라우팅할 수 있는 수신기가 필요합니다. 이 예제에서는 루트 URL에서 HTTPS 트래픽을 수신하는 기본 수신기를 만듭니다. 

사용 하 여 인증서 개체 [새로 만들기-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) 한 다음 라는 수신기를 만듭니다 *appGatewayHttpListener* 사용 하 여 [ 새 AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 프런트 엔드 구성, 프런트 엔드 포트 및 이전에 만든 인증서를 사용 하 여 합니다. 수신기에서 들어오는 트래픽에 사용할 백 엔드 풀을 인식할 수 있는 규칙이 필요합니다. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)을 사용하여 *rule1*이라는 기본 규칙을 만듭니다.

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

필요한 지원 리소스를 만들었으니 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)를 사용하여 *myAppGateway*라는 애플리케이션 게이트웨이에 대한 매개 변수를 지정한 다음, 인증서와 [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)를 사용하여 만듭니다.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>수신기 및 리디렉션 규칙 추가

### <a name="add-the-http-port"></a>HTTP 포트 추가

사용 하 여 응용 프로그램 게이트웨이에 HTTP 포트를 추가 [추가 AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport)합니다.

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>HTTP 수신기 추가

라는 HTTP 수신기를 추가 *myListener* 사용 하 여 응용 프로그램 게이트웨이 [추가-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)합니다.

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>리디렉션 구성 추가

HTTP 및 HTTPS 간의 리디렉션 구성을 사용 하 여 응용 프로그램 게이트웨이 추가 [추가 AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)합니다.

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>라우팅 규칙 추가

사용 하 여 응용 프로그램 게이트웨이에 리디렉션 구성 사용 하 여 라우팅 규칙을 추가 [추가 AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)합니다.

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

이 예제에서는 애플리케이션 게이트웨이에서 백 엔드 풀에 대한 서버를 제공하도록 가상 머신 확장 집합을 만듭니다. IP 설정을 구성할 때 확장 집합을 백 엔드 풀에 할당합니다.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
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

```powershell
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

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져올 수 있습니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 예를 들어 http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![보안 경고](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

자체 서명된 인증서를 사용하는 경우 보안 경고를 받으려면 **세부 정보**, **웹 페이지로 이동**을 차례로 선택합니다. 그러면 보안 IIS 웹 사이트가 다음 예제와 같이 표시됩니다.

![애플리케이션 게이트웨이의 기준 URL 테스트](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 자체 서명된 인증서 만들기
> * 네트워크 설정
> * 인증서가 있는 애플리케이션 게이트웨이 만들기
> * 수신기 및 리디렉션 규칙 추가
> * 기본 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기
