---
title: '자습서: Pega Systems와 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Pega Systems 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 3dd09770ef5f747f008090368c35b1f274374c5f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549078"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>자습서: Pega Systems와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pega Systems를 통합하는 방법에 대해 알아봅니다.

이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 Pega Systems에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Pega Systems에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Pega Systems와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.
* Pega Systems Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Pega Systems에서 SP 및 IdP 시작 SSO를 지원합니다.

## <a name="add-pega-systems-from-the-gallery"></a>갤러리에서 Pega Systems 추가

Pega Systems가 Azure AD에 통합되도록 설정하려면 갤러리의 Pega Systems를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **Pega Systems**를 입력합니다. 검색 결과에서 **Pega Systems**를 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Pega Systems에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 Pega Systems의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Pega Systems에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[Pega Systems Single Sign-On 구성](#configure-pega-systems-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[Pega Systems 테스트 사용자를 만듭니다](#create-a-pega-systems-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Pega Systems에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Pega Systems** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. IdP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![기본 SAML 구성 대화 상자](common/idp-intiated.png)

    1. **식별자** 상자에서 URL을 다음 패턴으로 입력합니다.

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. **회신 URL** 상자에서 URL을 다음 패턴으로 입력합니다.

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, 다음 단계를 수행합니다.

    ![Pega Systems 도메인 및 URL Single Sign-On 정보](common/both-advanced-urls.png)

    1. **로그온 URL** 상자에서 로그온 URL 값을 입력합니다.

    1. **릴레이 상태** 상자에서 URL을 `https://<customername>.pegacloud.io/prweb/sso` 패턴으로 입력합니다.

    > [!NOTE]
    > 여기서 제공하는 값은 자리 표시자입니다. 실제 식별자, 회신 URL, 로그온 URL 및 릴레이 상태 URL을 사용해야 합니다. 이 자습서의 뒷부분에서 설명하는 대로 Pega 애플리케이션에서 식별자 및 회신 URL 값을 가져올 수 있습니다. 릴레이 상태 값을 얻으려면 [Pega Systems 지원 팀](https://www.pega.com/contact-us)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. Pega Systems 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. SAML 어설션을 올바른 형식으로 가져오려면 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성을 보여 줍니다. **편집** 아이콘을 사용하여 **사용자 특성** 대화 상자를 엽니다.

    ![사용자 특성](common/edit-attribute.png)

7. 이전 스크린샷에 표시된 특성 외에도 Pega Systems 애플리케이션에는 SAML 응답에 다시 전달할 몇 가지 속성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 이러한 SAML 토큰 특성을 추가합니다.

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > 이러한 값은 조직에 따라 다릅니다. 적절한 값을 제공하세요.

    1. **새 클레임 추가**를 선택하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![새 클레임 추가 선택](common/new-save-attribute.png)

    ![[사용자 클레임 관리] 대화 상자](common/new-attribute-details.png)

    1. **이름** 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    1. **네임스페이스** 상자를 비워 둡니다.

    1. **원본**에서 **특성**을 선택합니다.

    1. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 선택합니다.

    1. **확인**을 선택합니다.

    1. **저장**을 선택합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **페더레이션 메타데이터 XML** 옆의 **다운로드** 링크를 선택하고, 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **Pega Systems 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-pega-systems-single-sign-on"></a>Pega Systems Single Sign-On 구성

1. **Pega Systems** 쪽에서 Single Sign-On을 구성하려면 다른 브라우저 창에서 관리자 계정을 사용하여 Pega 포털을 엽니다.

2. **만들기** > **SysAdmin** > **인증 서비스**를 차례로 선택합니다.

    ![인증 서비스 선택](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. **인증 서비스 만들기** 화면에서 다음 단계를 수행합니다.

    ![인증 서비스 만들기 화면](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. **형식** 목록에서 **SAML 2.0**을 선택합니다.

    1. **이름** 상자에서 이름(예: **Azure AD SSO**)을 입력합니다.

    1. **간단한 설명** 상자에서 설명을 입력합니다.  

    1. **만들기 및 열기**를 선택합니다.
    
4. **ID 공급자(IdP) 정보** 섹션에서 **IdP 메타데이터 가져오기**를 선택하고, Azure Portal에서 다운로드한 메타데이터 파일을 찾습니다. **제출**을 클릭하여 메타데이터를 로드합니다.

    ![ID 공급자(IdP) 정보 섹션](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    가져오기에서 IdP 데이터가 다음과 같이 채워집니다.

    ![가져온 IdP 데이터](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. **SP(서비스 공급자) 설정** 섹션에서 다음 단계를 수행합니다.

    ![서비스 공급자 설정](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. **엔터티 ID** 값을 복사하고, Azure Portal의 **기본 SAML 구성**에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    1. **ACS(Assertion Consumer Service) 위치** 값을 복사하고. Azure Portal의 **기본 SAML 구성**에 있는 **회신 URL** 상자에 붙여넣습니다.

    1. **요청 서명 사용 안 함**을 선택합니다.

7. **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 화면의 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 상자에 **brittasimon@\<yourcompanydomain>.\<extension>** 를 입력합니다. (예: BrittaSimon@contoso.com)

    다. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Pega Systems에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Pega Systems**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Pega Systems**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-pega-systems-test-user"></a>Pega Systems 테스트 사용자 만들기

다음으로, Pega Systems에서 Britta Simon이라는 사용자를 만들어야 합니다. [Pega Systems 지원 팀](https://www.pega.com/contact-us)과 협력하여 사용자를 만듭니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 Pega Systems 타일을 선택하면 SSO를 설정한 Pega Systems 인스턴스에 자동으로 로그인됩니다. 자세한 내용은 [내 앱 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)