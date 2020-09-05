---
title: '자습서: Citrix ShareFile과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Citrix ShareFile 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: cfbb704799a1884c689bd0de547526a33f1ba7ce
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651905"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>자습서: Citrix ShareFile과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Citrix ShareFile을 통합하는 방법에 대해 알아봅니다.
Citrix ShareFile을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Citrix ShareFile에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Citrix ShareFile에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Citrix ShareFile과의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Citrix ShareFile Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Citrix ShareFile이 **SP**에서 시작된 SSO를 지원
* Citrix ShareFile이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-citrix-sharefile-from-the-gallery"></a>갤러리에서 Citrix ShareFile 추가

Citrix ShareFile의 Azure AD 통합을 구성하려면 갤러리의 Citrix ShareFile을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Citrix ShareFile**을 입력합니다.
1. 결과 패널에서 **Citrix ShareFile**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Citrix ShareFile에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Citrix ShareFile의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Citrix ShareFile에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[Citrix ShareFile SSO 구성](#configure-citrix-sharefile-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Citrix ShareFile 테스트 사용자 만들기](#create-citrix-sharefile-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Citrix ShareFile에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Citrix ShareFile** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<tenant-name>.sharefile.com/saml/login` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [Citrix ShareFile 클라이언트 지원 팀](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Citrix ShareFile 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Citrix ShareFile에 대한 액세스 권한을 부여하여 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Citrix ShareFile**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-citrix-sharefile-sso"></a>Citrix ShareFile SSO 구성

1. 다른 웹 브라우저 창에서 **Citrix ShareFile** 회사 사이트에 관리자로 로그인합니다.

1. 왼쪽 메뉴에서 **설정 아이콘** -> **보안** -> **로그인 및 보안 정책**을 클릭합니다.
   
    ![계정 관리](./media/sharefile-tutorial/settings-security.png "계정 관리")

1. **기본 설정**의 **Single Sign-On/SAML 2.0 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On](./media/sharefile-tutorial/saml-configuration.png "SSO(Single sign-on)")
   
    a. **SAML 사용**에서 **예**를 선택합니다.

    b. **ShareFile 발급자/ 엔터티 ID** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **식별자 URL** 상자에 붙여넣습니다.
    
    c. **IDP 발급자/엔터티 ID** 텍스트 상자에, Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    d. **X.509 인증서** 필드 옆의 **변경**을 클릭한 다음 Azure Portal에서 다운로드한 인증서를 업로드합니다.
    
    e. **로그인 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.
    
    f. **로그아웃 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

5. Citrix ShareFile 관리 포털에서 **저장**을 클릭합니다.

## <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile 테스트 사용자 만들기

1. **Citrix ShareFile** 테넌트에 로그인합니다.

2. **사람** -> **사용자 홈 관리** -> **새 사용자 만들기** -> **직원 만들기**을 클릭합니다.
   
    ![직원 만들기](./media/sharefile-tutorial/create-user.png "직원 만들기")

3. **기본 정보** 섹션에서 다음 단계를 수행합니다.
   
    ![기본 정보](./media/sharefile-tutorial/user-form.png "기본 정보")
   
    a. **이름** 텍스트 상자에 사용자의 **이름**을 **Britta**로 입력합니다.
   
    b.  **성** 텍스트 상자에 사용자의 **성**을 **Simon**으로 입력합니다.
   
    c. **이메일 주소** 텍스트 상자에 Britta Simon의 이메일 주소(예: **brittasimon\@contoso.com**)를 입력합니다.

4. **사용자 추가**를 클릭합니다.
  
    >[!NOTE]
    >Azure AD 계정 보유자에게 이메일이 발송되며 여기에 포함된 링크를 클릭하여 계정을 확인하면 계정이 활성화됩니다. 다른 Citrix ShareFile 사용자 계정 생성 도구 또는 Citrix ShareFile에서 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수도 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Citrix ShareFile 타일을 클릭하면 SSO를 설정한 Citrix ShareFile에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

