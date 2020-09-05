---
title: '자습서: SignalFx와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 SignalFx 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 3eae423763d2d4a05b33e4e1d4b6e9e087a47ba3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>자습서: SignalFx와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 SignalFx를 통합하는 방법에 대해 알아봅니다. Azure AD와 SignalFx를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 SignalFx에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SignalFx에 자동으로 로그인되도록 설정합니다.
* 한 위치(Azure Portal)에서 계정을 관리합니다.

Azure AD와 SaaS 앱의 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음이 필요합니다.

* Azure AD 구독
    * 구독이 없는 경우 [여기서 체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SignalFx SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SignalFx는 **IDP** 시작 SSO를 지원합니다.
* SignalFx는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* SignalFx가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>1단계: Azure에서 SignalFx 애플리케이션 추가

다음 지침을 사용하여 SignalFx 애플리케이션을 관리형 SaaS 앱 목록에 추가합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **SignalFx**를 입력하여 선택합니다.
     * 애플리케이션이 테넌트에 추가될 때까지 몇 분 정도 기다려야 할 수 있습니다.
1. Azure Portal을 열어 둔 다음, 새 웹 탭을 엽니다.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>2단계: SignalFx SSO 구성 시작

다음 지침을 사용하여 SignalFx SSO 구성 프로세스를 시작합니다.

1. 새로 열린 탭에서 SignalFx UI에 액세스하여 로그인합니다. 
1. 위쪽 메뉴에서 **Integrations(통합)** 를 클릭합니다. 
1. 검색 필드에서 **Azure Active Directory**를 입력하여 선택합니다.
1. **Create New Integration(새 통합 만들기)** 을 클릭합니다.
1. **Name(이름)** 에서 사용자가 쉽게 인식하여 이해할 수 있는 이름을 입력합니다.
1. **Show on login page(로그인 페이지 표시)** 를 표시합니다.
    * 이 기능은 로그인 페이지에 사용자가 클릭할 수 있는 사용자 지정 단추를 표시합니다. 
    * **Name**에 입력한 정보가 단추에 표시됩니다. 따라서 사용자가 인식할 **Name**을 입력합니다. 
    * 이 옵션은 사용자 지정 하위 도메인(예: **yourcompanyname.signalfx.com**)을 SignalFx 애플리케이션에 사용하는 경우에만 작동합니다. 사용자 지정 하위 도메인을 얻으려면 SignalFx 고객 지원팀에 문의하세요. 
1. **Integration ID(통합 ID)** 를 복사합니다. 이 정보는 이후 단계에서 필요합니다. 
1. SignalFx UI를 열어 둡니다. 

## <a name="step-3-configure-azure-ad-sso"></a>3단계: Azure AD SSO 구성

다음 지침을 사용하여 Azure Portal에서 Azure AD SSO를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)로 돌아가서 **SignalFx** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 펜(편집) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 다음 필드를 완성합니다. 

    a. **식별자**에 `https://api.<realm>.signalfx.com/v1/saml/metadata` URL을 입력하고, `<realm>`을 SignalFx 영역으로 바꿉니다. 

    b. **회신 URL**에서 `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` URL을 입력하고, `<realm>`을 SignalFx 영역으로 바꾸고, `<integration ID>`를 이전에 SignalFx UI에서 복사한  **Integration ID(통합 ID)** 로 바꿉니다.

1. SignalFx 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 
    
1. 다음 클레임이 Active Directory에 채워지는 원본 특성에 매핑되는지 검토하고 확인합니다. 

    | 속성 |  원본 특성|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > 이 프로세스를 수행하려면 Active Directory가 하나 이상의 확인된 사용자 지정 도메인으로 구성되고 이 도메인의 이메일 계정에 액세스할 수 있어야 합니다. 이 구성에 대해 확실하지 않거나 도움이 필요한 경우 SignalFx 지원 팀에 문의하세요.  

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 다음, **다운로드**를 선택합니다. 인증서를 다운로드하여 컴퓨터에 저장합니다. 그런 다음, **앱 페더레이션 메타데이터 URL** 값을 복사합니다. 이 정보는 SignalFx UI의 이후 단계에서 필요합니다. 

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **SignalFx 설정** 섹션에서 **Azure AD 식별자** 값을 복사합니다. 이 정보는 SignalFx UI의 이후 단계에서 필요합니다. 

## <a name="step-4-create-an-azure-ad-test-user"></a>4단계: Azure AD 테스트 사용자 만들기

다음 지침을 사용하여 Azure Portal에서 **B.Simon**이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 탐색 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 페이지 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음을 수행합니다.
   1. **사용자 이름**에서 `username@companydomain.extension`을 입력합니다(예: `b.simon@contoso.com`).
   1. **이름**에 `B.Simon`를 입력합니다.
   1. **암호 표시**를 표시한 다음, 표시된 값을 **암호**에 복사합니다. 이 정보는 이후 단계에서 이 통합을 테스트하는 데 필요합니다. 
   1. **만들기**를 클릭합니다.

## <a name="step-5-assign-the-azure-ad-test-user"></a>5단계: Azure AD 테스트 사용자 할당

다음 지침을 사용하여 테스트 사용자가 Azure Single Sign-On을 SignalFx에 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **SignalFx**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾은 다음, **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon**을 선택한 다음, 페이지 아래쪽에서 **선택**을 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 페이지 아래쪽에서 **선택**을 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당**을 클릭합니다.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>6단계: SignalFx SSO 구성 완료 

1. 이전 탭을 열고, SignalFx UI로 돌아가서 현재 Azure Active Directory 통합 페이지를 봅니다. 
1. **인증서(Base64)** 옆에 있는 **파일 업로드**를 클릭한 다음, 이전에 Azure Portal에서 다운로드한 **Base64로 인코딩된 인증서** 파일을 찾습니다.
1. 이전에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Azure AD 식별자** 옆에 붙여넣습니다. 
1. 이전에 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **Federation Metadata URL** 옆에 붙여넣습니다. 
1. **저장**을 클릭합니다.

## <a name="step-7-test-sso"></a>7단계: SSO 테스트

SSO 테스트 방법 및 SignalFx에 처음 로그인하는 데 필요한 항목과 관련된 다음 정보를 검토합니다. 

### <a name="test-logins"></a>로그인 테스트

* 로그인을 테스트하려면 프라이빗/incognito 창을 사용하거나 Azure Portal에서 로그아웃할 수 있습니다. 그렇지 않으면 애플리케이션을 구성한 사용자의 쿠키로 인해 테스트 사용자의 성공적인 로그인이 방해되거나 차단됩니다.

* 새 테스트 사용자가 처음 로그인하면 Azure에서 암호 변경을 강제로 수행합니다. 이 경우 SSO 로그인 프로세스가 완료되지 않고 테스트 사용자가 Azure Portal로 이동합니다. 문제를 해결하려면 테스트 사용자가 암호를 변경하고 SignalFx 로그인 페이지 또는 액세스 패널로 이동하여 다시 시도해야 합니다.
    * 액세스 패널에서 SignalFx 타일을 클릭하면 SignalFx에 자동으로 로그인됩니다. 
        * 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

* SignalFx 애플리케이션은 액세스 패널 또는 조직에 할당된 사용자 지정 로그인 페이지를 통해 액세스할 수 있습니다. 테스트 사용자는 이러한 위치 중 하나에서 시작하여 통합을 테스트해야 합니다.
    * 테스트 사용자는 이 프로세스에서 이전에 만든 자격 증명을 **b.simon@contoso.com** 에 사용할 수 있습니다.

### <a name="first-time-logins"></a>처음 로그인

* 사용자는 SAML SSO에서 SignalFx에 처음 로그인하는 경우 링크가 포함된 SignalFx 이메일을 받습니다. 인증을 위해 사용자는 이 링크를 클릭해야 합니다. 이 이메일 유효성 검사는 처음 사용자에 대해서만 수행됩니다. 

* SignalFx는 **Just-In-Time** 사용자 만들기를 지원합니다. 즉, 사용자가 SignalFx에 없는 경우 처음 로그인할 때 사용자 계정이 만들어집니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD를 사용하여 SignalFx 사용해 보기](https://aad.portal.azure.com/)