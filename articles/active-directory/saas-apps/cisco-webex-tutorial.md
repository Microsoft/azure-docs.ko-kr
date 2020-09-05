---
title: '자습서: Cisco Webex Meetings와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 Cisco Webex Meetings 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 52481eb40a46457a0374c776ee4897c81ebddf7c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551288"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>자습서: Cisco Webex Meetings와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Cisco Webex Meetings를 연결하는 방법에 대해 알아봅니다. Azure AD와 Cisco Webex Meetings를 연결하는 경우 다음을 수행할 수 있습니다.

* Cisco Webex Meetings에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Cisco Webex Meetings에 자동으로 로그인(Single Sign-On)되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Cisco Webex Meetings SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Cisco Webex Meetings는 **SP 및 IDP** 시작 SSO를 지원합니다.

* Cisco Webex Meetings에서 **Just In Time** 사용자 프로비전을 지원합니다.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>갤러리에서 Cisco Webex Meetings 추가

Cisco Webex Meetings의 Azure AD 통합을 구성하려면 갤러리의 Cisco Webex Meetings를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Cisco Webex Meetings**를 입력합니다.
1. 결과 패널에서 **Cisco Webex Meetings**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Cisco Webex Meetings에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Cisco Webex Meetings에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Cisco Webex Meetings의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Cisco Webex Meetings에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[Cisco Webex Meetings SSO 구성](#configure-cisco-webex-meetings-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Cisco Webex Meetings 테스트 사용자 만들기](#create-cisco-webex-meetings-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Cisco Webex Meetings에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Cisco Webex Meetings** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML을 사용하여 Single Sign-On 설정** 페이지에서 다음과 같이 **서비스 공급자 메타데이터** 파일을 업로드하여 **IDP** 시작 모드에서 애플리케이션을 구성할 수 있습니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    다. 서비스 공급자 메타데이터 파일이 성공적으로 업로드되면 **기본 SAML 구성** 섹션에 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

    >[!Note]
    >이 자습서의 뒷부분에 설명되어 있는 **Cisco Webex Meetings SSO 구성** 섹션에서 서비스 공급자 메타데이터 파일을 가져올 수 있습니다. 

1. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 다음 단계를 수행합니다.  

    a. **기본 SAML 구성 섹션**에서 편집/펜 아이콘을 클릭합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)
    
    b. **로그온 URL** 텍스트 상자에서 ` https://<customername>.my.webex.com` 패턴을 사용하여 URL을 입력합니다.

5. Cisco Webex Meetings 애플리케이션은 특정 서식에서 SAML 어설션이 필요합니다. 이를 위해서는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도, Cisco Webex Meetings 애플리케이션에는 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 사용자 특성 대화 상자의 사용자 클레임 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다. 

    | 속성 | 원본 특성|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   이메일       | user.mail |
    |   uid    | user.mail |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 드롭다운 목록에서 선택합니다.

    f. **저장**을 클릭합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Cisco Webex Meetings 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Cisco Webex Meetings에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Cisco Webex Meetings**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco Webex Meetings SSO 구성

1. 관리 자격 증명을 사용하여 `https://<customername>.webex.com/admin` URL로 이동합니다.

2. **일반 사이트 설정**으로 이동하여 **SSO 구성**을 찾습니다.
 
    ![Single Sign-On 구성](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. **Webex 관리** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. **페더레이션 프로토콜**로 **SAML 2.0**을 선택합니다.

    b. **SAML 메타데이터 가져오기** 링크를 클릭하여, Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    다. **내보내기** 단추를 클릭하여 서비스 공급자 메타데이터 파일을 다운로드한 후 Azure Portal의 **기본 SAML 구성** 섹션에서 업로드합니다.

    d. **AuthContextClassRef** 텍스트 상자에 `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`를 입력하고 Azure AD를 사용하여 MFA를 사용 설정하려는 경우 두 개의 값(예: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`)을 입력합니다.

    e. **자동 계정 만들기**를 선택합니다.

    >[!NOTE]
    >**Just-In-Time** 사용자 프로비전을 사용 설정하려면 **자동 계정 만들기**를 선택해야 합니다. 또한 SAML 토큰 특성을 SAML 응답에 전달해야 합니다.

    f. **저장**을 클릭합니다.

    >[!NOTE]
    >이 구성은 Webex UserID를 이메일 형식으로 사용하는 고객에만 해당됩니다.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex Meetings 테스트 사용자 만들기

이 섹션에서는 Cisco Webex Meetings에서 B.Simon이라는 사용자를 만드는 것이 목적입니다. Cisco Webex Meetings는 **Just-In-Time** 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. Cisco Webex Meetings에 사용자가 아직 없는 경우 Cisco Webex Meetings에 액세스하려고 할 때 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Cisco Webex Meetings 타일을 클릭하면 SSO를 설정한 Cisco Webex Meetings에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 ServiceNow 사용해보기](https://aad.portal.azure.com)
