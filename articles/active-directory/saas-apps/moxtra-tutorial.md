---
title: '자습서: Moxtra와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Moxtra 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: 7fffe58dfdb63da28edc19c19b56b576f4fbadd3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>자습서: Moxtra와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Moxtra를 통합하는 방법에 대해 알아봅니다. Azure AD와 Moxtra를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Moxtra에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Moxtra에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Moxtra SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Moxtra가 **SP**에서 시작된 SSO를 지원

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-moxtra-from-the-gallery"></a>갤러리에서 Moxtra 추가

Moxtra의 Azure AD 통합을 구성하려면 갤러리의 Moxtra를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Moxtra**를 입력합니다.
1. 결과 패널에서 **Moxtra**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Moxtra에 대한 Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Moxtra에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Moxtra의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Moxtra에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Moxtra SSO 구성](#configure-moxtra-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Moxtra 테스트 사용자 만들기](#create-moxtra-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Moxtra에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Moxtra** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **로그온 URL** 텍스트 상자에 `https://www.moxtra.com/service/#login` URL을 입력합니다.

1. Moxtra 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Moxtra 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. [사용자 특성] 대화 상자의 [사용자 클레임] 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | 속성 | 원본 특성|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD 식별자 >

    > [!Note]
    > **idpid** 특성 값은 실제 값이 아닙니다. 8단계의 **Moxtra 설정** 섹션에서 실제 값을 가져올 수 있습니다. 

    1. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    1. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    1. **네임스페이스**를 비워 둡니다.

    1. 원본을 **특성**으로 선택합니다.

    1. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    1. **확인**을 클릭합니다.

    1. **저장**을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Moxtra 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Moxtra에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Moxtra**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-moxtra-sso"></a>Moxtra SSO 구성

1. 다른 웹 브라우저 창에서 Moxtra 회사 사이트에 관리자로 로그인합니다.

2. 왼쪽의 도구 모음에서 **관리 콘솔 > SAML Single Sign-On**을 클릭하고 나서 **새로 만들기**를 클릭합니다.
   
    ![Single Sign-on 구성](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. **SAML** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-on 구성](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. **이름** 텍스트 상자에 구성할 이름을 입력합니다(예: *SAML*). 
  
    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IdP 엔터티 ID** 텍스트 상자에 붙여넣습니다. 
 
    다. **로그인 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다. 
 
    d. **AuthnContextClassRef** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**를 입력합니다. 
 
    e. **NameID 형식** 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**를 입력합니다. 
 
    f. Azure Portal에서 다운로드한 인증서를 메모장에서 열고, 내용을 복사한 다음, **인증서** 텍스트 상자에 붙여넣습니다.    
 
    g. SAML 전자 메일 도메인 텍스트 상자에 SAML 전자 메일 도메인을 입력합니다.    
  
    >[!NOTE]
    >도메인을 확인하는 단계를 보려면 아래 "**i**"를 클릭합니다.

    h. **업데이트**를 클릭합니다.

### <a name="create-moxtra-test-user"></a>Moxtra 테스트 사용자 만들기

이 섹션은 Moxtra에서 B.Simon이라는 사용자를 만들기 위한 것입니다.

**Moxtra에서 B.simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. Moxtra 회사 사이트에 관리자로 로그온합니다.

1. 왼쪽의 도구 모음에서 **관리 콘솔 > 사용자 관리**, **사용자 추가**를 차례로 클릭합니다.
   
    ![Single Sign-on 구성](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.
  
    a. **First Name**(이름) 텍스트 상자에서 **B**를 입력합니다.
  
    b. **성** 텍스트 상자에 **Simon**을 입력합니다.
  
    다. **Email**(이메일) 텍스트 상자에서 Azure Portal과 동일한 B.simon의 이메일 주소를 입력합니다.
  
    d. **사업부** 텍스트 상자에 **Dev**를 입력합니다.
  
    e. **부서** 텍스트 상자에 **IT**를 입력합니다.
  
    f. **관리자**를 선택합니다.
  
    g. **추가**를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Moxtra 타일을 클릭하면 SSO를 설정한 Moxtra에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Moxtra 사용해 보기](https://aad.portal.azure.com/)

