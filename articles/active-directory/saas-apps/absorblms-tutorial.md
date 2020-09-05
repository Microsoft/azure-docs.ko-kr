---
title: '자습서: Absorb LMS와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 Absorb LMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 59fb36765ad3cd584af4d6459cd78e2886d0edce
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538708"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>자습서: Absorb LMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Absorb LMS를 통합하는 방법에 대해 알아봅니다.
Absorb LMS를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서는 Absorb LMS에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Absorb LMS에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Absorb LMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Absorb LMS Single Sign-On이 설정된 플랜

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Absorb LMS가 **IDP**에서 시작된 SSO를 지원

## <a name="adding-absorb-lms-from-the-gallery"></a>갤러리에서 Absorb LMS 추가

Absorb LMS가 Azure AD에 통합되도록 구성하려면 갤러리의 Absorb LMS를 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Absorb LMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Absorb LMS**를 입력하고 결과 패널에서 **Absorb LMS**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Absorb LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Absorb LMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Absorb LMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Absorb LMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Absorb LMS Single Sign-On 구성](#configure-absorb-lms-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Absorb LMS 테스트 사용자 만들기](#create-absorb-lms-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Absorb LMS에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Absorb LMS에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Absorb LMS** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Absorb LMS 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    **Absorb 5 - UI**를 사용하는 경우에는 다음 구성을 사용합니다.

    a. **식별자** 텍스트 상자에서 `https://company.myabsorb.com/account/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://company.myabsorb.com/account/saml` 패턴을 사용하여 URL을 입력합니다.

    **Absorb 5 - New Learner Experience**를 사용하는 경우에는 다음 구성을 사용합니다.

    a. **식별자** 텍스트 상자에서 `https://company.myabsorb.com/api/rest/v2/authentication/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://company.myabsorb.com/api/rest/v2/authentication/saml` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Absorb LMS 클라이언트 지원 팀](https://support.absorblms.com/hc/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑되는 기본 특성 목록을 보여줍니다.

    ![이미지](common/edit-attribute.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Absorb LMS 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-absorb-lms-single-sign-on"></a>Absorb LMS Single Sign-On 구성

1. 새 웹 브라우저 창에서 Absorb LMS 회사 사이트에 관리자로 로그인합니다.

2. 오른쪽 상단에서 **계정** 단추를 선택합니다.

    ![계정 단추](./media/absorblms-tutorial/1.png)

3. 계정 창에서 선택 **포털 설정**을 선택합니다.

    ![포털 설정 링크](./media/absorblms-tutorial/2.png)

4. **Manage SSO Settings**(SSO 설정 구성) 탭을 선택합니다.

    ![사용자 탭](./media/absorblms-tutorial/managesso.png)

5. **Manage Single Sign-On Settings**(Single Sign-On 설정 구성) 페이지에서 다음을 수행합니다.

    ![Single Sign-On 구성 페이지](./media/absorblms-tutorial/settings.png)

    a. **Name**(이름) 텍스트 상자에 이름(예: Azure AD Marketplace SSO)을 입력합니다.

    b. **Method**(메서드)로 **SAML**을 선택합니다.

    c. 메모장에서 Azure Portal에서 다운로드한 인증서를 엽니다. **---BEGIN CERTIFICATE---** 및 **---END CERTIFICATE---** 태그를 제거합니다. 그런 다음 **키** 상자에 나머지 콘텐츠를 붙여넣습니다.

    d. **모드** 상자에서 **ID 공급자 시작됨**을 선택합니다.

    e. **ID 속성** 상자에 Azure AD에서 사용자 ID로 구성한 속성을 선택합니다. 예를 들어 Azure AD에서 *nameidentifier*를 선택한 경우 **사용자 이름**을 선택합니다.

    f. **Signature Type**(서명 유형)으로 **Sha256**을 선택합니다.

    g. Azure Portal 애플리케이션의 **속성** 페이지에 있는 **사용자 액세스 URL**을 **로그인 URL**에 붙여넣습니다.

    h. Azure Portal의 **로그온 구성** 창에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL**에 붙여넣습니다.

    i. **Automatically Redirect**(자동으로 리디렉션)를 **On**(설정)으로 토글합니다.

6. **저장**을 선택합니다.

    ![SSO 로그인만 허용 설정/해제](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon\@yourcompanydomain.extension`을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Absorb LMS에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **Absorb LMS**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Absorb LMS**를 입력하고 선택합니다.

    ![애플리케이션 목록의 Absorb LMS 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-absorb-lms-test-user"></a>Absorb LMS 테스트 사용자 만들기

Azure AD 사용자가 Absorb LMS에 로그인하려면 Absorb LMS에서 해당 사용자가 설정되어 있어야 합니다. Absorb LMS의 경우 프로비전은 수동 작업입니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. Absorb LMS 회사 사이트에 관리자로 로그인합니다.

2. **사용자** 창에서 **사용자**를 선택합니다.

    ![사용자 링크](./media/absorblms-tutorial/absorblms_userssub.png)

3. **User**(사용자) 탭을 선택합니다.

    ![새로 추가 드롭다운 목록](./media/absorblms-tutorial/absorblms_createuser.png)

4. **사용자 추가** 페이지에서 다음을수행합니다.

    ![사용자 추가 페이지](./media/absorblms-tutorial/user.png)

    a. **이름** 텍스트 상자에 **Britta** 등의 이름을 입력합니다.

    b. **성** 텍스트 상자에 **Simon** 등의 성을 입력합니다.

    c. **사용자 이름** 텍스트 상자에 **Britta Simon** 등의 성명을 입력합니다.

    d. **Password**(암호) 상자에 사용자 암호를 입력합니다.

    e. **암호 확인** 상자에 암호를 다시 입력합니다.

    f. **활성** 전환을 **활성**으로 설정합니다.

5. **저장**을 선택합니다.

    ![SSO 로그인만 허용 설정/해제](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 기본적으로 SSO에서는 사용자 프로비전을 사용하도록 설정되어 있지 않습니다. 고객이 이 기능을 사용하도록 설정하려면, [현재](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) 문서의 설명에 따라 설정해야 합니다. 또한, 사용자 프로비전은 ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`을 통해 **Absorb 5 - New Learner Experience**에서만 사용할 수 있다는 점에 유의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Absorb LMS 타일을 클릭하면, SSO를 설정한 Absorb LMS에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
