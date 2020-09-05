---
title: '자습서: Watch by Colors와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Watch by Colors 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: 79fd6c5212b2ad956259f2cfbfb36ec3e7e92844
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523819"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>자습서: Watch by Colors와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Watch by Colors를 연결하는 방법에 대해 알아봅니다. Azure AD와 Watch by Colors를 연결하면 다음을 수행할 수 있습니다.

* Watch by Colors에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Watch by Colors에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Watch by Colors SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Watch by Colors에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-watch-by-colors-from-the-gallery"></a>갤러리에서 Watch by Colors 추가

Watch by Colors가 Azure AD에 통합되도록 구성하려면 갤러리에서 Watch by Colors를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Watch by Colors**를 입력합니다.
1. 결과 패널에서 **Watch by Colors**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Watch by Colors에 대한 Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Watch by Colors에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Watch by Colors의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Watch by Colors에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Watch by Colors SSO 구성](#configure-watch-by-colors-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Watch by Colors 테스트 사용자 만들기](#create-watch-by-colors-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Watch by Colors에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Watch by Colors** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 **IDP** 시작 모드로 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://app.colorscorporation.com/login` URL을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Watch by Colors에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Watch by Colors**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-watch-by-colors-sso"></a>Watch by Colors SSO 구성

1. Watch by Colors 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Watch by Colors 설정**을 클릭하면 Watch by Colors 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Watch by Colors에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Watch by Colors를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Watch by Colors 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위 모서리에서 **프로필** > **계정 설정** > **SSO(Single Sign On)** 를 클릭합니다.

    ![Watch by Colors 구성](./media/watch-by-colors-tutorial/config01.png)

5. **SSO(Single Sign On)** 페이지에서 다음 단계를 수행합니다.

    ![Watch by Colors 구성](./media/watch-by-colors-tutorial/config02.png)

    a. **SAML 사용**을 **켜기**로 설정합니다.

    b. **URL** 텍스트 상자에 Azure Portal에서 복사한 **페더레이션 메타데이터 URL**을 붙여넣습니다.

    다. **가져오기**를 클릭하면 다음 필드가 페이지에서 자동으로 채워집니다.

    d. **저장**을 클릭합니다.

### <a name="create-watch-by-colors-test-user"></a>Watch by Colors 테스트 사용자 만들기

Azure AD 사용자가 Watch by Colors에 로그인할 수 있도록 하려면 Watch by Colors로 프로비저닝되어야 합니다. Watch by Colors에서는 프로비저닝이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Watch by Colors에 보안 관리자 권한으로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에서 **프로필** > **사용자** > **사용자 추가**를 클릭합니다.

    ![Watch by Colors 구성](./media/watch-by-colors-tutorial/config03.png)

1. **사용자 세부 정보** 페이지에서 다음 단계를 수행합니다.

    ![Watch by Colors 구성](./media/watch-by-colors-tutorial/config04.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **B**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **전자 메일** 텍스트 상자에 사용자의 전자 메일(예: `B.Simon@contoso.com`)을 입력합니다.

    d. **암호** 텍스트 상자에 암호를 입력합니다.

    e. 조직별 **계정 권한**을 선택합니다.

    f. **저장**을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Watch by Colors 타일을 클릭하면 SSO를 설정한 Watch by Colors에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Watch by Colors 체험하기](https://aad.portal.azure.com/)

