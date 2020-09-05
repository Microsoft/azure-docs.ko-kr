---
title: '자습서: ThousandEyes와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 ThousandEyes 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: 2b99d5ea33693431fb3811af50385891684a366e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>자습서: ThousandEyes와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 ThousandEyes를 연결하는 방법에 대해 알아봅니다. Azure AD와 ThousandEyes를 연결하는 경우 다음을 수행할 수 있습니다.

* ThousandEyes에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 ThousandEyes에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* ThousandEyes SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* ThousandEyes는 **SP 및 IDP** 시작 SSO를 지원합니다.
* ThousandEyes는 [**자동화된** 사용자 프로비전](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-thousandeyes-from-the-gallery"></a>갤러리에서 ThousandEyes 추가

ThousandEyes가 Azure AD에 통합되도록 구성하려면 갤러리의 ThousandEyes를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ThousandEyes**를 입력합니다.
1. 결과 패널에서 **ThousandEyes**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>ThousandEyes에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 ThousandEyes에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ThousandEyes의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ThousandEyes에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[ThousandEyes SSO 구성](#configure-thousandeyes-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[ThousandEyes 테스트 사용자 만들기](#create-thousandeyes-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 ThousandEyes에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ThousandEyes** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://app.thousandeyes.com/login/sso` URL을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **ThousandEyes 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 ThousandEyes에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **ThousandEyes**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-thousandeyes-sso"></a>ThousandEyes SSO 구성

1. 다른 웹 브라우저 창에서 **ThousandEyes** 회사 사이트에 관리자로 로그인합니다.

2. 위쪽의 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/thousandeyes-tutorial/ic790066.png "설정")

3. 페이지 맨 아래에 있는 **계정**

    ![계정](./media/thousandeyes-tutorial/ic790067.png "계정")

4. **보안 및 인증** 탭을 클릭합니다.

    ![보안 및 인증](./media/thousandeyes-tutorial/ic790068.png "보안 및 인증")

5. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 설정](./media/thousandeyes-tutorial/ic790069.png "Single Sign-On 설정")

    a. **Single Sign-On 사용**을 선택합니다.

    b. **로그인 페이지 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL**을 붙여넣습니다.

    다. **로그아웃 페이지 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL**을 붙여넣습니다.

    d. **ID 공급자 발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    e. **확인 인증서**에서 **파일 선택**을 클릭하고 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    f. **저장**을 클릭합니다.

### <a name="create-thousandeyes-test-user"></a>ThousandEyes 테스트 사용자 만들기

이 섹션은 ThousandEyes에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. ThousandEyes는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](thousandeyes-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. ThousandEyes 회사 사이트에 관리자로 로그인합니다.

2. **설정**을 클릭합니다.

    ![설정](./media/thousandeyes-tutorial/IC790066.png "설정")

3. **계정**을 클릭합니다.

    ![계정](./media/thousandeyes-tutorial/IC790067.png "계정")

4. **계정 및 사용자** 탭을 클릭합니다.

    ![계정 및 사용자](./media/thousandeyes-tutorial/IC790073.png "계정 및 사용자")

5. **사용자 및 계정 추가** 섹션에서 다음 단계를 수행합니다.

    ![사용자 계정 추가](./media/thousandeyes-tutorial/IC790074.png "사용자 계정 추가")

    a. **이름** 텍스트 상자에 **B.Simon**과 같은 사용자의 이름을 입력합니다.

    b. **이메일** 텍스트 상자에 사용자 이메일(예: b.simon@contoso.com)을 입력합니다.

    b. **계정에 새 사용자 추가**를 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 계정을 확인하고 활성화하기 위한 링크가 포함된 메일을 받습니다.

> [!NOTE]
> 다른 ThousandEyes 사용자 계정 생성 도구 또는 ThousandEyes가 제공한 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전할 수 있습니다.


## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ThousandEyes 타일을 클릭하면 SSO를 설정한 ThousandEyes에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 ThousandEyes 사용해보기](https://aad.portal.azure.com/)

- [사용자 프로비저닝 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)