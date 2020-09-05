---
title: Azure AD 앱 갤러리에 앱 게시
description: Azure Active Directory 앱 갤러리에서 Single Sign-On을 지 원하는 응용 프로그램을 나열 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 1ce8f376d3264a06618cb7321c75142dcf92d9ea
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891864"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 앱 게시

Azure AD 앱 갤러리에 앱을 게시할 수 있습니다. 앱이 게시 되 면 앱이 테 넌 트에 앱을 추가할 때 고객에 게 옵션으로 표시 됩니다. 

Azure AD 갤러리에 앱을 추가할 때의 이점은 다음과 같습니다.

- 고객은 앱에 가장 적합 한 Single Sign-On 환경을 찾을 수 있습니다.
- 애플리케이션 구성이 간단하고 최소화됩니다.
- 갤러리에서 빠른 검색에 애플리케이션을 제공합니다.
- 무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.

또한 고객이 앱에 대 한 id 공급자로 Azure AD를 사용 하는 경우 많은 이점이 있습니다. 그 중 일부는 다음과 같습니다.

- 사용자에 Single Sign-On을 제공 합니다. SSO를 사용 하면 고객에 게 더 쉽게 Single Sign-On 수 있도록 하 여 지원 비용을 절감할 수 있습니다. 한 번 클릭 SSO가 사용 하도록 설정 된 경우 고객의 IT 관리자는 조직에서 사용 하기 위해 응용 프로그램을 구성 하는 방법을 배울 필요가 없습니다. Single Sign-On에 대한 자세한 내용은 [Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
- 앱은 Microsoft 365 앱 갤러리, Microsoft 365 앱 시작 관리자 및 Office.com의 Microsoft Search 내에서 검색할 수 있습니다. 
- 통합 앱 관리. Azure AD의 앱 관리에 대해 자세히 알아보려면 [응용 프로그램 관리용 이란?](../manage-apps/what-is-application-management.md)을 참조 하세요.
- 앱은 [Graph API](https://docs.microsoft.com/graph/) 를 사용 하 여 Microsoft 에코 시스템에서 사용자 생산성을 유도 하는 데이터에 액세스할 수 있습니다.
- 상호 고객을 위해 Azure AD 팀과 공동 생성 된 응용 프로그램별 설명서는 채택을 용이 하 게 합니다.
- 고객에 게 직원 및 게스트 id의 인증 및 권한 부여를 완전히 관리할 수 있는 기능을 제공 합니다.
- 이러한 id의 고객 소유자에 게 모든 계정 관리 및 규정 준수 책임을 배치 합니다.
- 특정 id 공급자, 그룹 또는 사용자가 비즈니스 요구에 맞게 SSO를 사용 하거나 사용 하지 않도록 설정 하는 기능을 제공 합니다.
- Marketability 및 adoptability를 늘립니다. 많은 조직에서 직원 들이 모든 응용 프로그램에서 원활 하 게 SSO 환경을 필요로 하는 것을 요구 합니다 (또는 따르는). SSO를 용이 하 게 하는 것이 중요 합니다.
- 최종 사용자의 원활한 감소를 줄이고 최종 사용자의 사용량을 높이고 수익을 늘릴 수 있습니다.
- [Scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(도메인 간 id 관리)을 위해 시스템을 사용 하는 고객은 동일한 앱에 대 한 프로 비전을 사용할 수 있습니다.
- 사용자가 Azure AD SSO를 사용 하 여 응용 프로그램에 로그온 하 고 별도의 자격 증명 필요를 제거 하는 경우 보안 및 편의를 추가 합니다.

> [!TIP]
> 구매 또는 구독을 통해 다른 회사에서 사용할 수 있도록 응용 프로그램을 제공 하는 경우 자신의 Azure 테 넌 트 내에서 고객이 응용 프로그램을 사용할 수 있도록 합니다. 다중 테 넌 트 응용 프로그램을 만드는 것으로 알려져 있습니다. 이 개념에 대 한 개요는 [Azure의 다중 테 넌 트 응용 프로그램](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) 및 [Azure Active Directory의 테 넌 트](single-and-multi-tenant-apps.md)를 참조 하세요.

> [!IMPORTANT]
> Azure AD 갤러리에 앱을 게시 하려면 특정 사용 약관에 동의 해야 합니다. 시작 하기 전에 사용 [약관](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)을 읽고 동의 해야 합니다.

Azure AD 앱 갤러리에 앱을 게시 하는 단계는 다음과 같습니다.
1. 앱에 적합 한 Single Sign-On 표준을 선택 합니다.
2. 앱에서 Single Sign-On을 구현 합니다.
3. Azure 테 넌 트를 만들고 앱을 테스트 합니다.
4. 설명서를 만들고 게시 합니다.
5. 앱을 제출 합니다.
6. Microsoft 파트너 네트워크에 가입 합니다.


## <a name="prerequisites"></a>전제 조건

두 명 이상의 사용자를 등록 하 여 테스트 하려면 영구 계정이 필요 합니다.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>1 단계-앱에 대 한 올바른 Single Sign-On 표준 선택

Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 지원 되는 Single Sign-On 옵션 중 하나 이상을 구현 해야 합니다. Single Sign-On 옵션 및 고객이 Azure AD에서이를 구성 하는 방법에 대 한 자세한 내용은 [SSO 옵션](../manage-apps/sso-options.md)을 참조 하세요.

다음 표에서는 기본 표준 인 Open Authentication 2.0 (OAuth 2.0)을 Openid connect Connect (OIDC), Security Assertion Markup Language (SAML) 및 Web Services Federation (WS 급지됨)를 비교 합니다.

| 기능| OAuth/OIDC| SAML/WS 공급 |
| - |-|-|
| 웹 기반 Single Sign-On| √| √ |
| 웹 기반 single sign-on| √| √ |
| 모바일 기반 Single Sign-On| √| √* |
| 모바일 기반 single sign-on| √| √* |
| 모바일 응용 프로그램에 대 한 조건부 액세스 정책| √| X |
| 모바일 응용 프로그램에 대 한 원활한 MFA 환경| √| X |
| 액세스 Microsoft Graph| √| X |

* 가능 하지만 Microsoft는 샘플 또는 지침을 제공 하지 않습니다.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 및 OpenID Connect
OAuth 2.0은 권한 부여를 위한 [업계 표준](https://oauth.net/2/) 프로토콜입니다. OIDC (Openid connect Connect)는 OAuth 2.0 프로토콜을 기반으로 구축 된 [업계 표준](https://openid.net/connect/) id 인증 계층입니다. 

**OAuth/OIDC를 선택 하는 이유**
- 이러한 프로토콜에 내재 된 권한 부여를 통해 응용 프로그램은 Microsoft Graph API를 통해 다양 한 사용자 및 조직 데이터에 액세스 하 고 통합할 수 있습니다.
- 응용 프로그램에 SSO를 채택할 때 고객의 최종 사용자 환경을 간소화 합니다. 필요한 권한 집합을 쉽게 정의할 수 있습니다. 그러면 관리자 또는 최종 사용자 동의 자동으로 표시 됩니다.
- 이러한 프로토콜을 사용 하면 고객이 MFA (조건부 액세스 및 Multi-Factor Authentication) 정책을 사용 하 여 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. 
- Microsoft는 개발을 지원 하기 위해 [여러 기술 플랫폼에서 라이브러리 및 코드 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 을 제공 합니다.  

**고려해 야 할 몇 가지 사항**
- 응용 프로그램에 대 한 SAML 기반 Single Sign-On를 이미 구현한 경우 갤러리에서 앱을 가져오기 위해 새 표준을 구현 하지 않으려고 할 수 있습니다.

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 또는 WS-급지됨

SAML은 웹 응용 프로그램을 위해 널리 채택 되 고 널리 채택 되는 [Single Sign-On 표준](https://www.oasis-open.org/standards#samlv2.0) 입니다. Azure에서 SAML를 사용 하는 방법에 대 한 자세한 내용은 [azure에서 saml 프로토콜을 사용 하는 방법](active-directory-saml-protocol-reference.md)을 참조 하세요. 

Web Services Federation (WS 급지됨)는 .NET 플랫폼을 사용 하 여 개발 된 웹 응용 프로그램에 일반적으로 사용 되는 [업계 표준](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) 입니다.

**SAML을 선택 하는 이유**
- SAML 2.0은 성숙한 표준 이며 대부분의 기술 플랫폼은 SAML 2.0 용 오픈 소스 라이브러리를 지원 합니다. 
- SAML SSO를 구성 하기 위해 고객에 게 관리 인터페이스를 제공할 수 있습니다. Saml SSO Microsoft Azure AD 및 SAML을 지 원하는 다른 모든 id 공급자를 구성할 수 있습니다.

**고려해 야 할 몇 가지 사항**
- 모바일 응용 프로그램에 SAML 2.0 또는 WSFed 프로토콜을 사용 하는 경우 MFA (Multi-factor Authentication)를 비롯 한 특정 조건부 액세스 정책에는 성능 저하가 발생 합니다.
- Microsoft Graph에 액세스 하려는 경우 OAuth 2.0을 통해 권한 부여를 구현 하 여 필요한 토큰을 생성 해야 합니다. 

### <a name="password-based"></a>암호 기반
암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유 해야 하는 시나리오에 유용 합니다.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>2 단계-앱에서 Single Sign-On 구현
갤러리의 모든 앱은 지원 되는 Single Sign-On 옵션 중 하나를 구현 해야 합니다. 지원 되는 옵션에 대 한 자세한 내용은 [SSO 옵션](../manage-apps/sso-options.md)을 참조 하세요.

OAuth 및 OIDC의 경우 [인증 패턴](v2-app-types.md) 및 [Azure active Directory 코드 샘플](sample-v2-code.md)에 대 한 지침을 참조 하세요.

SAML 및 WS 급지됨의 경우 응용 프로그램에 SP 또는 IDP 모드에서 SSO 통합을 수행할 수 있는 기능이 있어야 합니다. 요청을 제출 하기 전에이 기능이 제대로 작동 하는지 확인 합니다.

인증에 대해 자세히 알아보려면 인증 이란 [?](../azuread-dev/v1-authentication-scenarios.md)을 참조 하세요.

> [!IMPORTANT]
> 페더레이션된 응용 프로그램 (Openid connect 및 SAML/WS 급지됨)의 경우 앱에서 SaaS (Software as a Service) 모델을 지원 해야 합니다. Azure AD 갤러리 응용 프로그램은 여러 고객 구성을 지원 해야 하며 단일 고객과만 국한 되어서는 안 됩니다.

### <a name="implement-oauth-20-and-openid-connect"></a>OAuth 2.0 및 Openid connect Connect 구현

Openid connect Connect의 경우 응용 프로그램은 다중 테 넌 트 해야 하며, 응용 프로그램에 대해 [AZURE AD 동의 프레임 워크](consent-framework.md) 를 올바르게 구현 해야 합니다. 사용자는 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 일반 끝점에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.

특정 예제를 검토 하려면 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요. 

모바일 관련 예제를 검토 하려면 다음을 참조 하세요. 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [유니버설 Windows 플랫폼](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2.0 구현

앱이 SAML 2.0을 지 원하는 경우 Azure AD 테 넌 트와 직접 통합할 수 있습니다. Azure AD를 사용 하 여 SAML 구성에 대해 자세히 알아보려면 [saml 기반 Single Sign-On 구성](../manage-apps/configure-saml-single-sign-on.md)을 참조 하세요.

Microsoft는 SAML 구현을 위해 라이브러리를 제공 하거나 권장 하지 않습니다. 사용할 수 있는 여러 오픈 소스 라이브러리가 있습니다.

### <a name="implement-ws-fed"></a>WS-급지됨 구현
ASP.NET Core에서 WS를 사용 하는 방법에 대해 자세히 알아보려면 [ASP.NET Core에서 ws-federation을 사용 하 여 사용자 인증](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation)을 참조 하세요.

### <a name="implement-password-vaulting"></a>암호 보관 구현

HTML 로그인 페이지가 있는 웹 응용 프로그램을 만듭니다. 응용 프로그램에서 폼 인증을 지원 하는지 확인 하 여 예상 대로 작동 하도록 Single Sign-On 수 있도록 암호 보관을 수행할 수 있도록 합니다.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>3 단계-Azure 테 넌 트 만들기 및 앱 테스트

앱을 테스트 하려면 Azure AD 테 넌 트가 필요 합니다. 개발 환경을 설정 하려면 [빠른 시작: 테 넌 트 설정](quickstart-create-new-tenant.md)을 참조 하세요.

또는 Azure AD 테 넌 트는 모든 Microsoft 365 구독과 함께 제공 됩니다. 무료 Microsoft 365 개발 환경을 설정 하려면 [Microsoft 365 개발자 프로그램 참여](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program)를 참조 하세요.

테 넌 트가 있으면 single sign-on 액세스를 사용 하도록 설정 하 고 테스트 해야 합니다. 

**OIDC 또는 Oath 응용 프로그램의**경우 [응용 프로그램](quickstart-register-app.md) 을 다중 테 넌 트 응용 프로그램으로 등록 합니다. 지원 되는 계정 유형에서 조직 디렉터리 및 개인 Microsoft 계정 옵션의 계정을 선택 합니다.

**Saml 및 WS 급지됨 기반 응용 프로그램의**경우 Azure AD에서 일반 saml 템플릿을 사용 하 여 [saml 기반 Single sign-on](../manage-apps/configure-saml-single-sign-on.md) 응용 프로그램을 구성 합니다.

필요한 경우 [단일 테 넌 트 응용 프로그램을 다중 테 넌 트로 변환할](howto-convert-app-to-be-multi-tenant.md) 수도 있습니다.


## <a name="step-4---create-and-publish-documentation"></a>4 단계-문서 만들기 및 게시

### <a name="documentation-on-your-site"></a>사이트에 대 한 설명서

도입의 용이성은 엔터프라이즈 소프트웨어 결정의 중요 한 요소입니다. 따르기 쉬운 설명서는 도입 과정에서 고객을 지원 하 고 지원 비용을 절감 합니다. Microsoft는 수천 개의 소프트웨어 공급 업체와 협력 하 여 작동 하는 것을 살펴보았습니다.

사이트에 대 한 설명서는 최소한 다음 항목을 포함 하는 것이 좋습니다.

* SSO 기능 소개
  * 지원되는 프로토콜
  * 버전 및 SKU
  * 설명서 링크를 사용 하 여 지원 되는 Id 공급자 목록
* 응용 프로그램에 대 한 라이선스 정보
* SSO 구성을 위한 역할 기반 액세스 제어
* SSO 구성 단계
  * 공급자의 예상 값을 포함 하는 SAML의 UI 구성 요소
  * Id 공급자에 전달할 서비스 공급자 정보입니다.
* OIDC/OAuth 인 경우
  * 비즈니스 근거 승인에 필요한 사용 권한 목록
* 파일럿 사용자를 위한 테스트 단계
* 오류 코드 및 메시지를 비롯 한 문제 해결 정보
* 고객을 위한 지원 메커니즘

### <a name="documentation-on-the-microsoft-site"></a>Microsoft 사이트 설명서

응용 프로그램을 Azure Marketplace에 게시 하는 Azure Active Directory 응용 프로그램 갤러리를 사용 하 여 응용 프로그램을 나열 하는 경우 Microsoft는 단계별 프로세스를 설명 하는 상호 고객에 대 한 설명서를 생성 합니다. [여기](https://aka.ms/appstutorial)에서 예제를 확인할 수 있습니다. 이 설명서는 갤러리에 대 한 제출에 따라 만들어지며, GitHub 계정을 사용 하 여 응용 프로그램을 변경 하는 경우 쉽게 업데이트할 수 있습니다.


## <a name="step-5---submit-your-app"></a>5 단계-앱 제출

응용 프로그램 통합이 Azure AD에서 작동 하는지 테스트 한 후에는 [Microsoft 응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 응용 프로그램 요청을 제출 합니다.

처음으로 포털에 로그인 할 때 두 화면 중 하나가 표시 됩니다. 

"작동 하지 않습니다." 라는 메시지가 표시 되 면 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 해야 합니다. 요청을 제출 하는 데 사용할 전자 메일 계정을 제공 합니다. 과 같은 비즈니스 전자 메일 주소를 `name@yourbusiness.com` 선호 합니다. Azure AD 팀이 Microsoft 응용 프로그램 네트워크 포털에 계정을 추가 합니다.

"액세스 요청" 페이지가 표시 되 면 비즈니스 근거를 입력 하 고 **액세스 요청**을 선택 합니다.

계정이 추가 된 후에는 홈 페이지에서 Microsoft 응용 프로그램 네트워크 포털에 로그인 하 **여 요청을** 제출할 수 있습니다.

![홈 페이지의 ISV (요청 제출) 타일](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>포털에 로그인 하는 문제

로그인 하는 동안이 오류가 표시 되는 경우 문제에 대 한 세부 정보와 문제를 해결 하는 방법이 여기에 있습니다.

* 아래와 같이 로그인이 차단 된 경우:

  ![갤러리에서 응용 프로그램을 확인 하는 문제](./media/howto-app-gallery-listing/blocked.png)

**무슨 일이죠:**

게스트 사용자는 Azure AD 이기도 한 홈 테 넌 트에 페더레이션 됩니다. 게스트 사용자는 위험 수준이 높습니다. Microsoft는 높은 위험 수준의 사용자가 해당 리소스에 액세스할 수 없도록 합니다. 모든 위험 수준 사용자 (직원 또는 손님/공급 업체)는 Microsoft 리소스에 액세스 하기 위해 위험을 수정 하거나 종결 해야 합니다. 게스트 사용자의 경우이 사용자 위험은 홈 테 넌 트에서 발생 하 고 정책은 리소스 테 넌 트 (이 경우 Microsoft)에서 제공 됩니다.
 
**보안 솔루션:**

* MFA 등록 게스트 사용자는 자신의 사용자 위험을 수정 합니다. 게스트 사용자가 https://aka.ms/sspr) 해당 홈 테 넌 트에서 (홈 테 넌 트에서 MFA 및 SSPR 필요) 보안 된 암호 변경 또는 재설정을 수행 하 여이 작업을 수행할 수 있습니다. 보안 된 암호 변경 또는 재설정은 온-프레미스이 아닌 Azure AD에서 시작 해야 합니다.

* 게스트 사용자는 관리자에 게 위험을 해결 합니다. 이 경우 관리자가 암호 재설정 (임시 암호 생성)을 수행 합니다. Id 보호는 필요 하지 않습니다. 게스트 사용자의 관리자는로 이동 하 여 https://aka.ms/RiskyUsers ' 암호 재설정 '을 클릭할 수 있습니다.

* 게스트 사용자는 관리자에 게 위험을 종결 하거나 해제 합니다. 이 경우에도 Id 보호가 필요 하지 않습니다. 관리자는로 이동 하 여 https://aka.ms/RiskyUsers ' 사용자 위험 해제 '를 클릭할 수 있습니다. 그러나 관리자는 사용자 위험을 닫기 전에이가 거짓 긍정 위험 평가 인지 확인 하기 위해 기한 성실을 수행 해야 합니다. 그렇지 않으면 조사 하지 않고 위험 평가를 억제 하 여 및 Microsoft의 리소스를 위험에 노출 합니다.

> [!NOTE]
> 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

### <a name="implementation-specific-options"></a>구현 관련 옵션
Openid connect Connect를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려는 경우 다음과 같이 **Openid connect Connect & OAuth 2.0** 을 선택 합니다.

![갤러리에서 Openid connect Connect 응용 프로그램 나열](./media/howto-app-gallery-listing/openid.png)

**Saml 2.0** 또는 **ws 급지됨**를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려는 경우 표시 된 대로 **saml 2.0/ws 공급** 을 선택 합니다.

![갤러리에 SAML 2.0 또는 WS-급지됨 응용 프로그램 나열](./media/howto-app-gallery-listing/saml.png)

암호 SSO를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려면 다음과 같이 **암호 sso** 를 선택 합니다.

![갤러리에 암호 SSO 응용 프로그램 나열](./media/howto-app-gallery-listing/passwordsso.png)

사용자 프로 비전을 위해 SCIM 2.0 끝점을 구현 하는 경우 표시 된 대로 옵션을 선택 합니다. 

   ![사용자 프로 비전 요청](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>기존 목록을 업데이트 하거나 제거 합니다.

[Microsoft 응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 기존 갤러리 앱을 업데이트 하거나 제거할 수 있습니다.

![갤러리에 SAML 응용 프로그램 나열](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> 액세스에 문제가 있는 경우 계정을 만드는 방법에 대 한 이전 섹션을 검토 합니다. 작동 하지 않는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.


### <a name="timelines"></a>타임라인

갤러리에서 SAML 2.0 또는 WS (WS) 응용 프로그램을 나열 하는 프로세스에 대 한 타임 라인은 영업일의 7 ~ 10 일입니다.

![갤러리에서 SAML 응용 프로그램을 나열 하기 위한 타임 라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에서 Openid connect Connect 응용 프로그램을 나열 하는 프로세스에 대 한 타임 라인은 영업일/영업일입니다.

![갤러리에서 Openid connect Connect 응용 프로그램을 나열 하는 타임 라인](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>에스컬레이션

모든 에스컬레이션에 대해 [AZURE AD SSO 통합 팀](mailto:SaaSApplicationIntegrations@service.microsoft.com)에 전자 메일을 보내면 가능한 한 빨리 응답 하 게 됩니다.


## <a name="step-6---join-the-microsoft-partner-network"></a>6 단계-Microsoft 파트너 네트워크에 가입
Microsoft 파트너 네트워크는 전용 리소스, 프로그램, 도구 및 연결에 대 한 즉각적인 액세스를 제공 합니다. 네트워크에 가입 하 고 시장 출시 계획을 만들려면 [상업 고객](https://partner.microsoft.com/explore/commercial#gtm)에 게 도달 하세요 .를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure AD 로그인을 지원하는 애플리케이션 구축에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](authentication-flows-app-scenarios.md)를 참조하세요.
