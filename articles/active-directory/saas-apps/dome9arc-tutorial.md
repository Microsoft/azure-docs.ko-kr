---
title: '자습서: Check Point CloudGuard Dome9 Arc와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 Check Point CloudGuard Dome9 Arc 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 9435a0eccff5a3be0dc9e0a2844bae175d753a5c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555881"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>자습서: Check Point CloudGuard Dome9 Arc와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Check Point CloudGuard Dome9 Arc를 통합하는 방법에 대해 알아봅니다. Azure AD와 Check Point CloudGuard Dome9 Arc를 통합하면 다음을 수행할 수 있습니다.

* Check Point CloudGuard Dome9 Arc에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Check Point CloudGuard Dome9 Arc에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Check Point CloudGuard Dome9 Arc SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Check Point CloudGuard Dome9 Arc는 **SP 및 IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>갤러리의 Check Point CloudGuard Dome9 Arc 추가

Azure AD에 Check Point CloudGuard Dome9 Arc를 통합하도록 구성하려면 갤러리의 Check Point CloudGuard Dome9 Arc를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Check Point CloudGuard Dome9 Arc**를 입력합니다.
1. 결과 패널에서 **Check Point CloudGuard Dome9 Arc**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Check Point CloudGuard Dome9 Arc에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Check Point CloudGuard Dome9 Arc에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Check Point CloudGuard Dome9 Arc의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Check Point CloudGuard Dome9 Arc에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Check Point CloudGuard Dome9 Arc SSO 구성](#configure-check-point-cloudguard-dome9-arc-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Check Point CloudGuard Dome9 Arc 테스트 사용자 만들기](#create-check-point-cloudguard-dome9-arc-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Check Point CloudGuard Dome9 Arc에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Check Point CloudGuard Dome9 Arc** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에 URL을 입력합니다. `https://secure.dome9.com/`

    b. **회신 URL** 텍스트 상자에서 `https://secure.dome9.com/sso/saml/<yourcompanyname>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://secure.dome9.com/sso/saml/<yourcompanyname>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이 자습서의 뒷부분에 설명되어 있는 **Check Point CloudGuard Dome9 Arc SSO 구성** 섹션에서 `<company name>` 값을 가져옵니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Check Point CloudGuard Dome9 Arc 애플리케이션은 특정 형식의 SAML 어설션을 예상하며, 따라서 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Check Point CloudGuard Dome9 Arc 애플리케이션에는 아래에 표시된 대로 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | 속성 |  원본 특성|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >[여기](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial)를 클릭하여 Azure AD에서 역할을 만드는 방법을 알아봅니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Check Point CloudGuard Dome9 Arc 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Check Point CloudGuard Dome9 Arc에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Check Point CloudGuard Dome9 Arc**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Check Point CloudGuard Dome9 Arc SSO 구성

1. Check Point CloudGuard Dome9 Arc 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후에는 **Check Point CloudGuard Dome9 Arc 설정**을 클릭하여 Check Point CloudGuard Dome9 Arc 애플리케이션으로 이동합니다. 여기에서 애플리케이션에서 관리자 자격 증명을 입력하여 Check Point CloudGuard Dome9 Arc에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Check Point CloudGuard Dome9 Arc를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Check Point CloudGuard Dome9 Arc 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

2. 오른쪽 위 모서리에서 **프로필 설정**을 클릭한 다음, **계정 설정**을 클릭합니다. 

    ![Check Point CloudGuard Dome9 Arc 구성](./media/dome9arc-tutorial/configure1.png)

3. **SSO**로 이동한 다음 **사용**을 클릭합니다.

    ![Check Point CloudGuard Dome9 Arc 구성](./media/dome9arc-tutorial/configure2.png)

4. [SSO 구성] 섹션에서 다음 단계를 수행합니다.

    ![Check Point CloudGuard Dome9 Arc 구성](./media/dome9arc-tutorial/configure3.png)

    a. **계정 ID** 텍스트 상자에서 회사 이름을 입력합니다. 이 값은 Azure Portal의 **기본 SAML 구성 섹션**에서 언급한 **Reply** 및 **Sign on** URL에서 사용됩니다.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **발급자** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **Idp 엔드포인트 URL** 텍스트 상자에 붙여넣습니다.

    d. 다운로드한 Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음, **X.509 인증서** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Check Point CloudGuard Dome9 Arc 테스트 사용자 만들기

Azure AD 사용자가 Check Point CloudGuard Dome9 Arc에 로그인할 수 있게 하려면 해당 사용자를 애플리케이션에 프로비저닝해야 합니다. Check Point CloudGuard Dome9 Arc는 JIT(Just-In-Time) 프로비저닝을 지원하지만 이 기능이 제대로 작동하려면 사용자가 특정 **역할**을 선택하여 사용자에게 할당해야 합니다.

   >[!Note]
   >**역할**을 만드는 방법 및 다른 자세한 내용은 [Check Point CloudGuard Dome9 Arc 클라이언트 지원 팀](mailto:Dome9@checkpoint.com)에 문의하세요.

**사용자 계정을 수동으로 프로비전하려면 다음 단계를 수행합니다.**

1. Check Point CloudGuard Dome9 Arc 회사 사이트에 관리자 권한으로 로그인합니다.

2. **사용자 및 역할**을 클릭한 다음, **사용자**를 클릭합니다.

    ![직원 추가](./media/dome9arc-tutorial/user1.png)

3. **사용자 추가**를 클릭합니다.

    ![직원 추가](./media/dome9arc-tutorial/user2.png)

4. **사용자 만들기** 섹션에서 다음 단계를 수행합니다.

    ![직원 추가](./media/dome9arc-tutorial/user3.png)

    a. **이메일** 텍스트 상자에서 사용자의 이메일(예: B.Simon@contoso.com)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: B)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. **SSO 사용자**를 **설정**으로 지정합니다.

    e. **만들기**를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Check Point CloudGuard Dome9 Arc 타일을 클릭하면 SSO를 설정한 Check Point CloudGuard Dome9 Arc에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Check Point CloudGuard Dome9 Arc 사용해보기](https://aad.portal.azure.com/)