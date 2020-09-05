---
title: '자습서: Everbridge와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Everbridge 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 1da2fd879dbeac1836469d46567566769f6163a2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555402"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>자습서: Everbridge와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Everbridge를 통합하는 방법에 대해 알아봅니다.
Azure AD와 Everbridge를 통합하는 경우 다음을 수행할 수 있습니다.

* Everbridge에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Everbridge에 자동으로 로그인할 수 있도록 합니다. 이 액세스 제어를 SSO(Single Sign-On)라고 합니다.
* Azure Portal을 사용하여 중앙의 한 위치에서 계정을 관리합니다.
Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Everbridge와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On을 사용하는 Everbridge 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Everbridge는 IDP 시작 SSO를 지원합니다.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Azure Marketplace에서 Everbridge 추가

Everbridge의 Azure AD 통합을 구성하려면 Azure Marketplace의 Everbridge를 관리되는 SaaS 앱 목록에 추가합니다.

Azure Marketplace에서 Everbridge를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Everbridge**를 입력합니다. 결과 패널에서 **Everbridge**를 선택하고 **추가**를 선택합니다.

     ![결과 목록의 EverBridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 테스트 사용자 Britta Simon을 기준으로 Everbridge에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Everbridge의 관련 사용자 간에 연결 관계를 설정합니다.

Everbridge에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

- [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
- [Everbridge를 Everbridge 관리자 포털 Single Sign-On으로 구성](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
- [Everbridge를 Everbridge 멤버 포털 Single Sign-On으로 구성](#configure-everbridge-as-everbridge-member-portal-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
- [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
- [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
- [Everbridge 테스트 사용자 만들기](#create-an-everbridge-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Everbridge에 만듭니다.
- [Single Sign-on 테스트](#test-single-sign-on) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Everbridge에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Everbridge** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집**을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

    >[!NOTE]
    >애플리케이션을 Azure Portal 및 Everbridge 포털 둘 다에서 관리자 포털 또는  멤버 포털로 구성합니다.

4. **Everbridge** 애플리케이션을 **Everbridge 관리자 포털**로 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Everbridge 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 상자에 `https://sso.everbridge.net/<API_Name>` 패턴을 따르는 URL을 입력합니다.

    b. **회신 URL** 상자에 `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias` 패턴을 따르는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL 값으로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Everbridge 지원 팀](mailto:support@everbridge.com)에 문의합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **Everbridge** 애플리케이션을 **Everbridge 멤버 포털**로 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

  * IDP 시작 모드로 애플리케이션을 구성하려는 경우 다음 단계를 수행합니다.

     ![IDP 시작 모드에 대한 EverBridge 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 상자에 `https://sso.everbridge.net/<API_Name>/<Organization_ID>` 패턴을 따르는 URL을 입력합니다.

    b. **회신 URL** 상자에 `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias` 패턴을 따르는 URL을 입력합니다.

   * SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, 다음 단계를 수행합니다.

     ![SP 시작 모드에 대한 Everbridge 도메인 및 URL Single Sign-On 정보](common/both-signonurl.png)

     a. **로그온 URL** 상자에 `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true` 패턴을 따르는 URL을 입력합니다.

     > [!NOTE]
     > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL 값을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Everbridge 지원 팀](mailto:support@everbridge.com)에 문의합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하여 **페더레이션 메타데이터 XML**을 다운로드합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Everbridge 설정** 섹션에서 요구 사항에 따라 필요한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    - 로그인 URL
    - Azure AD 식별자
    - 로그아웃 URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge를 Everbridge 관리자 포털 Single Sign-On으로 구성

**Everbridge**의 SSO를 **Everbridge 관리자 포털**로 구성하려면 다음 단계를 수행합니다.
 
1. 다른 웹 브라우저 창에서 Everbridge에 관리자 권한으로 로그인합니다.

1. 위쪽 메뉴에서 **설정** 탭을 선택합니다. **보안**에서 **Single Sign-on**을 선택합니다.
   
     ![Single Sign-On 구성](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. **이름** 상자에 식별자 공급자의 이름을 입력합니다. 회사 이름을 예로 들 수 있습니다.
   
     b. **API 이름** 상자에 API의 이름을 입력합니다.
   
     다. **파일 선택**을 선택하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.
   
     d. **SAML ID 위치**에서 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
   
     e. **ID 공급자 로그인 URL** 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.
   
     f. **서비스 공급자가 시작한 요청 바인딩**에서 **HTTP 리디렉션**을 선택합니다.

     g. **저장**을 선택합니다.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge를 Everbridge 멤버 포털 Single Sign-On으로 구성

**Everbridge**의 Single Sign-On을 **EverBridge 멤버 포털**로 구성하려면 다운로드한 **페더레이션 메타데이터 XML**을 [Everbridge 지원 팀](mailto:support@everbridge.com)에 보냅니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

Azure Portal에서 테스트 사용자 Britta Simon을 만들려면 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 모든 사용자 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 상자에 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예제는 BrittaSimon@contoso.com입니다.

    다. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시되는 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Everbridge에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** >**Everbridge**를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Everbridge**를 선택합니다.

    ![애플리케이션 목록의 Everbridge 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 대화 상자](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다. 화면 하단에서 **선택**을 선택합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 화면 하단에서 **선택**을 선택합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-an-everbridge-test-user"></a>Everbridge 테스트 사용자 만들기

이 섹션에서는 Everbridge에서 테스트 사용자인 Britta Simon을 만듭니다. Everbridge 플랫폼에서 사용자를 추가하려면 [Everbridge 지원 팀](mailto:support@everbridge.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 Everbridge에서 활성화해야 합니다. 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Everbridge 타일을 선택하면 SSO를 설정한 Everbridge 계정에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

