---
title: 보안
description: App Service에서 앱 보안을 유지하는 방법과 위협으로부터 앱을 추가로 잠그는 방법을 알아봅니다.
keywords: azure app service, 웹앱, 모바일 앱, API 앱, 함수 앱, 보안, 안전, 보호, 규정 준수, 준수, 인증서, 트러스트, 암호화, 암호화됨, IP 제한, 인증, 권한 부여, 관리 서비스 ID, 관리 ID, 비밀, 패칭, 패치, 버전, 격리, 네트워크 격리, web app, mobile app, api app, function app, security, secure, secured, compliance, compliant, certificate, certificates, https, ftps, tls, trust, encryption, encrypt, encrypted, ip restriction, authentication, authorization, authn, autho, msi, managed service identity, managed identity, secrets, secret, patching, patch, patches, version, isolation, network isolation, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 55ffb2d03a42809a41583e6be25066b0b8e104b1
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961502"
---
# <a name="security-in-azure-app-service"></a>Azure App Service의 보안

이 문서에서는 [Azure App Service](overview.md)에서 웹앱, 모바일 앱 백 엔드, API 앱 및 [함수 앱](../azure-functions/index.yml)의 보안을 유지하는 방법을 보여 줍니다. 또한 기본 제공 App Service 기능을 사용하여 앱의 보안을 추가로 유지할 수 있는 방법도 보여 줍니다.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

다음 섹션에서는 위협으로부터 App Service 앱을 추기로 보호하는 방법에 대해 설명합니다.

## <a name="https-and-certificates"></a>HTTPS 및 인증서

App Service를 사용하면 [HTTPS](https://wikipedia.org/wiki/HTTPS)를 통해 앱을 보호할 수 있습니다. 앱을 만들 때 해당 기본 도메인 이름 ( \<app_name> . azurewebsites.net)은 이미 HTTPS를 사용 하 여 액세스할 수 있습니다. [앱에 대해 사용자 지정 도메인을 구성](app-service-web-tutorial-custom-domain.md)한 경우 클라이언트 브라우저에서 사용자 지정 도메인에 보안 HTTPS를 연결할 수 있도록 [TLS/SSL 인증서로도 보안을 설정](configure-ssl-bindings.md)해야 합니다. App Service에서 지원하는 여러 유형의 인증서가 있습니다.

- 체험 App Service 관리형 인증서
- App Service 인증서
- 타사 인증서
- Azure Key Vault에서 가져온 인증서

자세한 내용은 [Azure App Service에서 TLS/SSL 인증서 추가](configure-ssl-certificate.md)를 참조하세요.

## <a name="insecure-protocols-http-tls-10-ftp"></a>보안되지 않은 프로토콜(HTTP, TLS 1.0, FTP)

암호화되지 않은(HTTP) 모든 연결로부터 앱을 보호하기 위해 App Service에서 한 번 클릭 구성을 제공하여 HTTPS를 적용합니다. 보안되지 않은 요청은 애플리케이션 코드에 도달하기도 전에 거부됩니다. 자세한 내용은 [HTTPS 적용](configure-ssl-bindings.md#enforce-https)을 참조하세요.

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0은 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)와 같은 산업 표준에서 더 이상 안전하지 않은 것으로 간주됩니다. App Service를 사용하면 [TLS 1.1/1.2를 적용](configure-ssl-bindings.md#enforce-tls-versions)하여 오래된 프로토콜을 사용하지 않도록 설정할 수 있습니다.

App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 그러나 가능한 경우 FTP 대신 FTPS를 사용해야 합니다. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다.

## <a name="static-ip-restrictions"></a>고정 IP 제한

기본적으로 App Service 앱은 인터넷의 모든 IP 주소로부터 요청을 수락하지만, IP 주소의 작은 하위 집합에만 액세스하도록 제한할 수 있습니다. Windows에서 App Service를 사용하면 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.

Windows에서 App Service _web.config_를 구성 하 여 IP 주소를 동적으로 제한할 수도 있습니다. 자세한 내용은 [동적 IP 보안 \<dynamicIpSecurity> ](/iis/configuration/system.webServer/security/dynamicIpSecurity/)을 참조 하세요.

## <a name="client-authentication-and-authorization"></a>클라이언트 인증 및 권한 부여

Azure App Service는 사용자 또는 클라이언트 앱의 턴키 인증 및 권한 부여를 제공합니다. 이 기능을 사용하도록 설정하면 애플리케이션 코드가 거의 또는 전혀 없는 사용자 및 클라이언트 앱에 로그인할 수 있습니다. 사용자 고유의 인증 및 권한 부여 솔루션을 구현하거나 App Service에서 대신 처리하도록 허용할 수 있습니다. 인증 및 권한 부여 모듈은 웹 요청을 애플리케이션 코드로 전달하기 전에 해당 웹 요청을 처리하고, 코드에 도달하기 전에 승인되지 않은 요청을 거부합니다.

App Service 인증 및 권한 부여는 Azure Active Directory, Microsoft 계정, Facebook, Google 및 Twitter를 포함한 여러 인증 공급자를 지원합니다. 자세한 내용은 [Azure App Service에서 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요.

## <a name="service-to-service-authentication"></a>서비스 간 인증

App Service는 백 엔드 서비스를 인증할 때 필요에 따라 별도의 다음 두 가지 메커니즘을 제공합니다.

- **서비스 ID** - 앱 자체의 ID를 사용하여 원격 리소스에 로그인합니다. App Service를 사용하면 다른 서비스(예: [Azure SQL Database](/azure/sql-database/) 또는 [Azure Key Vault](../key-vault/index.yml))에서 인증하는 데 사용할 수 있는 [관리 ID](overview-managed-identity.md)를 쉽게 만들 수 있습니다. 이 방식을 설명하는 엔드투엔드 자습서는 [관리 ID를 사용하여 App Service에서 Azure SQL Database 연결 보호](app-service-web-tutorial-connect-msi.md)를 참조하세요.
- **OBO(On-Behalf-Of)** - 원격 리소스에 대해 사용자를 대신하도록 위임된 액세스 권한을 만듭니다. Azure Active Directory를 인증 공급자로 사용하면 App Service 앱에서 App Service의 원격 서비스(예: [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) 또는 원격 API 앱)에 위임된 로그인을 수행할 수 있습니다. 이 방법에 대한 엔드투엔드 자습서는 [Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)를 참조하세요.

## <a name="connectivity-to-remote-resources"></a>원격 리소스에 대한 연결

앱에서 액세스해야 하는 원격 리소스에는 다음 세 가지 종류가 있습니다. 

- [Azure 리소스](#azure-resources)
- [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)
- [온-프레미스 리소스](#on-premises-resources)

이러한 경우 각각에서 App Service는 보안 연결을 설정할 수 있는 방법을 제공하지만 여전히 보안 모범 사례를 준수해야 합니다. 예를 들어 백 엔드 리소스에서 암호화되지 않은 연결을 허용하는 경우에도 항상 암호화된 연결을 사용합니다. 또한 백 엔드 Azure 서비스에서 최소한의 IP 주소 집합을 허용해야 합니다. [Azure App 서비스의 인바운드 및 아웃바운드 IP 주소](overview-inbound-outbound-ips.md)에서 앱에 대한 아웃바운드 IP 주소를 확인할 수 있습니다.

### <a name="azure-resources"></a>Azure 리소스

앱에서 [SQL Database](https://azure.microsoft.com/services/sql-database/) 및 [Azure Storage](../storage/index.yml)와 같은 Azure 리소스에 연결하는 경우 해당 연결은 Azure 내에서 유지되며 네트워크 경계를 벗어나지 않습니다. 그러나 Azure에서 공유 네트워킹을 통해 연결되므로 연결이 항상 암호화되어 있어야 합니다. 

[App Service 환경](environment/intro.md)에서 앱이 호스팅되는 경우 [Virtual Network 서비스 엔드포인트를 사용하여 지원되는 Azure 서비스에 연결](../virtual-network/virtual-network-service-endpoints-overview.md)해야 합니다.

### <a name="resources-inside-an-azure-virtual-network"></a>Azure Virtual Network 내 리소스

앱은 [Virtual Network 통합](web-sites-integrate-with-vnet.md)을 통해 [Azure Virtual Network](../virtual-network/index.yml)의 리소스에 액세스할 수 있습니다. Virtual Network와의 통합은 지점-사이트 간 VPN을 사용하여 설정됩니다. 그러면 앱에서 개인 IP 주소를 사용하여 Virtual Network의 리소스에 액세스할 수 있습니다. 그러나 지점-사이트 간 연결은 여전히 Azure에서 공유 네트워크를 통과합니다. 

Azure의 공유 네트워크에서 리소스 연결을 완전히 분리하려면 [App Service 환경](environment/intro.md)에서 앱을 만듭니다. App Service 환경은 항상 전용 Virtual Network에 배포되므로 Virtual Network 내에서 앱과 리소스 간의 연결은 완전히 격리됩니다. App Service 환경의 네트워크 보안에 대한 다른 측면은 [네트워크 격리](#network-isolation)를 참조하세요.

### <a name="on-premises-resources"></a>온-프레미스 리소스

데이터베이스와 같은 온-프레미스 리소스에는 다음 세 가지 방법으로 안전하게 액세스할 수 있습니다. 

- [하이브리드 연결](app-service-hybrid-connections.md) - TCP 터널을 통해 원격 리소스에 대한 지점 간 연결을 설정합니다. TCP 터널은 SAS(공유 액세스 서명) 키가 있는 TLS 1.2를 사용하여 설정됩니다.
- 사이트 간 VPN을 통한 [Virtual Network 통합](web-sites-integrate-with-vnet.md) - [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)에서 설명한 대로 액세스할 수 있지만, Virtual Network는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 통해 온-프레미스 네트워크에 연결할 수 있습니다. 이 네트워크 토폴로지에서 앱은 Virtual Network의 다른 리소스와 같은 온-프레미스 리소스에 연결할 수 있습니다.
- 사이트 간 VPN을 통한 [App Service 환경](environment/intro.md) - [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)에서 설명한 대로 액세스할 수 있지만, Virtual Network는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 통해 온-프레미스 네트워크에 연결할 수 있습니다. 이 네트워크 토폴로지에서 앱은 Virtual Network의 다른 리소스와 같은 온-프레미스 리소스에 연결할 수 있습니다.

## <a name="application-secrets"></a>애플리케이션 비밀

애플리케이션 비밀(예: 데이터베이스 자격 증명, API 토큰 및 프라이빗 키)을 코드 또는 구성 파일에 저장하지 않습니다. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. App Service에서 환경 변수를 정의하는 방법은 [앱 설정](configure-common.md#configure-app-settings)(특히 .NET 애플리케이션의 경우 [연결 문자열](configure-common.md#configure-connection-strings))을 통해 이루어집니다. 앱 설정과 연결 문자열은 Azure에서 암호화되어 저장되며, 앱이 시작될 때 앱의 프로세스 메모리에 삽입되기 전에만 해독됩니다. 암호화 키는 정기적으로 회전합니다.

또는 고급 비밀 관리를 위해 App Service 앱을 [Azure Key Vault](../key-vault/index.yml)와 통합할 수 있습니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다.

## <a name="network-isolation"></a>네트워크 격리

**App Service 격리** 가격 책정 계층을 제외한 모든 계층에서는 App Service의 공유 네트워크 인프라에서 앱을 실행합니다. 예를 들어 공용 IP 주소 및 프런트 엔드 부하 분산 장치는 다른 테넌트와 공유됩니다. **App Service 격리** 계층은 전용 [App Service 환경](environment/intro.md) 내에서 앱을 실행하여 완벽한 네트워크 격리를 제공합니다. App Service 환경은 [Azure Virtual Network](../virtual-network/index.yml)의 사용자 인스턴스에서 실행됩니다. 수행할 수 있는 작업은 다음과 같습니다. 

- 전용 프런트 엔드를 사용하여 전용 공용 엔드포인트를 통해 앱을 제공합니다.
- ILB(내부 부하 분산 장치)를 사용하여 내부 애플리케이션을 제공합니다. 이렇게 하면 Azure Virtual Network 내에서만 해당 애플리케이션을 액세스할 수 있습니다. ILB에는 인터넷으로부터 응용 프로그램을 완전히 격리하는 프라이빗 서브넷의 IP 주소가 있습니다.
- [WAF(웹 애플리케이션 방화벽) 뒤에서 ILB를 사용합니다](environment/integrate-with-application-gateway.md). WAF는 공용 애플리케이션에 대한 엔터프라이즈 수준의 보호(예: DDoS 보호, URI 필터링 및 SQL 삽입 방지)를 제공합니다.

자세한 내용은 [Azure App Service Environment 소개](environment/intro.md)를 참조하세요.