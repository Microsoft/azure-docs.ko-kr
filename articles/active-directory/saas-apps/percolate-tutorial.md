---
title: '자습서: Percolate와 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Percolate 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: deb64aa0c344e818b5fd85ca1a161293fd35d6f6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554002"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>자습서: Percolate와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Percolate를 통합하는 방법에 대해 알아봅니다.

이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 Percolate에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Percolate에 자동으로 로그인(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Percolate와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Percolate Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Percolate에서 SP 및 IdP 시작 SSO를 지원합니다.

## <a name="add-percolate-from-the-gallery"></a>갤러리에서 Percolate 추가

Percolate의 Azure AD 통합을 구성하려면 갤러리의 Percolate를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **Percolate**를 입력합니다. 검색 결과에서 **Percolate**를 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Percolate에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 Percolate의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Percolate에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[Percolate Single Sign-On 구성](#configure-percolate-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[Percolate 테스트 사용자를 만듭니다](#create-a-percolate-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Percolate에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Percolate** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. IdP 시작 모드에서 애플리케이션을 구성하기 위해 **기본 SAML 구성** 대화 상자에서 어떠한 작업도 수행할 필요가 없습니다. 앱이 이미 Azure와 통합되어 있습니다.

    ![Percolate 도메인 및 URL Single Sign-On 정보](common/preintegrated.png)

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, **로그온 URL** 상자에서 **https://percolate.com/app/login** 을 입력합니다.

   ![Percolate 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)
6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **복사** 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL**을 복사합니다. 이 URL을 저장합니다.

    ![앱 페더레이션 메타데이터 URL 복사](common/copy-metadataurl.png)

7. **Percolate 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-percolate-single-sign-on"></a>Percolate Single Sign-On 구성

1. 새 웹 브라우저 창에서 Percolate에 관리자 권한으로 로그인합니다.

2. 홈 페이지의 왼쪽에서 **설정**을 선택합니다.
    
    ![설정 선택](./media/percolate-tutorial/configure01.png)

3. 왼쪽 창의 **조직** 아래에서 **SSO**를 선택합니다.

    ![조직 아래에서 SSO 선택](./media/percolate-tutorial/configure02.png)

    1. Azure Portal에서 복사한 **로그인 URL**을 **로그인 URL** 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 상자에 붙여넣습니다.

    1. Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 메모장에서 엽니다. 해당 콘텐츠를 복사하여 **x509 인증서** 상자에 붙여넣습니다.

    1. **이메일 특성** 상자에서 **emailaddress**를 입력합니다.

    1. **ID 공급자 메타데이터 URL** 상자는 선택적 필드입니다. Azure Portal에서 **앱 페더레이션 메타데이터 URL**을 복사한 경우 이 상자에 붙여넣을 수 있습니다.

    1. **AuthNRequests에 서명해야 합니까?** 목록에서 **아니요**를 선택합니다.

    1. **SSO 자동 프로비저닝 사용** 목록에서 **아니요**를 선택합니다.

    1. **저장**을 선택합니다.

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

이 섹션에서는 Azure AD Single Sign-On을 사용할 수 있도록 Britta Simon에게 Percolate에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Percolate**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Percolate**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-percolate-test-user"></a>Percolate 테스트 사용자 만들기

Azure AD 사용자가 Percolate에 로그인할 수 있도록 설정하려면 해당 사용자를 Percolate에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. Percolate에 관리자 권한으로 로그인합니다.

2. 왼쪽 창의 **조직** 아래에서 **사용자**를 선택합니다. **새 사용자**를 선택합니다.

    ![새 사용자 선택](./media/percolate-tutorial/configure03.png)

3. **사용자 만들기** 페이지에서 다음 단계를 수행합니다.

    ![사용자 만들기 페이지](./media/percolate-tutorial/configure04.png)

    1. **이메일** 상자에서 사용자의 이메일 주소를 입력합니다. brittasimon@contoso.com)을 입력합니다.

    1. **전체 이름** 상자에서 사용자의 이름을 입력합니다. 예를 들어 **Brittasimon**입니다.

    1. **사용자 만들기**를 선택합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 Percolate 타일을 선택하면 SSO를 설정한 Percolate 인스턴스에 자동으로 로그인됩니다. 자세한 내용은 [내 앱 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)