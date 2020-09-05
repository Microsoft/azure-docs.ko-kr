---
title: '자습서: Confluence SAML SSO by Microsoft와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Confluence SAML SSO by Microsoft 사이에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9238a65643c9ff24166e9a9b9a05e8c924abfb37
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544481"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>자습서: Confluence SAML SSO by Microsoft와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Confluence SAML SSO by Microsoft를 통합하는 방법을 알아봅니다. Azure AD와 Confluence SAML SSO by Microsoft를 통합하면 다음 작업을 수행할 수 있습니다.

* Confluence SAML SSO by Microsoft에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Confluence SAML SSO by Microsoft에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="description"></a>설명:

Atlassian Confluence 서버와 함께 Microsoft Azure Active Directory 계정을 사용하여 Single Sign-On을 사용하도록 설정합니다. 이러한 방식으로 모든 조직 사용자는 Azure AD 자격 증명을 사용하여 Confluence 애플리케이션에 로그인할 수 있습니다. 이 플러그 인은 페더레이션에 대해 SAML 2.0을 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

Confluence SAML SSO by Microsoft와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Confluence 서버 애플리케이션이 Windows 64비트 서버(온-프레미스 또는 클라우드 IaaS 인프라)에 설치되어 있어야 합니다.
- Confluence 서버에서 HTTPS를 사용해야 합니다.
- 지원되는 Confluence 플러그 인 버전은 아래 섹션에 설명되어 있습니다.
- Confluence 서버가 인터넷에 연결되어 있고 인증을 위해 특히 Azure AD 로그인 페이지에 접속되고 Azure AD에서 토큰을 받을 수 있어야 합니다.
- Confluence에 관리자 자격 증명이 설정되어 있어야 합니다.
- Confluence에서 WebSudo를 사용하지 않아야 합니다.
- Confluence 서버 애플리케이션에서 생성된 테스트 사용자

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 Confluence의 프로덕션 환경을 사용하는 것은 권장되지 않습니다. 애플리케이션 개발 또는 스테이징 환경에서 통합을 먼저 테스트 한 다음 프로덕션 환경을 사용하세요.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

시작하려면 다음 항목이 필요합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Confluence SAML SSO by Microsoft SSO(Single Sign-On) 사용 구독

## <a name="supported-versions-of-confluence"></a>지원되는 Confluence 버전

현재 기준으로 다음 버전의 Confluence가 지원됩니다.

- Confluence: 5.0 ~ 5.10
- Confluence: 6.0.1 ~ 6.15.9
- Confluence: 7.0.1 ~ 7.6.1

> [!NOTE]
> Confluence 플러그 인도 Ubuntu 버전 16.04에서 작동한다는 점에 유의하세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Confluence SAML SSO by Microsoft에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>갤러리에서 Confluence SAML SSO by Microsoft 추가

Confluence SAML SSO by Microsoft가 Azure AD에 통합되도록 구성하려면 갤러리에서 Confluence SAML SSO by Microsoft를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Confluence SAML SSO by Microsoft**를 입력합니다.
1. 결과 패널에서 **Confluence SAML SSO by Microsoft**를 선택하고 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Confluence SAML SSO by Microsoft에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Confluence SAML SSO by Microsoft에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Confluence SAML SSO by Microsoft의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Confluence SAML SSO by Microsoft에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Confluence SAML SSO by Microsoft SSO 구성](#configure-confluence-saml-sso-by-microsoft-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Confluence SAML SSO by Microsoft 테스트 사용자 만들기](#create-confluence-saml-sso-by-microsoft-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Confluence SAML SSO by Microsoft에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Confluence SAML SSO by Microsoft** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<domain:port>/plugins/servlet/saml/auth` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에서 `https://<domain:port>/` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://<domain:port>/plugins/servlet/saml/auth` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 명명된 URL인 경우 포트는 선택 사항입니다. 이러한 값은 Confluence 플러그 인 구성 중에 수신되며 자습서의 뒷부분에 설명되어 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 Confluence SAML SSO by Microsoft에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Confluence SAML SSO by Microsoft**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Confluence SAML SSO by Microsoft SSO 구성

1. 다른 웹 브라우저 창에서 Confluence 인스턴스에 관리자로 로그인합니다.

1. 마우스로 선 위를 가리키고 **추가 기능**을 클릭합니다.

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/addon1.png)

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=56503)에서 플러그 인을 다운로드합니다. **업로드 추가 기능** 메뉴를 사용하여 Microsoft에서 제공하는 플러그 인을 수동으로 업로드합니다. 플러그 인 다운로드에는 [Microsoft 서비스 계약](https://www.microsoft.com/servicesagreement/)이 적용됩니다.

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/addon12.png)

1. Confluence 역방향 프록시 시나리오 또는 부하 분산 장치 시나리오를 실행하려면 다음 단계를 수행합니다.

    > [!NOTE]
    > 아래 지침에 따라 먼저 서버를 구성한 다음, 플러그 인을 설치해야 합니다.

    a. JIRA 서버 애플리케이션의 **server.xml** 파일에 **connector** 포트의 다음 특성을 추가합니다.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. 프록시/부하 분산 장치에 따라 **시스템 설정**에서 **기준 URL**을 변경합니다.

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. 플러그 인이 설치되면 **추가 기능 관리** 섹션의 **사용자가 설치한** 추가 기능 섹션에 표시됩니다. **구성**을 클릭하여 새 플러그 인을 구성합니다.

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/addon15.png)

1. 구성 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > 메타데이터를 확인하는 데 오류가 없도록 앱에 매핑된 인증서가 하나만 있는지 확인합니다. 인증서가 여러 개 있으면 메타데이터를 확인할 때 관리자에게 오류가 표시됩니다.

    1. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣고 **해결** 단추를 클릭합니다. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

    1. **식별자, 회신 URL 및 로그인 URL** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자, 회신 URL 및 로그인 URL** 텍스트 상자에 각각 붙여넣습니다.

    1. **Login Button Name**(로그인 단추 이름)에 조직이 사용자의 로그인 화면에 표시하려는 단추의 이름을 입력합니다.
    
    1. **로그인 단추 설명**의 조직에서 사용자가 로그인 화면에 표시하려는 단추에 대한 설명을 입력합니다.

    1. **SAML 사용자 ID 위치**에서 **사용자 ID는 Subject 문의 NameIdentifier 요소에 있습니다.** 또는 **사용자 ID는 Attribute 요소에 있습니다.** 를 선택합니다.  이 ID는 Confluence 사용자 ID여야 합니다. 사용자 ID가 일치하지 않으면 시스템에서 사용자 로그인을 허용하지 않습니다. 

       > [!Note]
       > 기본 SAML 사용자 ID 위치는 이름 식별자입니다. 이것을 특성 옵션으로 변경하고 적절한 특성 이름을 입력할 수 있습니다.

    1. **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.) 옵션을 선택하는 경우 **특성 이름** 텍스트 상자에 사용자 ID가 필요한 특성의 이름을 입력합니다. 

    1. Azure AD에서 페더레이션된 도메인(예: ADFS 등)을 사용하는 경우 **Enable Home Realm Discovery**(홈 영역 검색 사용) 옵션을 클릭하고 **도메인 이름**을 구성합니다.

    1. **도메인 이름**에 ADFS 기반 로그인인 경우 여기에 도메인 이름을 입력합니다.

    1. 사용자가 Confluence에서 로그아웃할 때 Azure AD에서 로그아웃하려면 **Single Sign-Out 사용**을 선택합니다. 

    1. Azure AD 자격 증명을 통해서만 로그인하려면 **Force Azure Login** 확인란을 활성화합니다.

       > [!Note]
       > Azure 강제 로그인을 활성화한 경우 로그인 페이지에서 관리자 로그인에 대한 기본 로그인 양식을 사용하려면 브라우저 URL에 쿼리 매개 변수를 추가합니다.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. **저장** 단추를 클릭하여 설정을 저장합니다.

       > [!NOTE]
       > 설치 및 문제 해결에 대한 자세한 내용은 [MS Confluence SSO 커넥터 관리자 가이드](../ms-confluence-jira-plugin-adminguide.md)를 참조하세요. [FAQ](../ms-confluence-jira-plugin-faq.md)도 도움이 될 것입니다.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft 테스트 사용자 만들기

Azure AD 사용자가 Confluence 온-프레미스 서버에 로그인할 수 있게 하려면 사용자를 Confluence SAML SSO by Microsoft에 프로비저닝해야 합니다. Confluence SAML SSO by Microsoft의 경우 프로비전이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Confluence 온-프레미스 서버에 관리자로 로그인합니다.

1. 마우스로 선 위를 가리키고 **사용자 관리**를 클릭합니다.

    ![직원 추가](./media/confluencemicrosoft-tutorial/user1.png)

1. [사용자] 섹션에서 **사용자 추가** 탭을 클릭합니다. **사용자 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/confluencemicrosoft-tutorial/user2.png)

    a. **사용자 이름** 텍스트 상자에 B.Simon과 같은 사용자의 메일을 입력합니다.

    b. **전체 이름** 텍스트 상자에 B.Simon과 같은 사용자의 전체 이름을 입력합니다.

    다. **전자 메일** 텍스트 상자에서 B.Simon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에 B.Simon의 암호를 입력합니다.

    e. **암호 확인**을 클릭하여 암호를 다시 입력합니다.

    f. **추가** 단추를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Confluence SAML SSO by Microsoft 타일을 클릭하면 Confluence SAML SSO by Microsoft 애플리케이션에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Confluence SAML SSO by Microsoft 체험하기](https://aad.portal.azure.com/)
