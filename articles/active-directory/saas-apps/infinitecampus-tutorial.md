---
title: '자습서: Infinite Campus와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Infinite Campus 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: ca154caf67d8dd715ad1341e9fe3c6cfde20fde0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553090"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>자습서: Infinite Campus와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Infinite Campus를 통합하는 방법에 대해 알아봅니다.
Infinite Campus를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Infinite Campus에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Infinite Campus에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Infinite Campus와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On이 설정된 Infinite Campus 구독
* 구성을 완료하려면 최소한 Azure Active Directory 관리자이고, "SIS(Student Information System)"의 캠퍼스 제품 보안 역할을 갖고 있어야 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Infinite Campus는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-infinite-campus-from-the-gallery"></a>갤러리에서 Infinite Campus 추가

Infinite Campus의 Azure AD 통합을 구성하려면 갤러리의 Infinite Campus를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Infinite Campus를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위에 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Infinite Campus**를 입력하고 결과 패널에서 **Infinite Campus**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Infinite Campus](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Infinite Campus에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Infinite Campus의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Infinite Campus에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Infinite Campus Single Sign-On 구성](#configure-infinite-campus-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Infinite Campus 테스트 사용자 만들기](#create-infinite-campus-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Infinite Campus에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Infinite Campus에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Infinite Campus** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. 기본 SAML 구성 섹션에서 다음 단계를 수행합니다(도메인은 호스팅 모델에 따라 달라지지만, **FULLY-QUALIFIED-DOMAIN** 값은 설치된 Infinite Campus와 일치해야 함).

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. **식별자** 텍스트 상자에서 `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Infinite Campus Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Infinite Campus에 보안 관리자 권한으로 로그인합니다.

2. 왼쪽 메뉴에서 **시스템 관리**를 클릭합니다.

    ![관리자](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. **사용자 보안** > **SAML 관리** > **SSO 서비스 공급자 구성**으로 이동합니다.

    ![saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. **SSO 서비스 공급자 구성** 페이지에서 다음 단계를 수행합니다.

    ![sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. **SAML Single Sign-On 사용**을 선택합니다.

    b. **이름**을 포함하도록 **선택적 특성 이름**을 편집합니다.

    다. **IDP(ID 공급 기업) 서버 데이터 검색 옵션 선택** 섹션에서 **메타데이터 URL**을 선택하고, Azure Portal에서 복사한 **앱 페더레이션 메타데이터 Url** 값을 상자에 붙여넣은 다음, **동기화**를 클릭합니다.

    d. **동기화**를 클릭하면 값이 **SSO 서비스 공급자 구성** 페이지에 자동으로 입력됩니다. 이러한 값이 위의 4단계에서 본 값과 일치하는지 확인할 수 있습니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다. BrittaSimon@contoso.com)을 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

> [!NOTE]
> 모든 Azure 사용자가 Infinite Campus에 Single Sign-On 되도록 만들고 Infinite Campus 내부 권한 시스템을 사용하여 액세스를 제어하려면 애플리케이션의 **사용자 할당 필요** 속성을 [아니요]로 설정하고 다음 단계를 건너뜁니다.

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Infinite Campus에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Infinite Campus**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Infinite Campus**를 선택합니다.

    ![애플리케이션 목록의 Infinite Campus 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-infinite-campus-test-user"></a>Infinite Campus 테스트 사용자 만들기

Infinite Campus는 인구 통계 기반 아키텍처입니다. Infinite Campus 플랫폼에 사용자를 추가하려면 [Infinite Campus 지원 팀](mailto:sales@infinitecampus.com)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Infinite Campus 타일을 클릭하면 SSO를 설정한 Infinite Campus에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
