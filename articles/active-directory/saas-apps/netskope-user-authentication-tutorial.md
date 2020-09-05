---
title: '자습서: Netskope User Authentication과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Netskope User Authentication 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a2bd9b7240033df47f57dc3432ce50819d8754b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544029"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>자습서: Netskope User Authentication과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Netskope User Authentication을 통합하는 방법을 알아봅니다. Azure AD와 Netskope User Authentication을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Netskope User Authentication에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Netskope User Authentication에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Netskope User Authentication SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Netskope User Authentication은 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>갤러리에서 Netskope User Authentication 추가

Azure AD에 Netskope User Authentication 통합을 구성하려면 갤러리의 Netskope User Authentication을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Netskope User Authentication**을 입력합니다.
1. 결과 패널에서 **Netskope User Authentication**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Netskope User Authentication에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Netskope User Authentication에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Netskope User Authentication의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Netskope User Authentication에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Netskope User Authentication SSO 구성](#configure-netskope-user-authentication-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Netskope User Authentication 테스트 사용자 만들기](#create-netskope-user-authentication-test-user)** - B.Simon의 Azure AD 표현과 연결된 사용자를 Netskope User Authentication에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Netskope User Authentication** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<tenantname>.goskope.com/<customer entered string>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값은 자습서의 뒷부분에서 설명합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<tenantname>.goskope.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 로그온 URL 값을 실제 로그온 URL로 업데이트하세요. 로그온 URL 값을 가져오려면 [Netskope User Authentication 클라이언트 지원 팀](mailto:support@netskope.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Netskope User Authentication 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Netskope User Authentication에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Netskope User Authentication**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-netskope-user-authentication-sso"></a>Netskope User Authentication SSO 구성

1. 브라우저에서 새 탭을 열고, 관리자 권한으로 Netskope User Authentication 회사 사이트에 로그인합니다.

1. **활성 플랫폼** 탭을 클릭합니다.

    ![Netskope User Authentication 구성](./media/netskope-user-authentication-tutorial/user1.png)

1. **프록시 전달**까지 아래로 스크롤하여 **SAML**을 선택합니다.

    ![Netskope User Authentication 구성](./media/netskope-user-authentication-tutorial/config-saml.png)

1. **SAML 설정** 페이지에서 다음 단계를 수행합니다.

    ![Netskope User Authentication 구성](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. **SAML 엔터티 ID** 값을 복사하고 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

    b. **SAML ACS URL** 값을 복사하고 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

1. **계정 추가**를 클릭합니다.

    ![Netskope User Authentication 구성](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. **SAML 계정 추가** 페이지에서 다음 단계를 수행합니다.

    ![Netskope User Authentication 구성](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. **이름** 텍스트 상자에 Azure AD와 같은 이름을 입력합니다.

    b. Azure Portal에서 복사한 **IDP URL** 값을 **IDP URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IDP 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    d. 다운로드된 메타데이터 파일을 메모장에서 열고, 내용을 클립보드에 복사한 다음, **IDP 인증서** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-netskope-user-authentication-test-user"></a>Netskope User Authentication 테스트 사용자 만들기

1. 브라우저에서 새 탭을 열고, 관리자 권한으로 Netskope User Authentication 회사 사이트에 로그인합니다.

1. 왼쪽 탐색 창에서 **설정**을 클릭합니다.

    ![Netskope User Authentication 사용자 만들기](./media/netskope-user-authentication-tutorial/config-settings.png)

1. **활성 플랫폼** 탭을 클릭합니다.

    ![Netskope User Authentication 사용자 만들기](./media/netskope-user-authentication-tutorial/user1.png)

1. **사용자** 탭을 클릭합니다.

    ![Netskope User Authentication 사용자 만들기](./media/netskope-user-authentication-tutorial/add-user.png)

1. **사용자 추가**를 클릭합니다.

    ![Netskope User Authentication 사용자 만들기](./media/netskope-user-authentication-tutorial/user-add.png)

1. 추가할 사용자의 이메일 주소를 입력하고 **추가**를 클릭합니다.

    ![Netskope User Authentication 사용자 만들기](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Netskope User Authentication 타일을 클릭하면 SSO를 설정한 Netskope User Authentication에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Netskope User Authentication 사용해보기](https://aad.portal.azure.com/)