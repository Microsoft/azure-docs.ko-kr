---
title: '자습서: Pingboard와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Pingboard 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 0e7b09c13cd27bd8197f6b65a1213d3154db6ac3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553837"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>자습서: Pingboard와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pingboard를 통합하는 방법을 알아봅니다.
Pingboard를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* Azure AD에서 Pingboard에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Pingboard에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Pingboard와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Pingboard Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Pingboard는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

* Pingboard는 [자동 사용자 프로비전](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)을 지원합니다. 

## <a name="adding-pingboard-from-the-gallery"></a>갤러리에서 Pingboard 추가

Pingboard와 Azure AD의 통합을 구성하려면 갤러리의 Pingboard를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Pingboard를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Pingboard**를 입력하고, 결과 패널에서 **Pingboard**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Pingboard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Pingboard에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Pingboard의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Pingboard에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Pingboard Single Sign-On 구성](#configure-pingboard-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Pingboard 테스트 사용자 만들기](#create-pingboard-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Pingboard에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Pingboard에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Pingboard** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Pingboard 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에 URL을 입력합니다. `http://app.pingboard.com/sp`

    b. **회신 URL** 텍스트 상자에서 `https://<entity-id>.pingboard.com/auth/saml/consume` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    ![Pingboard 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://<sub-domain>.pingboard.com/sign_in` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Pingboard 클라이언트 지원 팀](https://support.pingboard.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Pingboard 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-pingboard-single-sign-on"></a>Pingboard Single Sign-On 통합

1. Pingboard 쪽에서 SSO를 구성하려면 새 브라우저 창을 열고 Pingboard 계정에 로그인합니다. Single Sign-On을 설정하려면 Pingboard 관리자여야 합니다.

2. 상단 메뉴에서 **앱 > 통합**을 선택합니다.

    ![Single Sign-on 구성](./media/pingboard-tutorial/Pingboard_integration.png)

3. **통합** 페이지에서 **"Azure Active Directory"** 타일을 찾아 클릭합니다.

    ![Pingboard Single Sign-on 통합](./media/pingboard-tutorial/Pingboard_aad.png)

4. 다음에 나오는 모달에서 **"구성"** 을 클릭합니다.

    ![Pingboard 구성 단추](./media/pingboard-tutorial/Pingboard_configure.png)

5. 다음 페이지에서 "Azure SSO 통합을 사용합니다."라는 메시지를 확인할 수 있습니다. 다운로드한 메타데이터 XML 파일을 메모장에서 열고 **IDP 메타데이터**에 콘텐츠를 붙여 넣습니다.

    ![Pingboard SSO 구성 화면](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. 파일 유효성을 검사하여 모든 것이 올바르면 지금부터 Single Sign-on이 사용됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Pingboard에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Pingboard**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Pingboard**를 선택합니다.

    ![애플리케이션 목록의 Pingboard 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-pingboard-test-user"></a>Pingboard 테스트 사용자 만들기

이 섹션은 Pingboard에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Pingboard는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](pingboard-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. Pingboard 회사 사이트에 관리자로 로그인합니다.

2. **디렉터리** 페이지에서 **"직원 추가"** 단추를 클릭합니다.

    ![직원 추가](./media/pingboard-tutorial/create_testuser_add.png)

3. **“직원 추가”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![피플 초대](./media/pingboard-tutorial/create_testuser_name.png)

    a. **전체 이름** 텍스트 상자에서 **Britta Simon**과 같은 사용자의 전체 이름을 입력합니다.

    b. **이메일** 텍스트 상자에서 **brittasimon@contoso.com** 과 같은 사용자의 이메일 주소를 입력합니다.

    다. **직함** 텍스트 상자에 Britta Simon의 직함을 입력합니다.

    d. **위치** 드롭다운에서 Britta Simon의 위치를 선택합니다.

    e. **추가**를 클릭합니다.

4. 사용자 추가를 확인하는 확인 화면이 나타납니다.

    ![확인](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Pingboard 타일을 클릭하면 SSO를 설정한 Pingboard에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비저닝 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
