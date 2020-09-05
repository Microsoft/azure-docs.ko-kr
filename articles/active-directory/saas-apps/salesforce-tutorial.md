---
title: '자습서: Salesforce와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Salesforce 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.openlocfilehash: 7228f4fbf348b8112654ece91aa5e9e831ac1201
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>자습서: Salesforce와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Salesforce를 연결하는 방법에 대해 알아봅니다. Azure AD와 Salesforce를 연결하면 다음을 수행할 수 있습니다.

* Salesforce에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Salesforce에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Salesforce SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Salesforce가 **SP**에서 시작된 SSO를 지원

* Salesforce가 [**자동** 사용자 프로비저닝 및 프로비전 해제(권장)](salesforce-provisioning-tutorial.md) 지원

* Salesforce가 **JIT(Just-in-time)** 사용자 프로비전을 지원

* 이제 Salesforce 모바일 애플리케이션을 Azure AD에서 SSO가 가능하도록 설정할 수 있습니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.
* Salesforce를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>갤러리에서 Salesforce 추가

Salesforce의 Azure AD 통합을 구성하려면 갤러리의 Salesforce를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Salesforce**를 입력합니다.
1. 결과 패널에서 **Salesforce**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Salesforce에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Salesforce에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Salesforce의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Salesforce에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Salesforce SSO 구성](#configure-salesforce-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Salesforce 테스트 사용자 만들기](#create-salesforce-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Salesforce에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **B.Simon** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`

    다. **식별자** 텍스트 상자에 다음 패턴으로 값을 입력합니다.

    엔터프라이즈 계정: `https://<subdomain>.my.salesforce.com`

    개발자 계정: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [Salesforce 클라이언트 지원 팀](https://help.salesforce.com/support)에 문의하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Salesforce 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Salesforce에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Salesforce**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-salesforce-sso"></a>Salesforce SSO 구성

1. Salesforce 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Salesforce 설정**을 클릭하면 Salesforce Single Sign-On 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Salesforce Single Sign-On에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~13단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Salesforce를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Salesforce 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/configure1.png)

1. 탐색 창에서 **설정**으로 스크롤하고 **ID**를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-admin-sso.png)

1. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce 계정에 Single Sign-On을 사용하도록 설정할 수 없는 경우 [Salesforce 클라이언트 지원 팀](https://help.salesforce.com/support)에 문의해야 합니다.

1. **SAML 사용**을 선택한 다음 **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-enable-saml.png)

1. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. **파일 선택**을 클릭하여 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드하고 **만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/xmlchoose.png)

1. **SAML Single Sign-On 설정** 페이지에서 필드에 내용이 자동으로 입력되면 **사용자 프로비저닝 사용**을 선택한 다음, **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/salesforcexml.png)

1. Salesforce의 왼쪽 탐색 패널에서 **회사 설정**을 클릭하여 관련 섹션을 확장하고 **내 도메인**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-my-domain.png)

1. **인증 구성** 섹션으로 스크롤하여 **편집** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. **인증 구성** 섹션에서 SAML SSO 구성의 **인증 서비스**로 **AzureSSO**를 선택한 다음, **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 둘 이상의 인증 서비스를 선택하는 경우 사용자가 Salesforce 환경으로 Single Sign-On을 시작하면 로그인하려는 인증 서비스를 선택하라는 메시지가 표시됩니다. 이 메시지가 표시되지 않도록 하려면 **다른 모든 인증 서비스를 선택하지 않은 상태로 유지**해야 합니다.

### <a name="create-salesforce-test-user"></a>Salesforce 테스트 사용자 만들기

이 섹션에서는 Salesforce에서 B.Simon이라는 사용자를 만듭니다. Salesforce는 기본적으로 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Salesforce에 사용자가 없는 경우 Salesforce에 액세스하려고 시도하면 새 사용자가 만들어집니다. Salesforce는 자동 사용자 프로비전도 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](salesforce-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Salesforce 타일을 클릭하면, SSO를 설정한 Salesforce에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce에 대한 SSO 테스트(모바일)

1. Salesforce 모바일 애플리케이션을 엽니다. 로그인 페이지에서 **사용자 지정 도메인**을 클릭합니다.

    ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app1.png)

1. **사용자 지정 도메인** 텍스트 상자에 등록된 사용자 지정 도메인 이름을 입력하고 **계속**을 클릭합니다.

    ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app2.png)

1. Salesforce 애플리케이션에 로그인하기 위해 Azure AD 자격 증명을 입력하고 **다음**을 클릭합니다.

    ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app3.png)

1. 아래와 같이 **액세스 허용** 페이지에서 **허용**을 클릭하여 Salesforce 애플리케이션에 대한 액세스 권한을 부여합니다.

    ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app4.png)

1. 마지막으로 로그인하면 애플리케이션 홈 페이지가 표시됩니다.

    ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app5.png) ![Salesforce 모바일 앱](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비저닝 구성](salesforce-provisioning-tutorial.md)

- [Azure AD로 Salesforce 사용해보기](https://aad.portal.azure.com)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Salesforce를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/protect-salesforce)
