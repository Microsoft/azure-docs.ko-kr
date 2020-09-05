---
title: '자습서: Workspot Control과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Workspot Control에 대해 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: bfbecc71638e6feaaf29809f09dda752dd29b2ae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88526570"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>자습서: Workspot Control과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workspot Control을 통합하는 방법에 대해 알아봅니다. Workspot Control을 Azure AD와 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 Workspot Control에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정을 사용하여 SSO(Single Sign-On)로 Workspot Control에 자동으로 로그인하도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure AD의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Workspot Control와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

* Workspot Control Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

> [!Note]
> Workspot Control에서 SP 시작 및 IDP 시작 SSO를 지원합니다.


## <a name="adding-workspot-control-from-the-gallery"></a>갤러리에서 Workspot Control 컨트롤 추가

Workspot Control의 Azure AD 통합을 구성하려면 갤러리의 Workspot Control을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workspot Control을 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

3. 창의 맨 위에 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Workspot Control**을 입력하고 결과 패널에서 **Workspot Control**을 선택한 후 **추가**를 선택합니다.

     ![“갤러리에서 추가” 창](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 기반으로 Workspot Control에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Workspot Control의 관련 사용자 간에 연결을 설정해야 합니다.

Workspot Control에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 작업을 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 설정합니다.
2. [Workspot Control Single Sign-On 구성](#configure-workspot-control-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Workspot Control 테스트 사용자 만들기](#create-a-workspot-control-test-user) - 사용자의 Azure AD 표현과 연결된 Britta Simon의 해당 사용자를 Workspot Control에 만듭니다.
6. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Workspot Control에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. **Azure Portal**의 [Workspot Control](https://portal.azure.com/) 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 창에서 **SAML** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 방법 선택 창](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집**(연필) 아이콘을 선택하여 **기본 SAML 구성**에 액세스합니다.

    !["기본 SAML 구성"에서 강조 표시된 편집 아이콘](common/edit-urls.png)

4. IDP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Workspot Control 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    1. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. **회신 URL** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택합니다.

    ![Workspot Control 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 바꿉니다. 이러한 값을 얻으려면 [Workspot Control 클라이언트 지원 팀](mailto:support@workspot.com)에 문의하세요. 또는 Azure Portal의 **기본 SAML 구성** 섹션에 있는 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 사용 가능한 옵션에서 **인증서(Base64)** 를 다운로드합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서(Base64) 다운로드 링크](common/certificatebase64.png)

7. **Workspot Control 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    - **로그인 URL**

    - **Azure AD 식별자**

    - **로그아웃 URL**

### <a name="configure-workspot-control-single-sign-on"></a>Workspot Control Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Workspot Control에 보안 관리자로 로그인합니다.

2. 페이지의 위쪽에 있는 도구 모음에서 **설정**을 선택하고 **SAML**을 선택합니다.

    ![설치 옵션](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. **Security Assertion Markup Language 구성** 창에서 다음 단계를 수행합니다.
 
    ![Security Assertion Markup Language 구성 창](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. **엔터티 ID** 상자에 Azure Portal에서 복사한 **Azure AD 식별자**를 붙여넣습니다.

    1. **로그온 서비스 URL** 상자에 Azure Portal에서 복사한 **로그인 URL**을 붙여넣습니다.

    1. **로그아웃 서비스 URL** 상자에 Azure Portal에서 복사한 **로그아웃 URL**을 붙여넣습니다.

    1. **파일 업데이트**를 선택하여 Azure Portal에서 다운로드한 base-64 인코딩 인증서를 X.509 인증서에 업로드합니다.

    1. **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 창 위쪽에서 **새 사용자**를 선택합니다.

    ![“새 사용자” 단추](common/new-user.png)

3. 사용자의 속성에서 다음 단계를 수행합니다.

    ![사용자 속성 창](common/user-properties.png)

    1. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 필드에 **brittasimon@* yourcompanydomain.extension***을 입력합니다. 예를 들어, **BrittaSimon@contoso.<i></i>com**을 입력합니다.

    1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Workspot Control에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택하고 **Workspot Control**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workspot Control**을 선택합니다.

    ![애플리케이션 목록의 Workspot Control 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 선택합니다. 그런 후 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![“할당 추가” 창](common/add-assign-user.png)

5. **사용자 및 그룹** 창의 **사용자** 목록에서 **Britta Simon**을 선택합니다. 그런 다음 **선택**을 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창에서 목록에 있는 사용자에게 적절한 역할을 선택합니다. 그런 후 아래쪽에서 **선택**을 클릭합니다.

7. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-workspot-control-test-user"></a>Workspot Control 테스트 사용자 만들기

Azure AD 사용자가 Workspot Control에 로그인할 수 있도록 하려면 Workspot Control로 프로비저닝되어야 합니다. 프로비저닝은 수동으로 수행합니다.

**사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.**

1. 보안 관리자 권한으로 Workspot Control에 로그인합니다.

2. 페이지의 위쪽에 있는 도구 모음에서 **사용자**를 선택하고 **사용자 추가**를 선택합니다.

    !["사용자" 옵션](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. **새 사용자 추가** 창에서 다음 단계를 수행합니다.

    !["새 사용자 추가" 창](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. **이름** 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    1. **성** 텍스트 상자에 사용자의 성(예: **simon**)을 입력합니다.

    1. **메일** 상자에 사용자의 메일 주소(예: **Brittasimon@contoso.<i></i>com**)를 입력합니다.

    1. **역할** 드롭다운 목록에서 적절한 사용자 역할을 선택합니다.

    1. **그룹** 드롭다운 목록에서 적절한 사용자 그룹을 선택합니다.

    1. **사용자 추가**를 선택합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 *액세스 패널*을 통해 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **Workspot Control** 타일을 클릭하면 SSO를 설정한 Workspot Control에 자동으로 로그인됩니다. 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
