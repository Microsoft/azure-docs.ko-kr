---
title: '자습서: Displayr과 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory와 Displayr 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: a69b4a068e62c6376660a79b3a7c8ab5402b5704
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88536260"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>자습서: Displayr과 Azure Active Directory 통합

이 자습서에서는 Displayr을 Azure AD(Azure Active Directory)와 연결하는 방법을 알아봅니다. Azure AD와 Displayr을 통합하는 경우 다음을 수행할 수 있습니다.

* Displayr에 대한 액세스 권한이 있는 사용자를 Azure AD에 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Displayr에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Displayr SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Displayr에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-displayr-from-the-gallery"></a>갤러리에서 Displayr 추가

Displayr이 Azure AD에 연결되도록 구성하려면 갤러리의 Displayr을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Displayr**를 입력합니다.
1. 결과 패널에서 **Displayr**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

테스트 사용자 **Britta Simon**을 사용하여 Displayr로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Displayr의 관련 사용자 간에 연결이 형성되어야 합니다.

Displayr에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. **[Displayr 구성](#configure-displayr)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Displayr 테스트 사용자 만들기](#create-displayr-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Displayr에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Displayr** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<YOURDOMAIN>.displayr.com` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `<YOURDOMAIN>.displayr.com` 패턴을 사용하는 URL을 입력합니다.

    >[!NOTE]
    >이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Displayr 클라이언트 지원 팀](mailto:support@displayr.com)에 문의하세요. Azure Portal의 기본 SAML 구성 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 다음, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. Displayr 애플리케이션에는 특정 형식의 SAML 어설션이 필요하며, 이를 위해서는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Displayr 애플리케이션에는 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **그룹 클레임(미리 보기)** 대화 상자의 **사용자 특성 및 클레임** 섹션에서 다음 단계를 수행합니다.

    a. **클레임에서 반환되는 그룹** 옆에 있는 **펜**을 클릭합니다.

    ![이미지](./media/displayr-tutorial/config04.png)

    ![이미지](./media/displayr-tutorial/config05.png)

    b. 라디오 단추 목록에서 **모든 그룹**을 선택합니다.

    다. **그룹 ID**의 **원본 특성**을 선택합니다.

    d. **그룹 클레임 이름 사용자 지정**을 선택합니다.

    e. **그룹을 역할 클레임으로 내보내기**를 선택합니다.

    f. **저장**을 클릭합니다.

1. **Displayr 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr 구성

1. Displayr 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Displayr 설정**을 클릭하면 Displayr 애플리케이션으로 이동됩니다. 여기에서 관리자 자격 증명을 입력하여 Displayr에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Displayr를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Displayr 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. **설정**을 클릭한 후 **계정**으로 이동합니다.

    ![구성](./media/displayr-tutorial/config01.png)

5. 맨 위 메뉴에서 **설정**으로 전환하고 페이지 아래로 스크롤하여 **Single Sign On 구성(SAML)** 을 클릭합니다.

    ![구성](./media/displayr-tutorial/config02.png)

6. **Single Sign-On(SAML)** 페이지에서 다음 단계를 수행합니다.

    ![구성](./media/displayr-tutorial/config03.png)

    a. **Single Sign-On 사용(SAML)** 확인란을 선택합니다.

    b. Azure AD의 **기본 SAML 구성** 섹션에서 실제 **식별자** 값을 복사한 후 **발급자** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. 메모장에서 인증서(Base64)를 열고 내용을 복사한 다음, **인증서** 텍스트 상자에 붙여넣습니다.

    f. **그룹 매핑**은 선택 사항입니다.

    g. **저장**을 클릭합니다.  

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `BrittaSimon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Displayr에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Displayr**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-displayr-test-user"></a>Displayr 테스트 사용자 만들기

Azure AD 사용자가 Displayr에 로그인할 수 있으려면 해당 사용자가 Displayr에 프로비저닝되어야 합니다. Displayr에서는 프로비저닝이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Displayr에 관리자로 로그인합니다.

2. **설정**을 클릭한 후 **계정**으로 이동합니다.

    ![Displayr 구성](./media/displayr-tutorial/config01.png)

3. 맨 위 메뉴에서 **설정**으로 전환하고 **사용자** 섹션까지 페이지 아래로 스크롤한 후 **새 사용자**를 클릭합니다.

    ![Displayr 구성](./media/displayr-tutorial/config07.png)

4. **새 사용자** 페이지에서 다음 단계를 수행합니다.

    ![Displayr 구성](./media/displayr-tutorial/config06.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Brittasimon**)을 입력합니다.

    b. **전자 메일** 텍스트 상자에 사용자의 전자 메일(예: `Brittasimon@contoso.com`)을 입력합니다.

    다. 적절한 **그룹 멤버 자격**을 선택합니다.

    d. **저장**을 클릭합니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Displayr 타일을 선택하면 SSO를 설정한 Displayr에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
