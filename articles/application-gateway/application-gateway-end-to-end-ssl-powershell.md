---
title: Azure Application Gateway에서 엔드투엔드 SSL 구성
description: 이 문서에서는 PowerShell을 사용하여 Azure Application Gateway로 엔드투엔드 SSL을 구성하는 방법에 대해 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286459"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>PowerShell과 함께 Application Gateway를 사용하여 종단 간 SSL 구성

## <a name="overview"></a>개요

Azure Application Gateway는 트래픽의 엔드투엔드 암호화를 지원합니다. Application Gateway는 애플리케이션 게이트웨이에서 SSL 연결을 종료합니다. 그러면 게이트웨이에서 트래픽에 라우팅 규칙을 적용하고, 패킷을 다시 암호화하고, 정의된 라우팅 규칙에 따라 적절한 백 엔드 서버에 패킷을 전달합니다. 웹 서버의 모든 응답은 동일한 프로세스를 거쳐 최종 사용자에게 돌아갑니다.

Application Gateway가 사용자 지정 SSL 옵션 정의를 지원합니다. 또한 **TLSv1.0**/**TLSv1.1**/**TLSv1.2** 프로토콜 버전을 사용하지 않도록 설정할 수 있으며, 사용할 암호 그룹 및 기본 설정 순서도 정의할 수 있습니다. 구성 가능한 SSL 옵션에 대한 자세한 내용은 [SSL 정책 개요](application-gateway-SSL-policy-overview.md)를 참조하세요.

> [!NOTE]
> SSL 2.0 및 SSL 3.0은 기본적으로 사용할 수 없도록 설정되며 사용하도록 설정할 수 없습니다. 보안되지 않은 것으로 간주되며 Application Gateway와 함께 사용할 수 없습니다.

![시나리오 이미지][scenario]

## <a name="scenario"></a>시나리오

이 시나리오에서는 PowerShell과 함께 엔드투엔드 SSL을 사용하여 Application Gateway를 만드는 방법을 알아봅니다.

이 시나리오에서는 다음을 수행합니다.

* **appgw-rg**라는 리소스 그룹을 만듭니다.
* 주소 공간이 **10.0.0.0/16**인 **appgwvnet**이라는 가상 네트워크를 만듭니다.
* **appgwsubnet** 및 **appsubnet**라는 두 개의 서브넷을 만듭니다.
* SSL 프로토콜 버전 및 암호 그룹을 제한하는 엔드투엔드 SSL 암호화를 지원하는 소형 Application Gateway를 만듭니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Application Gateway를 사용하여 엔드투엔드 SSL을 구성하려면 게이트웨이에 사용할 인증서와 백 엔드 서버에 사용할 인증서가 필요합니다. 게이트웨이 인증서는 SSL 프로토콜 사양에 따라 대칭 키를 파생하는 데 사용됩니다. 이렇게 파생된 대칭 키는 게이트웨이로 전송되는 트래픽을 암호화하고 암호를 해독하는 데 사용됩니다. 게이트웨이 인증서는 개인 정보 교환(PFX) 형식이어야 합니다. 이 파일 형식을 사용하면 애플리케이션 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 프라이빗 키를 내보낼 수 있습니다.

종단 간 SSL 암호화의 경우 응용 프로그램 게이트웨이에서 백 엔드를 명시적으로 허용 해야 합니다. 백 엔드 서버의 공용 인증서를 Application Gateway에 업로드합니다. 인증서를 추가하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 그러면 엔드투엔드 통신의 보안이 유지됩니다.

구성 프로세스는 다음 섹션에 설명되어 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

이 섹션에서는 Application Gateway를 포함하는 리소스 그룹을 만드는 과정을 안내합니다.

1. Azure 계정에 로그인합니다.

   ```powershell
   Connect-AzAccount
   ```

2. 이 시나리오에 사용할 구독을 선택합니다.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. 리소스 그룹을 만듭니다. (기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뜁니다.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>애플리케이션 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 가상 네트워크 및 두 개의 서브넷을 만듭니다. 하나의 서브넷은 Application Gateway를 보관하는 데 사용되고 다른 서브넷은 웹 애플리케이션을 호스트하는 백 엔드 서버에 사용됩니다.

1. Application Gateway에 사용할 서브넷의 주소 범위를 할당합니다.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Application Gateway를 구성하는 서브넷은 크기가 적절해야 합니다. Application Gateway는 최대 10개의 인스턴스로 구성할 수 있습니다. 각 인스턴스는 서브넷에서 1개의 IP 주소를 사용합니다. 서브넷이 너무 적으면 Application Gateway의 크기에 불리한 영향을 미칠 수 있습니다.
   >

2. 백 엔드 주소 풀에 사용할 주소 범위를 할당합니다.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. 이전 단계에서 정의한 서브넷을 사용하여 가상 네트워크를 만듭니다.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. 다음 단계에 사용할 가상 네트워크 리소스와 서브넷 리소스를 검색합니다.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

애플리케이션 게이트웨이에 사용할 공용 IP 리소스를 만듭니다. 이 공용 IP 주소는 다음 단계 중 하나에 사용됩니다.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway는 정의된 도메인 레이블로 만든 공용 IP 주소의 사용을 지원하지 않습니다. 도메인 레이블이 동적으로 생성된 공용 IP 주소만 지원됩니다. Application Gateway에 친숙한 DNS 이름이 필요한 경우 CNAME 레코드를 별칭으로 사용하는 것이 좋습니다.

## <a name="create-an-application-gateway-configuration-object"></a>애플리케이션 게이트웨이 구성 개체 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정합니다. 다음 단계 애플리케이션 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

1. Application Gateway IP 구성 만들기 이 설정으로 Application Gateway에서 사용하는 서브넷을 구성합니다. Application Gateway가 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. 프런트 엔드 IP 구성을 만듭니다. 이 설정은 프라이빗 또는 공용 IP 주소를 Application Gateway의 프런트 엔드에 매핑합니다. 다음 단계는 이전 단계의 공용 IP 주소를 프런트 엔드 IP 구성과 연결합니다.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. 백 엔드 웹 서버의 IP 주소를 사용하여 백 엔드 IP 주소 풀을 구성합니다. 이러한 IP 주소는 프런트 엔드 IP 엔드포인트에서 들어오는 네트워크 트래픽을 수신합니다. 샘플의 IP 주소를 사용자 고유의 애플리케이션 IP 주소 엔드포인트로 바꿉니다.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > FQDN(정규화된 도메인 이름) 역시 백 엔드 서버의 IP 주소를 대체하는 데 사용할 수 있는 유효한 값입니다. **-BackendFqdns** 스위치를 사용하여 활성화합니다. 

4. 공용 IP 엔드포인트에 대한 프런트 엔드 IP 포트를 구성합니다. 이 포트는 최종 사용자가 연결하는 포트입니다.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Application Gateway에 대한 인증서를 구성합니다. 이 인증서는 Application Gateway의 트래픽을 다시 암호화하고 암호 해독하는 데 사용됩니다.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > 이 샘플은 SSL 연결에 사용된 인증서를 구성합니다. 인증서는 .pfx 형식으로 4~12자의 암호여야 합니다.

6. Application Gateway에 대한 HTTP 수신기를 만듭니다. 사용할 프런트 엔드 IP 구성, 포트 및 SSL 인증서를 할당합니다.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. SSL이 활성화된 백 엔드 풀 리소스에 사용할 인증서를 업로드합니다.

   > [!NOTE]
   > 기본 프로브는 공용 키를 백엔드의 IP 주소에 바인딩된 *기본* SSL에서 가져오고 프로브가 받는 공용 키 값과 여기서 사용자가 제공한 공용 키 값을 비교합니다. 
   > 
   > 백 엔드에서 호스트 헤더와 SNI(서버 이름 표시)를 사용하는 경우 검색된 공개 키는 트래픽이 흐르는 의도된 사이트가 아닐 수 있습니다. 확실하지 않은 경우 백 엔드 서버에서 https://127.0.0.1/ 을 방문하여 *기본* SSL 바인딩에 사용되는 인증서를 확인하세요. 이 섹션에서는 해당 요청에서 공개 키를 사용합니다. HTTPS 바인딩에서 호스트 헤더 및 SNI를 사용하고, 수동 브라우저 요청에서 백 엔드 서버의 https://127.0.0.1/ 로 응답 및 인증서를 받지 않은 경우 기본 SSL 바인딩을 설정해야 합니다. 그렇게 하지 않으면 프로브가 실패하고 백 엔드가 허용 목록에 추가되지 않습니다.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > 이전 단계에서 제공 하는 인증서는 백 엔드에 있는 .pfx 인증서의 공개 키 여야 합니다. 백 엔드 서버에 설치된 인증서(루트 인증서 제외)를 CER(Claim, Evidence, Reasoning) 형식으로 내보내고 이 단계에서 사용합니다. 이 단계에서는 Application Gateway를 통해 백 엔드를 허용 목록에 추가합니다.

   Application Gateway v2 SKU를 사용하는 경우 인증 인증서 대신 신뢰할 수 있는 루트 인증서를 만듭니다. 자세한 내용은 [Application Gateway의 종단 간 SSL 개요](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)를 참조하세요.

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Application Gateway 백 엔드에 대해 HTTP 설정을 구성합니다. 이전 단계에서 업로드한 인증서를 HTTP 설정에 할당합니다.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Application Gateway v2 SKU의 경우 다음 명령을 사용합니다.

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. 부하 분산 장치 동작을 구성하는 부하 분산 장치 라우팅 규칙을 만듭니다. 이 예제에서는 기본 라운드 로빈 규칙을 만듭니다.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. 애플리케이션 게이트웨이의 인스턴스 크기를 구성합니다. 사용 가능한 크기는 **Standard\_Small**, **Standard\_Medium** 및 **Standard\_Large**입니다.  용량의 경우 사용 가능한 값은 **1**부터 **10**까지입니다.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 작은 게이트웨이는 개발 테스트용이며 프로덕션용으로는 사용되지 않습니다.

11. Application Gateway에서 사용할 SSL 정책을 구성합니다. Application Gateway는 SSL 프로토콜 버전에 최소 버전을 설정하는 기능을 지원합니다.

    다음 값은 정의될 수 있는 프로토콜 버전 목록입니다.

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    다음 예제에서는 최소 프로토콜 버전을 **TLSv1_2**로 설정하고 **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** 및 **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**만 사용하도록 설정합니다.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

위의 모든 단계를 사용하여 Application Gateway를 만듭니다. 게이트웨이 만들기는 실행하는 데 시간이 오래 걸리는 과정입니다.

V1 SKU의 경우 아래 명령을 사용 합니다.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

V2 SKU의 경우 아래 명령을 사용 합니다.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>백 엔드 인증서가 만료 된 경우 새 인증서 적용

이 절차를 사용 하 여 백 엔드 인증서가 만료 된 경우 새 인증서를 적용 합니다.

1. 업데이트할 Application Gateway를 검색합니다.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. 인증서의 공개 키를 포함 하는 .cer 파일의 새 인증서 리소스를 추가 하 고 응용 프로그램 게이트웨이에서 SSL 종료를 위해 수신기에 추가 된 것과 동일한 인증서를 사용할 수도 있습니다.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. 새 인증 인증서 개체를 변수로 가져옵니다 (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. **BackendHttp** 설정에 새 인증서를 할당 하 고 $AuthCert 변수를 참조 합니다. 변경 하려는 HTTP 설정 이름을 지정 합니다.
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. 응용 프로그램 게이트웨이에 대한 변경 내용을 커밋하고 $out 변수에 포함 된 새 구성을 전달 합니다.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>HTTP 설정에서 사용 되지 않은 만료 된 인증서 제거

이 절차를 사용 하 여 HTTP 설정에서 사용 되지 않은 만료 된 인증서를 제거 합니다.

1. 업데이트할 Application Gateway를 검색합니다.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. 제거 하려는 인증 인증서의 이름을 나열 합니다.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. 응용 프로그램 게이트웨이에서 인증 인증서를 제거 합니다.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. 변경 내용을 커밋합니다.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>기존 Application Gateway의 SSL 프로토콜 버전 제한

이전 단계에서는 엔드투엔드 SSL을 사용하여 애플리케이션을 만들고 특정 SSL 프로토콜 버전을 비활성화했습니다. 다음 예제에서는 기존 Application Gateway의 특정 SSL 정책을 비활성화합니다.

1. 업데이트할 Application Gateway를 검색합니다.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. SSL 정책을 정의합니다. 다음 예제에서 **TLSv1.0** 및 **TLSv1.1**은 사용하지 않고 암호 그룹 **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** 및 **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**만 허용합니다.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. 마지막으로 게이트웨이를 업데이트합니다. 이 마지막 단계는 오래 실행되는 작업입니다. 작업이 완료되면 Application Gateway에 엔드투엔드 SSL이 구성됩니다.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>애플리케이션 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 애플리케이션 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 애플리케이션 게이트웨이의 공용 엔드포인트를 가리키도록 합니다. 자세한 내용은 [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. 

별칭을 구성하려면 애플리케이션 게이트웨이에 연결된 **PublicIPAddress** 요소를 사용하여 애플리케이션 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 애플리케이션 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 애플리케이션을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. A 레코드를 사용할 경우 애플리케이션 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>다음 단계

Application Gateway를 통한 웹 애플리케이션 방화벽의 웹 애플리케이션 보안 강화에 대한 자세한 내용은 [웹 애플리케이션 방화벽 개요](application-gateway-webapplicationfirewall-overview.md)를 참조하세요.

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
