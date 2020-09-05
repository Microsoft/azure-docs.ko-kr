---
title: '자습서: 자습서: SAP NetWeaver와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 SAP NetWeaver 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.openlocfilehash: 38ac4f1bf6a1dd4656b4e7d5783051f3b381940c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546817"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>자습서: SAP NetWeaver와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP NetWeaver를 연결하는 방법에 대해 알아봅니다. Azure AD와 SAP NetWeaver를 연결하면 다음을 수행할 수 있습니다.

* Azure AD에서 SAP NetWeaver에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP NetWeaver에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP NetWeaver SSO(Single Sign-On)를 사용하도록 설정된 구독
* SAP NetWeaver V7.20 필수

## <a name="scenario-description"></a>시나리오 설명

SAP NetWeaver는 **SAML**(**SP 시작 SSO**) 및 **OAuth**를 모두 지원합니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

> [!NOTE]
> 조직 요구 사항에 따라 SAML 또는 OAuth에서 애플리케이션을 구성합니다. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>갤러리에서 SAP NetWeaver 추가

SAP NetWeaver의 Azure AD 통합을 구성하려면 갤러리의 SAP NetWeaver를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **SAP NetWeaver**를 입력합니다.
1. 결과 패널에서 **SAP NetWeaver**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>SAP NetWeaver에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 SAP NetWeaver에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP NetWeaver의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP NetWeaver에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAML을 사용하여 SAP NetWeaver 구성](#configure-sap-netweaver-using-saml)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. **[SAP NetWeaver 테스트 사용자 만들기](#create-sap-netweaver-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP NetWeaver에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.
1. **[OAuth에 대한 SAP NetWeaver 구성](#configure-sap-netweaver-for-oauth)** - 애플리케이션 쪽에서 OAuth 설정을 구성합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SAP NetWeaver에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. 새 웹 브라우저 창을 열고, SAP NetWeaver 회사 사이트에 관리자 권한으로 로그인합니다.

1. **http** 및 **https** 서비스가 활성화되고 적절한 포트가 **SMICM** T-코드에서 할당되었는지 확인합니다.

1. SSO가 필요한 SAP 시스템(T01)의 비즈니스 클라이언트에 로그온하고, HTTP 보안 세션 관리를 활성화합니다.

    a. 트랜잭션 코드 **SICF_SESSIONS**로 이동합니다. 현재 값을 사용하여 모든 관련 프로필 매개 변수를 표시합니다. 아래처럼 보입니다.
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > 조직 요구 사항에 따라 위의 매개 변수를 조정합니다. 위의 매개 변수는 여기에서 표시용으로만 제공됩니다.

    b. SAP 시스템의 인스턴스/기본 프로필에서 매개 변수를 조정해야 하는 경우 SAP 시스템을 다시 시작합니다.

    다. HTTP 보안 세션을 사용하도록 설정하려면 관련 클라이언트를 두 번 클릭합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 아래 SICF 서비스 활성화:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. SAP 시스템[T01/122]의 비즈니스 클라이언트에서 트랜잭션 코드 **SAML2**로 이동합니다. 브라우저에서 사용자 인터페이스가 열립니다. 이 예제에서는 SAP 비즈니스 클라이언트로 122를 가정합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. 사용자 이름 및 암호를 제공하여 사용자 인터페이스에 입력하고 **편집**을 클릭합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. **공급자 이름**을 T01122에서 `http://T01122`로 바꾸고 **저장**을 클릭합니다.

    > [!NOTE]
    > 기본적으로 공급자 이름은 `<sid><client>` 형식이지만 Azure AD는 `<protocol>://<name>`의 형식으로 이름을 예상합니다. 공급자 이름을 `https://<sid><client>`로 유지하기 위해 Azure AD에서 여러 SAP NetWeaver ABAP 엔진을 구성하도록 허용하는 것이 좋습니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **서비스 공급자 메타데이터 생성**: SAML 2.0 사용자 인터페이스에서 **로컬 공급자** 및 **신뢰할 수 있는 공급자** 설정 구성을 완료한 후 다음 단계는 서비스 공급자의 메타데이터 파일을 생성하는 것입니다(모든 설정, 인증 컨텍스트 및 SAP의 다른 구성 포함). 이 파일이 생성되면 Azure AD에서 업로드해야 합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. **로컬 공급자 탭**으로 이동합니다.

    b. **메타데이터**를 클릭합니다.

    다. 컴퓨터에 생성된 **메타데이터 XML 파일**을 저장하고 **기본 SAML 구성** 섹션에 업로드하여 Azure Portal에서 **식별자** 및 **회신 URL** 값을 자동으로 채웁니다.

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAP NetWeaver** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭하여 이전에 가져온 **서비스 공급자 메타데이터 파일**을 업로드합니다.

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    다. 메타데이터 파일이 정상적으로 업로드되면 아래 그림과 같이 **기본 SAML 구성** 섹션에서 **식별자** 및 **회신 URL** 값이 자동으로 입력됩니다.

    d. **로그인 URL** 텍스트 상자에서 `https://<your company instance of SAP NetWeaver>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 인스턴스에 대해 잘못된 회신 URL이 구성되었다는 오류를 보고하는 고객은 많지 않습니다. 이러한 오류 메시지가 표시되는 경우 다음 PowerShell 스크립트를 해결 방법으로 사용하여 인스턴스에 대해 올바른 회신 URL을 설정할 수 있습니다.
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal 개체 ID는 먼저 직접 설정하거나 여기에서 전달할 수 있습니다.

1. SAP NetWeaver 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    a. **편집** 아이콘을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![이미지](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    다. **매개 변수 1** 목록에서 **user.userprincipalname**을 선택합니다.

    d. **저장**을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

1. **SAP NetWeaver 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP NetWeaver에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **SAP NetWeaver**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-netweaver-using-saml"></a>SAML을 사용하여 SAP NetWeaver 구성

1. SAP 시스템에 로그인하여 트랜잭션 코드 SAML2로 이동합니다. SAML 구성 화면으로 새 브라우저 창이 열립니다.

2. 신뢰할 수 있는 ID 공급자(Azure AD)에 대한 엔드포인트 구성은 **신뢰할 수 있는 공급자** 탭으로 이동합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. **추가** 키를 누르고 바로 가기 메뉴에서 **메타데이터 파일 업로드**를 선택합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. 다음 화면에서 별칭 이름을 입력합니다. 예를 들어 aadsts를 입력하고 **Next**(다음)를 눌러 계속합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. **다이제스트 알고리즘**은 **SHA-256**이어야 하며 변경이 필요하지 않으며 **다음**을 누릅니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. **Single Sign-On 엔드포인트**에서 **HTTP POST**를 사용하고 **다음**을 클릭하여 계속합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. **단일 로그아웃 엔드포인트**에서 **HTTPRedirect**를 선택하고 **다음**을 클릭하여 계속합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. **아티팩트 엔드포인트**에서 **다음**을 눌러 계속합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. **인증 요구 사항**에서 **마침**을 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. 탭 **신뢰할 수 있는 공급자** > **ID 페더레이션**(화면 맨 아래에서)으로 이동합니다. **편집**을 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. **ID 페더레이션** 탭(아래쪽 창) 아래에서 **추가**를 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. 팝업 창의 **Supported NameID formats**(지원되는 NameID 형식)에서 **Unspecified**(지정되지 않음)를 선택하고 [확인]을 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. **사용자 ID 원본** 및 **사용자 ID 매핑 모드** 값은 SAP 사용자와 Azure AD 클레임 간의 링크를 결정합니다.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>시나리오: 사용자를 Azure AD 사용자 매핑으로 SAP합니다.

    a. SAP의 NameID 세부 정보 스크린샷

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Azure AD에서 필요한 클레임을 언급하는 스크린샷

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>시나리오: SU01에 구성된 이메일 주소를 기반으로 SAP 사용자 ID를 선택합니다. 이 경우 이메일 ID는 SSO를 필요로 하는 각 사용자에 대해 su01에서 구성되어야 합니다.

    a.  SAP의 NameID 세부 정보 스크린샷

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Azure AD에서 필요한 클레임을 언급하는 스크린샷

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/claimsaad2.png)

15. **저장**을 클릭한 다음, **사용**을 클릭하여 ID 공급자를 사용하도록 설정합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/configuration1.png)

16. 메시지가 표시되면 **확인**을 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver 테스트 사용자 만들기

    이 섹션에서는 SAP NetWeaver에서 B.Simon이라는 사용자를 만듭니다. SAP NetWeaver 플랫폼에서 사용자를 추가하려면 사내 SAP 전문가 팀과 작업하거나 조직 SAP 파트너와 함께 작업하세요.

## <a name="test-sso"></a>SSO 테스트

1. ID 공급자 Azure AD가 활성화되면 아래 URL에 액세스하여 SSO를 확인합니다(사용자 이름 및 암호에 대한 메시지가 표시되지 않음).

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (또는) 아래의 URL 사용

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > sapurl을 실제 SAP 호스트 이름으로 바꿉니다.

2. 위의 URL은 아래 언급한 화면으로 안내합니다. 아래 페이지에 연결할 수 있는 경우 Azure AD SSO 설정이 성공적으로 수행된 것입니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/testingsso.png)

3. 사용자 이름 및 암호에 대한 메시지가 표시되는 경우 아래 URL을 사용하여 추적을 활성화하여 문제를 진단하세요.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>OAuth에 대한 SAP NetWeaver 구성

1. SAP에서 문서화된 프로세스는 [NetWeaver Gateway Service 사용 및 OAuth 2.0 범위 만들기](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation) 위치에서 제공됩니다.

2. SPRO로 이동하여 **Activate and Maintain services**(서비스 활성화 및 유지 관리)를 찾습니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth01.png)

3. 다음 예에서는 OAuth를 사용하여 OData 서비스(`DAAG_MNGGRP`)를 Azure AD SSO에 연결하려고 합니다. `DAAG_MNGGRP` 서비스에 대한 기술 서비스 이름 검색을 사용하고, 아직 활성 상태가 아닌 경우 이를 활성화합니다(ICF 노드 탭에서 `green` 상태 찾기). 시스템 별칭(서비스가 실제로 실행되고 있는 연결된 백 엔드 시스템)이 올바른지 확인합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth02.png)

    * 그런 다음, 위쪽의 단추 모음에서 **OAuth** 누름 단추를 클릭하고 `scope`를 할당합니다(기본 이름은 제공된 대로 유지).

4. 이 예에서 범위는 `DAAG_MNGGRP_001`이며, 숫자를 자동으로 추가하여 서비스 이름에서 생성됩니다. `/IWFND/R_OAUTH_SCOPES` 보고서를 사용하여 범위 이름을 변경하거나 수동으로 만들 수 있습니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > 문제가 없으므로 `soft state status is not supported` 메시지는 무시할 수 있습니다. 자세한 내용은 [여기](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)를 참조하세요.

### <a name="create-a-service-user-for-the-oauth-20-client"></a>OAuth 2.0 클라이언트에 대한 서비스 사용자 만들기

1. OAuth2는 `service ID`를 사용하여 최종 사용자에 대한 액세스 토큰을 대신 가져옵니다. OAuth 설계의 중요한 제한 사항인 `OAuth 2.0 Client ID`는 액세스 토큰을 요청할 때 OAuth 2.0 클라이언트에서 로그인에 사용하는 `username`과 동일해야 합니다. 따라서 이 예에서는 OAuth 2.0 클라이언트를 CLIENT1이라는 이름으로 등록하며, 필수 조건으로 동일한 이름(CLIENT1)의 사용자가 SAP 시스템에 있어야 하며 해당 사용자는 참조된 애플리케이션에서 사용하도록 구성해야 합니다. 

2. OAuth 클라이언트를 등록하는 경우 `SAML Bearer Grant type`을 사용합니다.

    >[!NOTE]
    >자세한 내용은 [여기](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)서 설명하는 SAML 전달자 권한 부여 유형에 대한 OAuth 2.0 클라이언트 등록을 참조하세요.

3. tcod: SU01 / CLIENT1 사용자를 `System type`으로 만들고, 암호를 할당하고, 사용자 이름을 사용하여 호출 코드에 구워야 하는 API 프로그래머에게 자격 증명을 제공해야 하는 필요에 따라 저장합니다. 프로필 또는 역할은 할당하지 않아야 합니다.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>만들기 마법사를 사용하여 새 OAuth 2.0 클라이언트 ID 등록

1. 새 **OAuth 2.0 클라이언트**를 등록하려면 **SOAUTH2** 트랜잭션을 시작합니다. 트랜잭션에는 이미 등록된 OAuth 2.0 클라이언트에 대한 개요가 표시됩니다. 이 예에서 **Create**(만들기)를 선택하여 CLIENT1이라는 이름으로 지정된 새 OAuth 클라이언트에 대한 마법사를 시작합니다.

2. T-Code: **SOAUTH2**로 이동하여 설명을 제공하고, **Next**를 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth04.png)

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth05.png)

3. 드롭다운 목록에서 이미 추가된 **SAML2 IdP – Azure AD**를 선택하고 저장합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth06.png)

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth07.png)

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth08.png)

4. 이전에 만든 `DAAG_MNGGRP_001` 범위를 추가하려면 범위 할당 아래에서 **Add**(추가)를 클릭합니다.

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth09.png)

    ![Single Sign-on 구성](./media/sapnetweaver-tutorial/oauth10.png)

5. **마침**(Finish)을 클릭합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD를 사용하여 SAP NetWeaver 사용해 보기](https://aad.portal.azure.com/)