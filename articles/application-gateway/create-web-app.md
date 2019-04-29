---
title: Azure Application Gateway로 웹앱 보호 - PowerShell
description: 이 문서에서는 기존 또는 새 애플리케이션 게이트웨이에 웹앱을 백 엔드 호스트로 구성하는 방법을 안내합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 10/16/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: dcf21fe111ab742074ab4fe580a021338e1f7c43
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122220"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

Application Gateway를 통해 App Service 앱 또는 다른 다중 테넌트 서비스를 백 엔드 풀 멤버로 사용할 수 있습니다. 이 문서에서는 Application Gateway를 사용하여 App Service 앱을 구성하는 방법을 알아봅니다. 첫 번째 예에서는 웹앱을 백 엔드 풀 멤버로 사용하도록 기존 애플리케이션 게이트웨이를 구성하는 방법을 보여 줍니다. 두 번째 예에서는 웹앱을 백 엔드 풀 멤버로 사용하여 새 애플리케이션 게이트웨이를 만드는 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-web-app-behind-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이 뒤에 웹앱 구성

다음 예에서는 웹앱을 기존 애플리케이션 게이트웨이에 백 엔드 풀 멤버로 추가합니다. 웹앱이 작동하려면 프로브 구성의 `-PickHostNamefromBackendHttpSettings` 스위치와 백 엔드 http 설정의 `-PickHostNameFromBackendAddress` 스위치를 모두 제공해야 합니다.

```powershell
# FQDN of the web app
$webappFQDN = "<enter your webapp FQDN i.e mywebsite.chinacloudsites.cn>"

# Retrieve the resource group
$rg = Get-AzResourceGroup -Name 'your resource group name'

# Retrieve an existing application gateway
$gw = Get-AzApplicationGateway -Name 'your application gateway name' -ResourceGroupName $rg.ResourceGroupName

# Define the status codes to match for the probe
$match=New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Add a new probe to the application gateway
Add-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Retrieve the newly added probe
$probe = Get-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw

# Configure an existing backend http settings
Set-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -ApplicationGateway $gw -PickHostNameFromBackendAddress -Port 80 -Protocol http -CookieBasedAffinity Disabled -RequestTimeout 30 -Probe $probe

# Add the web app to the backend pool
Set-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -ApplicationGateway $gw -BackendFqdns $webappFQDN

# Update the application gateway
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="configure-a-web-application-behind-a-new-application-gateway"></a>새 애플리케이션 게이트웨이 뒤에 웹 애플리케이션 구성

이 시나리오에서는 asp.net 시작 웹 사이트 및 애플리케이션 게이트웨이를 사용하여 웹앱을 배포합니다.

```powershell
# Defines a variable for a dotnet get started web app repository location
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"

# Unique web app name
$webappname="mywebapp$(Get-Random)"

# Creates a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location ChinaNorth

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location ChinaNorth -ResourceGroupName $rg.ResourceGroupName -Tier Free

# Creates a web app
$webapp = New-AzWebApp -ResourceGroupName $rg.ResourceGroupName -Name $webappname -Location ChinaNorth -AppServicePlan $webappname

# Configure GitHub deployment from your GitHub repo and deploy once to web app.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}
Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName $rg.ResourceGroupName -ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webappname/web -ApiVersion 2015-08-01 -Force

# Creates a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Creates a vnet for the application gateway
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for use later
$subnet=$vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location ChinaNorth -AllocationMethod Dynamic

# Create a new IP configuration
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool with the hostname of the web app
$pool = New-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -BackendFqdns $webapp.HostNames

# Define the status codes to match for the probe
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Create a probe with the PickHostNameFromBackendHttpSettings switch for web apps
$probeconfig = New-AzApplicationGatewayProbeConfig -name webappprobe -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Define the backend http settings
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120 -PickHostNameFromBackendAddress -Probe $probeconfig

# Create a new front-end port
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Create a new front end IP configuration
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener using the front-end ip configuration and port created earlier
$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Create a new rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the application gateway SKU to use
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzApplicationGateway -Name ContosoAppGateway -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -Probes $probeconfig -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>애플리케이션 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 Application Gateway를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 Application Gateway의 공용 엔드포인트를 가리키도록 합니다. 별칭을 만들려면 애플리케이션 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 애플리케이션 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 이 작업은 [공용 IP 주소](../dns/dns-custom-domain.md#public-ip-address)를 가리키는 CNAME 레코드를 만들어 Azure DNS 또는 다른 DNS 공급자를 사용하여 수행할 수 있습니다. A 레코드를 사용할 경우 애플리케이션 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : chinanorth
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.chinacloudapp.cn"
                            }
```

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 응용 프로그램 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음을 방문하여 리디렉션을 구성하는 방법을 알아봅니다. [PowerShell을 사용하여 Application Gateway에서 리디렉션 구성](redirect-overview.md)

<!-- Update_Description: code update -->