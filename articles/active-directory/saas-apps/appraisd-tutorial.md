---
title: '자습서: Appraisd와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Appraisd 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 2b8e8a6df2fc355ad6a8ef50d801dc7457e97c63
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547786"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>자습서: Azure Active Directory와 Appraisd 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Appraisd를 통합하는 방법에 대해 알아봅니다. Azure AD와 Appraisd를 통합하면 다음 작업을 수행할 수 있습니다.

* Azure AD에서 Appraisd에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Appraisd에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Appraisd SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Appraisd는 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-appraisd-from-the-gallery"></a>갤러리에서 Appraisd 추가

Appraisd의 Azure AD 통합을 구성하려면 갤러리의 Appraisd를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Appraisd**를 입력합니다.
1. 결과 패널에서 **Appraisd**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Appraisd에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Appraisd의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Appraisd에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. 애플리케이션 쪽에서 SSO 설정을 구성하도록 **[Appraisd를 구성](#configure-appraisd)** 합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B. Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Appraisd 테스트 사용자 만들기](#create-appraisd-test-user)** - B. Simon의 Azure AD 표현과 연결된 사용자를 Appraisd에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Appraisd** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 [저장] 단추를 클릭하여 구성을 저장하고 다음 단계를 수행해야 합니다.

    a. **추가 URL 설정**을 클릭합니다.

    b. **릴레이 상태** 텍스트 상자에 URL을 입력합니다. `<TENANTCODE>`

    다. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > 자습서 뒷부분에서 설명하는 Appraisd SSO 구성 페이지에서 실제 로그온 URL 및 릴레이 상태 값을 확인합니다.

1. Appraisd 애플리케이션에는 특정 형식의 SAML 어설션이 필요하기 때문에, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑되는 기본 특성 목록을 보여줍니다. Appraisd 애플리케이션에서는 **nameidentifier**가 **user.mail**과 매핑되어야 하기 때문에, **편집** 아이콘을 클릭하고 특성 매핑을 변경하여 특성을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Appraisd 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd 구성

1. Appraisd 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Appraisd 설정**을 클릭하면 Appraisd 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Appraisd에 로그인합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-7단계를 자동화합니다.

    ![설정 구성](common/setup-sso.png)

3. Appraisd를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Appraisd 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위에 있는 **설정** 아이콘을 클릭한 후 **구성**으로 이동합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. 메뉴 왼쪽에서 **SAML Single Sign-On**을 클릭합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. **SAML 2.0 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. **기본 릴레이 상태** 값을 복사하여 Azure Portal의 **기본 SAML 구성**에 있는 **릴레이 상태** 텍스트 상자에 붙여넣습니다.

    b. **서비스에서 시작한 로그인 URL** 값을 복사하여 Azure Portal의 **기본 SAML 구성**에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

7. **사용자 식별** 아래 동일한 페이지를 아래로 스크롤하여 다음 단계를 수행합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Azure Portal에서 복사한 **로그온 URL** 값을 **ID 공급자 Single Sign-On URL** 텍스트 상자에 붙여넣고 **저장**을 클릭합니다.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **ID 공급자 발급자 URL** 텍스트 상자에 붙여넣고 **저장**을 클릭합니다.

    다. 메모장에서 Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 열고, 콘텐츠를 복사한 다음, **X.509 인증서** 상자에 붙여넣고, **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B. Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B. Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Appraisd에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Appraisd**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B. Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-appraisd-test-user"></a>Appraisd 테스트 사용자 만들기

Azure AD 사용자가 Appraisd에 로그인할 수 있게 하려면 Appraisd로 프로비전되어야 합니다. Appraisd에서는 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 보안 관리자 권한으로 Appraisd에 로그인합니다.

2. 페이지의 오른쪽 위에 있는 **설정** 아이콘을 클릭한 후 **관리 센터**로 이동합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. 페이지의 위쪽에 있는 도구 모음에서 **사용자**를 클릭한 다음, **새 사용자 추가**로 이동합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. **새 사용자 추가** 페이지에서 다음 단계를 수행합니다.

    ![이미지](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **simon**)을 입력합니다.

    다. **전자 메일** 텍스트 상자에 사용자의 전자 메일(예: `B. Simon@contoso.com`)을 입력합니다.

    d. **사용자 추가**를 클릭합니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Appraisd 타일을 선택하면 SSO를 설정한 Appraisd에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
