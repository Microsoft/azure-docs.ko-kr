---
title: '자습서: Prezi와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Prezi 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 994d427fb91f04ea0d2002a2c95e12366f4c6420
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553568"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>자습서: Prezi와 Azure Active Directory Single Sign-On 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Prezi를 통합하는 방법에 대해 알아봅니다. Azure AD와 Prezi를 통합하는 경우 다음을 수행할 수 있습니다.

* Prezi에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Prezi에 자동으로 로그인되도록 설정합니다.
* Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)가 설정된 Prezi 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Prezi는 SP 및 IDP 시작 SSO를 지원합니다.
* Prezi는 Just-In-Time 사용자 프로비저닝을 지원합니다.
* Prezi가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. 자세한 내용은 [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)를 참조하세요.

## <a name="add-prezi-from-the-gallery"></a>갤러리에서 Prezi 추가

Prezi가 Azure AD에 통합되도록 구성하려면 갤러리에서 Prezi를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 맨 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Prezi**를 입력합니다.
1. 결과 패널에서 **Prezi**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Prezi에 대한 Azure AD SSO 구성 및 테스트

B.Simon이라는 테스트 사용자를 사용하여 Prezi에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Prezi의 관련 사용자 간에 연결을 설정해야 합니다.

Prezi에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완성합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - B.Simon을 사용하여 Azure AD SSO를 테스트합니다.
    1. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B.Simon이 Azure AD SSO를 사용할 수 있도록 할당합니다.
1. [Prezi SSO 구성](#configure-prezi-sso) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. [Prezi 테스트 사용자 만들기](#create-a-prezi-test-user) - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Prezi에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Prezi** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에서 **편집** 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 설정 편집](common/edit-urls.png)

1. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, 다음 단계를 수행합니다.

    **로그온 URL** 상자에 URL `https://prezi.com/login/sso/`를 입력합니다.

1. **저장**을 선택합니다.

1. Prezi 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![사용자 특성 및 클레임](common/default-attributes.png)

1. Prezi 애플리케이션에는 SAML 응답에서 다시 전달되는 다음과 같은 몇 가지 특성도 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | 속성 | 원본 특성|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Prezi 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 맨 왼쪽 창에서 **Azure Active Directory**를 선택합니다. **사용자**로 이동한 다음, **모든 사용자**를 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. 사용자 속성에서 다음 단계를 수행합니다.
   1. **이름** 상자에서 **B.Simon**을 입력합니다.
   1. **사용자 이름** 상자에서 `username@companydomain.extension`(예: `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시되는 값을 적어 둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 B.Simon에게 Prezi에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Prezi**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택하고 화면 아래쪽에서 **선택**을 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택한 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

## <a name="configure-prezi-sso"></a>Prezi SSO 구성

1. 다른 웹 브라우저 창에서 사용자의 팀 계정으로 Prezi에 로그인하고 [관리 콘솔](https://prezi.com/organizations/manage)로 이동합니다.

1. **관리 콘솔**에서 **설정** 탭을 선택합니다.

    ![설정 탭](./media/prezi-tutorial/settings-image.png)

1. **SSO(Single Sign-On)** 섹션으로 이동하고 토글을 켜서 SSO를 사용하도록 설정합니다.
    
    ![SSO(Single Sign-On) 토글](./media/prezi-tutorial/single-signon.png)

1. **SSO(Single Sign-On)** 섹션에서 다음 단계를 수행합니다.

    ![SSO(Single Sign-On) 섹션](./media/prezi-tutorial/configuration.png)

    1. **식별자 또는 발급자 URL** 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    1. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML 2.0 엔드포인트(HTTP)** 상자에 붙여넣습니다.

    1. Azure Portal에서 다운로드한 **인증서(Base64)** 를 메모장에서 엽니다. 인증서의 내용을 복사하고 **인증서(X.509)** 상자에 붙여넣습니다.

    1. **저장**을 선택합니다.

### <a name="create-a-prezi-test-user"></a>Prezi 테스트 사용자 만들기

이 섹션에서는 Prezi에서 Britta Simon이라는 사용자를 만듭니다. Prezi는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에는 작업 항목이 없습니다. Prezi에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Prezi 타일을 선택하면 SSO를 설정한 Prezi 계정에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Azure AD로 Prezi 사용해 보기](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [고급 표시 유형 및 컨트롤을 사용하여 Prezi를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

