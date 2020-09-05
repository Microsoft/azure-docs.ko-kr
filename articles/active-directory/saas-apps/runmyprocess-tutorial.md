---
title: '자습서: RunMyProcess와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 RunMyProcess 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: db47849575c097cf56824b623dddbb6e0835d1b4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543614"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>자습서: Azure Active Directory와 RunMyProcess 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 RunMyProcess를 연결하는 방법에 대해 알아봅니다. Azure AD와 RunMyProcess를 연결하는 경우 다음을 수행할 수 있습니다.

* RunMyProcess에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 RunMyProcess에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* RunMyProcess SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* RunMyProcess에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-runmyprocess-from-the-gallery"></a>갤러리에서 RunMyProcess 추가

RunMyProcess의 Azure AD 통합을 구성하려면 갤러리의 RunMyProcess를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **RunMyProcess**를 입력합니다.
1. 결과 패널에서 **RunMyProcess**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 RunMyProcess에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 RunMyProcess의 관련 사용자 간에 연결 관계를 설정해야 합니다.

RunMyProcess에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[RunMyProcess SSO 구성](#configure-runmyprocess-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[RunMyProcess 테스트 사용자 만들기](#create-runmyprocess-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 RunMyProcess에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **RunMyProcess** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **로그인 URL** 텍스트 상자에서 `https://live.runmyprocess.com/live/<tenant id>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [RunMyProcess 클라이언트 지원 팀](mailto:support@runmyprocess.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **RunMyProcess 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO 구성

1. 다른 웹 브라우저 창에서 RunMyProcess 테넌트에 관리자로 로그인합니다.

1. 왼쪽 탐색 패널에서 **계정**을 클릭하고 **구성**을 선택합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. **인증 방법** 섹션으로 이동하고 아래 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. **메서드**로 **Samlv2를 사용한 SSO**를 선택합니다.

    b. **SSO 리디렉션** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    다. **로그아웃 리디렉션** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    d. **이름 ID 형식** 텍스트 상자에 **이름 식별자 형식** 값을 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**로 입력합니다.

    e. Azure Portal에서 다운로드한 인증서 파일을 메모장에서 열고 인증서 파일의 내용을 복사하여 **인증서** 텍스트 상자에 붙여넣습니다.

    f. **저장** 아이콘을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 RunMyProcess에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **RunMyProcess**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-runmyprocess-test-user"></a>RunMyProcess 테스트 사용자 만들기

Azure AD 사용자가 RunMyProcess에 로그인할 수 있도록 하려면 RunMyProcess로 프로비저닝되어야 합니다. RunMyProcess의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. RunMyProcess 회사 사이트에 관리자 권한으로 로그인합니다.

1. 왼쪽 탐색 패널에서 **계정**을 클릭하고 **사용자**를 선택한 다음 **새 사용자**를 클릭합니다.

    ![새 사용자](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "새 사용자")

1. **사용자 설정** 섹션에서 다음 단계를 수행합니다.

    ![프로필](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "프로필")
  
    a. 관련된 텍스트 상자에 프로비전할 유효한 Azure AD 계정의 **이름** 및 **전자 메일**을 입력합니다.

    b. **IDE 언어**, **언어** 및 **프로필**을 선택합니다.

    다. **나에게 계정 만들기 메일 보내기**를 선택합니다.

    d. **저장**을 클릭합니다.

    > [!NOTE]
    > RunMyProcess에서 제공하는 다른 RunMyProcess 사용자 계정 만들기 도구 또는 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전합니다.

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 RunMyProcess 타일을 클릭하면, SSO를 설정한 RunMyProcess에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)