---
title: '자습서: Workplace by Facebook과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c58ff181ec144dcd0b1089b0e53f868f241be73f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>자습서: Workplace by Facebook과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Workplace by Facebook을 통합하는 방법에 대해 알아봅니다. Azure AD와 Workplace by Facebook을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Workplace by Facebook에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Workplace by Facebook에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Workplace by Facebook SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> Facebook에는 Workplace Standard(무료)와 Workplace Premium(유료)의 두 가지 제품이 있습니다. 모든 Workplace Premium 테넌트는 비용 또는 필요한 라이선스에 영향을 미치지 않고 SCIM 및 SSO 통합을 구성할 수 있습니다. SSO 및 SCIM은 Workplace Standard에서 사용할 수 없는 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Workplace by Facebook이 **SP**에서 시작된 SSO를 지원
* Workplace by Facebook이 **Just-In-Time 프로비전**을 지원
* Workplace by Facebook이 **[자동 사용자 프로비전](workplacebyfacebook-provisioning-tutorial.md)** 을 지원
* 이제 Workplace by Facebook 모바일 애플리케이션을 Azure AD에서 SSO를 사용하도록 설정할 수 있습니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.
* Workplace by Facebook을 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>갤러리에서 Workplace by Facebook 추가

Azure AD에 Workplace by Facebook을 통합하도록 구성하려면 갤러리의 Workplace by Facebook을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Workplace by Facebook**을 입력합니다.
1. 결과 패널에서 **Workplace by Facebook**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Workplace by Facebook용 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Workplace by Facebook에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Workplace by Facebook의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Workplace by Facebook에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[Workplace by Facebook SSO 구성](#configure-workplace-by-facebook-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Workplace by Facebook 테스트 사용자 만들기](#create-workplace-by-facebook-test-user)** - B.Simon의 Azure AD 표현과 연결된 사용자를 Workplace by Facebook에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Workplace by Facebook** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<instancename>.facebook.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://www.facebook.com/company/<instanceID>` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://www.facebook.com/company/<instanceID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. Workplace 커뮤니티에 대한 올바른 값은 Workplace 회사 대시보드의 인증 페이지를 참조하세요. 이는 자습서의 뒷부분에 설명되어 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Workplace by Facebook 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 Workplace by Facebook에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Workplace by Facebook**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-workplace-by-facebook-sso"></a>Workplace by Facebook SSO 구성

1. Facebook별 Workplace 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Facebook별 Workplace 설정**을 클릭하면 Facebook별 Workplace 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Facebook별 Workplace에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Facebook별 Workplace를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Facebook별 Workplace 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

    > [!NOTE]
    > SAML 인증 프로세스의 일환으로 Workplace는 Azure AD에 매개 변수를 전달하기 위해 최대 2.5KB 크기의 쿼리 문자열을 사용할 수 있습니다.

1. 왼쪽 탐색 패널에서 **Security** > **Authentication** 탭으로 이동합니다.

    ![관리 패널](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. **SSO(Single-Sign On)** 옵션을 선택합니다.
    
    b. **+새 SSO 공급자 추가**를 클릭합니다.

1. **인증** 탭에서 **SSO(Single Sign-On)** 를 선택하고 다음 단계를 수행합니다.

    ![[인증] 탭](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. **SSO 공급자 이름**에 Azureadsso와 같은 SSO 인스턴스 이름을 입력합니다.

    b. **SAML URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    다. **SAML 발급자 URL 텍스트 상자**에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    d. Azure Portal에서 다운로드한 **base-64로 인코딩된 인증서**를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음 **SAML 인증서** 텍스트 상자에 붙여 넣습니다.

    e. 인스턴스의 **대상 URL**을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.

    f. 인스턴스의 **받는 사람 URL**을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    g. 인스턴스용 **ACS(Assertion Consumer Service) URL**을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    h. 섹션 맨 아래로 스크롤하여 **SSO 테스트** 단추를 클릭합니다. Azure AD 로그인 페이지가 있는 팝업 창이 나타납니다. 일반적인 인증처럼 자격 증명을 입력합니다.

    **문제 해결:** Azure AD에서 반환되는 이메일 주소가 로그인한 Workplace 계정과 동일한지 확인합니다.

    i. 테스트가 성공적으로 완료되면 페이지 하단으로 스크롤하여 **저장** 단추를 클릭합니다.

    j. Workplace를 사용하는 모든 사용자에게 이제 인증을 위한 Azure AD 로그인 페이지가 제공됩니다.

1. **SAML 로그아웃 리디렉션(선택 사항)**  -

    Azure AD의 로그아웃 페이지를 가리키는 데 사용할 수 있는 SAML 로그아웃 URL을 선택적으로 구성하도록 선택할 수 있습니다. 이 설정을 사용하도록 설정하고 구성하면 더 이상 사용자가 Workplace 로그아웃 페이지로 이동하지 않습니다. 대신 SAML 로그아웃 리디렉션 설정에 추가된 URL로 사용자가 리디렉션됩니다.

### <a name="configuring-reauthentication-frequency"></a>재인증 빈도 구성

매일, 3일, 1주, 2주, 매달을 주기로 SAML 확인을 요청하거나 SAML 확인을 요청하지 않도록 Workplace를 구성할 수 있습니다.

> [!NOTE]
> 모바일 애플리케이션에서 SAML 확인에 대한 최소값은 일주일로 설정되어 있습니다.

단추를 사용하여 모든 사용자에 대해 강제로 SAML 재설정을 수행할 수도 있습니다. 모든 사용자에 대해 SAML 인증이 필요합니다.

### <a name="create-workplace-by-facebook-test-user"></a>Workplace by Facebook 테스트 사용자 만들기

이 섹션에서는 Workplace by Facebook에서 B.Simon이라는 사용자를 만듭니다. Workplace by Facebook은 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다.

이 섹션에는 사용자의 작업이 없습니다. Workplace by Facebook에 사용자가 없는 경우 Workplace by Facebook에 액세스하려고 하면 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Workplace by Facebook 클라이언트 지원 팀](https://workplace.fb.com/faq/)에 문의합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Workplace by Facebook 타일을 클릭하면 SSO를 설정한 Workplace by Facebook에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Workplace by Facebook에 대한 SSO 테스트(모바일)

1. Workplace by Facebook 모바일 애플리케이션을 엽니다. 로그인 페이지에서 **로그인**을 클릭합니다.

    ![로그인](./media/workplacebyfacebook-tutorial/test05.png)

2. 회사 이메일을 입력하고 **계속**을 클릭합니다.

    ![이메일](./media/workplacebyfacebook-tutorial/test02.png)

3. **한 번만**을 클릭합니다.

    ![한 번](./media/workplacebyfacebook-tutorial/test04.png)

4. **허용**을 클릭합니다.

    ![허용](./media/workplacebyfacebook-tutorial/test03.png)

5. 마지막으로 로그인하면 애플리케이션 홈 페이지가 표시됩니다.    

    ![홈페이지](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비저닝 구성](workplacebyfacebook-provisioning-tutorial.md)

- [Azure AD를 사용하여 Workplace by Facebook 체험](https://aad.portal.azure.com)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

