---
title: '자습서: Proxyclick과 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Proxyclick 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b3974c5e2a46ede6ef3f92d74759f58ed55b8497
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553447"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>자습서: Proxyclick과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Proxyclick을 통합하는 방법에 대해 알아봅니다.
이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 Proxyclick에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Proxyclick에 자동으로 로그인(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Proxyclick과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.
* Single Sign-On을 사용하도록 설정된 Proxyclick 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Proxyclick에서 SP 및 IdP 시작 SSO를 지원합니다.

## <a name="add-proxyclick-from-the-gallery"></a>갤러리에서 Proxyclick 추가

Proxyclick이 Azure AD에 통합되도록 설정하려면 갤러리의 Proxyclick을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **Proxyclick**을 입력합니다. 검색 결과에서 **Proxyclick**을 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Proxyclick에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 Proxyclick의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Proxyclick에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[Proxyclick Single Sign-On을 구성](#configure-proxyclick-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[Proxyclick 테스트 사용자를 만듭니다](#create-a-proxyclick-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Proxyclick에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 Proxyclick 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. IdP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![기본 SAML 구성 대화 상자](common/idp-intiated.png)

    1. **식별자** 상자에서 URL을 다음 패턴으로 입력합니다.
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. **회신 URL** 상자에서 URL을 다음 패턴으로 입력합니다.

       `https://saml.proxyclick.com/consume/<companyId>`

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택합니다. **로그온 URL** 상자에 다음 패턴으로 URL을 입력합니다.
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > 이러한 값은 자리 표시자입니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용해야 합니다. 이러한 값을 가져오기 위한 단계는 이 자습서의 뒷부분에서 설명합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **인증서(Base64)** 옆의 **다운로드** 링크를 선택하고 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **Proxyclick 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick Single Sign-On 구성

1. 새 웹 브라우저 창에서 Proxyclick 회사 사이트에 관리자 권한으로 로그인합니다.

2. **Account & Settings**(계정 및 설정)를 선택합니다.

    ![Account & Settings 선택](./media/proxyclick-tutorial/configure1.png)

3. **Integrations**(통합) 섹션까지 아래로 스크롤하여 **SAML**을 선택합니다.

    ![SAML 선택](./media/proxyclick-tutorial/configure2.png)

4. **SAML** 섹션에서 다음 단계를 수행합니다.

    ![SAML 섹션](./media/proxyclick-tutorial/configure3.png)

    1. **SAML Consumer URL**(SAML 소비자 URL) 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **회신 URL** 상자에 붙여넣습니다.

    1. **SAML SSO Redirect URL**(SAML SSO 리디렉션 URL) 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **로그온 URL** 및 **식별자** 상자에 붙여넣습니다.

    1. **SAML Request Method**(SAML 요청 메서드) 목록에서 **HTTP Redirect**(HTTP 리디렉션)를 선택합니다.

    1. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Issuer**(발급자) 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 **로그인 URL**을 **SAML 2.0 Endpoint URL**(SAML 2.0 엔드포인트 URL) 상자에 붙여넣습니다.

    1. Azure Portal에서 다운로드한 인증서 파일을 메모장에서 엽니다. 이 파일의 내용을 **Certificate**(인증서) 상자에 붙여넣습니다.

    1. **변경 내용 저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 화면의 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **BrittaSimon@\<yourcompanydomain>\<extension>.** 을 입력합니다. (예: BrittaSimon@contoso.com)

    1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Proxyclick에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Proxyclick**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Proxyclick**을 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고, 창 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 창 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-proxyclick-test-user"></a>Proxyclick 테스트 사용자 만들기

Azure AD 사용자가 Proxyclick에 로그인할 수 있도록 설정하려면 해당 사용자를 Proxyclick에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. Proxyclick 회사 사이트에 관리자 권한으로 로그인합니다.

1. 창 위쪽에서 **Colleagues**(동료)를 선택합니다.

    ![Colleagues 선택](./media/proxyclick-tutorial/user1.png)

1. **Add Colleague**(동료 추가)를 클릭합니다.

    ![동료 추가 선택](./media/proxyclick-tutorial/user2.png)

1. **Add a colleague**(동료 추가) 섹션에서 다음 단계를 수행합니다.

    ![동료 추가 섹션](./media/proxyclick-tutorial/user3.png)

    1. **이메일** 상자에서 사용자의 이메일 주소를 입력합니다. 여기서는 **brittasimon\@contoso.com**입니다.

    1. **First Name**(이름) 텍스트 상자에서 사용자의 이름을 입력합니다. 여기서는 **Britta**입니다.

    1. **Last Name**(성) 텍스트 상자에서 사용자의 성을 입력합니다. 여기서는 **Simon**입니다.

    1. **사용자 추가**를 선택합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 Proxyclick 타일을 선택하면 SSO를 설정한 Proxyclick 인스턴스에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

