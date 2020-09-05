---
title: '자습서: TMWS(Trend Micro Web Security)와 Azure AD SSO 통합'
description: Azure Active Directory와 TMWS(Trend Micro Web Security) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: f2a6598cc28c39719d73be333bd74c24fce9371b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551905"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>자습서: TMWS(Trend Micro Web Security)와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TMWS(Trend Micro Web Security)를 통합하는 방법에 대해 알아봅니다. Azure AD와 TMWS를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 TMWS에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 TMWS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO를 사용하도록 설정된 TMWS 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* TMWS에서 SP 시작 SSO를 지원합니다.
* TMWS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아 보려면 [앱에 대한 조건부 액세스 앱 제어 온보드 및 배포](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)를 참조하세요.

## <a name="add-tmws-from-the-gallery"></a>갤러리에서 TMWS 추가

TMWS가 Azure AD에 통합되도록 구성하려면 갤러리의 TMWS를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 또는 학교 계정, 개인 Microsoft 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **TMWS(Trend Micro Web Security)** 를 입력합니다.
1. 검색 결과에서 **TMWS(Trend Micro Web Security)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>TMWS에 대한 Azure AD SSO 구성 및 테스트

B.Simon이라는 테스트 사용자를 사용하여 TMWS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 TMWS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TMWS에서 Azure AD SSO를 구성하고 테스트하려면 다음 기본 단계를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 [Azure AD SSO를 구성](#configure-azure-ad-sso)합니다.
    1. [Azure AD 사용자를 만들어](#create-an-azure-ad-test-user) Azure AD Single Sign-On을 테스트합니다.
    1. TMWS에 대한 액세스 권한을 [Azure AD 테스트 사용자에게 부여](#grant-the-azure-ad-test-user-access-to-tmws)합니다.
    1. [Azure AD에서 사용자 및 그룹 동기화 설정을 구성](#configure-user-and-group-synchronization-settings-in-azure-ad)합니다.
1. 애플리케이션 쪽에서 [TMWS SSO를 구성](#configure-tmws-sso)합니다.
1. [SSO를 테스트](#test-sso)하여 구성을 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com/)의 **TMWS(Trend Micro Web Security)** 애플리케이션 통합 페이지에 있는 **관리** 섹션에서 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 펜 단추를 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 설정 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션의 다음 상자에서 값을 입력합니다.

    a. **식별자(엔터티 ID)** 상자에서 다음 패턴의 URL을 입력합니다.

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. **회신 URL** 텍스트 상자에서 다음 URL을 입력합니다.

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 이전 단계의 식별자 값은 입력해야 하는 값이 아닙니다. 실제 식별자를 사용해야 합니다. 이 값은 **관리 > 디렉터리 서비스**에 있는 Azure AD에 대한 **인증 방법** 페이지의 **Azure 관리 포털에 대한 서비스 공급자 설정** 섹션에서 가져올 수 있습니다.

1. TMWS에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성을 보여 줍니다.

    ![기본 특성](common/default-attributes.png)

1. 이전 스크린샷의 특성 외에도 TMWS에는 SAML 응답에서 다시 전달되어야 하는 둘 이상의 특성이 추가로 필요합니다. 이러한 특성은 다음 표에 나와 있습니다. 특성은 미리 채워져 있지만 요구 사항에 맞게 변경할 수 있습니다.
    
    | 속성 | 원본 특성|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. 이 인증서 이름 옆에 있는 **다운로드** 링크를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **TMWS(Trend Micro Web Security) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 상자에서 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 상자에서 ***username *@* companydomain *.* extension***을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Azure AD 테스트 사용자에게 TMWS에 대한 액세스 권한 부여

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 TMWS에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **TMWS(Trend Micro Web Security)** 를 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹 선택](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon**을 선택하고, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택한 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Azure AD에서 사용자 및 그룹 동기화 설정 구성

1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

1. **관리** 아래에서 **앱 등록**을 선택한 다음, **모든 애플리케이션** 아래에서 새 엔터프라이즈 애플리케이션을 선택합니다.

1. **관리**에서 **인증서 및 암호**를 선택합니다.

1. **클라이언트 암호** 영역에서 **새 클라이언트 암호**를 선택합니다.

1. **클라이언트 암호 추가** 화면에서 필요에 따라 설명을 추가하고, 이 클라이언트 암호의 만료 기간을 선택한 다음, **추가**를 선택합니다. 새 클라이언트 암호는 **클라이언트 암호** 영역에 표시됩니다.

1. 클라이언트 암호 값을 기록합니다. 이 값은 나중에 TMWS에 입력합니다.

1. **관리** 아래에서 **API 권한**을 선택합니다. 

1. **API 권한** 창에서 **권한 추가**를 선택합니다.

1. **API 권한 요청** 창의 **Microsoft API** 탭에서 **Microsoft Graph**, **애플리케이션 권한**을 차례로 선택합니다.

1. 다음 권한을 찾아서 추가합니다. 

    * Group.Read.All
    * User.Read.All

1. **권한 추가**를 선택합니다. 설정이 저장되었는지 확인하는 메시지가 표시됩니다. 새 권한이 **API 권한** 창에 표시됩니다.

1. **동의 허용** 영역에서  ***관리자 계정*에 대한 관리자 동의 허용(기본 디렉터리)** , **예**를 차례로 선택합니다. 요청된 권한에 대한 관리자 동의가 부여되었는지 확인하는 메시지가 표시됩니다.

1. **개요**를 선택합니다. 

1. 오른쪽 창에 표시되는 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID**를 기록합니다. 이 정보는 나중에 TMWS에 입력합니다. 또한 **Azure Active Directory > 관리** 아래에서 **사용자 지정 도메인 이름**을 선택하고, 오른쪽 창에 표시되는 도메인 이름을 기록할 수 있습니다.

## <a name="configure-tmws-sso"></a>TMWS SSO 구성

애플리케이션 쪽에서 TMWS SSO를 구성하려면 다음 단계를 완료합니다.

1. TMWS 관리 콘솔에 로그인하고, **Administration(관리)**  > **USERS & AUTHENTICATION(사용자 및 인증)**  > **Directory Services(디렉터리 서비스)** 로 차례로 이동합니다.

1. 화면의 위쪽 영역에서 **here(여기)** 를 선택합니다.

1. **Authentication Method(인증 방법)** 페이지에서 **Azure AD**를 선택합니다.

1. **On(켜기)** 또는 **Off(끄기)** 를 선택하여 데이터가 TMWS와 동기화되지 않은 경우 조직의 AD 사용자가 TMWS를 통해 웹 사이트를 방문할 수 있도록 허용할지 여부를 구성합니다.

    > [!NOTE]
    > Azure AD에서 동기화되지 않은 사용자는 알려진 TMWS 게이트웨이 또는 조직의 전용 포트를 통해서만 인증될 수 있습니다.

1. **Identity Provider Settings(ID 공급자 설정)** 섹션에서 다음 단계를 완료합니다.

    a. Azure Portal에서 복사한 **로그인 URL** 값을 **Service URL(서비스 URL)** 상자에 입력합니다.

    b. Azure Portal의 **user.onpremisessamaccountname** 원본 특성과 함께 **사용자 클레임 이름**을 **Logon name attribute(로그온 이름 특성)** 상자에 붙여넣습니다.

    다. Azure Portal에서 다운로드한 **인증서(Base64)** 를 **Public SSL certificate(퍼블릭 SSL 인증서)** 상자에 사용합니다.

1. **Synchronization Setting(동기화 설정)** 섹션에서 다음 단계를 완료합니다.

    a. Azure Portal의 **디렉터리(테넌트) ID** 또는 **사용자 지정 도메인 이름** 값을 **Tenant(테넌트)** 상자에 사용합니다.

    b. Azure Portal의 **애플리케이션(클라이언트) ID** 값을 **Application ID(애플리케이션 ID)** 상자에 사용합니다.

    다. Azure Portal의 **클라이언트 암호**를 **Client secret(클라이언트 암호)** 상자에 입력합니다.

    d. 수동으로 또는 일정에 따라 Azure AD와 동기화할 **Synchronization schedule(동기화 일정)** 을 선택합니다. **Manually(수동)** 를 선택하는 경우 Active Directory 사용자 정보가 변경될 때마다 **Directory Services** 페이지로 돌아가서 TMWS의 정보가 최신 상태로 유지되도록 동기화를 수동으로 수행해야 합니다.

    e. **Test Connection(연결 테스트)** 을 선택하여 Azure AD 서비스가 성공적으로 연결되는지 여부를 확인합니다.
    
    f. **저장**을 선택합니다.
 
 > [!NOTE]
 > Azure AD를 사용하여 TMWS를 구성하는 방법에 대한 자세한 내용은 [TMWS에서 Azure AD 설정 구성](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)을 참조하세요.

## <a name="test-sso"></a>SSO 테스트 

Azure AD 서비스가 구성되고 Azure AD가 사용자 인증 방법으로 지정되면 TMWS 프록시 서버에 로그온하여 설정을 확인할 수 있습니다. Azure AD 로그인에서 계정이 확인되면 인터넷을 방문할 수 있습니다.

> [!NOTE]
> TMWS는 Azure AD 포털의 **개요** > **Single Sign-On** > **SAML로 Single Sign-On 설정** > **새 엔터프라이즈 애플리케이션 테스트** 아래에서 Single Sign-On 테스트를 지원하지 않습니다.

1. 브라우저에서 모든 쿠키를 지운 다음, 브라우저를 다시 시작합니다. 

1. 브라우저에서 TMWS 프록시 서버를 가리킵니다. 자세한 내용은 [PAC 파일을 사용한 트래픽 전달](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)을 참조하세요.

1. 인터넷 웹 사이트를 방문합니다. TMWS에서 TMWS 종속 포털로 안내합니다.

1. Active Directory 계정(형식: *domain*\\*sAMAccountName* 또는 *sAMAccountName*@*domain*), 이메일 주소 또는 UPN을 지정한 다음, **로그온**을 선택합니다. TMWS에서 사용자를 Azure AD 로그인 창으로 보냅니다.

1. Azure AD 로그인 창에서 Azure AD 계정 자격 증명을 입력합니다. 이제 TMWS에 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Trend Micro Web Security 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 제어를 사용하여 Trend Micro Web Security를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

