---
title: 보안 Azure AD 웹 응용 프로그램 개발 | Microsoft Docs
description: 이 간단한 샘플 앱은 Azure에서 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선 하는 보안 모범 사례를 구현 합니다.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937974"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Azure AD 앱에 대 한 보안 앱 개발
## <a name="overview"></a>개요

이 샘플은 Azure에서 앱을 개발 하기 위한 보안 리소스에 연결 되는 웹 앱을 사용 하는 간단한 Azure Active Directory입니다. 앱은 Azure에서 앱을 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선 하는 데 도움이 될 수 있는 보안 모범 사례를 구현 합니다.

배포 스크립트는 인프라를 설정 합니다. 배포 스크립트를 실행 한 후 구성 요소와 서비스를 함께 연결 하려면 Azure Portal에서 몇 가지 수동 구성을 수행 해야 합니다. 이 샘플은 소매 업계 내에서 작업 하 고 보안 Azure 인프라를 사용 하 여 보안 Azure Active Directory를 구축 하려는 Azure의 숙련 된 개발자를 대상으로 합니다. 


이 앱을 개발 하 고 배포 하는 방법에 대해 알아봅니다. 
- Azure Key Vault 인스턴스를 만들고 저장 한 다음 암호를 검색 합니다.
- 프런트 엔드 방화벽 액세스와 전용으로 격리 된 Azure 웹 앱을 배포 합니다. 
- OWASP Top 10 규칙 집합을 사용 하는 방화벽을 사용 하 여 Azure 애플리케이션 게이트웨이 인스턴스를 만들고 구성 합니다. 
- Azure 서비스를 사용 하 여 전송 중인 데이터와 미사용 데이터의 암호화를 사용 하도록 설정 합니다. 
- Azure policy 및 security center를 설정 하 여 compliancies를 평가 합니다. 

이 앱을 개발 하 고 배포한 후 설명 된 구성 및 보안 측정값과 함께 다음 샘플 웹 앱을 설정 합니다.

## <a name="architecture"></a>아키텍처
앱은 3 개의 계층을 포함 하는 일반적인 n 계층 응용 프로그램입니다. 모니터링 및 비밀 관리 구성 요소가 통합 된 프런트 엔드, 백 엔드 및 데이터베이스 계층이 다음과 같이 표시 됩니다.

![앱 아키텍처](./media/secure-aad-app/architecture.png)

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처의 세부 정보는 배포 아키텍처 섹션에 있습니다. 

아키텍처는 이러한 구성 요소로 구성 됩니다.

- [Azure Application Gateway](../../application-gateway/index.yml). 응용 프로그램 아키텍처에 대 한 게이트웨이 및 방화벽을 제공 합니다.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). 는 여러 플랫폼에서 확장 가능한 APM (응용 프로그램 성능 관리) 서비스를 제공 합니다.
- [Azure Key Vault](../../key-vault/index.yml) 앱의 암호를 저장 및 암호화 하 고 그에 대 한 액세스 정책 생성을 관리 합니다.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). 클라우드 기반 id 및 액세스 관리 서비스, 로그인 및 액세스 리소스를 제공 합니다.
- [Azure 도메인 이름 시스템](../../dns/dns-overview.md) 도메인을 호스트 하는 서비스를 제공 합니다.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). 에서는 응용 프로그램 크기를 조정 하 고 서비스에 대 한 고가용성을 제공 합니다.
- [Azure Web App](../../app-service/overview.md).  웹 응용 프로그램을 호스팅하기 위한 HTTP 기반 서비스를 제공 합니다.
- [Azure Security Center](../../security-center/index.yml). 클라우드의 하이브리드 워크 로드에서 고급 위협 방지 기능을 제공 합니다.
- [Azure Policy](../../governance/policy/overview.md). 할당 된 정책을 준수 하지 않는 리소스 평가를 제공 합니다.

## <a name="threat-model"></a>위협 모델
위협 모델링은 비즈니스 및 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 한 다음 적절 한 완화 계획이 적용 되도록 하는 프로세스입니다.

이 샘플에서는 [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) 를 사용 하 여 보안 샘플 앱에 대 한 위협 모델링을 구현 했습니다. 구성 요소와 데이터 흐름을 다이어그램으로 작성 하 여 개발 프로세스 초기에 문제 및 위협을 식별할 수 있습니다. 시간과 money는 나중에이를 사용 하 여 저장 됩니다.

샘플 앱에 대 한 위협 모델은 다음과 같습니다.

![위협 모델](./media/secure-aad-app/threat-model.png)

위협 모델링 도구가 생성 하는 몇 가지 샘플 위협과 잠재적 취약성은 다음 스크린샷에 나와 있습니다. 위협 모델은 노출 된 공격 노출 영역에 대 한 개요를 제공 하 고 개발자에 게 문제를 완화 하는 방법을 고려 하는 메시지를 표시 합니다.

![위협 모델 출력](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>필수 조건
응용 프로그램을 실행 하려면 다음과 같은 도구를 설치 해야 합니다.

- 응용 프로그램 코드를 수정 하 고 볼 수 있는 코드 편집기입니다. [Visual Studio Code](https://code.visualstudio.com/) 는 오픈 소스 옵션입니다.
- 개발 컴퓨터에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) 합니다.
- 시스템에서 [Git](https://git-scm.com/) 를 설정 합니다. Git은 소스 코드를 로컬에서 복제 하는 데 사용 됩니다.
- 사용자에 게 친숙 한 방식으로 JSON을 쿼리 하는 UNIX 도구인 [jq](https://stedolan.github.io/jq/)입니다.

샘플 앱의 리소스를 배포 하려면 Azure 구독이 필요 합니다. Azure 구독이 없는 경우 [무료 계정을 만들어](https://azure.microsoft.com/free/) 샘플 앱을 테스트할 수 있습니다.

이러한 도구를 설치 하 고 나면 Azure에 앱을 배포할 준비가 되었습니다.

### <a name="implementation-guidance"></a>구현 가이드
배포 스크립트는 네 단계로 나눌 수 있는 하나의 스크립트입니다. 각 단계에서 [아키텍처 다이어그램](#architecture)에 있는 Azure 리소스를 배포 하 고 구성 합니다.

네 가지 단계는 다음과 같습니다.

- Azure Key Vault를 배포 합니다.
- Azure Web Apps를 배포 합니다.
- 웹 응용 프로그램 방화벽을 사용 하 여 Application Gateway를 배포 합니다.
- 배포 된 앱으로 Azure AD를 구성 합니다.

각 단계는 이전에 배포 된 리소스의 구성을 사용 하 여 이전 단계를 기반으로 합니다.

구현 단계를 완료 하려면 [필수 구성 요소](#prerequisites)아래에 나열 된 도구를 설치 했는지 확인 합니다.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault 배포
이 섹션에서는 암호 및 인증서를 저장 하는 데 사용 되는 Azure Key Vault 인스턴스를 만들고 배포 합니다.

배포를 완료 한 후 Azure에 Azure Key Vault 인스턴스를 배포 했습니다.

Powershell을 사용 하 여 Azure Key Vault을 배포 하려면
 
1. Azure Key Vault에 대 한 변수를 선언 합니다.
2. Azure Key Vault 공급자를 등록 합니다.
3. 인스턴스에 대 한 리소스 그룹을 만듭니다.
4. 3 단계에서 만든 리소스 그룹에 Azure Key Vault 인스턴스를 만듭니다.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>아래 Azure AD 사용자는 Key Vault에 대 한 관리자 권한이 있어야 합니다.
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az Providers 등록
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 만들기
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Key Vault에 관리자 정책 추가
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>사용자 계정 이름을 알고 있는 경우 사용자가 암호화 키, 인증서 및 비밀을 가져오고 나열할 수 있도록 하는 액세스 정책을 만들려면 다음을 수행 합니다.
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Key Vault를 사용 하 여 리소스에 액세스 하는 앱에서 Azure 리소스에 대 한 관리 되는 id를 사용 하는 것이 가장 좋습니다. Key Vault에 대 한 액세스 키가 코드 또는 구성에 저장 되지 않을 경우 보안 상태가 향상 됩니다.

컨테이너에 루트 인증서가 포함 되어 있습니다. 인증서를 가져오기 위해 수행 하는 단계는 다음과 같습니다.

1. 인증 [기관](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)에서 인증서 파일을 다운로드 합니다.
2. 인증서 파일 디코딩:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
이 스크립트는 코드 또는 구성에서 암호를 하드 코딩 하지 않고 Azure Key Vault와 상호 작용 하는 데 MSI에서 사용할 수 있는 App Service 인스턴스에 대해 할당 된 id를 만듭니다.

포털의 Azure Key Vault 인스턴스로 이동 하 여 액세스 정책 탭에서 할당 된 id에 권한을 부여 합니다. **새 액세스 정책 추가**를 선택 합니다. **보안 주체 선택**에서 만든 App Service 인스턴스의 이름과 비슷한 응용 프로그램 이름을 검색 합니다.
응용 프로그램에 연결 된 서비스 사용자가 표시 되어야 합니다. 다음 스크린샷에 표시 된 것 처럼 it를 선택 하 여 액세스 정책 저장 페이지를 선택 합니다.

응용 프로그램은 키를 검색 하기만 하면 되므로 암호 옵션에서 **Get** 권한을 선택 하 여 액세스를 허용 하는 동시에 액세스를 허용 합니다.

![Key Vault 액세스 정책](./media/secure-aad-app/kv-access-policy.png)

*Key Vault 액세스 정책 만들기*

액세스 정책을 저장 한 다음 **액세스** 정책 탭에서 새 변경 내용을 저장 하 여 정책을 업데이트 합니다.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>웹 응용 프로그램 방화벽을 사용 하 여 Application Gateway 배포
웹 앱에서는 서비스를 인터넷의 외부 세계에 직접 노출 하지 않는 것이 좋습니다.
부하 분산 및 방화벽 규칙은 들어오는 트래픽에 대 한 보안을 강화 하 고 제어 하 고 관리 하는 데 도움을 줍니다.

Application Gateway 인스턴스를 배포 하려면

1. 응용 프로그램 게이트웨이를 저장할 리소스 그룹을 만듭니다.
2. 게이트웨이에 연결할 가상 네트워크를 프로 비전 합니다.
3. 가상 네트워크에서 게이트웨이에 대 한 서브넷을 만듭니다.
4. 공용 IP 주소를 프로 비전 합니다.
5. Application gateway를 프로 비전 합니다.
6. 게이트웨이에서 웹 응용 프로그램 방화벽을 사용 하도록 설정 합니다.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure Web Apps 배포
Azure App Service를 사용 하면 Python, Ruby, C#및 Java와 같은 언어를 사용 하 여 웹 앱을 빌드하고 호스트할 수 있습니다. 또한 Azure는 거의 모든 프로그래밍 언어를 Azure App Service 플랫폼에서 실행할 수 있는 사용자 지정 컨테이너를 지원 합니다.

#### <a name="create-an-app-service-plan-in-free-tier"></a>무료 계층에서 App Service 계획 만들기
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>웹 응용 프로그램 만들기
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>계속 하기 전에 사용자 지정 도메인에 대 한 Azure 도메인 이름 시스템 구성 UI로 이동 하 고 https://aka.ms/appservicecustomdns 의 지침에 따라 호스트 이름 "www"에 대 한 CNAME 레코드를 구성 하 고 웹 앱의 기본 도메인 이름을 가리키도록 합니다.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>App Service 계획을 공유 계층으로 업그레이드 (사용자 지정 도메인에 필요한 최소)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>웹 앱에 사용자 지정 도메인 이름 추가
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="network"></a>네트워크
배포를 완료 한 후에는 웹 응용 프로그램 방화벽이 설정 된 응용 프로그램 게이트웨이를 갖게 됩니다.

게이트웨이 인스턴스는 HTTPS에 대해 443 포트를 노출 합니다. 이 구성은 포트 443에서 HTTPS를 통해서만 앱에 액세스할 수 있도록 합니다.

사용 하지 않는 포트를 차단 하 고 공격 노출 영역 노출을 제한 하는 것이 보안 모범 사례입니다.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service 인스턴스에 네트워크 보안 그룹 추가

App Service 인스턴스를 가상 네트워크와 통합할 수 있습니다. 이러한 통합을 통해 앱의 들어오고 나가는 트래픽을 관리 하는 네트워크 보안 그룹 정책을 사용 하 여 구성할 수 있습니다.

1. 이 기능을 사용 하도록 설정 하려면 Azure 앱 서비스 인스턴스 블레이드의 **설정**에서 **네트워킹**을 선택 합니다. 오른쪽 창에서 **VNet 통합**아래를 구성 합니다.

   ![새 가상 네트워크 통합](./media/secure-web-app/app-vnet-menu.png)

    *App Service에 대 한 새 가상 네트워크 통합*
1. 다음 페이지에서 **VNET 추가 (미리 보기)** 를 선택 합니다.

1. 다음 메뉴에서 `aad-vnet`으로 시작 하는 배포에서 만든 가상 네트워크를 선택 합니다. 새 서브넷을 만들거나 기존 서브넷을 선택할 수 있습니다.
   이 경우 새 서브넷을 만듭니다. **주소 범위** 를 **10.0.3.0/24** 로 설정 하 고 서브넷의 **이름을 서브넷으로 설정 합니다.**

   ![가상 네트워크 구성 App Service](./media/secure-web-app/app-vnet-config.png)

    *App Service에 대 한 가상 네트워크 구성*

가상 네트워크 통합을 사용 하도록 설정 했으므로 응용 프로그램에 네트워크 보안 그룹을 추가할 수 있습니다.

1. 검색 상자를 사용 하 여 **네트워크 보안 그룹**을 검색 합니다. 결과에서 **네트워크 보안 그룹** 을 선택 합니다.

    ![네트워크 보안 그룹 검색](./media/secure-web-app/nsg-search-menu.png)

    *네트워크 보안 그룹 검색*

2. 다음 메뉴에서 **추가**를 선택 합니다. NSG의 **이름과** 이를 찾을 **리소스 그룹** 을 입력 합니다. 이 NSG는 application gateway의 서브넷에 적용 됩니다.

    ![NSG 만들기](./media/secure-web-app/nsg-create-new.png)

    *NSG 만들기*

3. NSG를 만든 후 선택 합니다. 블레이드의 **설정**에서 **인바운드 보안 규칙**을 선택 합니다. 443 포트를 통해 응용 프로그램 게이트웨이로 들어오는 연결을 허용 하도록 이러한 설정을 구성 합니다.

   ![NSG 구성](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 구성*

4. 게이트웨이 NSG에 대 한 아웃 바운드 규칙에서 서비스 태그를 대상으로 하는 규칙을 만들어 App Service 인스턴스에 대 한 아웃 바운드 연결을 허용 하는 규칙을 추가 `AppService`

   ![NSG에 대 한 아웃 바운드 규칙 추가](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG에 대 한 아웃 바운드 규칙 추가*

    게이트웨이가 아웃 바운드 규칙을 가상 네트워크로 보낼 수 있도록 다른 아웃 바운드 규칙을 추가 합니다.

   ![다른 아웃 바운드 규칙 추가](./media/secure-web-app/nsg-outbound-vnet.png)

    *다른 아웃 바운드 규칙 추가*

5. NSG의 서브넷 블레이드에서 **연결**을 선택 하 고, 배포에서 만든 가상 네트워크를 선택 하 고, **gw**이라는 게이트웨이 서브넷을 선택 합니다. NSG가 서브넷에 적용 됩니다.

6. 이전 단계에서와 같이 App Service 인스턴스에 대 한 다른 NSG를 만듭니다. 이름을 지정 합니다. 응용 프로그램 게이트웨이 NSG와 마찬가지로 포트 443에 대 한 인바운드 규칙을 추가 합니다.

   이 앱의 경우를 포함 하지 않는 App Service Environment 인스턴스에 App Service 인스턴스가 배포 되어 있는 경우 App Service NSG의 인바운드 보안 그룹에서 포트 454-455를 열어 Azure Service Health 프로브를 허용 하는 인바운드 규칙을 추가할 수 있습니다. 구성:

   ![Azure Service Health 프로브에 대 한 규칙 추가](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure Service Health 프로브에 대 한 규칙 추가 (App Service Environment에만 해당)*

공격 노출 영역을 제한 하려면 응용 프로그램 게이트웨이만 응용 프로그램에 액세스할 수 있도록 App Service 네트워크 설정을 수정 합니다.
설정을 적용 하려면 App Service 네트워크 탭으로 이동 하 고, **IP 제한** 탭을 선택 하 고, 응용 프로그램 게이트웨이의 ip만 서비스에 직접 액세스 하도록 허용 하는 허용 규칙을 만듭니다. 해당 개요 페이지에서 게이트웨이의 IP 주소를 검색할 수 있습니다. **Ip 주소 CIDR** 탭에서 ip 주소를 `<GATEWAY_IP_ADDRESS>/32`형식으로 입력 합니다.

![게이트웨이만 허용](./media/secure-web-app/app-allow-gw-only.png)

*게이트웨이 IP만 App Service에 액세스할 수 있도록 허용*

### <a name="azure-domain-name-system"></a>Azure 도메인 이름 시스템 
Azure Domain Name System 또는 Azure Domain Name System은 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환 (또는 확인) 하는 일을 담당 합니다. Azure 도메인 이름 시스템 (https://docs.microsoft.com/azure/dns/dns-overview) 는 Azure 인프라를 사용 하 여 이름 확인을 제공 하는 도메인 이름 시스템 도메인에 대 한 호스팅 서비스입니다. 사용자는 Azure에서 도메인을 호스트 하 여 다른 Azure 서비스와 동일한 자격 증명, Api, 도구 및 대금 청구를 사용 하 여 도메인 이름 시스템 레코드를 관리할 수 있습니다. Azure 도메인 이름 시스템은 사설 도메인 이름 시스템 도메인도 지원 합니다.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption는 Windows의 BitLocker 기능을 활용 하 여 데이터 디스크에 대 한 볼륨 암호화를 제공 합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

### <a name="identity-management"></a>ID 관리
다음 기술은 Azure 환경의 카드 소유자 데이터에 대 한 액세스를 관리 하는 기능을 제공 합니다.
- Azure Active Directory는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 id 관리 서비스입니다. 이 솔루션에 대 한 모든 사용자는 Azure WebApp에 액세스 하는 사용자를 포함 하 여 Azure Active Directory에서 생성 됩니다.
- 관리자는 Azure 역할 기반 액세스 제어를 통해 세분화 된 액세스 권한을 정의 하 여 사용자가 작업을 수행 하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 카드 소유자 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- Azure Active Directory Privileged Identity Management를 통해 고객은 카드 소유자 데이터와 같은 특정 정보에 대 한 액세스 권한이 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- Azure Active Directory Identity Protection는 조직의 id에 영향을 주는 잠재적 취약성을 검색 하 고, 조직의 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 하 고, 의심 스러운 조사 인시던트를 해결 하기 위해 적절 한 조치를 취해야 합니다.
### <a name="secrets-management"></a>비밀 관리
이 솔루션은 키와 비밀을 관리 하는 데 Azure Key Vault를 사용 합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 다음 Azure Key Vault 기능을 통해 고객은 이러한 데이터를 보호 하 고 액세스할 수 있습니다.
   - 필요에 따라 고급 액세스 정책이 구성됩니다.
   - Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
   - Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
   - Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 형식은 HSM (하드웨어 보안 모듈) 보호 된 2048 비트 RSA 키입니다.
   - Key Vault를 사용 하 여 키와 비밀 (예: 인증 키, 저장소 계정 키, 데이터 암호화 키)을 암호화할 수 있습니다. PFX 파일 및 암호)를 사용 하 여 하드웨어 보안 모듈 (Hsm)로 보호 되는 키를 사용 합니다. 
   - RBAC (역할 기반 Access Control)를 사용 하 여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 사용 권한을 할당 합니다.     
   - 자동 갱신를 사용 하 여 TLS 인증서를 관리 하려면 Key Vault을 사용 합니다. 
   - Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
   - 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다
### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center를 통해 고객은 워크 로드에 걸쳐 보안 정책을 중앙에서 적용 하 고 관리 하며, 위협에 대 한 노출을 제한 하 고, 공격을 감지 하 고 대응할 수 있습니다. 게다가 
   - Azure Security Center 보안 상태를 개선 하 고 데이터를 보호 하는 데 도움이 되는 구성과 서비스 권장 사항을 제공 하기 위해 Azure 서비스의 기존 구성에 액세스 합니다.
   - Azure Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Azure Security Center에는 위협 또는 의심 스러운 활동이 발생 했을 때 트리거되는 미리 정의 된 보안 경고 집합이 있습니다. Azure Security Center의 사용자 지정 경고 규칙은 고객이 자신의 환경에서 이미 수집 된 데이터를 기반으로 새 보안 경고를 정의할 수 있도록 합니다.
   - Azure Security Center에서는 우선 순위가 지정된 보안 경고 및 인시던트를 제공하여 고객이 잠재적인 보안 문제를 더 쉽게 검색하고 해결하게 합니다. 위협 인텔리전스 보고서는 위협에 대 한 사고 대응 팀을 지원 하기 위해 검색 된 각 위협에 대해 생성 됩니다.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   이 아키텍처는 웹 애플리케이션 방화벽이 구성되어 있고 OWASP 규칙 집합을 사용할 수 있는 Azure Application Gateway를 통해 보안 취약성의 위험을 줄입니다. 추가 기능에는
   - 종단 간 SSL.
   - TLS v1.0 및 v 1.1을 사용 하지 않도록 설정 합니다.
   - TLSv 1.2를 사용 하도록 설정 합니다.
   - 웹 응용 프로그램 방화벽 (방지 모드).
   - OWASP 3.0 규칙 집합을 사용 하는 방지 모드입니다.
   - 진단 로깅을 사용 하도록 설정 합니다.
   - 사용자 지정 상태 프로브.
   - Azure Security Center 및 Azure Advisor는 추가 보호 및 알림을 제공 합니다. Azure Security Center는 평판 시스템도 제공합니다.
### <a name="logging-and-auditing"></a>로깅 및 감사
Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.
   - 활동 로그: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) 는 구독의 리소스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
   - 진단 로그: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 는 모든 리소스에서 내보낸 모든 로그를 포함 합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Storage 로그, Key Vault 감사 로그, Application Gateway 액세스 및 방화벽 로그가 포함 됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다.
### <a name="azure-monitor-logs"></a>Azure Monitor 로그
   이러한 로그는 처리, 저장 및 대시보드 보고를 위해 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 에 통합 되어 있습니다. 수집이 완료되면 데이터는 Log Analytics 작업 영역 내의 각 데이터 형식에 대해 별도 테이블로 구성되어 원본에 관계없이 모든 데이터가 함께 분석되도록 합니다. 또한 Azure Security Center는 고객이 Kusto 쿼리를 사용 하 여 보안 이벤트 데이터에 액세스 하 고 다른 서비스의 데이터와 결합할 수 있도록 하는 Azure Monitor 로그와 통합 됩니다.

   다음 Azure [모니터링 솔루션](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 은이 아키텍처의 일부로 포함 되어 있습니다.

   - [Active Directory 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 검사 솔루션은 표준 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
   - [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포 된 에이전트 수 및 해당 지리적 배포를 보고 하며, 응답 하지 않는 에이전트 수와 작업 데이터를 제출 하는 에이전트 수를 보고 합니다.
   - [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 사용 하면 조직에서 Azure 리소스의 API 호출 추적을 포함 하 여 성능을 추적 하 고, 경고를 만들고, 데이터를 보관할 수 있습니다.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 응용 프로그램 성능 관리 서비스입니다. Application Insights는 성능 이상을 감지하며, 고객이 라이브 웹 애플리케이션을 모니터링하는 데 사용할 수 있습니다. 고객이 문제를 진단하고 실제로 앱을 사용하여 수행하는 작업을 파악할 수 있는 강력한 분석 도구를 포함하고 있습니다. 고객이 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

### <a name="azure-key-vault"></a>Azure Key Vault
   키를 저장할 조직에 대 한 자격 증명 모음을 만들고 아래와 같은 운영 작업에 대 한 책임을 유지 합니다.

   - Key Vault 포함 된 데이터   
   - Application Insight 키
   - 데이터 저장소 액세스 키
   - 연결 문자열
   - 데이터 테이블 이름
   - 사용자 자격 증명
   - 필요에 따라 고급 액세스 정책이 구성됩니다.
   - Key Vault 액세스 정책은 키와 암호에 필요한 최소 권한으로 정의 됩니다.
   - Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
   - Key Vault의 모든 키는 HSM (하드웨어 보안 모듈)에 의해 보호 됩니다 [키 유형 = HSM (하드웨어 보안 모듈) 보호 됨       
     2048 비트 RSA 키]
   - 모든 사용자/id에는 RBAC (역할 기반 Access Control)를 사용 하 여 필요한 최소한의 권한이 부여 됩니다.
   - 애플리케이션이 서로 신뢰하고 런타임 시 동일한 비밀에 액세스할 필요가 없으면 애플리케이션에서 Key Vault를 공유하지 않습니다.
   - Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
   - 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
   이 PaaS 웹 응용 프로그램 참조 아키텍처의 일부로 배포 된 리소스에 대 한 연결을 안전 하 게 설정 하 여 구성 하는 데 필요한 보안 VPN 터널 또는 [express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로입니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

   Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 프라이빗 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 수행 되며 고객이 고객의 네트워크와 Azure 간의 암호화 된 링크 내에서 정보를 안전 하 게 "터널링" 할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포한 안전하고 완성도가 높은 기술입니다. IPsec 터널 모드는이 옵션에서 암호화 메커니즘으로 사용 됩니다.

   VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

   온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC 구현

1. 소스 코드 리포지토리를 복제 하려면 다음 Git 명령을 사용 합니다.

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>리디렉션 Url 업데이트
1.  Azure Portal으로 다시 이동 합니다. 왼쪽 탐색 창에서 Azure Active Directory 서비스를 선택 하 고 앱 등록를 선택 합니다.
2.  결과 화면에서 WebApp-OpenIDConnect-DotNet 응용 프로그램을 선택 합니다.
3.  리디렉션 Uri 섹션의 인증 탭 o에서 콤보 상자에서 웹을 선택 하 고 다음 리디렉션 Uri를 추가 합니다.
    [https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net](https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ) https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc 고급 설정 섹션에서 로그 아웃 URL을로 설정 합니다. https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  브랜딩 탭에서 app service의 주소로 홈 페이지 URL을 업데이트 합니다 (예: https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ).
        o 구성을 저장 합니다.
5.  응용 프로그램이 web api를 호출 하는 경우에는 프로젝트에 필요한 변경 내용을 적용 해야 합니다 .이 경우 localhost 대신 게시 된 API URL을 호출 합니다.
샘플 게시
    1.  App Service의 개요 탭에서 게시 프로필 가져오기 링크를 클릭 하 여 게시 프로필을 다운로드 하 고 저장 합니다. 원본 제어에서와 같이 다른 배포 메커니즘도 사용할 수 있습니다.
    2.  Visual Studio로 전환 하 고 WebApp-OpenIDConnect-code-v2 프로젝트로 이동 합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 게시를 선택 합니다. 아래쪽 표시줄에서 프로필 가져오기를 클릭 하 고 이전에 다운로드 한 게시 프로필을 가져옵니다.
    3.  구성을 클릭 하 고 연결 탭에서 홈 페이지 url에 https가 되도록 대상 URL을 업데이트 합니다 (예: https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ). 다음을 클릭합니다.
    4.  설정 탭에서 조직 인증 사용이 선택 되어 있지 않은지 확인 합니다. [저장]을 클릭합니다. 주 화면에서 게시를 클릭 합니다.
    5.  Visual Studio에서 프로젝트를 게시하고 브라우저를 프로젝트의 URL로 자동으로 엽니다. 프로젝트의 기본 웹 페이지가 표시되면 게시가 성공적으로 수행된 것입니다.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory에 대 한 Multi-Factor Authentication 구현
   관리자는 포털의 구독 계정이 보호 되는지 확인 해야 합니다. 구독은 만든 리소스를 관리 하기 때문에 공격에 취약 합니다. 구독을 보호 하려면 구독의 **Azure Active Directory** 탭에서 Multi-Factor Authentication를 사용 하도록 설정 합니다.

   Azure AD는 특정 조건에 맞는 사용자 또는 사용자 그룹에 적용 되는 정책에 따라 작동 합니다.
Azure는 관리자가 포털에 로그인 하는 데 2 단계 인증이 필요 하도록 지정 하는 기본 정책을 만듭니다.
이 정책을 사용 하도록 설정 하면 로그 아웃 하 고 Azure Portal에 다시 로그인 하 라는 메시지가 표시 될 수 있습니다.

관리자 로그인에 MFA를 사용 하도록 설정 하려면

   1. Azure Portal에서 **Azure Active Directory** 탭으로 이동 합니다.
   2. 보안 범주 아래에서 조건부 액세스를 선택 합니다. 이 화면이 표시 됩니다.

       ![조건부 액세스-정책](./media/secure-aad-app/ad-mfa-conditional-add.png)

새 정책을 만들 수 없는 경우

   1. **MFA** 탭으로 이동 합니다.
   2. 무료 평가판을 구독 하려면 Azure AD Premium **무료 평가판** 링크를 선택 합니다.

   ![Azure AD Premium 무료 평가판](./media/secure-aad-app/ad-trial-premium.png)

조건부 액세스 화면으로 돌아갑니다.

   1. 새 정책 탭을 선택 합니다.
   2. 정책 이름을 입력합니다.
   3. MFA를 사용 하도록 설정할 사용자 또는 그룹을 선택 합니다.
   4. **액세스 제어**에서 **허용** 탭을 선택한 후 **다단계 인증 필요** 및 기타 설정을 선택 합니다.

   ![MFA 요구](./media/secure-aad-app/ad-mfa-conditional-add.png)

   화면 위쪽의 확인란을 선택 하 여 정책을 사용 하도록 설정 하거나 **조건부 액세스** 탭에서이 작업을 수행할 수 있습니다. 이 정책을 사용 하도록 설정 하면 사용자가 포털에 로그인 하려면 MFA가 필요 합니다.

   모든 Azure 관리자에 대해 MFA를 요구 하는 기준 정책이 있습니다. 포털에서 즉시 사용 하도록 설정할 수 있습니다. 이 정책을 사용 하도록 설정 하면 현재 세션이 무효화 되 고 다시 로그인 되도록 할 수 있습니다.

   기준 정책을 사용 하지 않는 경우
   1.   **관리자 용 MFA 필요**를 선택 합니다.
   2.   **즉시 정책 사용**을 선택 합니다.

   ![즉시 정책 사용을 선택 합니다.](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure 센티널을 사용 하 여 앱 및 리소스 모니터링

   응용 프로그램이 커지면 리소스에서 수신 된 모든 보안 신호 및 메트릭을 집계 하 고 작업 지향 방식으로 유용 하 게 만드는 것이 어려워집니다.

   Azure 센티널은 데이터를 수집 하 고, 가능한 위협의 유형을 검색 하 고, 보안 인시던트에 대 한 가시성을 제공 하도록 설계 되었습니다.
수동 작업을 대기 하는 동안 Azure 센티널은 미리 작성 된 플레이 북을 사용 하 여 경고 및 인시던트 관리 프로세스를 시작할 수 있습니다.

   샘플 앱은 Azure 센티널에서 모니터링할 수 있는 몇 가지 리소스로 구성 되어 있습니다.
Azure 센티널을 설정 하려면 먼저 다양 한 리소스에서 수집 된 모든 데이터를 저장 하는 Log Analytics 작업 영역을 만들어야 합니다.

이 작업 영역을 만들려면

   1. Azure Portal 검색 상자에서 **Log Analytics**를 검색 합니다. **Log Analytics 작업 영역**을 선택합니다.

   ![Log Analytics 작업 영역 검색](./media/secure-aad-app/sentinel-log-analytics.png)

   *Log Analytics 작업 영역 검색*

   2. 다음 페이지에서 **추가** 를 선택 하 고 작업 영역에 대 한 이름, 리소스 그룹 및 위치를 제공 합니다.
   ![Log Analytics 작업 영역 만들기](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Log Analytics 작업 영역 만들기*

   3. 검색 상자를 사용 하 여 **Azure 센티널**을 검색 합니다.

   ![Azure 센티널 검색](./media/secure-aad-app/sentinel-add.png)

   *Azure 센티널 검색*

   4. **추가** 를 선택한 다음 이전에 만든 Log Analytics 작업 영역을 선택 합니다.

   ![Log Analytics 작업 영역 추가](./media/secure-aad-app/sentinel-workspace-add.png)

   *Log Analytics 작업 영역 추가*

   5. **Azure 센티널-데이터 커넥터** 페이지의 **구성**에서 **데이터 커넥터**를 선택 합니다. Azure 센티널의 분석을 위해 Log Analytics storage 인스턴스에 연결할 수 있는 Azure 서비스 배열이 표시 됩니다.

   ![Log Analytics 데이터 커넥터](./media/secure-aad-app/sentinel-connectors.png)

      *Azure 센티널에 데이터 커넥터 추가*

   예를 들어 응용 프로그램 게이트웨이를 연결 하려면 다음 단계를 수행 합니다.

   1. Azure 애플리케이션 게이트웨이 인스턴스 블레이드를 엽니다.
   2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
   3. **진단 설정 추가**를 선택 합니다.

   ![Application Gateway 진단 추가](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Application Gateway 진단 추가*

   4. **진단 설정** 페이지에서 사용자가 만든 Log Analytics 작업 영역을 선택 하 고 수집 하 여 Azure 센티널로 보낼 모든 메트릭을 선택 합니다. **저장**을 선택합니다.

   ![Azure 센티널 커넥터 설정](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>비용 고려 사항
   Azure 계정이 아직 없는 경우 무료 계정을 만들 수 있습니다. 무료 [계정 페이지로](https://azure.microsoft.com/free/) 이동 하 여 시작 하 고, 무료 Azure 계정으로 수행할 수 있는 작업을 확인 하 고, 12 개월 동안 무료 인 제품을 알아보세요.

   보안 기능을 사용 하 여 샘플 앱에 리소스를 배포 하려면 일부 프리미엄 기능에 대해 비용을 지불 해야 합니다. 앱 크기를 조정 하 고 Azure에서 제공 하는 무료 계층 및 평가판을 응용 프로그램 요구 사항에 맞게 업그레이드 해야 하는 경우 비용이 늘어날 수 있습니다. Azure [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 비용을 예측 합니다.

## <a name="next-steps"></a>다음 단계
   다음 문서는 보안 응용 프로그램을 디자인, 개발 및 배포 하는 데 도움이 될 수 있습니다.

- [디자인](secure-design.md)
- [개발](secure-develop.md)
- [배포](secure-deploy.md)
