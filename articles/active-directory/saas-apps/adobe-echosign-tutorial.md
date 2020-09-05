---
title: '자습서: Adobe Sign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adobe Sign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: b7d1ea6c1ee21970a598855471284130160e1d65
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537933"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>자습서: Adobe Sign과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Sign을 통합하는 방법에 대해 알아봅니다.
Adobe Sign과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

* Adobe Sign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Adobe Sign에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Adobe Sign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Adobe Sign Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Adobe Sign에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-adobe-sign-from-the-gallery"></a>갤러리에서 Adobe Sign 추가

Adobe Sign의 Azure AD 통합을 구성하려면 갤러리의 Adobe Sign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adobe Sign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Adobe Sign**을 입력하고 결과 패널에서 **Adobe Sign**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Adobe Sign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Adobe Sign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Adobe Sign의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Adobe Sign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Adobe Sign Single Sign-On 구성](#configure-adobe-sign-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Adobe Sign 테스트 사용자 만들기](#create-adobe-sign-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Adobe Sign에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Adobe Sign에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Adobe Sign** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Adobe Sign 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<companyname>.echosign.com/` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<companyname>.echosign.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Adobe Sign 클라이언트 지원팀](https://helpx.adobe.com/in/contact/support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Adobe Sign 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-adobe-sign-single-sign-on"></a>Adobe Sign Single Sign-On 구성

1. 구성하기 전에 [Adobe Sign 클라이언트 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 문의하여 Adobe Sign 허용 목록에 도메인을 추가하세요. 도메인을 추가하는 방법을 다음과 같습니다.

    a. [Adobe Sign 클라이언트 지원 팀](https://helpx.adobe.com/in/contact/support.html)에서 임의로 생성된 토큰을 보냅니다. 사용자의 도메인에 대한 토큰은 다음과 같습니다. **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. DNS 텍스트 레코드에 확인 토큰을 게시하고 [Adobe Sign 클라이언트 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 알려야 합니다.
    
    > [!NOTE]
    > 며칠 또는 그 이상 걸릴 수 있습니다. DNS 전파 지연은 DNS에 게시된 값이 한 시간 이상 동안 보이지 않을 수도 있음을 의미합니다. IT 관리자는 이 토큰을 DNS 텍스트 레코드에 게시하는 방법을 알아야 합니다.
    
    다. 토큰이 게시된 후 지원 티켓을 통해 [Adobe Sign 클라이언트 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 알리면 해당 팀이 도메인의 유효성을 검사한 후 사용자 계정에 추가합니다.
    
    d. 일반적으로 DNS 레코드에 토큰을 게시하는 방법은 다음과 같습니다.

    * 도메인 계정에 로그인합니다.
    * DNS 레코드를 업데이트할 페이지를 찾습니다. 이 페이지는 DNS 관리, 서버 관리자 이름 또는 고급 설정 페이지일 수 있습니다.
    * 도메인에 대한 TXT 레코드를 찾습니다.
    * Adobe에서 제공한 전체 토큰 값으로 TXT 레코드를 추가합니다.
    * 변경 내용을 저장합니다.

1. 다른 웹 브라우저 창에서 Adobe Sign 회사 사이트에 관리자로 로그인합니다.

1. SAML 메뉴에서 **계정 설정** > **SAML 설정**을 선택합니다.
   
    ![Adobe Sign SAML 설정 페이지의 스크린샷](./media/adobe-echosign-tutorial/ic789520.png "계정")

1. **SAML 설정** 섹션에서 다음 단계를 수행합니다.
  
   ![SAML 설정의 스크린샷](./media/adobe-echosign-tutorial/ic789521.png "SAML 설정")
   
   ![SAML 설정의 스크린샷](./media/adobe-echosign-tutorial/ic789522.png "SAML 설정")

   a. **SAML 모드**에서 **SAML 필수**를 선택합니다.
   
   b. **Echosign 계정 관리자가 Echosign 자격 증명을 사용하여 로그인할 수 있게 허용**을 선택합니다.
   
   다. **사용자 만들기**에서 **SAML을 통해 인증된 사용자를 자동으로 추가**를 선택합니다.

   d. Azure Portal에서 복사한 **Azure AD 식별자**를 **IdD 엔터티 ID** 텍스트 상자에 붙여넣습니다.
    
   e. Azure Portal에서 복사한 **로그인 URL**을 **Idp 로그인 URL** 텍스트 상자에 붙여넣습니다.
   
   f. Azure Portal에서 복사한 **로그아웃 URL**을 **Idp 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

   g. 다운로드한 **인증서(Base64)** 파일을 메모장에서 엽니다. 내용을 클립보드로 복사한 다음, **IdP 인증서** 텍스트 상자에 붙여넣습니다.

   h. **변경 내용 저장**을 선택합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adobe Sign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택하고, **모든 애플리케이션**을 선택한 다음, **Adobe Sign**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Adobe Sign**을 입력하고 선택합니다.

    ![애플리케이션 목록의 Adobe Sign 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign 테스트 사용자 만들기

Azure AD 사용자가 Adobe Sign에 로그인할 수 있도록 하려면 Adobe Sign으로 프로비전되어야 합니다. 이는 수동 프로세스입니다.

>[!NOTE]
>다른 Adobe Sign 사용자 계정 생성 도구 또는 Adobe Sign이 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다. 

1. **Adobe Sign** 회사 사이트에 관리자 권한으로 로그인합니다.

2. 상단 메뉴에서 **계정**을 선택합니다. 그런 다음, 왼쪽 창에서 **사용자 및 그룹** > **새 사용자 만들기**를 선택합니다.
   
    ![계정, 사용자 및 그룹, 새 사용자 만들기가 강조 표시된 Adobe Sign 회사 사이트의 스크린샷](./media/adobe-echosign-tutorial/ic789524.png "계정")
   
3. **새 사용자 만들기** 섹션에서 다음 단계를 수행합니다.
   
    ![새 사용자 만들기 섹션의 스크린샷](./media/adobe-echosign-tutorial/ic789525.png "사용자 만들기")
   
    a. 관련된 텍스트 상자에 프로비전할 유효한 Azure AD 계정의 **이메일 주소**, **이름** 및 **성**을 입력합니다.
   
    b. **사용자 만들기**를 선택합니다.

>[!NOTE]
>Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 이메일을 받습니다. 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adobe Sign 타일을 클릭하면 SSO를 설정한 Adobe Sign에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

