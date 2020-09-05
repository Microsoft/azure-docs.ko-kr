---
title: '자습서: Workday와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Workday 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>자습서: Workday와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Workday를 통합하는 방법에 대해 알아봅니다. Azure AD와 Workday를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Workday에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Workday에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Workday SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Workday에서 **SP** 시작 SSO를 지원합니다.

* Workday를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>갤러리에서 Workday 추가

Workday가 Azure AD에 통합되도록 구성하려면 갤러리에서 Workday를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Workday**를 입력합니다.
1. 결과 패널에서 **Workday**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Workday용 Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Workday에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Workday의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Workday에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[Workday 구성](#configure-workday)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. **[Workday 테스트 사용자 만들기](#create-workday-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Workday에 만듭니다.
3. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Workday** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://impl.workday.com/<tenant>/login-saml2.flex` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에서 `http://www.workday.com` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://impl.workday.com/<tenant>/login-saml.htmld` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 회신 URL에 하위 도메인(예: www, wd2, wd3, wd3-impl, wd5, wd5-impl)이 있어야 합니다.
    > `http://www.myworkday.com`과 같이 사용해도 되지만 `http://myworkday.com`은 가능하지 않습니다. 이러한 값을 얻으려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. Workday 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑되는 기본 특성 목록을 보여줍니다. Workday 애플리케이션에서는 **nameidentifier**가 **user.mail**, **UPN** 등과 매핑되어야 하므로 **편집** 아이콘을 클릭하여 특성 매핑을 편집하여 특성 매핑을 변경해야 합니다.

    ![이미지](common/edit-attribute.png)

    > [!NOTE]
    > 여기에서는 기본적으로 UPN(user.userprincipalname)을 사용하여 이름 ID를 매핑했습니다. SSO를 성공적으로 수행하려면 Workday 계정(이메일, UPN 등)에서 이름 ID를 실제 사용자 ID와 매핑해야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. 요구 사항에 따라 **서명** 옵션을 수정하려면 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![이미지](common/edit-certificate.png) 

    ![이미지](./media/workday-tutorial/signing-option.png)

    a. **서명 옵션**에 대해 **SAML 응답 및 어설션 서명**을 선택합니다.

    b. 페이지 맨 아래에 있는 **저장**

1. **Workable 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Workday에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Workday**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-workday"></a>Workday 구성

1. 다른 웹 브라우저 창에서 Workday 회사 사이트에 관리자 권한으로 로그인합니다.

2. **검색 상자**에서 홈페이지 왼쪽 상단의 이름 **테넌트 설정 편집 - 보안**으로 검색합니다.

    ![테넌트 보안 편집](./media/workday-tutorial/IC782925.png "테넌트 보안 편집")

3. **리디렉션 URL** 섹션에서 다음 단계를 수행합니다.

    ![리디렉션 URL](./media/workday-tutorial/IC7829581.png "리디렉션 URL")

    a. **행 추가**를 클릭합니다.

    b. Azure Portal의 **Workday 설정** 섹션에서 복사한 **로그인 URL**을 **로그인 리디렉션 URL**, **시간 제한 리디렉션 URL** 및 **모바일 리디렉션 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal의 **Workday 설정** 섹션에서 복사한 **로그아웃 URL**을 **로그아웃 리디렉션 URL** 텍스트 상자에 붙여넣습니다.

    d. **환경에 사용됨** 텍스트 상자에서 환경 이름을 선택합니다.  

   > [!NOTE]
   > 테넌트 URL의 값에 연결된 환경 특성의 값은:  
   > -Workday 테넌트 URL의 도메인 이름이 impl로 시작되는 경우(예: *https://www.myworkday.com/"tenant"/login-saml2.htmld*) **Environment** 특성이 Implementation으로 설정되어 있어야 합니다.  
   > -도메인 이름이 다르게 시작되면 일치하는 **환경** 값을 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의해야 합니다.

4. **SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![SAML 설정](./media/workday-tutorial/IC782926.png "SAML 설정")

    a.  **SAML 인증 사용**을 선택합니다.

    b.  **행 추가**를 클릭합니다.

5. **SAML ID 공급자** 섹션에서 다음 단계를 수행합니다.

    ![SAML ID 공급자](./media/workday-tutorial/IC7829271.png "SAML ID 공급자")

    a. **ID 공급 기업 이름** 텍스트 상자에 공급 기업 이름(예: *SPInitiatedSSO*)을 입력합니다.

    b. Azure Portal의 **Workday 설정** 섹션에서 **Azure AD 식별자** 값을 복사한 다음, **발급자** 텍스트 상자에 붙여넣습니다.

    ![SAML ID 공급자](./media/workday-tutorial/IC7829272.png "SAML ID 공급자")

    다. Azure Portal의 **Workday 설정** 섹션에서 **로그아웃 URL** 값을 복사한 다음, **로그아웃 응답 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal의 **Workday 설정** 섹션에서 **로그인 URL** 값을 복사한 다음, **IdP SSO 서비스 URL** 텍스트 상자에 붙여넣습니다.

    e. **환경에 사용됨** 텍스트 상자에서 환경 이름을 선택합니다.

    f. **ID 공급자 공개 키 인증서**를 클릭한 다음 **만들기**를 클릭합니다.

    ![만들기](./media/workday-tutorial/IC782928.png "생성")

    g. **x509 공개 키 만들기**를 클릭합니다.

    ![만들기](./media/workday-tutorial/IC782929.png "생성")

6. **x509 공개 키 보기** 섹션에서 다음 단계를 수행합니다.

    ![x509 공개 키 보기](./media/workday-tutorial/IC782930.png "x509 공개 키 보기")

    a. **이름** 텍스트 상자에 인증서 이름(예: *PPE\_SP*)을 입력합니다.

    b. **유효 시작** 텍스트 상자에 인증서의 유효 시작 특성 값을 입력합니다.

    다.  **유효 만료** 텍스트 상자에 인증서의 유효 만료 특성 값을 입력합니다.

    > [!NOTE]
    > 유효 시작일과 유효 만료일은 다운로드한 인증서를 두 번 클릭하여 확인할 수 있습니다.  날짜는 **세부 정보** 탭에 나열됩니다.
    >
    >

    d.  메모장에서 Base-64로 인코딩된 인증서를 열고 콘텐츠를 복사합니다.

    e.  **인증서** 텍스트 상자에 클립보드의 내용을 붙여 넣습니다.

    f.  **확인**을 클릭합니다.

7. 다음 단계를 수행합니다.

    ![SSO 구성](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO 구성")

    a.  **서비스 공급자 ID** 텍스트 상자에 **http://www.workday.com** 을 입력합니다.

    b. **SP에서 시작한 인증 요청을 Deflate하지 않음**을 선택합니다.

    다. **인증 요청 서명 메서드**로 **SHA256**을 선택합니다.

    ![인증 요청 서명 메서드](./media/workday-tutorial/WorkdaySSOConfiguration.png "인증 요청 서명 메서드")

    d. **확인**을 클릭합니다.

    ![확인](./media/workday-tutorial/IC782933.png "확인")

    > [!NOTE]
    > Single Sign-On을 올바르게 설정했는지 확인하세요. 잘못된 설정으로 Single Sign-On을 활성화한 경우 자격 증명을 사용하여 애플리케이션을 입력하고 잠글 수 없을 수도 있습니다. 이 경우 Workday는 사용자가 [Your Workday URL]/login.flex?redirect=n 형식으로 해당 일반 사용자 이름 및 암호를 사용하여 로그인할 수 있는 백업 로그인 url을 제공합니다.

### <a name="create-workday-test-user"></a>Workday 테스트 사용자 만들기

이 섹션에서는 Workday에 B.Simon이라는 사용자를 만듭니다. Workday 플랫폼에서 사용자를 추가하려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Workday 타일을 선택하면 SSO를 설정한 Workday에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Workday 체험](https://aad.portal.azure.com)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Workday를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/protect-workday)