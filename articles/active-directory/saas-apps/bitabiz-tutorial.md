---
title: '자습서: BitaBIZ와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 BitaBIZ 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 397197c2ab3ba4f135912eab800f1abd7ab73a0f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531084"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>자습서: BitaBIZ와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 BitaBIZ를 통합하는 방법에 대해 알아봅니다.
BitaBIZ를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* BitaBIZ에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 BitaBIZ에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

BitaBIZ와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* BitaBIZ Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* BitaBIZ에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-bitabiz-from-the-gallery"></a>갤러리에서 BitaBIZ 추가

BitaBIZ와 Azure AD의 통합을 구성하려면 갤러리의 BitaBIZ를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 BitaBIZ를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **BitaBIZ**를 입력하고, 결과 패널에서 **BitaBIZ**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 BitaBIZ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 BitaBIZ에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 BitaBIZ의 관련 사용자 간에 연결 관계를 설정해야 합니다.

BitaBIZ에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[BitaBIZ Single Sign-On 구성](#configure-bitabiz-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[BitaBIZ 테스트 사용자 만들기](#create-bitabiz-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 해당 사용자를 BitaBIZ에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

BitaBIZ에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **BitaBIZ** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![BitaBIZ 도메인 및 URL Single Sign-On 정보](common/idp-identifier.png)

    **식별자** 텍스트 상자에 `https://www.bitabiz.com/<instanceId>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 위 URL의 값은 데모용입니다. 자습서의 뒷부분에서 설명하는 실제 식별자로 값을 업데이트합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    ![이미지](common/both-preintegrated-signon.png)

    **로그온 URL** 텍스트 상자에 `https://www.bitabiz.com/dashboard` URL을 입력합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **BitaBIZ 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-bitabiz-single-sign-on"></a>BitaBIZ Single Sign-On 구성

1. 다른 웹 브라우저 창에서 BitaBIZ 테넌트에 관리자 권한으로 로그인합니다.

2. **SETUP ADMIN(설치 관리자)** 을 클릭합니다.

    ![BitaBIZ 구성](./media/bitabiz-tutorial/settings1.png)

3. **값 추가** 섹션 아래에서 **Microsoft 통합**을 클릭합니다.

    ![BitaBIZ 구성](./media/bitabiz-tutorial/settings2.png)

4. **Microsoft Azure AD(Single Sign-On 사용)** 섹션까지 아래로 스크롤하고 다음 단계를 수행합니다.

    ![BitaBIZ 구성](./media/bitabiz-tutorial/settings3.png)

    a. **엔터티 ID(Azure AD의 "식별자")** 텍스트 상자의 값을 복사하고, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다. 

    b. Azure Portal에서 복사한 **로그인 URL**을 **Azure AD Single Sign-On 서비스 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **Azure AD 식별자**를 **Azure AD SAML 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    d. 다운로드한 **인증서(Base64)** 파일을 메모장에서 열고, 내용을 클립보드에 복사한 다음, **Azure AD 서명 인증서(Base64 인코딩)** 텍스트 상자에 붙여넣습니다.

    e. **도메인 이름** 텍스트 상자에서 비즈니스 전자 메일 도메인 이름인 mycompany.com을 추가하여 이 전자 메일 도메인이 있는 회사의 사용자에게 SSO를 할당합니다(필수 항목이 아님).

    f. BitaBIZ 계정을 **SSO 사용**으로 표시합니다.

    g. **Azure AD 구성 저장**을 클릭하여 SSO 구성을 저장하고 활성화합니다.

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
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 BitaBIZ에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **BitaBIZ**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **BitaBIZ**를 선택합니다.

    ![애플리케이션 목록의 BitaBIZ 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-bitabiz-test-user"></a>BitaBIZ 테스트 사용자 만들기

Azure AD 사용자가 BitaBIZ에 로그인할 수 있게 하려면 BitaBIZ에 프로비전해야 합니다.  
BitaBIZ의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. BitaBIZ 회사 사이트에 관리자 권한으로 로그인합니다.

2. **SETUP ADMIN(설치 관리자)** 을 클릭합니다.

    ![BitaBIZ 사용자 추가](./media/bitabiz-tutorial/settings1.png)

3. **조직**  섹션 아래에서 **사용자 추가**를 클릭합니다.

    ![BitaBIZ 사용자 추가](./media/bitabiz-tutorial/user1.png)

4. **새 직원 추가**를 클릭합니다.

    ![BitaBIZ 사용자 추가](./media/bitabiz-tutorial/user2.png)

5. **새 직원 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![BitaBIZ 사용자 추가](./media/bitabiz-tutorial/user3.png)

    a. **이름** 텍스트 상자에서 사용자의 이름(예: Britta)을 입력합니다.

    b. **성** 텍스트 상자에서 사용자의 성(예: Simon)을 입력합니다.

    다. **전자 메일** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **고용 날짜**에서 날짜를 선택합니다.

    e. 사용자에 대해 설정할 수 있는 필수가 아닌 다른 사용자 속성이 있습니다. 자세한 내용은 [직원 설정 문서(영문)](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)를 참조하세요.

    f. **직원 저장**을 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 BitaBIZ 타일을 클릭하면 SSO를 설정한 BitaBIZ에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
