---
title: '자습서: Nintex Promapp과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Nintex Promapp 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: 3b4102b4e8dcbd5aa04e354cec0ab91bf139572c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553481"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>자습서: Nintex Promapp과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Nintex Promapp을 통합하는 방법에 대해 알아봅니다. Azure AD와 Nintex Promapp을 통합할 경우 다음을 수행할 수 있습니다.

* Nintex Promapp에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Nintex Promapp에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Nintex Promapp SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Nintex Promapp은 **SP 및 IDP** 시작 SSO를 지원합니다.
* Nintex Promapp은 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-nintex-promapp-from-the-gallery"></a>갤러리에서 Nintex Promapp 추가

Nintex Promapp의 Azure AD 통합을 구성하려면 갤러리의 Nintex Promapp을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Nintex Promapp**을 입력합니다.
1. 결과 패널에서 **Nintex Promapp**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Nintex Promapp용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Nintex Promapp에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Nintex Promapp의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Nintex Promapp에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Nintex Promapp SSO 구성](#configure-nintex-promapp-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Nintex Promapp 테스트 사용자 만들기](#create-nintex-promapp-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Nintex Promapp에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Nintex Promapp** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    1. **식별자** 상자에서 URL을 다음 패턴으로 입력합니다.

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Nintex Promapp과 Azure AD의 통합은 현재 서비스 시작 인증에 대해서만 구성됩니다. (즉, Nintex Promapp URL로 이동하면 인증 프로세스가 시작됩니다.) 그러나 **회신 URL**은 필수 필드입니다.

    1. **회신 URL** 상자에서 URL을 다음 패턴으로 입력합니다.

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 상자에 `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate` 패턴으로 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 자리 표시자입니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용해야 합니다. 이 값을 얻으려면 [Nintex Promapp 지원 팀](https://www.promapp.com/about-us/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 대화 상자에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Nintex Promapp 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Nintex Promapp에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Nintex Promapp**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-nintex-promapp-sso"></a>Nintex Promapp SSO 구성

1. Nintex Promapp 회사 사이트에 관리자 권한으로 로그인합니다.

2. 창 위쪽의 메뉴에서 **Admin**(관리자)을 선택합니다.

    ![관리자 선택][12]

3. **Configure**(구성)를 선택합니다.

    ![구성 선택][13]

4. **Security**(보안) 대화 상자에서 다음 단계를 수행합니다.

    ![Security 대화 상자][14]

    1. Azure Portal에서 복사한 **로그인 URL**을 **SSO-Login URL**(SSO 로그인 URL) 상자에 붙여넣습니다.

    1. **SSO - Single Sign-on Mode**(SSO - Single Sign-On 모드) 목록에서 **Optional**(선택)을 선택합니다. **저장**을 선택합니다.

       > [!NOTE]
       > Optional 모드는 테스트 전용입니다. 구성에 만족하면 **SSO - Single Sign-on Mode** 목록에서 **Required**(필수)를 선택하여 모든 사용자가 Azure AD를 통해 인증하도록 합니다.

    1. 이전 섹션에서 다운로드한 인증서를 메모장에서 엽니다. 첫 번째 줄( **-----BEGIN CERTIFICATE-----** ) 또는 마지막 줄( **-----END CERTIFICATE-----** ) 없이 인증서의 내용을 복사합니다. 인증서의 내용을 **SSO-x.509 Certificate**(SSO-x.509 인증서) 상자에 붙여넣은 다음, **저장**을 선택합니다.

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp 테스트 사용자 만들기

이 섹션에서는 Nintex Promapp에서 B.Simon이라는 사용자를 만듭니다. Nintex Promapp은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Nintex Promapp에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Nintex Promapp 타일을 클릭하면 SSO를 설정한 Nintex Promapp에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Nintex Promapp 사용해보기](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png