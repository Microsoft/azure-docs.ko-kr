---
title: Atlassian Jira/Confluence 관리자 가이드 - Azure Active Directory | Microsoft Docs
description: Atlassian Jira 및 Confluence를 Azure AD(Azure Active Directory)와 함께 사용하기 위한 관리자 가이드입니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2f06b884cb1213e9d2cabff4e6e2b97a60339a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935776"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Azure Active Directory의 Atlassian Jira 및 Confluence 관리자 가이드

## <a name="overview"></a>개요

Azure AD(Azure Active Directory) SSO(Single Sign-On) 플러그 인을 통해 Microsoft Azure AD 고객은 Atlassian Jira 및 Confluence 서버 기반 제품에 로그인하는 데 해당 회사 또는 학교 계정을 사용할 수 있습니다. SAML 2.0 기반 SSO를 구현합니다.

## <a name="how-it-works"></a>작동 방법

사용자가 Atlassian Jira 또는 Confluence 애플리케이션에 로그인하려는 경우 로그인 페이지에 **Azure AD로 로그인** 단추가 보입니다. 해당 단추를 선택하면 Azure AD 조직 로그인 페이지(즉, 회사 또는 학교 계정)를 사용하여 로그인해야 합니다.

사용자가 인증되면 애플리케이션에 로그인할 수 있습니다. 회사 또는 학교 계정의 ID 및 암호를 사용하여 이미 인증된 경우 애플리케이션에 직접 로그인합니다. 

로그인은 Jira 및 Confluence에서도 작동합니다. 사용자가 Jira 애플리케이션에 로그인되어 있고 Confluence가 동일한 브라우저 창에 열려 있는 경우, 다른 앱에서 자격 증명을 제공할 필요가 없습니다. 

사용자는 회사 또는 학교 계정으로 My Apps를 통해 Atlassian 제품을 얻을 수도 있습니다. 자격 증명을 입력하지 않고 로그인되어야 합니다.

> [!NOTE]
> 사용자 프로비전은 플러그 인을 통해 수행되지 않습니다.

## <a name="audience"></a>대상

Jira 및 Confluence 관리자는 Azure AD를 사용하여 SSO를 활성화하도록 플러그 인 사용할 수 있습니다.

## <a name="assumptions"></a>가정

* Jira 및 Confluence 인스턴스는 HTTPS를 사용하도록 설정됩니다.
* 사용자는 Jira 또는 Confluence에서 이미 생성되었습니다.
* 사용자는 Jira 또는 Confluence에서 할당된 역할을 갖습니다.
* 관리자는 플러그 인 구성에 필요한 정보에 액세스할 수 있습니다.
* 회사 네트워크 외부에서도 Jira 또는 Confluence를 사용할 수 있습니다.
* 플러그 인은 Jira 및 Confluence의 온-프레미스 버전에서만 작동합니다.

## <a name="prerequisites"></a>필수 조건

플러그 인을 설치하기 전에 다음 정보를 참조하세요.

* Windows 64비트 버전에 Jira 및 Confluence가 설치되어 있습니다.
* Jira 및 Confluence 버전에서 HTTPS가 사용됩니다.
* Jira 및 Confluence는 인터넷에서 사용 가능합니다.
* Jira 및 Confluence의 관리자 자격 증명이 있습니다.
* Azure AD의 관리자 자격 증명이 있습니다.
* WebSudo는 Jira 및 Confluence에서 비활성화됩니다.

## <a name="supported-versions-of-jira-and-confluence"></a>지원되는 Jira 및 Confluence 버전

플러그 인에서는 다음 버전의 Jira 및 Confluence를 지원합니다.

* JIRA Core 및 Software: 6.0~7.12
* Jira Service Desk: 3.0.0-3.5.0
* JIRA는 5.2도 지원합니다. 자세한 내용을 보려면 [JIRA 5.2용 Microsoft Azure Active Directory Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)을 클릭하세요.
* Confluence: 5.0 ~ 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>설치

플러그 인을 설치하려면 다음 단계를 따르세요.

1. Jira 또는 Confluence 인스턴스에 관리자로 로그인합니다.

2. Jira/Confluence 관리 콘솔로 이동하고 **추가 기능**을 선택합니다.

3. Microsoft 다운로드 센터에서 [Jira용 Microsoft SAML SSO 플러그 인](https://www.microsoft.com/download/details.aspx?id=56506)/ [Confluence용 Microsoft SAML SSO 플러그 인](https://www.microsoft.com/download/details.aspx?id=56503)을 다운로드합니다.

   적절한 버전의 플러그 인이 검색 결과에 나타납니다.

4. 플러그 인을 선택하면 UPM(범용 플러그 인 관리자)에서 설치합니다.

플러그 인이 설치되면 **추가 기능 관리**의 **사용자가 설치한 추가 기능** 섹션에 표시됩니다.

## <a name="plug-in-configuration"></a>플러그 인 구성

플러그 인 사용을 시작하기 전에 구성해야 합니다. 플러그 인을 선택하고, **구성** 단추를 선택하고, 구성 세부 정보를 제공합니다.

다음 이미지는 Jira 및 Confluence의 구성 화면을 보여줍니다.

![플러그 인 구성 화면](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **메타데이터 URL**: Azure AD에서 페더레이션 메타데이터를 가져오는 URL입니다.

* **식별자**: Azure AD가 요청의 원본 유효성을 검사하는 데 사용하는 URL입니다. Azure AD의 **식별자** 요소에 매핑됩니다. 플러그 인 https://로이 URL에 자동으로 파생*\<도메인: 포트 >*/입니다.

* **회신 URL**: SAML 로그인을 시작하는 IdP(ID 공급자)의 회신 URL입니다. Azure AD의 **회신 URL** 요소에 매핑됩니다. 플러그 인 https://로이 URL에 자동으로 파생*\<도메인: 포트 >*/plugins/servlet/saml/auth.

* **로그온 URL**: SAML 로그인을 시작하는 IdP의 로그온 URL입니다. Azure AD의 **로그온** 요소에 매핑됩니다. 플러그 인 https://로이 URL에 자동으로 파생*\<도메인: 포트 >*/plugins/servlet/saml/auth.

* **IdP 엔터티 ID**: IdP에서 사용하는 엔터티 ID입니다. 이 상자는 메타데이터 URL을 확인할 때 채워집니다.

* **로그인 URL**: IdP의 로그인 URL입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.

* **로그아웃 URL**: IdP의 로그아웃 URL입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.

* **X.509 인증서**: IdP의 X.509 인증서입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.

* **로그인 단추 이름**: 조직이 사용자에게 로그인 페이지에 표시하려는 로그인 단추의 이름입니다.

* **SAML 사용자 ID 위치**: SAML 응답에서 Jira 또는 Confluence 사용자 ID가 필요한 위치입니다. **NameID**에 있을 수도 있고 사용자 지정 특성 이름에 있을 수도 있습니다.

* **특성 이름**: 사용자 ID가 필요한 특성 이름입니다.

* **홈 영역 검색 사용**: 회사에서 AD FS(Active Directory Federation Services) 기반 로그인을 사용하는 경우 선택합니다.

* **도메인 이름**: 로그인이 AD FS 기반인 경우 도메인 이름입니다.

* **Single Sign-On 사용**: 사용자가 Jira 또는 Confluence에서 로그아웃할 때 Azure AD에서 로그아웃하려는 경우 선택합니다.

## <a name="troubleshooting"></a>문제 해결

* **여러 인증서 오류가 발생하는 경우**: Azure AD에 로그인하여 앱에서 사용할 수 있는 여러 인증서를 제거합니다. 인증서를 하나만 남겨 둡니다.

* **인증서가 Azure AD에서 곧 만료되는 경우**: 추가 기능이 인증서의 자동 롤오버를 처리합니다. 인증서가 곧 만료되는 경우 새 인증서를 활성으로 표시하고 사용하지 않는 인증서를 삭제해야 합니다. 이 시나리오에서 사용자가 Jira에 로그인하려고 시도하면 플러그 인은 새 인증서를 페치하고 저장합니다.

* **WebSudo를 사용하지 않으려는 경우(보안 관리자 세션 해제)**:

  * Jira의 경우 보안 관리자 세션(즉, 관리 기능에 액세스하기 전에 암호 확인)이 기본적으로 사용됩니다. Jira 인스턴스에서 이 기능을 제거하려는 경우 jira-config.properties 파일에서 다음 줄을 지정합니다. `ira.websudo.is.disabled = true`

  * Confluence의 경우 [Confluence 지원 사이트](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)의 단계를 따릅니다.

* **메타데이터 URL에서 채우는 필드가 채워지지 않는 경우**:

  * URL이 올바른지 확인합니다. 올바른 테넌트 및 앱 ID를 매핑했는지 확인합니다.

  * 브라우저에서 URL을 입력하고 페더레이션 메타데이터 XML을 수신하는지 확인합니다.

* **내부 서버 오류가 있는 경우**: 설치의 로그 디렉터리에서 로그를 검토합니다. 사용자가 Azure AD SSO를 사용하여 로그인하려고 시도할 때 오류가 발생하는 경우 지원 팀과 함께 로그를 공유할 수 있습니다.

* **사용자가 로그인을 시도할 때 “사용자 ID를 찾을 수 없음” 오류가 있는 경우**: Jira 또는 Confluence에서 사용자 ID를 만듭니다.

* **Azure AD에 “앱을 찾을 수 없음” 오류가 있는 경우**: Azure AD에서 적절한 URL이 앱에 매핑되는지 확인합니다.

* **지원이 필요한 경우**: [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 연락합니다. 팀에서 24~48시간(업무 시간) 이내로 응답해 드립니다.

  또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다.

## <a name="plug-in-faq"></a>플러그 인 FAQ

이 플러그 인과 관련된 쿼리가 있으면 아래 FAQ를 참조하세요.

### <a name="what-does-the-plug-in-do"></a>플러그 인은 무엇을 수행하나요?

플러그 인은 Atlassian Jira(Jira Core, Jira Software, Jira Service Desk 포함) 및 Confluence 온-프레미스 소프트웨어에 대한 SSO(Single Sign-On) 기능을 제공합니다. 플러그 인은 IdP(ID 공급자)로 Azure AD(Azure Active Directory)와 함께 작동합니다.

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>플러그 인과 함께 작동하는 Atlassian 제품은 무엇인가요?

플러그 인은 Jira 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>플러그 인은 클라우드 버전에서 작동하나요?

아니요. 플러그 인은 Jira 및 Confluence의 온-프레미스 버전만을 지원합니다.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>플러그 인에서 지원하는 Jira 및 Confluence 버전은 무엇인가요?

플러그 인에서는 이러한 버전을 지원합니다.

* JIRA Core 및 Software: 6.0~7.12
* Jira Service Desk: 3.0.0-3.5.0
* JIRA는 5.2도 지원합니다. 자세한 내용을 보려면 [JIRA 5.2용 Microsoft Azure Active Directory Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)을 클릭하세요.
* Confluence: 5.0 ~ 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>플러그 인은 무료인가요, 유료인가요?

무료 추가 기능입니다.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>플러그 인을 배포한 후 Jira 또는 Confluence를 다시 시작해야 하나요?

다시 시작할 필요가 없습니다. 플러그 인 사용을 즉시 시작할 수 있습니다.

### <a name="how-do-i-get-support-for-the-plug-in"></a>플러그 인에 대한 지원을 받으려면 어떻게 하나요?

이 플러그 인에 필요한 지원은 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의할 수 있습니다. 팀에서 24~48시간(업무 시간) 이내로 응답해 드립니다.

또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>플러그 인은 Jira 및 Confluence에 설치된 Mac 또는 Ubuntu에서 작동하나요?

플러그 인은 Jira 및 Confluence에 설치된 64비트 Windows 서버에서만 테스트를 거쳤습니다.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>플러그 인은 Azure AD 이외에 IdP와 함께 작동하나요?

아니요. Azure AD와만 함께 작동합니다.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>플러그 인과 함께 작동하는 SAML 버전은 무엇인가요?

SAML 2.0과 함께 작동합니다.

### <a name="does-the-plug-in-do-user-provisioning"></a>플러그 인은 사용자 프로비저닝을 수행하나요?

아니요. 플러그 인은 SAML 2.0 기반 SSO만 제공합니다. SSO 로그인 전에 애플리케이션에서 사용자를 프로비전해야 합니다.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>플러그 인에서 지원하는 Jira 및 Confluence 버전은 무엇인가요?

아니요. 플러그 인은 Jira 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>플러그 인은 Jira 및 Confluence의 HTTP 버전과 함께 작동하나요?

아니요. 플러그 인은 HTTPS가 설정된 설치 버전에서만 작동합니다.
