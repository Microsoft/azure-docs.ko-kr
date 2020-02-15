---
title: '자습서: Tableau Online과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Tableau Online 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>자습서: Tableau Online과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Tableau Online을 통합하는 방법에 대해 알아봅니다. Azure AD와 Tableau Online을 통합하면 다음 작업을 수행할 수 있습니다.

* Tableau Online에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Tableau Online에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Tableau Online SSO(Single Sign-On)를 사용하도록 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Tableau Online은 **SP** 시작 SSO를 지원합니다.
* Tableau Online을 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>갤러리에서 Tableau Online 추가

Tableau Online의 Azure AD 통합을 구성하려면 갤러리의 Tableau Online을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Tableau Online**을 입력합니다.
1. 결과 패널에서 **Tableau Online**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Tableau Online에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Tableau Online의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Tableau Online에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Tableau Online SSO 구성](#configure-tableau-online-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Tableau Online 테스트 사용자 만들기](#create-tableau-online-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Tableau Online에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Tableau Online에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Tableau Online** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Tableau Online 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에 `https://sso.online.tableau.com/public/sp/login?alias=<entityid>` URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>` URL을 입력합니다.

    > [!NOTE]
    > 이 자습서의 **Tableau Online 설정** 섹션에서 `<entityid>` 값을 얻을 것입니다. 엔터티 ID 값은 **Tableau Online 설정** 섹션의 **Azure AD 식별자** 값이 됩니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Tableau Online 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예: BrittaSimon\@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Tableau Online에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Tableau Online**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Tableau Online**을 선택합니다.

    ![애플리케이션 목록의 Tableau Online 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-tableau-online-sso"></a>Tableau Online SSO 구성

1. 다른 브라우저 창에서 Tableau Online 애플리케이션에 로그온합니다. **설정** 및 **인증**에 차례로 이동합니다.

    ![Single Sign-on 구성](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. SAML을 사용하도록 설정하려면 **인증 유형** 섹션 아래에서 **추가 인증 방법 사용**을 선택한 다음, **SAML** 확인란을 선택합니다.

    ![Single Sign-on 구성](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. **Tableau Online으로 메타데이터 파일 가져오기** 섹션이 나올 때까지 아래로 스크롤합니다.  찾아보기를 클릭하여 Azure AD에서 다운로드한 메타데이터 파일을 가져옵니다. 그런 후 **Apply**를 클릭합니다.

   ![Single Sign-on 구성](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. **어설션 일치** 섹션에서 **이메일 주소**, **이름** 및 **성**에 대한 해당 ID 공급자 어설션 이름을 삽입합니다. Azure Ad에서 이 정보 얻으려면 
  
    a. Azure Portal에서 **Tableau Online** 애플리케이션 통합 페이지로 이동합니다.

    b. **사용자 특성 및 클레임** 섹션에서 편집 아이콘을 클릭합니다.

   ![Single Sign-on 구성](./media/tableauonline-tutorial/attributesection.png)

    다. 다음 단계에 따라 givenname, email, surname 특성의 네임스페이스 값을 복사합니다.

   ![Azure AD Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. **user.givenname** 값을 클릭합니다.

    e. **네임스페이스** 텍스트 상자의 값을 복사합니다.

    ![Single Sign-on 구성](./media/tableauonline-tutorial/attributesection2.png)

    f. 이메일 및 성의 네임스페이스 값을 복사하려면 위의 단계를 반복합니다.

    g. Tableau Onlin 애플리케이션으로 전환한 다음, **사용자 특성 및 클레임** 섹션을 다음과 같이 설정합니다.

    * 전자 메일: **메일** 또는 **userprincipalname**

    * 이름: **givenname**

    * 성: **surname**

    ![Single Sign-on 구성](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau Online 테스트 사용자 만들기

이 섹션에서는 Tableau Online에서 Britta Simon이라는 사용자를 만듭니다.

1. **Tableau Online**에서 **설정**을 클릭한 후 **인증** 섹션을 클릭합니다. **사용자 관리** 섹션이 나올 때까지 아래로 스크롤합니다. **사용자 추가**를 클릭하고 **이메일 주소 입력**을 클릭합니다.
  
    ![Azure AD 테스트 사용자 만들기](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **(SAML) 인증에 대한 사용자 추가**를 선택합니다. **이메일 주소 입력** 텍스트 상자에 britta.simon\@contoso.com을 추가합니다.
  
    ![Azure AD 테스트 사용자 만들기](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. **사용자 추가**를 클릭합니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Tableau Online 타일을 클릭하면 SSO를 설정한 Tableau Online에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)