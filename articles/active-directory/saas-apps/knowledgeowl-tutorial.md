---
title: '자습서: KnowledgeOwl과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 KnowledgeOwl 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: a35fb30ca19e08c68c99c9c9524231706b066e11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549894"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>자습서: KnowledgeOwl과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 KnowledgeOwl을 연결하는 방법에 대해 알아봅니다. Azure AD와 KnowledgeOwl을 연결하는 경우 다음을 수행할 수 있습니다.

* KnowledgeOwl에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 KnowledgeOwl에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* KnowledgeOwl SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* KnowledgeOwl은 **SP 및 IDP** 시작 SSO를 지원합니다.
* KnowledgeOwl은 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-knowledgeowl-from-the-gallery"></a>갤러리에서 KnowledgeOwl 추가

KnowledgeOwl이 Azure AD에 통합되도록 구성하려면 갤러리의 KnowledgeOwl을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **KnowledgeOwl**을 입력합니다.
1. 결과 패널에서 **KnowledgeOwl**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>KnowledgeOwl용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 KnowledgeOwl에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 KnowledgeOwl의 관련 사용자 간에 연결 관계를 설정해야 합니다.

KnowledgeOwl에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[KnowledgeOwl SSO 구성](#configure-knowledgeowl-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[KnowledgeOwl 테스트 사용자 만들기](#create-knowledgeowl-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 KnowledgeOwl에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **KnowledgeOwl** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서의 뒷부분에 설명된 실제 식별자, 회신 URL 및 로그온 URL에서 이러한 값을 업데이트해야 합니다.

1. KnowledgeOwl 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 KnowledgeOwl 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 | 원본 특성 | 네임스페이스 |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(원시)** 를 찾고, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificateraw.png)

1. **KnowledgeOwl 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 KnowledgeOwl에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **KnowledgeOwl**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl SSO 구성

1. 다른 웹 브라우저 창에서 KnowledgeOwl 회사 사이트에 관리자로 로그인합니다.

1. **설정**을 클릭한 다음, **보안**을 선택합니다.

    ![KnowledgeOwl 구성](./media/knowledgeowl-tutorial/configure1.png)

1. **SAML SSO 통합**으로 스크롤하여 다음 단계를 수행합니다.

    ![KnowledgeOwl 구성](./media/knowledgeowl-tutorial/configure2.png)

    a. **SAML SSO 사용**을 선택합니다.

    b. **SP 엔터티 ID** 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자(엔터티 ID)** 에 붙여넣습니다.

    다. **SP 로그인 URL** 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **로그온 URL 및 회신 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IdP 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그인 URL** 값을 **IdP 로그인 URL** 텍스트 상자에 붙여넣습니다.

    f. Azure Portal에서 복사한 **로그아웃 URL** 값을 **IdP 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    g. **IdP 인증서 업로드**를 클릭하여 Azure Portal에서 다운로드한 인증서 양식을 업로드합니다.

    h. **맵 SAML 특성**을 클릭하여 특성을 매핑하고 다음 단계를 수행합니다.

    ![KnowledgeOwl 구성](./media/knowledgeowl-tutorial/configure3.png)

    * **SSO ID** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`를 입력합니다.
    * **사용자 이름/이메일** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.
    * **이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.
    * **성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`을 입력합니다.
    * 페이지 맨 아래에 있는 **저장**

    i. 페이지 맨 아래에서 **저장**을 클릭합니다.

    ![KnowledgeOwl 구성](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl 테스트 사용자 만들기

이 섹션에서는 KnowledgeOwl에서 B.Simon이라는 사용자를 만듭니다. KnowledgeOwl은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. KnowledgeOwl에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [KnowledgeOwl 지원 팀](mailto:support@knowledgeowl.com)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 KnowledgeOwl 타일을 클릭하면 SSO를 설정한 KnowledgeOwl에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 KnowledgeOwl 사용해보기](https://aad.portal.azure.com/)