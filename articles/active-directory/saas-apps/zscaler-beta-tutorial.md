---
title: '자습서: Azure Active Directory와 Zscaler Beta 통합 | Microsoft Docs'
description: Azure Active Directory와 Zscaler Beta 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546018"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>자습서: Azure Active Directory와 Zscaler Beta 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zscaler Beta를 통합하는 방법에 대해 알아봅니다.
Azure AD와 Zscaler Beta를 통합하는 경우 다음을 수행할 수 있습니다.

* Zscaler Beta에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Zscaler Beta에 자동으로 로그인되도록 허용합니다. 이 액세스 제어를 SSO(Single Sign-On)라고 합니다.
* Azure Portal을 사용하여 중앙의 한 위치에서 계정을 관리합니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Zscaler Beta와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On을 사용하는 Zscaler Beta 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Zscaler Beta는 SP 시작 SSO를 지원합니다.
* Zscaler Beta는 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Azure Marketplace에서 Zscaler Beta 추가

Zscaler Beta가 Azure AD에 통합되도록 구성하려면 Azure Marketplace의 Zscaler Beta를 관리형 SaaS 앱 목록에 추가합니다.

Azure Marketplace에서 Zscaler Beta를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zscaler Beta**를 입력합니다. 결과 패널에서 **Zscaler Beta**를 선택한 다음, **추가**를 선택합니다.

     ![결과 목록의 Zscaler Beta](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Zscaler Beta에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Zscaler Beta의 관련 사용자 간에 연결 관계를 설정합니다.

Zscaler Beta에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

- [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
- [Zscaler Beta Single Sign-On 구성](#configure-zscaler-beta-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
- [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
- [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
- [Zscaler Beta 테스트 사용자 만들기](#create-a-zscaler-beta-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Beta에 만듭니다.
- [Single Sign-on 테스트](#test-single-sign-on) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Zscaler Beta에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zscaler Beta** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집**을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Zscaler Beta 도메인 및 URL Single Sign-On 정보](common/sp-intiated.png)

    - **로그온 URL** 상자에 사용자가 Zscaler Beta 애플리케이션에 로그인할 때 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL 값으로 업데이트합니다. 이 값을 얻으려면 [Zscaler Beta 클라이언트 지원 팀](https://www.zscaler.com/company/contact)에 문의하세요.

5. Zscaler Beta 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집**을 선택하여 **사용자 특성** 대화 상자를 엽니다.

    ![사용자 특성 대화 상자](common/edit-attribute.png)

6. Zscaler Beta 애플리케이션을 사용하려면 몇 가지 추가 특성이 SAML 응답을 통해 다시 전달되어야 합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표처럼 SAML 토큰 특성을 추가합니다.
    
    | 속성 | 원본 특성 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. **새 클레임 추가**를 선택하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![사용자 클레임 대화 상자](common/new-save-attribute.png)

    ![[사용자 클레임 관리] 대화 상자](common/new-attribute-details.png)

    b. **이름** 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 상자를 비워 둡니다.

    d. **원본**에서 **특성**을 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 선택합니다.

    g. **저장**을 선택합니다.

    > [!NOTE]
    > Azure AD에서 역할을 구성하는 방법을 알아보려면 [역할 클레임 구성](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)을 참조하세요.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하여 **인증서(Base64)** 를 다운로드합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Zscaler Beta 설정** 섹션에서 요구 사항에 따라 필요한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    - 로그인 URL
    - Azure AD 식별자
    - 로그아웃 URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Zscaler Beta Single Sign-On 구성

1. Zscaler Beta 내에서 구성을 자동화하려면 **확장 설치**를 선택하여 **내 앱 보안 로그인 브라우저 확장**을 설치합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가하면 **Zscaler Beta 설정**에서 Zscaler Beta 애플리케이션으로 안내합니다. 여기서 관리자 자격 증명을 입력하여 Zscaler Beta에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Zscaler Beta를 수동으로 설정하려면 새 웹 브라우저 창을 엽니다. Zscaler Beta 회사 사이트에 관리자로 로그인하고, 다음 단계를 수행합니다.

4. **관리** > **인증** > **인증 설정**으로 이동하여 다음 단계를 수행합니다.
   
    ![관리](./media/zscaler-beta-tutorial/ic800206.png "관리")

    a. **인증 형식**에서 **SAML**을 선택합니다.

    b. **SAML 구성**을 선택합니다.

5. **SAML 편집** 창에서 다음 단계를 수행합니다. 
            
    ![사용자 및 인증 관리](./media/zscaler-beta-tutorial/ic800208.png "사용자 & 인증 관리")
    
    a. **SAML 포털 URL** 상자에 Azure Portal에서 복사한 **로그인 URL**을 붙여넣습니다.

    b. **로그인 이름 특성** 상자에 **NameID**를 입력합니다.

    다. **공용 SSL 인증서** 상자에서 **업로드**를 선택하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 업로드합니다.

    d. **SAML 자동 프로비저닝 사용**을 선택/해제합니다.

    e. displayName 특성에 SAML 자동 프로비저닝을 사용하려면 **사용자 표시 이름 특성** 상자에 **displayName**을 입력합니다.

    f. memberOf 특성에 SAML 자동 프로비저닝을 사용하려면 **그룹 이름 특성** 상자에 **memberOf**를 입력합니다.

    g. 부서 특성에 SAML 자동 프로비저닝을 사용하려면 **부서 이름 특성** 상자에 **department**를 입력합니다.

    h. **저장**을 선택합니다.

6. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![활성화 메뉴 및 활성화 단추](./media/zscaler-beta-tutorial/ic800207.png)

    a. 왼쪽 아래에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화**를 선택합니다.

## <a name="configure-proxy-settings"></a>프록시 설정 구성
Internet Explorer에서 프록시 설정을 구성하려면 다음 단계를 수행합니다.

1. **Internet Explorer**를 시작합니다.

2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다. 
    
     ![인터넷 옵션 대화 상자](./media/zscaler-beta-tutorial/ic769492.png "인터넷 옵션")

3. **연결** 탭을 선택합니다. 
  
     ![연결 탭](./media/zscaler-beta-tutorial/ic769493.png "Connections")

4. **LAN 설정**을 선택하여 **LAN 설정** 대화 상자를 엽니다.

5. **프록시 서버** 섹션에서 다음 단계를 수행합니다. 
   
    ![프록시 서버 섹션](./media/zscaler-beta-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용** 확인란을 선택합니다.

    b. **주소 상자**에 **gateway.Zscaler Beta.net**을 입력합니다.

    다. **포트** 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버** 확인란을 선택합니다.

    e. **확인**을 선택하여 **LAN 설정** 대화 상자를 닫습니다.

6. **확인**을 선택하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 모든 사용자 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 상자에 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예제는 BrittaSimon@contoso.com입니다.

    다. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시되는 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler Beta에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Zscaler Beta**를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler Beta**를 입력하고 선택합니다.

    ![애플리케이션 목록의 Zscaler Beta 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 단추](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 목록에서 **Britta Simon** 같은 사용자를 선택합니다. 화면 하단에서 **선택**을 누릅니다.

    ![사용자 및 그룹 대화 상자](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. **역할 선택** 대화 상자의 목록에서 적절한 사용자 역할을 선택합니다. 화면 하단에서 **선택**을 누릅니다.

    ![역할 선택 대화 상자](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

    ![할당 추가 대화 상자](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler Beta 테스트 사용자 만들기

이 섹션에서는 Zscaler Beta에서 Britta Simon이라는 사용자를 만듭니다. Zscaler Beta는 기본적으로 사용하도록 설정되는 **Just-In-Time 사용자 프로비전**을 지원합니다. 이 섹션에서는 여러분이 할 일이 없습니다. Zscaler Beta에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들려면 [Zscaler Beta 지원 팀](https://www.zscaler.com/company/contact)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler Beta 타일을 선택하면 SSO를 설정한 Zscaler Beta에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

