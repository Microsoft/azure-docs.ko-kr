---
title: '자습서: SAML SSO for Confluence by resolution GmbH와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAML SSO for Confluence by resolution GmbH 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 87c24cf61974c284772aae23e48ffc907792895b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543485"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>자습서: SAML SSO for Confluence by resolution GmbH와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAML SSO for Confluence by resolution GmbH를 통합하는 방법에 대해 알아봅니다.
SAML SSO for Confluence by resolution GmbH와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

* SAML SSO for Confluence by resolution GmbH에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SAML SSO for Confluence by resolution GmbH에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

SAML SSO for Confluence by resolution GmbH와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAML SSO for Confluence by resolution GmbH Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAML SSO for Confluence by resolution GmbH에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>갤러리에서 SAML SSO for Confluence by resolution GmbH 추가

Azure AD에 SAML SSO for Confluence by resolution GmbH를 통합하도록 구성하려면 갤러리에서 관리되는 SaaS 앱 목록으로 SAML SSO for Confluence by resolution GmbH를 추가해야 합니다.

**갤러리에서 SAML SSO for Confluence by resolution GmbH를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **SAML SSO for Confluence by resolution GmbH**를 입력하고, 결과 패널에서 **SAML SSO for Confluence by resolution GmbH**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 SAML SSO for Confluence by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 SAML SSO for Confluence by resolution GmbH에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SAML SSO for Confluence by resolution GmbH의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAML SSO for Confluence by resolution GmbH에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SAML SSO for Confluence by resolution GmbH Single Sign-On 구성](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SAML SSO for Confluence by resolution GmbH 테스트 사용자 만들기](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 SAML SSO for Confluence by resolution GmbH에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SAML SSO for Confluence by resolution GmbH에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAML SSO for Confluence by resolution GmbH** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SAML SSO for Confluence by resolution GmbH 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    다. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![SAML SSO for Confluence by resolution GmbH 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SAML SSO for Confluence by resolution GmbH 지원 팀](https://www.resolution.de/go/support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>SAML SSO for Confluence by resolution GmbH Single Sign-On 구성

1. 다른 웹 브라우저 창에서 **SAML SSO for Confluence by resolution GmbH 관리 포털**에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **추가 기능**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon1.png)

3. [관리자 액세스] 페이지로 리디렉션됩니다. 암호를 입력하고 **확인** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon2.png)

4. **ATLASSIAN MARKETPLACE** 탭 아래에서 **새 추가 기능 찾기**를 클릭합니다. 

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon.png)

5. **SAML Single Sign On(SSO) for Confluence**를 검색하고 **설치** 단추를 클릭하여 새 SAML 플러그 인을 설치합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon7.png)

6. 플러그 인 설치가 시작됩니다. **닫기**를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon8.png)

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon9.png)

7.  **관리**를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon10.png)
    
8. **구성**을 클릭하여 새 플러그 인을 구성합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon11.png)

9. 또한 새로운 이 플러그 인은 **사용자 및 보안** 탭 아래에도 있습니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon3.png)
    
10. **SAML SingleSignOn 플러그인 구성** 페이지에서 **새 IdP 추가** 단추를 클릭하여 ID 공급자의 설정을 구성합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon4.png)

11. **SAML ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. **Azure AD**를 IdP 유형으로 설정합니다.
    
    b. ID 공급자의 **이름**(예: Azure AD)을 추가합니다.
    
    다. ID 공급자의 **설명**(예: Azure AD)을 추가합니다.
    
    d. **다음**을 클릭합니다.
    
12. **ID 공급자 구성** 페이지에서 **다음** 단추를클릭합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon5b.png)

13. **SAML IdP 메타데이터 가져오기** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon5c.png)

    a. **파일 로드** 단추를 클릭하고 5단계에서 다운로드한 메타데이터 XML 파일을 선택합니다.

    b. **가져오기** 단추를 클릭합니다.
    
    다. 가져오기가 완료될 때까지 잠시 대기합니다.
    
    d. **다음** 단추를 클릭합니다.
    
14. **사용자 ID 특성 및 변환** 페이지에서 **다음** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. **사용자 만들기 및 업데이트** 페이지에서 **저장 및 다음**을 클릭하여 설정을 저장합니다.   
    
    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. **설정 테스트** 페이지에서 **테스트 건너뛰기 및 수동으로 구성**을 클릭하여 사용자 테스트를 한 번 건너뜁니다. 다음 섹션에서 수행되며 Azure Portal에서 몇 가지 설명이 필요합니다. 
    
    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. **테스트 건너뛰기의 의미...** 내용이 담긴 대화 상자가 표시되면 **확인**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/samlssoconfluence-tutorial/addon6c.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAML SSO for Confluence by resolution GmbH에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SAML SSO for Confluence by resolution GmbH**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SAML SSO for Confluence by resolution GmbH**를 입력하고 선택합니다.

    ![애플리케이션 목록의 SAML SSO for Confluence by resolution GmbH 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO for Confluence by resolution GmbH 테스트 사용자 만들기

Azure AD 사용자가 SAML SSO for Confluence by resolution GmbH에 로그인할 수 있게 하려면 해당 사용자를 SAML SSO for Confluence by resolution GmbH에 프로비전해야 합니다.  
SAML SSO for Confluence by resolution GmbH에서 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. SAML SSO for Confluence by resolution GmbH 회사 사이트에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **사용자 관리**를 클릭합니다.

    ![직원 추가](./media/samlssoconfluence-tutorial/user1.png) 

3. [사용자] 섹션에서 **사용자 추가** 탭을 클릭합니다. **"사용자 추가"** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/samlssoconfluence-tutorial/user2.png) 

    a. **사용자 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 이메일 주소를 입력합니다.

    b. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    다. **전자 메일** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에서 Britta Simon에 대한 암호를 입력합니다.

    e. **암호 확인**을 클릭하여 암호를 다시 입력합니다.
    
    f. **추가** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAML SSO for Confluence by resolution GmbH 타일을 클릭하면 SSO를 설정한 SAML SSO for Confluence by resolution GmbH에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

