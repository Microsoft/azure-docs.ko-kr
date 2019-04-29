---
title: 보안 개요 - Azure App Service | Microsoft Docs
description: App Service에서 앱 보안을 유지하는 방법과 위협으로부터 앱을 추가로 잠그는 방법을 알아봅니다.
keywords: azure app service, 웹앱, 모바일 앱, API 앱, 함수 앱, 보안, 안전, 보호, 규정 준수, 준수, 인증서, 트러스트, 암호화, 암호화됨, IP 제한, 인증, 권한 부여, 관리 서비스 ID, 관리 ID, 비밀, 패칭, 패치, 버전, 격리, 네트워크 격리, web app, mobile app, api app, function app, security, secure, secured, compliance, compliant, certificate, certificates, https, ftps, tls, trust, encryption, encrypt, encrypted, ip restriction, authentication, authorization, authn, autho, msi, managed service identity, managed identity, secrets, secret, patching, patch, patches, version, isolation, network isolation, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e4feaed9f4e8f6dd3275da25e33e57197731572
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838963"
---
# <a name="security-in-azure-app-service"></a>Azure App Service의 보안

이 문서에서는 [Azure App Service](overview.md)에서 웹앱, 모바일 앱 백 엔드, API 앱 및 [함수 앱](/azure/azure-functions/)의 보안을 유지하는 방법을 보여 줍니다. 또한 기본 제공 App Service 기능을 사용하여 앱의 보안을 추가로 유지할 수 있는 방법도 보여 줍니다.

Azure VM, 저장소, 네트워크 연결, 웹 프레임워크, 관리 및 통합 기능을 포함한 App Service의 플랫폼 구성 요소는 적극적으로 보호되고 강화됩니다. App Service는 다음 사항을 확인하기 위해 지속적으로 활발한 준수 확인을 수행합니다.

- 사용자의 앱 리소스는 다른 고객의 Azure 리소스로부터 [보호](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)됩니다.
- 새로 검색된 취약성을 처리하기 위해 [VM 인스턴스 및 런타임 소프트웨어가 정기적으로 업데이트됩니다](overview-patch-os-runtime.md). 
- 앱과 다른 Azure 리소스(예: [SQL Database](https://azure.microsoft.com/services/sql-database/)) 간의 비밀(예: 연결 문자열) 통신이 Azure 내에서 유지되며 네트워크 경계를 벗어나지 않습니다. 비밀은 저장될 때 항상 암호화됩니다.
- [하이브리드 연결](app-service-hybrid-connections.md)과 같은 App Service 연결 기능을 통한 모든 통신이 암호화됩니다. 
- Azure PowerShell, Azure CLI, Azure SDK, REST API와 같은 원격 관리 도구와의 연결이 모두 암호화됩니다.
- 24시간 위협 관리를 통해 맬웨어, DDoS(배포된 서비스 거부), MITM(메시지 가로채기) 및 기타 위협으로부터 인프라와 플랫폼을 보호합니다.

Azure의 인프라 및 플랫폼 보안에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/overview/trusted-cloud/)를 참조하세요.

다음 섹션에서는 위협으로부터 App Service 앱을 추기로 보호하는 방법에 대해 설명합니다.

## <a name="https-and-certificates"></a>HTTPS 및 인증서

App Service를 사용하면 [HTTPS](https://wikipedia.org/wiki/HTTPS)를 통해 앱을 보호할 수 있습니다. 앱을 만들 때 기본 도메인 이름(\<app_name>.azurewebsites.net)은 이미 HTTPS를 사용하여 액세스할 수 있습니다. [앱에 대해 사용자 지정 도메인을 구성](app-service-web-tutorial-custom-domain.md)한 경우 클라이언트 브라우저에서 사용자 지정 도메인에 보안 HTTPS를 연결할 수 있도록 [사용자 지정 인증서로도 보안을 설정](app-service-web-tutorial-custom-ssl.md)해야 합니다. 이 작업을 수행하는 방법으로 다음 두 가지가 있습니다.

- **App Service 인증서** - Azure에서 직접 인증서를 만듭니다. 인증서는 [Azure Key Vault](/azure/key-vault/)에서 보호되며 App Service 앱으로 가져올 수 있습니다. 자세한 내용은 [Azure App Service에 대한 SSL 인증서 구입 및 구성](web-sites-purchase-ssl-web-site.md)을 참조하세요.
- **타사 인증서** - 신뢰할 수 있는 인증 기관에서 구입한 사용자 지정 SSL 인증서를 업로드하여 App Service 앱에 바인딩합니다. App Service는 단일 도메인 인증서와 와일드카드 인증서를 모두 지원합니다. 또한 테스트 목적으로 자체 서명된 인증서도 지원합니다. 자세한 내용은 [자습서: Azure App Service에 기존 사용자 지정 SSL 인증서 바인딩](app-service-web-tutorial-custom-ssl.md)을 참조하세요.

## <a name="insecure-protocols-http-tls-10-ftp"></a>보안되지 않은 프로토콜(HTTP, TLS 1.0, FTP)

암호화되지 않은(HTTP) 모든 연결로부터 앱을 보호하기 위해 App Service에서 한 번 클릭 구성을 제공하여 HTTPS를 적용합니다. 보안되지 않은 요청은 애플리케이션 코드에 도달하기도 전에 거부됩니다. 자세한 내용은 [HTTPS 적용](app-service-web-tutorial-custom-ssl.md#enforce-https)을 참조하세요.

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0은 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)와 같은 산업 표준에서 더 이상 안전하지 않은 것으로 간주됩니다. App Service를 사용하면 [TLS 1.1/1.2를 적용](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)하여 오래된 프로토콜을 사용하지 않도록 설정할 수 있습니다.

App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 그러나 가능한 경우 FTP 대신 FTPS를 사용해야 합니다. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다.

## <a name="static-ip-restrictions"></a>고정 IP 제한

기본적으로 App Service 앱은 인터넷의 모든 IP 주소로부터 요청을 수락하지만, IP 주소의 작은 하위 집합에만 액세스하도록 제한할 수 있습니다. Windows에서 App Service를 사용하면 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.

Windows에 App Service가 있는 경우 _web.config_를 구성하여 IP 주소를 동적으로 제한할 수도 있습니다. 자세한 내용은 [동적 IP 보안 \<dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/)합니다.

## <a name="client-authentication-and-authorization"></a>클라이언트 인증 및 권한 부여

Azure App Service는 사용자 또는 클라이언트 앱의 턴키 인증 및 권한 부여를 제공합니다. 이 기능을 사용하도록 설정하면 애플리케이션 코드가 거의 또는 전혀 없는 사용자 및 클라이언트 앱에 로그인할 수 있습니다. 사용자 고유의 인증 및 권한 부여 솔루션을 구현하거나 App Service에서 대신 처리하도록 허용할 수 있습니다. 인증 및 권한 부여 모듈은 웹 요청을 애플리케이션 코드로 전달하기 전에 해당 웹 요청을 처리하고, 코드에 도달하기 전에 승인되지 않은 요청을 거부합니다.

App Service 인증 및 권한 부여는 Azure Active Directory, Microsoft 계정, Facebook, Google 및 Twitter를 포함한 여러 인증 공급자를 지원합니다. 자세한 내용은 [Azure App Service에서 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요.

## <a name="service-to-service-authentication"></a>서비스 간 인증

App Service는 백 엔드 서비스를 인증할 때 필요에 따라 별도의 다음 두 가지 메커니즘을 제공합니다.

- **서비스 ID** - 앱 자체의 ID를 사용하여 원격 리소스에 로그인합니다. App Service를 사용하면 다른 서비스(예: [Azure SQL Database](/azure/sql-database/) 또는 [Azure Key Vault](/azure/key-vault/))에서 인증하는 데 사용할 수 있는 [관리 ID](overview-managed-identity.md)를 쉽게 만들 수 있습니다. 이 방식을 설명하는 전체 자습서는 [관리 ID를 사용하여 App Service에서 Azure SQL Database 연결 보호](app-service-web-tutorial-connect-msi.md)를 참조하세요.
- **OBO(On-Behalf-Of)** - 원격 리소스에 대해 사용자를 대신하도록 위임된 액세스 권한을 만듭니다. Azure Active Directory를 인증 공급자로 사용하면 App Service 앱에서 App Service의 원격 서비스(예: [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) 또는 원격 API 앱)에 위임된 로그인을 수행할 수 있습니다. 이 방법에 대한 종단 간 자습서는 [Azure App Service에서 종단 간 사용자 인증 및 권한 부여](app-service-web-tutorial-auth-aad.md)를 참조하세요.

## <a name="connectivity-to-remote-resources"></a>원격 리소스에 대한 연결

앱에서 액세스해야 하는 원격 리소스에는 다음 세 가지 종류가 있습니다. 

- [Azure 리소스](#azure-resources)
- [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)
- [온-프레미스 리소스](#on-premises-resources)

이러한 경우 각각에서 App Service는 보안 연결을 설정할 수 있는 방법을 제공하지만 여전히 보안 모범 사례를 준수해야 합니다. 예를 들어 백 엔드 리소스에서 암호화되지 않은 연결을 허용하는 경우에도 항상 암호화된 연결을 사용합니다. 또한 백 엔드 Azure 서비스에서 최소한의 IP 주소 집합을 허용해야 합니다. [Azure App 서비스의 인바운드 및 아웃바운드 IP 주소](overview-inbound-outbound-ips.md)에서 앱에 대한 아웃바운드 IP 주소를 확인할 수 있습니다.

### <a name="azure-resources"></a>Azure 리소스

앱에서 [SQL Database](https://azure.microsoft.com/services/sql-database/) 및 [Azure Storage](/azure/storage/)와 같은 Azure 리소스에 연결하는 경우 해당 연결은 Azure 내에서 유지되며 네트워크 경계를 벗어나지 않습니다. 그러나 Azure에서 공유 네트워킹을 통해 연결되므로 연결이 항상 암호화되어 있어야 합니다. 

[App Service 환경](environment/intro.md)에서 앱이 호스팅되는 경우 [Virtual Network 서비스 엔드포인트를 사용하여 지원되는 Azure 서비스에 연결](../virtual-network/virtual-network-service-endpoints-overview.md)해야 합니다.

### <a name="resources-inside-an-azure-virtual-network"></a>Azure Virtual Network 내 리소스

앱은 [Virtual Network 통합](web-sites-integrate-with-vnet.md)을 통해 [Azure Virtual Network](/azure/virtual-network/)의 리소스에 액세스할 수 있습니다. Virtual Network와의 통합은 지점-사이트 간 VPN을 사용하여 설정됩니다. 그러면 앱에서 개인 IP 주소를 사용하여 Virtual Network의 리소스에 액세스할 수 있습니다. 그러나 지점-사이트 간 연결은 여전히 Azure에서 공유 네트워크를 통과합니다. 

Azure의 공유 네트워크에서 리소스 연결을 완전히 분리하려면 [App Service 환경](environment/intro.md)에서 앱을 만듭니다. App Service 환경은 항상 전용 Virtual Network에 배포되므로 Virtual Network 내에서 앱과 리소스 간의 연결은 완전히 격리됩니다. App Service 환경의 네트워크 보안에 대한 다른 측면은 [네트워크 격리](#network-isolation)를 참조하세요.

### <a name="on-premises-resources"></a>온-프레미스 리소스

데이터베이스와 같은 온-프레미스 리소스에는 다음 세 가지 방법으로 안전하게 액세스할 수 있습니다. 

- [하이브리드 연결](app-service-hybrid-connections.md) - TCP 터널을 통해 원격 리소스에 대한 지점 간 연결을 설정합니다. TCP 터널은 SAS(공유 액세스 서명) 키가 있는 TLS 1.2를 사용하여 설정됩니다.
- 사이트 간 VPN을 통한 [Virtual Network 통합](web-sites-integrate-with-vnet.md) - [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)에서 설명한 대로 액세스할 수 있지만, Virtual Network는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 통해 온-프레미스 네트워크에 연결할 수 있습니다. 이 네트워크 토폴로지에서 앱은 Virtual Network의 다른 리소스와 같은 온-프레미스 리소스에 연결할 수 있습니다.
- 사이트 간 VPN을 통한 [App Service 환경](environment/intro.md) - [Azure Virtual Network 내 리소스](#resources-inside-an-azure-virtual-network)에서 설명한 대로 액세스할 수 있지만, Virtual Network는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 통해 온-프레미스 네트워크에 연결할 수 있습니다. 이 네트워크 토폴로지에서 앱은 Virtual Network의 다른 리소스와 같은 온-프레미스 리소스에 연결할 수 있습니다.

## <a name="application-secrets"></a>애플리케이션 비밀

애플리케이션 비밀(예: 데이터베이스 자격 증명, API 토큰 및 개인 키)을 코드 또는 구성 파일에 저장하지 않습니다. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. App Service에서 환경 변수를 정의하는 방법은 [앱 설정](web-sites-configure.md#app-settings)(특히 .NET 애플리케이션의 경우 [연결 문자열](web-sites-configure.md#connection-strings))을 통해 이루어집니다. 앱 설정과 연결 문자열은 Azure에서 암호화되어 저장되며, 앱이 시작될 때 앱의 프로세스 메모리에 삽입되기 전에만 해독됩니다. 암호화 키는 정기적으로 회전합니다.

또는 고급 비밀 관리를 위해 App Service 앱을 [Azure Key Vault](/azure/key-vault/)와 통합할 수 있습니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/tutorial-web-application-keyvault.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다.

## <a name="network-isolation"></a>네트워크 격리

**App Service 격리** 가격 책정 계층을 제외한 모든 계층에서는 App Service의 공유 네트워크 인프라에서 앱을 실행합니다. 예를 들어 공용 IP 주소 및 프런트 엔드 부하 분산 장치는 다른 테넌트와 공유됩니다. **App Service 격리** 계층은 전용 [App Service 환경](environment/intro.md) 내에서 앱을 실행하여 완벽한 네트워크 격리를 제공합니다. App Service 환경은 [Azure Virtual Network](/azure/virtual-network/)의 사용자 인스턴스에서 실행됩니다. 수행할 수 있는 작업은 다음과 같습니다. 

- [네트워크 보안 그룹](../virtual-network/virtual-networks-dmz-nsg.md)사용하여 네트워크 액세스를 제한합니다. 
- 전용 프런트 엔드를 사용하여 전용 공용 엔드포인트를 통해 앱을 제공합니다.
- ILB(내부 부하 분산 장치)를 사용하여 내부 애플리케이션을 제공합니다. 이렇게 하면 Azure Virtual Network 내에서만 해당 애플리케이션을 액세스할 수 있습니다. ILB에는 인터넷으로부터 응용 프로그램을 완전히 격리하는 개인 서브넷의 IP 주소가 있습니다.
- [WAF(웹 애플리케이션 방화벽) 뒤에서 ILB를 사용합니다](environment/integrate-with-application-gateway.md). WAF는 공용 애플리케이션에 대한 엔터프라이즈 수준의 보호(예: DDoS 보호, URI 필터링 및 SQL 삽입 방지)를 제공합니다.

자세한 내용은 [Azure App Service Environment 소개](environment/intro.md)를 참조하세요. 
