---
title: '자습서: Work.com과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Work.com 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 5fe8697327203a84f91b42e8742db75150b6cb19
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88541457"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>자습서: Work.com과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Work.com을 통합하는 방법에 대해 알아봅니다.
Work.com을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Work.com에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Work.com에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Work.com과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Work.com Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Work.com은 **SP** 시작 SSO를 지원합니다.

## <a name="adding-workcom-from-the-gallery"></a>갤러리에서 Work.com 추가

Work.com의 Azure AD 통합을 구성하려면 갤러리의 Work.com을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Work.com을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Work.com**을 입력하고 결과 패널에서 **Work.com**을 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Work.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Work.com에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Work.com의 관련 사용자 간에 연결 관계가 설정되어 있어야 합니다.

Work.com에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Work.com Single Sign-On 구성](#configure-workcom-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Work.com 테스트 사용자 만들기](#create-workcom-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Work.com에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

>[!NOTE]
>Single Sign-On을 구성하려면 사용자 할당 도메인 이름을 Work.com로 설정해야 합니다. 최소한 한 개의 도메인 이름을 정의하고, 도메인 이름을 테스트 한 후 전체 조직에 배포해야 합니다.

Work.com에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Work.com** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Work.com 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    **로그인 URL** 텍스트 상자에서 `http://<companyname>.my.salesforce.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Work.com 고객 지원 팀](https://help.salesforce.com/articleView?id=000159855&type=3)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Work.com 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-workcom-single-sign-on"></a>Work.com Single Sign-On 구성

1. Work.com 테넌트에 관리자로 로그인합니다.

2. **설정**으로 이동합니다.
   
    ![설치](./media/work-com-tutorial/ic794108.png "설치 프로그램")

3. 왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
   
    ![내 도메인](./media/work-com-tutorial/ic767825.png "내 도메인")

4. 도메인이 올바르게 설정되었는지 확인하려면 “**Step 4 Deployed to Users**(4단계 사용자에게 배포)”에 있는지 확인하고 “**My Domain Settings**(내 도메인 설정)”을 검토합니다.
   
    ![사용자에게 배포된 도메인](./media/work-com-tutorial/ic784377.png "사용자에게 배포된 도메인")

5. Work.com 테넌트에 로그인합니다.

6. **설정**으로 이동합니다.
    
    ![설치](./media/work-com-tutorial/ic794108.png "설치 프로그램")

7. **보안 제어** 메뉴를 확장한 다음 **Single Sign-On 설정**을 클릭합니다.
    
    ![Single Sign-On 설정](./media/work-com-tutorial/ic794113.png "Single Sign-On 설정")

8. **Single Sign-On 설정** 대화 상자 페이지에서 다음 단계를 수행합니다.
    
    ![SAML 사용](./media/work-com-tutorial/ic781026.png "SAML 사용")
    
    a. **SAML 사용**을 선택합니다.
    
    b. **새로 만들기**를 클릭합니다.

9. **SAML Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.
    
    ![SAML Single Sign-On 설정](./media/work-com-tutorial/ic794114.png "SAML Single Sign-On 설정")
    
    a. **이름** 텍스트 상자에 구성할 이름을 입력합니다.  
       
    > [!NOTE]
    > **이름**에 값을 제공하면 **API 이름** 텍스트 상자가 자동으로 채워집니다.
    
    b. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.
    
    다. Azure Portal에서 다운로드한 인증서를 업로드하려면 **찾아보기**를 클릭합니다.
    
    d. **엔터티 ID** 텍스트 상자에 `https://salesforce-work.com`을 입력합니다.
    
    e. **SAML ID 유형**으로 **사용자 개체에서 페더레이션 ID를 포함하는 어설션**을 선택합니다.
    
    f. **SAML ID 위치**에서 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
    
    g. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다.

    h. Azure Portal에서 복사한 **로그아웃 URL** 값을 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    
    i. **서비스 공급자가 시작한 요청 바인딩**에서 **HTTP Post**를 선택합니다.
    
    j. **저장**을 클릭합니다.

10. Work.com 클래식 포털의 왼쪽 탐색창에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
    
    ![내 도메인](./media/work-com-tutorial/ic794115.png "내 도메인")

11. **내 도메인** 페이지의 **로그인 페이지 브랜딩** 섹션에서 **편집**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/work-com-tutorial/ic767826.png "로그인 페이지 브랜딩")

12. **로그인 페이지 브랜딩** 페이지의 **인증 서비스** 섹션에 **SAML SSO 설정** 이름이 표시됩니다. 이름을 선택하고 **저장**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/work-com-tutorial/ic784366.png "로그인 페이지 브랜딩")

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Work.com에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Work.com**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Work.com**을 선택합니다.

    ![애플리케이션 목록의 Work.com 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-workcom-test-user"></a>Work.com 테스트 사용자 만들기

Azure Active Directory 사용자가 로그인하려면, Work.com에 프로비전되어야 합니다. Work.com의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면

1. Work.com 회사 사이트에 관리자 권한으로 로그인합니다.

2. **설정**으로 이동합니다.
   
    ![설치](./media/work-com-tutorial/IC794108.png "설치 프로그램")

3. **사용자 관리 \> 사용자**로 이동합니다.
   
    ![사용자 관리](./media/work-com-tutorial/IC784369.png "사용자 관리")

4. **새 사용자**를 클릭합니다.
   
    ![모든 사용자](./media/work-com-tutorial/IC794117.png "모든 사용자")

5. 사용자 편집 섹션에서, 관련 텍스트 상자에 프로비전하려는 유효한 Azure AD 계정의 특성에 다음 단계를 수행합니다.
   
    ![사용자 편집](./media/work-com-tutorial/ic794118.png "사용자 편집")
   
    a. **이름** 텍스트 상자에 사용자의 **이름**을 **Britta**로 입력합니다.
    
    b. **성** 텍스트 상자에 사용자의 **성**을 **Simon**으로 입력합니다.
    
    다. **별칭** 텍스트 상자에 사용자의 **이름**을 **BrittaS**로 입력합니다.
    
    d. **이메일** 텍스트 상자에 사용자 Brittasimon@contoso.com의 **이메일 주소**를 입력합니다.
    
    e. **User Name**(사용자 이름) 텍스트 상자에 사용자의 사용자 이름(예: Brittasimon@contoso.com)을 입력합니다.
    
    f. **Nick Name**(애칭) 텍스트 상자에 사용자의 **애칭** **Simon**을 입력합니다.
    
    g. **역할**, **사용자 라이선스** 및 **프로필**을 차례로 선택합니다.
    
    h. **저장**을 클릭합니다.  
      
    > [!NOTE]
    > Azure AD 계정 보유자에게 계정 확인 링크가 포함한 전자 메일이 발송되며, 확인을 마치면 계정이 활성화됩니다.
    > 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Work.com 타일을 클릭하면 SSO가 설정된 Work.com에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

