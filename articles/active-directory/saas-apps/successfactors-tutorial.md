---
title: '자습서: SuccessFactors와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 SuccessFactors 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 48524bec352d2fa9c169a1345e52ad4c789e59d3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>자습서: SuccessFactors와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 SuccessFactors를 통합하는 방법에 대해 알아봅니다. Azure AD와 SuccessFactors를 통합하면 다음을 수행할 수 있습니다.

* SuccessFactors에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 SuccessFactors에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SuccessFactors SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SuccessFactors에서 **SP** 시작 SSO를 지원합니다.
* SuccessFactors를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>갤러리에서 SuccessFactors 추가

SuccessFactors의 Azure AD 통합을 구성하려면 갤러리의 SuccessFactors를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SuccessFactors**를 입력합니다.
1. 결과 패널에서 **SuccessFactors**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>SuccessFactors용 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 SuccessFactors에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SuccessFactors의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SuccessFactors에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[SuccessFactors SSO 구성](#configure-successfactors-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SuccessFactors 테스트 사용자 만들기](#create-successfactors-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 SuccessFactors에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SuccessFactors** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다.

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [SuccessFactors 클라이언트 지원 팀](https://www.sap.com/support.html)에 문의하세요.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **SuccessFactors 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SuccessFactors에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **SuccessFactors**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-successfactors-sso"></a>SuccessFactors SSO 구성

1. 다른 웹 브라우저 창에서 **SuccessFactors 관리 포털**에 관리자로 로그인합니다.

2. **애플리케이션 보안**을 방문하고 **Single Sign On 기능**으로 이동합니다.

3. **토큰 재설정**에 값을 입력하고 **토큰 저장**을 클릭하여 SAML SSO를 사용하도록 설정합니다.

    ![앱 쪽에서 Single Sign-On 구성][11]

    > [!NOTE]
    > 이 값은 설정/해제 스위치로 사용됩니다. 값이 저장된 경우 SAML SSO는 ON입니다. 빈 값이 저장된 경우 SAML SSO는 OFF입니다.

4. 아래 스크린샷으로 이동하고 다음 작업을 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성][12]
  
    a. **SAML v2 SSO** 라디오 단추를 선택합니다.
  
    b. **SAML 어설션 파티 이름**(예: SAML 발급자 + 회사 이름)을 설정합니다.

    다. **발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    d. **필수 서명 요구**로 **어설션**을 선택합니다.

    e. **SAML 플래그 사용**으로 **사용**을 선택합니다.

    f. **로그인 요청 서명(SF 생성/SP/RP)** 으로 **아니요**를 선택합니다.

    g. **SAML 프로필**로 **브라우저/게시 프로필**을 선택합니다.

    h. **인증서 유효 기간 적용**으로 **아니요**를 선택합니다.

    i. Azure Portal에서 다운로드한 인증서 파일의 내용을 복사한 다음 **SAML 확인 인증서** 텍스트 상자에 붙여넣습니다.

    > [!NOTE] 
    > 인증서 내용에는 시작 인증서 및 끝 인증서 태그가 있어야 합니다.

5. SAML V2로 이동한 후 다음 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성][13]

    a. **SP 시작 전역 로그아웃 지원**으로 **예**를 선택합니다.

    b. **전역 로그아웃 서비스 URL(LogoutRequest 대상)** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    다. **요청 SP는 모든 NameID 요소를 암호화해야 합니다.** 로 **아니요**를 선택합니다.

    d. **NameID 형식**으로 **지정되지 않음**을 선택합니다.

    e. **SP 시작 로그인 사용(AuthnRequest)** 으로 **예**를 선택합니다.

    f. Azure Portal에서 복사한 **로그인 URL** 값을 **전사적 발급자로 요청 보내기** 텍스트 상자에 붙여넣습니다.

6. 로그인 사용자 이름의 대/소문자를 구분하지 않으려면 이 단계를 수행합니다.

    ![Single Sign-on 구성][29]

    a. a. **회사 설정**(아래쪽)을 방문합니다.

    b. **대/소문자를 구분하지 않는 사용자 이름 사용** 근처의 확인란을 선택합니다.

    다. **저장**을 클릭합니다.

    > [!NOTE]
    > 이 기능을 사용하려고 하면 시스템에서 중복되는 SAML 로그인 이름이 만들어지는지 확인합니다. 예를 들어 고객의 사용자 이름에 User1 및 user1이 있는 경우입니다. 대/소문자를 구분하지 않으면 이러한 중복 항목을 만듭니다. 시스템에서 오류 메시지를 제공하고 기능이 비활성화됩니다. 다른 철자가 되도록 고객은 사용자 이름 중 하나를 변경해야 합니다.

### <a name="create-successfactors-test-user"></a>SuccessFactors 테스트 사용자 만들기

Azure AD 사용자가 SuccessFactors에 로그인할 수 있도록 하려면 SuccessFactors로 프로비저닝되어야 합니다. SuccessFactors의 경우 프로비전은 수동 작업입니다.

SuccessFactors에서 사용자를 생성하려면 [SuccessFactors 지원 팀](https://www.sap.com/support.html)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SuccessFactors 타일을 클릭하면 SSO를 설정한 SuccessFactors에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 SuccessFactors 사용해보기](https://aad.portal.azure.com)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 SuccessFactors를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
