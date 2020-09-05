---
title: '자습서: Riskware와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Riskware 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b147037a58e4af59c0c3ccd56b6f6b241d775308
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553337"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>자습서: Riskware와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Riskware를 통합하는 방법에 대해 알아봅니다.
Riskware를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Riskware에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Rollbar에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Riskware와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Riskware Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Riskware에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-riskware-from-the-gallery"></a>갤러리에서 Riskware 추가

Riskware의 Azure AD 통합을 구성하려면 갤러리의 Riskware를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Riskware를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Riskware**를 입력하고 결과 패널에서 **Riskware**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Riskware에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Riskware의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Riskware에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Riskware Single Sign-On 구성](#configure-riskware-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Riskware 테스트 사용자 만들기](#create-riskware-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Riskware에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Riskware에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Riskware** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Riskware 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    
    | Environment| URL 패턴|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. **식별자(엔터티 ID)** 텍스트 상자에 다음 URL을 입력합니다.
    
    | Environment| URL 패턴|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [Riskware 클라이언트 지원 팀](mailto:support@pansoftware.com.au)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Riskware 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-riskware-single-sign-on"></a>Riskware Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Riskware 회사 사이트에 관리자로 로그인합니다.

1. 오른쪽 상단에 있는 **유지 관리**를 클릭하여 유지 관리 페이지를 엽니다.

    ![Riskware 구성 유지 관리](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 유지 관리 페이지에서 **인증**을 클릭합니다.

    ![Riskware 구성 인증](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. **인증 구성** 페이지에서 다음 단계를 수행합니다.

    ![Riskware 구성 인증 구성](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 인증 **형식**으로 **SAML**을 선택합니다.

    b. **코드** 텍스트 상자에 AZURE_UAT와 같은 코드를 입력합니다.

    다. **설명** 텍스트 상자에 SSO용 AZURE 구성과 같은 설명을 입력합니다.

    d. **Single Sign-On 페이지** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 페이지** 텍스트 상자에 붙여넣습니다.

    f. **게시 양식 필드** 텍스트 상자에 SAMLResponse와 같은 SAML이 포함된 게시 응답에 있는 필드 이름을 입력합니다.

    g. **XML ID 태그 이름** 텍스트 상자에 SAML 응답에서 고유 식별자를 포함하는 NameID와 같은 특성을 입력합니다.

    h. Azure Portal에서 다운로드한 **메타데이터 Xml**을 메모장에서 열고 메타데이터 파일에서 인증서를 복사하여 **인증서** 텍스트 상자에 붙여넣습니다.

    i. **소비자 URL** 텍스트 상자에 지원 팀으로부터 받은 **회신 URL** 값을 붙여넣습니다.

    j. **발급자** 텍스트 상자에 지원 팀으로부터 받은 **식별자** 값을 붙여넣습니다.

    > [!Note]
    > 이러한 값을 얻으려면 [Riskware 클라이언트 지원 팀](mailto:support@pansoftware.com.au)에 문의하세요.

    k. **POST 사용** 확인란을 선택합니다.

    l. **SAML 요청 사용** 확인란을 선택합니다.

    m. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Riskware에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Riskware**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Riskware**를 선택합니다.

    ![애플리케이션 목록의 Riskware 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-riskware-test-user"></a>Riskware 테스트 사용자 만들기

Azure AD 사용자가 Riskware에 로그인할 수 있도록 하려면 Riskware로 프로비전되어야 합니다. Riskware의 경우 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 보안 관리자 권한으로 Riskware에 로그인합니다.

1. 오른쪽 상단에 있는 **유지 관리**를 클릭하여 유지 관리 페이지를 엽니다. 

    ![Riskware 구성 유지 관리](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 유지 관리 페이지에서 **피플**을 클릭합니다.

    ![Riskware 구성 피플](./media/riskware-tutorial/tutorial_riskware_people.png)

1. **세부 정보** 탭을 선택하고 다음 단계를 수행합니다.

    ![Riskware 구성 세부 정보](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. 직원과 같은 **사람 유형**을 선택합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

1. **보안** 탭에서 다음 단계를 수행합니다.

    ![Riskware 구성 보안](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. **인증** 섹션에서 SSO용 AZURE 구성과 같이 설정한 **인증** 모드를 선택합니다.

    b. **로그온 세부 정보** 섹션에서 **사용자 ID** 텍스트 상자에 `brittasimon@contoso.com`과 같은 사용자의 메일을 입력합니다.

    다. **암호** 텍스트 상자에 사용자 암호를 입력합니다.

1. **조직** 탭에서 다음 단계를 수행합니다.

    ![Riskware 구성 조직](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. 옵션을 **Level1** 조직으로 선택합니다.

    b. **사용자의 주 작업 공간** 섹션에서 **위치** 텍스트 상자에 사용자의 위치를 입력합니다.

    다. **직원** 섹션에서 일반과 같은 **직원 상태**를 선택합니다.

    d. **저장**을 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Riskware 타일을 클릭하면 SSO를 설정한 Riskware에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
