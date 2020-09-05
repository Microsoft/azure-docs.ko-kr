---
title: '자습서: 8x8과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 8x8 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 52b45bf7457a81b605df91e63a7c6ad539309dd8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538657"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>자습서: 8x8과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 8x8을 통합하는 방법에 대해 알아봅니다. Azure AD와 8x8을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 8x8에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 8x8에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* 8x8 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* 8x8에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* 8x8이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-8x8-from-the-gallery"></a>갤러리에서 8x8 추가

8x8이 Azure AD에 통합되도록 구성하려면 갤러리의 8x8을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **8x8**을 입력합니다.
1. 결과 패널에서 **8x8**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>8x8에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 8x8에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 8x8의 관련 사용자 간에 연결 관계를 설정해야 합니다.

8x8에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[8x8 SSO 구성](#configure-8x8-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[8x8 테스트 사용자 만들기](#create-8x8-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 8x8에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **8x8** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 URL을 입력합니다. `https://sso.8x8.com/saml2`

    b. **회신 URL** 텍스트 상자에서 `https://sso.8x8.com/saml2` URL을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다. 이 인증서는 나중에 자습서의 **8x8 SSO 구성** 섹션에서 사용합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **8x8 설정** 섹션에서 URL을 복사합니다. 이러한 URL 값은 나중에 자습서에서 사용합니다.

    ![구성 URL 복사](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 8x8에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **8x8**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-8x8-sso"></a>8x8 SSO 구성

자습서의 다음 부분에서는 8x8을 사용하는 구독의 종류에 따라 달라집니다.

* 관리를 위해 Configuration Manager(구성 관리자)를 사용하는 8x8 버전 및 X 시리즈 고객은 [8x8 Configuration Manager 구성](#configure-8x8-configuration-manager)을 참조하세요.
* 관리를 위해 Account Manager(계정 관리자)를 사용하는 Virtual Office 고객의 경우 [8x8 Account Manager 구성](#configure-8x8-account-manager)을 참조하세요.

### <a name="configure-8x8-configuration-manager"></a>8x8 Configuration Manager 구성

1. 8x8 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **8x8 설정**을 클릭하면 8x8 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 8x8에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. 8x8을 수동으로 설정하려면 8x8 [Configuration Manager](https://vo-cm.8x8.com/)에 관리자 권한으로 로그인합니다.

1. 홈 페이지에서 **Identity Management(ID 관리)** 를 클릭합니다.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure1.png)

1. **SSO(Single Sign-On)** , **Microsoft Azure AD**를 차례로 선택합니다.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure2.png)

1. Azure AD의 **SAML로 Single Sign-On 설정** 페이지에 있는 세 개의 URL과 서명 인증서를 8x8 Configuration Manager의 **Microsoft Azure AD SAML Settings(Microsoft Azure AD SAML 설정)** 섹션에 복사합니다.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    a. **로그인 URL**을 **IDP Login URL(IDP 로그인 URL)** 에 복사합니다.

    b. **Azure AD 식별자**를 **IDP Issuer URL/URN(IDP 발급자 URL/URN)** 에 복사합니다.

    다. **로그아웃 URL**을 **IDP Logout URL(IDP 로그아웃 URL)** 에 복사합니다.

    d. **인증서(Base64)** 를 다운로드하여 **Certificate(인증서)** 에 업로드합니다.

    e. **저장**을 클릭합니다.

### <a name="configure-8x8-account-manager"></a>8x8 Account Manager 구성

1. 8x8 Virtual Office 테넌트에 관리자 권한으로 로그온합니다.

1. 애플리케이션 패널에서 **Virtual Office Account Mgr**를 선택합니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. **비즈니스** 계정을 선택하여 관리하고 **로그인** 단추를 클릭입니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. 메뉴 목록에서 **계정** 탭을 클릭합니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. 계정 목록에서 **Single Sign On** 을 클릭합니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. 인증 방법에서 **Single Sign-On**을 선택하고 **SAML**을 클릭합니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. **SAML Single Sign-On** 섹션에서 다음 단계를 수행합니다.

    ![앱 쪽에서 구성](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    다. **발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    d. **브라우저** 단추를 클릭하여 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    e. **저장** 단추를 클릭합니다.

### <a name="create-8x8-test-user"></a>8x8 테스트 사용자 만들기

이 섹션에서는 8x8에서 Britta Simon이라는 사용자를 만듭니다. [8x8 지원 팀](https://www.8x8.com/about-us/contact-us)과 협력하여 사용자를 8x8 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 8x8 타일을 클릭하면 SSO를 설정한 8x8에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 8x8 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 8x8을 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)