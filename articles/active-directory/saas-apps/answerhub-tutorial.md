---
title: '자습서: AnswerHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 AnswerHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: d453cc5300dc658e4b33bb0591100deae5f34aef
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544743"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>자습서: AnswerHub와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AnswerHub를 통합하는 방법에 대해 알아봅니다.
AnswerHub와 Azure AD를 통합하면 이러한 이점이 제공됩니다.

* Azure AD를 사용하여 AnswerHub에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 AnswerHub에 자동으로 로그인(Single Sign-On)되게 할 수 있습니다.
* 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

AnswerHub와 Azure AD의 통합을 구성하려면 다음이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 사용할 수 있습니다.
* Single Sign-On을 사용하도록 설정된 AnswerHub 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* AnswerHub에서 SP 시작 SSO를 지원합니다.

## <a name="add-answerhub-from-the-gallery"></a>갤러리에서 AnswerHub 추가

AnswerHub와 Azure AD의 통합을 설정하려면 갤러리의 AnswerHub를 관리형 SaaS 앱에 추가해야 합니다.

**갤러리에서 AnswerHub를 추가하려면:**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창 위쪽에 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **AnswerHub**를 입력합니다. 결과 목록에서 **AnswerHub**를 선택한 다음, **추가**를 선택합니다.

     ![결과 목록의 AnswerHub](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 설정 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 통해 AnswerHub에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On의 경우 Azure AD 사용자와 AnswerHub의 해당 사용자 간에 연결을 설정해야 합니다.

AnswerHub에서 Azure AD Single Sign-On을 구성하고 테스트하려면 이러한 작업을 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 설정합니다.
2. [AnswerHub Single Sign-On 구성](#configure-answerhub-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. Britta Simon이라는 [Azure AD 테스트 사용자를 만듭니다](#create-an-azure-ad-test-user).
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. Azure AD 테스트 사용자에 해당하고 연결되는 AnswerHub 테스트 사용자를 만듭니다.
6. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 설정합니다.

**AnswerHub를 사용하여 Azure AD Single Sign-On을 구성하려면:**

1. [Azure Portal](https://portal.azure.com/)의 **AnswerHub** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 단추](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 방법 대화 상자](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 편집 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![SAML로 Single Sign-On 설정 페이지](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 완료합니다.

    ![기본 SAML 구성 섹션](common/sp-identifier.png)

    a. **로그온 URL** 상자에 이 패턴이 있는 URL `https://<company>.answerhub.com`을 입력합니다.

    b. **식별자(엔터티 ID)** 상자에 이 패턴이 있는 URL `https://<company>.answerhub.com`을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 값을 얻으려면 [AnswerHub 지원 팀](mailto:success@answerhub.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **인증서(Base64)** 옆의 **다운로드** 링크를 선택하고 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **AnswerHub 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

   이러한 URL을 복사할 수 있습니다.
    - 로그인 URL

    - Azure AD 식별자

    - 로그아웃 URL

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub Single Sign-On 구성

이 섹션에서는 AnswerHub의 Single Sign-On을 설정합니다.  

**AnswerHub Single Sign-On을 구성하려면:**

1. 다른 웹 브라우저 창에서 AnswerHub 회사 사이트에 관리자 권한으로 로그인합니다.

    > [!NOTE]
    > AnswerHub를 구성하는 데 도움이 필요하면 [AnswerHub 지원 팀](mailto:success@answerhub.com.)에 문의하세요.

2. **관리**로 이동합니다.

3. **사용자 및 그룹** 탭의 왼쪽 창에 있는 **소셜 설정** 섹션에서 **SAML 설정**을 선택합니다.

4. **IDP 구성** 탭에서 다음 단계를 완료합니다.

    ![사용자 및 그룹 탭](./media/answerhub-tutorial/ic785172.png "SAML 설정")  
  
    a. **IDP 로그인 URL** 상자에 Azure Portal에서 복사한 **로그인 URL**을 붙여넣습니다.
  
    b. **IDP 로그아웃 URL** 상자에 Azure Portal에서 복사한 **로그아웃 URL**을 붙여넣습니다.

    다. Azure Portal의 **사용자 특성** 섹션에서 선택한 **식별자** 값을 **IDP 이름 식별자 형식** 상자에 입력합니다.
  
    d. **키 및 인증서**를 선택합니다.

5. **키 및 인증서** 섹션에서 다음 단계를 완료합니다.

    ![키 및 인증서 섹션](./media/answerhub-tutorial/ic785173.png "키 및 인증서")  

    a. 메모장의 Azure Portal에서 다운로드한 Base64로 인코딩된 인증서를 열고 콘텐츠를 복사한 다음, **IDP 공유 키(x509 형식)** 상자에 붙여넣습니다.
  
    b. **저장**을 선택합니다.

6. **IDP 구성** 탭에서 다시 **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

**Azure AD 테스트 사용자를 만들려면:**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure Active Directory, 사용자, 모든 사용자 선택](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 완료합니다.

    ![사용자 속성](common/user-properties.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 상자에 **brittasimon\@<yourcompanydomain.extension>** 을 입력합니다.  
    BrittaSimon@contoso.com)을 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 사용자 Britta Simon에게 AnswerHub에 대한 액세스 권한을 부여하여 Azure AD Single Sign-On을 사용하도록 설정합니다.

**Azure AD 테스트 사용자를 할당하려면:**

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **AnswerHub**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **AnswerHub**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **Britta Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.

6. SAML 어설션의 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 

7. 화면 아래쪽에 있는 **선택** 단추를 선택합니다.

8. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-an-answerhub-test-user"></a>AnswerHub 테스트 사용자 만들기

Azure AD 사용자가 AnswerHub에 로그인하도록 설정하려면 해당 사용자를 AnswerHub에 추가해야 합니다. AnswerHub에서 이 작업은 수동으로 수행됩니다.

**사용자 계정을 설정하려면:**

1. **AnswerHub** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **관리**로 이동합니다.

3. **사용자 및 그룹** 탭을 선택합니다.

4. 왼쪽 창의 **사용자 관리** 섹션에서 **사용자 만들기 또는 가져오기**를 선택한 다음, **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹 탭](./media/answerhub-tutorial/ic785175.png "사용자 및 그룹")

5. 추가하려는 유효한 Azure AD 계정의 **이메일 주소**, **사용자 이름** 및 **암호**를 적절한 상자에 입력한 다음, **저장**을 선택합니다.

> [!NOTE]
> 기타 모든 사용자 계정 생성 도구 또는 AnswerHub에서 제공된 API를 사용하여 Azure AD 사용자 계정을 설정할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AnswerHub 타일을 선택하면 SSO를 설정한 AnswerHub에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

