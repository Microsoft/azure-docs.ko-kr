---
title: Application Gateway에서 Virtual Network API Management를 사용 하는 방법
titleSuffix: Azure API Management
description: 내부 Virtual Network에서 프론트 엔드로 Application Gateway(WAF)와 함께 Azure API Management를 설정하고 구성하는 방법 알아보기
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 129f407dd66b32ea097daf4ed9110ffbba23660c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017602"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>내부 VNET에서 Application Gateway와 API Management 통합

## <a name="overview"></a> 개요

Virtual Network 내에서만 액세스할 수 있도록 내부 모드의 Virtual Network에서 API Management 서비스를 구성할 수 있습니다. Azure Application Gateway는 계층 7 부하 분산 장치를 제공하는 PAAS 서비스입니다. 역방향 프록시 서비스 역할을 하고 제품에 WAF(웹 애플리케이션 방화벽)를 제공합니다.

내부 VNET에서 프로비전된 API Management와 Application Gateway 프런트 엔드를 결합하면 다음 시나리오가 가능합니다.

* 내부 소비자 및 외부 소비자의 소비에 대해 동일한 API Management 리소스를 사용합니다.
* 단일 API Management 리소스를 사용하며 외부 소비자가 사용할 수 있는 API Management에서 정의된 API의 하위 집합을 갖습니다.
* 공용 인터넷에서 API Management에 대한 액세스를 켜기 및 끄기로 전환하는 턴키 방법을 제공합니다.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에 설명한 단계를 따르려면 다음 항목이 있어야 합니다.

* 활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 인증서 - API 호스트 이름에 대한 pfx 및 cer, 개발자 포털의 호스트 이름에 대한 pfx입니다.

## <a name="scenario"></a> 시나리오

이 문서에서는 내부 및 외부 소비자에 대해 단일 API Management 서비스를 사용 하 고 온-프레미스 및 클라우드 Api에 대한 단일 프런트 엔드로 작동 하도록 하는 방법을 설명 합니다. Application Gateway에서 사용 가능한 라우팅 기능을 사용하여 외부 소비에 대해 API(예제에서 녹색으로 강조 표시됨)의 하위 집합만을 노출하는 방법을 확인할 수도 있습니다.

첫 번째 설정 예제에서 모든 API는 Virtual Network 내에서만 관리됩니다. 내부 소비자(주황색으로 강조 표시됨)는 모든 내부 및 외부 API에 액세스할 수 있습니다. 트래픽은 인터넷으로 이동 하지 않습니다. 고속 연결은 Express 경로 회로를 통해 제공 됩니다.

![url 경로](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> 시작 하기 전에

* Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 설치 [Azure PowerShell](/powershell/azure/install-az-ps)에서 설치 지침을 참조 하세요. 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management 및 Application Gateway 간에 통합을 만드는 데 무엇이 필요한가요?

* **백 엔드 서버 풀:** API Management 서비스의 내부 가상 IP 주소입니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀 내의 모든 서버에 적용됩니다.
* **프런트 엔드 포트:** Application Gateway에 열려 있는 공용 포트입니다. 이 포트에 도달한 트래픽은 백 엔드 서버 중의 하나로 리디렉션됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기를 백 엔드 서버 풀에 바인딩합니다.
* **사용자 정의 상태 프로브:** 기본적으로 Application Gateway는 IP 주소 기반 프로브를 사용하여 BackendAddressPool의 어떤 서버가 활성 상태인지 파악합니다. API Management 서비스는 올바른 호스트 헤더가 있는 요청에만 응답하므로 기본 프로브는 실패합니다. 서비스가 활성 상태이고 요청을 전달해야 한다는 것을 Application Gateway가 결정할 수 있도록 사용자 지정 상태 프로브를 정의해야 합니다.
* **사용자 지정 도메인 인증서:** 인터넷에서 API Management에 액세스하려면 Application Gateway 프런트 엔드 DNS 이름에 대한 해당 호스트 이름의 CNAME을 매핑해야 합니다. 이렇게 하면 API Management에 전달되는 Application Gateway에 전송되는 호스트 이름 헤더 및 인증서를 APIM에서 유효한 것으로 인식할 수 있습니다. 이 예제에서는 백 엔드 및 개발자 포털에 대해 두 개의 인증서를 사용합니다.  

## <a name="overview-steps"></a> API Management 및 Application Gateway를 통합 하는 데 필요한 단계

1. Resource Manager에 대한 리소스 그룹을 만듭니다.
2. Application Gateway에 대한 Virtual Network, 서브넷 및 공용 IP를 만듭니다. API Management에 대한 다른 서브넷을 만듭니다.
3. 위에서 만든 VNET 서브넷 내에서 API Management 서비스를 만들고 내부 모드로 사용해야 합니다.
4. API Management 서비스에서 사용자 지정 도메인 이름을 설정합니다.
5. Application Gateway 구성 개체를 만듭니다.
6. Application Gateway 리소스를 만듭니다.
7. CNAME을 Application Gateway 리소스의 공용 DNS 이름에서 API Management 프록시 호스트 이름으로 만듭니다.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Application Gateway를 통해 외부적으로 개발자 포털 노출

이 가이드에서는 Application Gateway를 통해 외부 대상에게 **개발자 포털**도 노출합니다. 개발자 포털의 수신기, 프로브, 설정 및 규칙을 만드는 추가 단계가 필요합니다. 모든 세부 정보는 해당 단계에서 제공됩니다.

> [!WARNING]
> Azure AD 또는 타사 인증을 사용하는 경우 Application Gateway에서 [쿠키 기반 세션 선호도](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) 기능을 사용하도록 설정하세요.

> [!WARNING]
> Application Gateway WAF가 개발자 포털에서 OpenAPI 사양의 다운로드를 중단 하지 않도록 하려면 방화벽 규칙 `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`를 사용 하지 않도록 설정 해야 합니다.

## <a name="create-a-resource-group-for-resource-manager"></a>Resource Manager에 대한 리소스 그룹 만들기

### <a name="step-1"></a>1단계

Azure에 로그인

```powershell
Connect-AzAccount
```

자격 증명을 사용하여 인증합니다.

### <a name="step-2"></a>2단계

원하는 구독을 선택합니다.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>3단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 애플리케이션 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Application Gateway에 대한 Virtual Network 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용 하 여 Virtual Network을 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 Virtual Network를 만드는 동안 Application Gateway에 사용할 서브넷 변수에 할당합니다.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2단계

Virtual Network를 만드는 동안 주소 범위 10.0.1.0/24를 API Management에 사용할 서브넷 변수에 할당합니다.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3단계

미국 서부 지역에 리소스 그룹 **apim-appGw-RG**에서 **appgwvnet**이라는 Virtual Network를 만듭니다. 접두사 10.0.0.0/16과 서브넷 10.0.0.0/24 및 10.0.1.0/24를 사용합니다.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4단계:

다음 단계에 대한 서브넷 변수 할당

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>내부 모드로 구성된 VNET 내에서 API Management 서비스 만들기

다음 예제에서는 VNET에서 내부 액세스 전용으로 구성된 API Management 서비스를 만드는 방법을 보여 줍니다.

### <a name="step-1"></a>1단계

위에서 만든 $apimsubnetdata 서브넷을 사용하여 API Management Virtual Network 개체를 만듭니다.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>2단계

Virtual Network 내부에 API Management 서비스를 만듭니다.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

위의 명령이 성공한 후에 액세스하려면 [내부 VNET API Management 서비스에 액세스하는 데 필요한 DNS 구성](api-management-using-with-internal-vnet.md#apim-dns-configuration)을 참조하세요. 이 단계는 30분 이상이 걸릴 수 있습니다.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>API Management에서 사용자 지정 도메인 이름 설정

> [!IMPORTANT]
> [새 개발자 포털](api-management-howto-developer-portal.md) 을 사용 하려면 다음 단계 외에도 API Management의 관리 엔드포인트에 대한 연결을 설정 해야 합니다.

### <a name="step-1"></a>1단계

도메인에 대한 개인 키가 있는 인증서의 세부 정보를 사용 하 여 다음 변수를 초기화 합니다. 이 예제에서는 `api.contoso.net` 및 `portal.contoso.net`을 사용합니다.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>2단계

프록시 및 포털에 대한 호스트 이름 구성 개체를 만들고 설정 합니다.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> 레거시 개발자 포털 연결을 구성 하려면 `-HostnameType DeveloperPortal` `-HostnameType Portal`으로 바꾸어야 합니다.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

리소스 그룹에 공용 IP 리소스 **publicIP01**을 만듭니다.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

서비스를 시작할 때 애플리케이션 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-application-gateway-configuration"></a>애플리케이션 게이트웨이 구성 만들기

애플리케이션 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 애플리케이션 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계

**gatewayIP01**이라는 애플리케이션 게이트웨이 IP 구성을 만듭니다. Application Gateway는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2단계

공용 IP 엔드포인트에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3단계

공용 IP 엔드포인트로 프런트 엔드 IP를 구성합니다.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4단계:

Application Gateway의 인증서가 전달되는 트래픽을 암호화하고 해독하는 데 사용되도록 구성합니다.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5단계

Application Gateway에 대한 HTTP 수신기를 만듭니다. 프런트 엔드 IP 구성, 포트 및 ssl 인증서를 할당합니다.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6단계

API Management 서비스 `ContosoApi` 프록시 도메인 엔드포인트에 사용자 지정 프로브를 만듭니다. 경로 `/status-0123456789abcdef`는 모든 API Management 서비스에서 호스트되는 기본 상태 엔드포인트입니다. `api.contoso.net`을 사용자 지정 프로브 호스트 이름으로 지정하여 SSL 인증서로 보호합니다.

> [!NOTE]
> 호스트 이름 `contosoapi.azure-api.net`은 서비스 `contosoapi`가 공용 Azure에서 생성될 때 구성된 기본 프록시 호스트 이름입니다.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7단계

SSL이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다. 위의 4단계에서 제공하는 동일한 인증서입니다.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8단계

Application Gateway에 HTTP 백 엔드 설정을 구성합니다. 해당 설정이 취소된 후에 백 엔드 요청에 대한 제한 시간을 설정합니다. 이 값은 프로브 시간 초과와 다릅니다.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9단계

위에서 만든 API Management 서비스의 내부 가상 IP 주소로 **apimbackend**라는 백 엔드 IP 주소 풀을 구성합니다.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10단계

기본 라우팅을 사용하도록 Application Gateway에 대한 규칙을 만듭니다.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> 개발자 포털의 특정 페이지에 대한 액세스를 제한하도록 RuleType 및 라우팅을 변경합니다.

### <a name="step-11"></a>11단계

Application Gateway의 크기 및 인스턴스 수를 구성합니다. 이 예제에서는 [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md)를 사용하여 API Management 리소스의 보안을 강화합니다.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12단계

WAF를 "방지" 모드로 구성합니다.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway 만들기

이전 단계의 모든 구성 개체를 사용하여 Application Gateway를 만듭니다.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management 프록시 호스트 이름을 Application Gateway 리소스의 공용 DNS 이름으로 CNAME합니다.

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 쉽게 사용할 수 없는 동적으로 할당된 DNS 이름이 필요합니다.

Application Gateway의 DNS 이름은 APIM 프록시 호스트 이름(예: 위의 예제에서 `api.contoso.net`)을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용되어야 합니다. 프런트 엔드 IP CNAME 레코드를 구성하려면 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. A 레코드를 사용할 경우 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a> 요약
VNET에서 구성 된 Azure API Management은 온-프레미스 또는 클라우드에서 호스트 되는지에 관계 없이 구성 된 모든 Api에 대한 단일 게이트웨이 인터페이스를 제공 합니다. Application Gateway와 API Management의 통합을 통해 특정 API를 인터넷에 액세스할 수 있도록 선택적으로 유연성을 향상시키고 API Management 인스턴스에 대한 프런트 엔드로 웹 애플리케이션 방화벽을 제공합니다.

## <a name="next-steps"></a> 다음 단계
* Azure Application Gateway에 대한 자세한 정보
  * [Application Gateway 개요](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway 웹 애플리케이션 방화벽](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [경로 기반 라우팅을 사용하는 Application Gateway](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* API Management 및 VNET에 대한 자세한 정보
  * [VNET 내에서만 사용할 수 있는 API Management 사용](api-management-using-with-internal-vnet.md)
  * [VNET에서 API Management 사용](api-management-using-with-vnet.md)
