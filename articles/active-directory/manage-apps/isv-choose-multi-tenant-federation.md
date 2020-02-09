---
title: 다중 테넌트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜 선택
description: Azure Active Directory와의 통합에 대 한 독립 소프트웨어 공급 업체 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443387"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜을 선택 합니다.

SaaS (software as a service) 응용 프로그램을 개발 하는 경우와 고객의 요구에 가장 잘 맞는 페더레이션 프로토콜을 선택 해야 합니다. 이러한 결정은 개발 플랫폼을 기반으로 하며 고객의 Office 365 및 Azure AD 에코 시스템 내에서 사용 가능한 데이터와 통합 하고자 합니다.

Azure Active Directory와 [의 SSO 통합에 사용할 수 있는 프로토콜](what-is-single-sign-on.md) 의 전체 목록을 참조 하세요.
다음 표에서는 
* 오픈 인증 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* SAML(Security Assertion Markup Language )
* Web Services Federation (WSFed)

| 기능| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| 웹 기반 Single sign-on| √| √ |
| 웹 기반 Single sign-on| √| √ |
| 모바일 기반 Single sign-on| √| √* |
| 모바일 기반 Single sign-on| √| √* |
| 모바일 응용 프로그램에 대 한 조건부 액세스 정책| √| X |
| 모바일 응용 프로그램에 대 한 원활한 MFA 환경| √| X |
| 액세스 Microsoft Graph| √| X |

\* 가능 하지만 Microsoft는 샘플 또는 지침을 제공 하지 않습니다.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 및 Open ID Connect

OAuth 2.0은 권한 부여를 위한 [업계 표준](https://oauth.net/2/) 프로토콜입니다. OIDC (Openid connect Connect)는 OAuth 2.0 프로토콜을 기반으로 구축 된 [업계 표준](https://openid.net/connect/) id 인증 계층입니다.

### <a name="benefits"></a>혜택

프로토콜에 기본 제공 되는 인증 및 권한 부여가 있기 때문에 OIDC/OAuth 2.0를 사용 하는 것이 좋습니다. SAML을 사용 하 여 권한 부여를 추가로 구현 해야 합니다.

이러한 프로토콜에 내재 된 권한 부여를 통해 응용 프로그램은 Microsoft Graph API를 통해 다양 한 사용자 및 조직 데이터에 액세스 하 고 통합할 수 있습니다.

OAuth 2.0 및 OIDC를 사용 하면 응용 프로그램에 SSO를 채택할 때 고객의 최종 사용자 환경이 간소화 됩니다. 필요한 권한 집합을 쉽게 정의할 수 있습니다. 그러면 관리자 또는 최종 사용자 동의 자동으로 표시 됩니다.

또한 이러한 프로토콜을 사용 하 여 고객은 조건부 액세스 및 MFA 정책을 사용 하 여 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. Microsoft는 개발을 지원 하기 위해 [여러 기술 플랫폼에서 라이브러리 및 코드 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 을 제공 합니다.  

### <a name="implementation"></a>구현

OAuth 2.0 공급자 인 Microsoft Id를 사용 하 여 응용 프로그램을 등록 합니다. 그런 다음 통합 하려는 다른 Id 공급자를 사용 하 여 OAuth 2.0 기반 응용 프로그램을 등록할 수도 있습니다. 

응용 프로그램을 등록 하 고 이러한 프로토콜을 SSO에서 웹 앱으로 구현 하는 방법에 대 한 자세한 내용은 [Openid connect Connect를 사용 하 여 웹 응용 프로그램에 대 한 액세스 권한 부여 및 Azure Active Directory](../develop/sample-v2-code.md)  모바일 앱에서 이러한 프로토콜을 SSO에 구현 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요. 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [유니버설 Windows 플랫폼](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 및 WSFed

SAML (Security Assertion Markup Language)는 일반적으로 웹 응용 프로그램에 사용 됩니다. 개요는 [Azure에서 SAML 프로토콜을 사용 하는 방법](../develop/active-directory-saml-protocol-reference.md) 을 참조 하세요. 

WSFed (Web Services Federation)는 .Net 플랫폼을 사용 하 여 개발 된 웹 응용 프로그램에 일반적으로 사용 되는 [업계 표준](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) 입니다.

### <a name="benefits"></a>혜택

SAML 2.0은 성숙한 표준 이며 대부분의 기술 플랫폼은 SAML 2.0 용 오픈 소스 라이브러리를 지원 합니다. SAML SSO를 구성 하기 위해 고객에 게 관리 인터페이스를 제공할 수 있습니다. Saml SSO Microsoft Azure AD 및 SAML 2를 지 원하는 다른 모든 id 공급자를 구성할 수 있습니다.

### <a name="trade-offs"></a>장단점

모바일 응용 프로그램에 SAML 2.0 또는 WSFed 프로토콜을 사용 하는 경우 MFA (Multi-factor Authentication)를 비롯 한 특정 조건부 액세스 정책에는 성능 저하가 발생 합니다. 또한 Microsoft Graph에 액세스 하려는 경우 OAuth 2.0을 통해 권한 부여를 구현 하 여 필요한 토큰을 생성 해야 합니다. 

### <a name="implementation"></a>구현

Microsoft는 SAML 구현에 대 한 라이브러리를 제공 하거나 특정 라이브러리를 권장 하지 않습니다. 사용할 수 있는 여러 오픈 소스 라이브러리가 있습니다.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 및 Microsoft Graph Rest API 사용 

Microsoft Graph는 Office 365, Windows 10, Enterprise Mobility 및 Security, Dynamics 365 등의 추가 제품을 비롯 한 모든 Microsoft 365의 데이터 패브릭입니다. 여기에는 사용자의 생산성을 높이는 사용자, 그룹, 일정, 메일, 파일 등 엔터티의 핵심 스키마가 포함 됩니다. Microsoft Graph는 개발자를 위한 REST 기반 API, Microsoft Graph 데이터 연결 및 커넥터를 사용 하 여 개발자가 자신의 데이터를 Microsoft Graph에 추가할 수 있는 세 가지 인터페이스를 제공 합니다.  

SSO에 위의 프로토콜을 사용 하면 응용 프로그램에서 Microsoft Graph REST API를 통해 제공 되는 다양 한 데이터에 액세스할 수 있습니다. 이를 통해 고객은 Microsoft 365 투자에서 더 많은 가치를 얻을 수 있습니다. 예를 들어 응용 프로그램은 Microsoft Graph API를 호출 하 여 고객의 Office 365 인스턴스와 통합 하 고 응용 프로그램 내에서 사용자의 Microsoft Office 및 SharePoint 항목을 표시할 수 있습니다. 

Open ID Connect를 사용 하 여 인증 하는 경우 OAuth2를 사용 하 여 토큰을 획득 하기 위해 토큰을 획득 하는 데 사용할 수 있는 Api Microsoft Graph Api를 호출 하는 데 사용할 수 있으므로 개발 환경이 원활 합니다. 응용 프로그램이 SAML 또는 WSFed를 사용 하는 경우 응용 프로그램 내에서 추가 코드를 추가 하 여 Microsoft Graph Api를 호출 하는 데 필요한 토큰을 가져와야 합니다. 

## <a name="next-steps"></a>다음 단계

[다중 테넌트 응용 프로그램에 SSO 사용](isv-sso-content.md)

[다중 테넌트 응용 프로그램에 대 한 설명서 만들기](isv-create-sso-documentation.md)
