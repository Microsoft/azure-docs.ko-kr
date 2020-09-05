---
title: '자습서: F5와 Azure Active Directory Single Sign-On 통합 | Microsoft Docs'
description: Azure Active Directory와 F5 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: 2e0e727e73fa1eff21a4b2e481738be49306676a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542984"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>자습서: F5와 Azure AD(Active Directory) SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 F5를 통합하는 방법에 대해 알아봅니다. F5를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 F5에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 F5에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* F5 SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* F5는 **SP 및 IDP** 시작 SSO를 지원합니다.
* F5 SSO는 세 가지 방법으로 구성할 수 있습니다.

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>갤러리에서 F5 추가

F5가 Azure AD에 통합되도록 구성하려면 갤러리에서 F5를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **F5**를 입력합니다.
1. 결과 패널에서 **F5**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 F5에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 F5의 관련 사용자 간에 연결 관계를 설정해야 합니다.

F5에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[F5 SSO 구성](#configure-f5-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[F5 테스트 사용자 만들기](#create-f5-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 F5에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **F5** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [F5 클라이언트 지원 팀](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **F5 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 F5에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **F5**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-f5-sso"></a>F5 SSO 구성

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성

1. 새 웹 브라우저 창을 열고, F5(고급 Kerberos) 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. 메타데이터 인증서는 나중에 설정 프로세스에서 사용할 F5(고급 Kerberos)로 가져와야 합니다. **시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록**으로 차례로 이동합니다. 오른쪽 모서리의 **가져오기**를 클릭합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure01.png)
 
1. SAML IDP를 설정하려면 **Access > Federation > SAML Service Provider > Create > From Metadata**(액세스 > 페더레이션 > SAML 서비스 공급자 > 만들기 > 메타데이터에서)로 차례로 이동합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure05.png)
 
1. 작업 3에서 업로드된 인증서 지정

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure07.png)

 1. SAML SP를 설정하려면 **Access > Federation > SAML Service Federation > Local SP Services > Create**(액세스 > 페더레이션 > SAML 서비스 페더레이션 > 로컬 SP 서비스 > 만들기)로 차례로 이동합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure08.png)
 
1. **확인**을 클릭합니다.

1. SP 구성을 선택하고 **Bind/UnBind IdP Connectors**(IdP 커넥터 바인딩/바인딩 해제)를 클릭합니다.

     ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. **Add New Row**(새 행 추가)를 클릭하고, 이전 단계에서 만든 **External IdP connector**(외부 IdP 커넥터)를 선택합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Kerberos SSO를 구성하려면 **Access > Single Sign-on > Kerberos**(액세스 > Single Sign-On > Kerberos)로 차례로 이동합니다.

    >[!Note]
    >Kerberos 위임 계정을 만들고 지정해야 합니다. KCD 섹션 참조(변수 참조는 부록 참조)

    •   사용자 이름 원본: `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   사용자 영역 원본: `session.logon.last.domain`

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure11.png)

1. 액세스 프로필을 구성하려면 **Access > Profile/Policies > Access Profile (per session policies)** (액세스 > 프로필/정책 > 액세스 프로필(세션 정책당))로 차례로 이동합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. 새 노드를 추가하려면 **Local Traffic > Nodes > Node List > +** (로컬 트래픽 > 노드 > 노드 목록 > +)로 차례로 이동합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure21.png)
 
1. 새 풀을 만들려면 **Local Traffic > Pools > Pool List > Create**(로컬 트래픽 > 풀 > 풀 목록 > 만들기)로 차례로 이동합니다.

     ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure22.png)

 1. 새 가상 서버를 만들려면 **Local Traffic > Virtual Servers > Virtual Server List > +** (로컬 트래픽 > 가상 서버 > 가상 서버 목록 > +)로 차례로 이동합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure23.png)

1. 이전 단계에서 만든 액세스 프로필을 지정합니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos 위임 설정 

>[!Note]
>자세한 내용은 [여기](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)를 참조하세요.

* **1단계: 위임 계정 만들기**

    * 예제
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **2단계: SPN 설정(APM 위임 계정에서)**

    *  예제
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **3단계: SPN 위임(App Service 계정의 경우)**

    * F5 위임 계정에 대한 적절한 위임을 설정합니다.
    * 아래 예에서는 APM 위임 계정이 FRP-App1.superdemo.live 앱용 KCD에 대해 구성되어 있습니다.

        ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure25.png)

1. 위의 참조 문서에 나와 있는 [이 부분](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)에서 설명한 대로 세부 정보를 제공합니다.

1. 부록 - SAML – F5 BIG-IP 변수 매핑은 아래와 같습니다.

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5(고급 Kerberos) 구성](./media/advance-kerbf5-tutorial/configure27.png) 

1. 다음은 기본 SAML 특성에 대한 전체 목록입니다. GivenName은 다음 문자열을 사용하여 표시됩니다.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| 세션 | attribute |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>F5 테스트 사용자 만들기

이 섹션에서는 F5에서 B.Simon이라는 사용자를 만듭니다.  [F5 클라이언트 지원 팀](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)과 협력하여 사용자를 F5 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 F5 타일을 클릭하면 SSO를 설정한 F5에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 F5 사용해 보기](https://aad.portal.azure.com/)

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

