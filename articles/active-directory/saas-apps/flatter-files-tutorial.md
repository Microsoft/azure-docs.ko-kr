---
title: '자습서: Flatter Files와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Flatter Files 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 7dd13df6222f6441b7edb2bd9a789c1f102168d7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554974"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>자습서: Flatter Files와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Flatter Files를 통합하는 방법에 대해 알아봅니다.
Flatter Files를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Flatter Files에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Flatter Files에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Flatter Files와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Flatter Files Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Flatter Files에서 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-flatter-files-from-the-gallery"></a>갤러리에서 Flatter Files 추가

Flatter Files의 Azure AD 통합을 구성하려면 갤러리의 Flatter Files를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Flatter Files를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Flatter Files**를 입력하고, 결과 패널에서 **Flatter Files**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Flatter Files](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Flatter Files에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Flatter Files의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Flatter Files에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Flatter Files Single Sign-On 구성](#configure-flatter-files-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Flatter Files 테스트 사용자 만들기](#create-flatter-files-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Flatter Files에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Flatter Files에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Flatter Files** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

    ![Flatter Files 도메인 및 URL Single Sign-On 정보](common/preintegrated.png)

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Flatter Files 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-flatter-files-single-sign-on"></a>Flatter Files Single Sign-On 구성

1. Flatter Files 애플리케이션에 관리자 권한으로 로그온합니다.

2. **대시보드**를 클릭합니다. 
   
    ![Single Sign-on 구성](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. **설정**을 클릭하고 **회사** 탭에서 다음 단계를 수행합니다. 
   
    ![Single Sign-on 구성](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **인증에 SAML 2.0 사용**을 선택합니다.
    
    b. **SAML 구성**을 클릭합니다.

4. **SAML 구성** 대화 상자에서 다음 단계를 수행합니다. 
   
    ![Single Sign-on 구성](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. **도메인** 텍스트 상자에서 등록된 도메인을 입력합니다.
   
   > [!NOTE]
   > 등록된 도메인이 아직 없는 경우 [support@flatterfiles.com](mailto:support@flatterfiles.com)을 참조하세요. 
    
    b. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 URL** 텍스트 상자에 붙여넣습니다.
   
    다.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **ID 공급자 인증서** 텍스트 상자에 붙여넣습니다.

    d. **업데이트**를 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Flatter Files에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션** 및 **Flatter Files**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Flatter Files**를 선택합니다.

    ![애플리케이션 목록의 Flatter Files 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-flatter-files-test-user"></a>Flatter Files 테스트 사용자 만들기

이 섹션은 Flatter Files에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

**Flatter Files에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. **Flatter Files** 회사 사이트에 관리자로 로그인합니다.

2. 왼쪽 탐색 창에서 **설정**과 사용자 **탭**을 차례로 클릭합니다.
   
    ![Flatter Files 사용자 만들기](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. **사용자 추가**를 클릭합니다. 

4. **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.
   
    ![Flatter Files 사용자 만들기](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.
   
    b. **성** 텍스트 상자에 **Simon**을 입력합니다. 
   
    다. **이메일 주소** 텍스트 상자에 Azure Portal에 있는 Britta의 이메일 주소를 입력합니다.
   
    d. **제출**을 클릭합니다.   


### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Flatter Files 타일을 클릭하면 SSO를 설정한 Flatter Files에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

