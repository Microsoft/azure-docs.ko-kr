---
title: '자습서: SAP Fiori와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 SAP Fiori 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 4d2f6766fa32beb9cebaa8f77c04f6865e15e14d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543396"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>자습서: SAP Fiori와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Fiori를 통합하는 방법을 알아봅니다. Azure AD와 SAP Fiori를 통합하면 다음 작업을 수행할 수 있습니다.

* SAP Fiori에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SAP Fiori에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Fiori SSO(Single Sign-On) 사용 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SAP Fiori는 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> SAP Fiori에서 시작된 iFrame 인증의 경우, 자동 인증을 위해 SAML AuthnRequest에 **IsPassive** 매개 변수를 사용하는 것이 좋습니다. **IsPassive** 매개 변수에 대한 자세한 내용은 [Azure AD SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) 정보를 참조하세요.

## <a name="adding-sap-fiori-from-the-gallery"></a>갤러리에서 SAP Fiori 추가

SAP Fiori와 Azure AD의 통합을 구성하려면 갤러리의 SAP Fiori를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Fiori**를 입력합니다.
1. 결과 패널에서 **SAP Fiori**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>SAP Fiori에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 SAP Fiori에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Fiori의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Fiori에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAP Fiori SSO 구성](#configure-sap-fiori-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Fiori 테스트 사용자 만들기](#create-sap-fiori-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Fiori에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 새 웹 브라우저 창을 열고 SAP Fiori 회사 사이트에 관리자로 로그인합니다.

1. **http** 및 **https** 서비스가 활성화되고 적절한 포트가 트랜잭션 코드 **SMICM**에 할당되었는지 확인합니다.

1. SAP Business Client for SAP 시스템 **T01**에 로그인합니다. 여기서는 반드시 Single Sign-On이 필요합니다. 그런 다음, HTTP 보안 세션 관리를 활성화합니다.

    1. 트랜잭션 코드 **SICF_SESSIONS**로 이동합니다. 모든 관련 프로필 매개 변수와 현재 값이 표시됩니다. 다음 예제와 같아야 합니다.

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
        > 조직 요구 사항에 따라 매개 변수를 조정합니다. 위의 매개 변수는 예제로만 제공됩니다.

    1. 필요한 경우 SAP 시스템의 인스턴스(기본) 프로필에서 매개 변수를 조정하고 SAP 시스템을 다시 시작합니다.

    1. 해당 클라이언트를 두 번 클릭하여 HTTP 보안 세션을 사용하도록 설정합니다.

        ![SAP의 관련 프로필 매개 변수 페이지에 표시되는 현재 값](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. 다음 SICF 서비스를 활성화합니다.

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Business Client for SAP 시스템[**T01/122**]의 트랜잭션 코드 **SAML2**로 이동합니다. 구성 UI가 새 브라우저 창에서 열립니다. 이 예제에서는 Business Client for SAP 시스템 122를 사용합니다.

    ![SAP Fiori 비즈니스 클라이언트 로그인 페이지](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. 사용자 이름 및 암호를 입력한 다음, **로그온**을 선택합니다.

    ![SAP에서 ABAP 시스템 T01/122의 SAML 2.0 구성 페이지](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. **공급자 이름** 상자에서 **T01122**를 **http:\//T01122**로 바꾼 후 **저장**을 선택합니다.

    > [!NOTE]
    > 기본적으로 공급자 이름은 \<sid>\<client> 형식입니다. Azure AD에서는 \<protocol>://\<name> 형식의 이름을 요구합니다. Azure AD에서 여러 SAP Fiori ABAP 엔진을 구성할 수 있도록 공급자 이름으로 https\://\<sid>\<client>를 유지하는 것이 좋습니다.

    ![SAP에서 ABAP 시스템 T01/122의 SAML 2.0 구성 페이지에서 업데이트된 공급자 이름](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. **로컬 공급자 탭** > **메타데이터**를 선택합니다.

1. **SAML 2.0 메타데이터** 대화 상자에서 생성된 메타데이터 XML 파일을 다운로드하고 컴퓨터에 저장합니다.

    ![SAML 2.0 메타데이터 SAP 대화 상자의 메타데이터 다운로드 링크](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. [Azure Portal](https://portal.azure.com/)의 **SAP Fiori** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 창에 자동으로 채워집니다. **로그온 URL** 상자에 `https:\//\<your company instance of SAP Fiori\>` 패턴의 URL을 입력합니다.

    > [!NOTE]
    > 소수의 고객이 잘못 구성된 **회신 URL** 값과 관련된 오류를 보고합니다. 이러한 오류가 표시되면 다음 PowerShell 스크립트를 사용하여 인스턴스에 대해 올바른 회신 URL을 설정할 수 있습니다.
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > 스크립트를 실행하기 전에 `ServicePrincipal` 개체 ID를 직접 설정하거나 여기서 제공할 수 있습니다.

1. SAP Fiori 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 이러한 특성 값을 관리하려면 **SAML로 Single Sign-On 설정** 창에서 **편집**을 선택합니다.

    ![사용자 특성 창](common/edit-attribute.png)

1. **사용자 특성 및 클레임** 창에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성합니다. 그리고 나서 다음 단계를 완료합니다.

    1. **편집**을 선택하여 **사용자 클레임 관리** 창을 엽니다.

    1. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    1. **매개 변수 1** 목록에서 **user.userprincipalname**을 선택합니다.

    1. **저장**을 선택합니다.

       ![사용자 클레임 관리 창](./media/sapfiori-tutorial/nameidattribute.png)

       ![사용자 클레임 관리 창의 변환 섹션](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **SAP Fiori 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Fiori에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **SAP Fiori**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-fiori-sso"></a>SAP Fiori SSO 구성

1. SAP 시스템에 로그인하여 트랜잭션 코드 **SAML2**로 이동합니다. 새 브라우저 창에 SAML 구성 페이지가 열립니다.

1. 신뢰할 수 있는 ID 공급자(Azure AD)에 대한 엔드포인트를 구성하려면 **신뢰할 수 있는 공급자** 탭을 선택합니다.

    ![SAP의 신뢰할 수 있는 공급자 탭](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. **추가**를 선택하고 바로 가기 메뉴에서 **메타데이터 파일 업로드**를 선택합니다.

    ![SAP의 메타데이터 파일 추가 및 업로드 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다. **다음**을 선택합니다.

    ![SAP에서 업로드할 메타데이터 파일 선택](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 다음 페이지의 **별칭** 상자에 별칭 이름을 입력합니다. 예를 들어 **aadsts**를 입력합니다. **다음**을 선택합니다.

    ![SAP의 별칭 상자](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. **다이제스트 알고리즘** 상자의 값이 **SHA-256**인지 확인합니다. **다음**을 선택합니다.

    ![SAP의 다이제스트 알고리즘 값 확인](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. **Single Sign-On 엔드포인트**에서 **HTTP POST**를 선택한 후 **다음**을 선택합니다.

    ![SAP의 Single Sign-On 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. **단일 로그아웃 엔드포인트**에서 **HTTP 리디렉션**을 선택한 후 **다음**을 선택합니다.

    ![SAP의 단일 로그아웃 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. **아티팩트 엔드포인트** 아래에서 **다음**을 선택하여 계속 진행합니다.

    ![SAP의 아티팩트 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. **인증 요구 사항** 아래에서 **마침**을 선택합니다.

    ![SAP의 인증 요구 사항 옵션 및 마침 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. **신뢰할 수 있는 공급자** > **ID 페더레이션**(페이지 맨 위에 있음)을 선택합니다. **편집**을 선택합니다.

    ![SAP의 신뢰할 수 있는 공급자 및 ID 페더레이션 탭](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. **추가**를 선택합니다.

    ![ID 페더레이션이 탭의 추가 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. **지원되는 NameID 형식** 대화 상자에서 **지정되지 않음**을 선택합니다. **확인**을 선택합니다.

    ![SAP의 지원되는 NameID 형식 대화 상자 및 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    **사용자 ID 원본** 및 **사용자 ID 매핑 모드** 값은 SAP 사용자와 Azure AD 클레임 간의 링크를 결정합니다.  

    **시나리오 1**: SAP 사용자-Azure AD 사용자 매핑

    1. SAP의 **NameID 형식 "지정되지 않음"의 세부 정보** 아래에 표시되는 세부 정보를 기록해 둡니다.

        ![SAP의 NameID 형식 "지정되지 않음" 세부 정보 대화 상자](./media/sapfiori-tutorial/nameiddetails.png)

    1. Azure Portal의 **사용자 특성 및 클레임** 아래에서 Azure AD의 필수 클레임을 기록해 둡니다.

        ![Azure Portal의 사용자 특성 및 클레임 대화 상자](./media/sapfiori-tutorial/claimsaad1.png)

    **시나리오 2**: SU01에 구성된 이메일 주소를 기반으로 SAP 사용자 ID를 선택합니다. 이 경우 이메일 ID는 SSO를 필요로 하는 각 사용자에 대해 SU01에서 구성되어야 합니다.

    1.  SAP의 **NameID 형식 "지정되지 않음"의 세부 정보** 아래에 표시되는 세부 정보를 기록해 둡니다.

        ![SAP의 NameID 형식 "지정되지 않음" 세부 정보 대화 상자](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Azure Portal의 **사용자 특성 및 클레임** 아래에서 Azure AD의 필수 클레임을 기록해 둡니다.

       ![Azure Portal의 사용자 특성 및 클레임 대화 상자](./media/sapfiori-tutorial/claimsaad2.png)

1. **저장**을 선택한 다음, **사용**을 선택하여 ID 공급자를 사용하도록 설정합니다.

    ![SAP의 저장 및 사용 옵션](./media/sapfiori-tutorial/configuration1.png)

1. 메시지가 표시되면 **확인**을 선택합니다.

    ![SAP의 SAML 2.0 구성 대화 상자, 확인 옵션](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>SAP Fiori 테스트 사용자 만들기

이 섹션에서는 SAP Fiori에서 Britta Simon이라는 사용자를 만듭니다. 사내 SAP 전문가 팀 또는 조직 SAP 파트너와 협력하여 SAP Fiori 플랫폼에서 사용자를 추가합니다.

## <a name="test-sso"></a>SSO 테스트

1. SAP Fiori에서 ID 공급자 Azure AD를 활성화한 후 다음 URL 중 하나에 액세스하여 Single Sign-On을 테스트해 봅니다(사용자 이름 및 암호를 묻는 메시지가 표시되지 않아야 함).

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > *sapurl*를 실제 SAP 호스트 이름으로 바꿉니다.

1. 테스트 URL을 클릭하면 SAP의 다음 테스트 애플리케이션 페이지로 이동됩니다. 이 페이지가 열리면 Azure AD Single Sign-On이 성공적으로 설정된 것입니다.

    ![SAP의 표준 테스트 애플리케이션 페이지](./media/sapfiori-tutorial/testingsso.png)

1. 사용자 이름 및 암호를 묻는 메시지가 표시되면 추적을 사용하도록 설정하면 문제를 진단합니다. 추적을 위해 URL https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#을 사용합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 SAP Fiori 체험하기](https://aad.portal.azure.com/)
