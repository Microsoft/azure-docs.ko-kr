---
title: '자습서: EasySSO for Jira와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 EasySSO for Jira 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 9260276ab875a7858bd008f2d213fb7898c5229b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>자습서: EasySSO for Jira와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 EasySSO for Jira를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 EasySSO for Jira를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Jira에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Jira에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* EasySSO for Jira SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* EasySSO for Jira에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* EasySSO for Jira에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* EasySSO for Jira가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>갤러리에서 EasySSO for Jira 추가

EasySSO for Jira가 Azure AD에 통합되도록 구성하려면 갤러리에서 EasySSO for Jira를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **EasySSO for Jira**를 입력합니다.
1. 결과 패널에서 **EasySSO for Jira**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>EasySSO for Jira용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 EasySSO for Jira에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 EasySSO for Jira의 관련 사용자 간에 연결 관계를 설정해야 합니다.

EasySSO for Jira에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[EasySSO for Jira SSO 구성](#configure-easysso-for-jira-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[EasySSO for Jira 테스트 사용자 만들기](#create-easysso-for-jira-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 EasySSO for Jira에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **EasySSO for Jira** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/easysso/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/easysso/saml` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/login.jsp` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 확실치 않은 경우 [EasySSO 지원 팀](mailto:support@techtime.co.nz)에 문의하여 이러한 값을 구합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. EasySSO for Jira 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 EasySSO for Jira 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | Name | 원본 특성|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Azure AD 사용자에 대해 **sAMAccountName**이 구성된 경우 **urn:oid:0.9.2342.19200300.100.1.1**을 **sAMAccountName** 특성에 매핑해야 할 수 있습니다.
    
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 또는 **페더레이션 메타데이터 XML** 옵션에 대한 **다운로드** 링크를 클릭하여 둘 중 하나 또는 모두 컴퓨터에 저장합니다. 나중에 Jira EasySSO를 구성하는 데 필요합니다.

    ![인증서 다운로드 링크](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    인증서를 사용하여 수동으로 EasySSO for Jira 구성을 수행하려는 경우 아래 섹션에서 **로그인 URL** 및 **Azure AD 식별자**도 복사하여 컴퓨터에 저장해야 합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 EasySSO for Jira에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **EasySSO for Jira**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-easysso-for-jira-sso"></a>EasySSO for Jira SSO 구성

1. 관리자 권한으로 Atlassian Jira 인스턴스에 로그인하고 **앱 관리** 섹션으로 이동합니다. 

    ![앱 관리](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. 왼쪽에서 **EasySSO**를 찾아 클릭합니다.

    ![EasySSO](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. **SAML** 옵션을 선택합니다. 그러면 SAML 구성 섹션으로 이동됩니다.

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. 맨 위에 있는 **인증서** 탭을 선택하면 다음 화면이 표시됩니다.

    ![메타데이터 URL](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. 이제 **Azure AD SSO** 구성의 이전 단계에서 저장한 **인증서(Base64)** 또는 **메타데이터 파일**을 찾습니다. 진행 방법에 대한 다음 옵션을 사용할 수 있습니다.

    a. 컴퓨터의 로컬 파일로 다운로드한 앱 페더레이션 **메타데이터 파일**을 사용합니다. **업로드** 라디오 단추를 선택하고 운영 체제와 관련된 파일 업로드 대화 상자를 따릅니다.

    **OR**

    b. 앱 페더레이션 **메타데이터 파일**을 열어 파일의 콘텐츠(일반 텍스트 편집기)를 확인하고 클립보드에 복사합니다. **입력** 옵션을 선택하고 텍스트 필드에 클립보드 콘텐츠를 붙여넣습니다.

     **OR**

    다. 완전 수동 구성. 앱 페더레이션 **인증서(Base64)** 를 열어 파일의 콘텐츠(일반 텍스트 편집기)를 확인하고 클립보드에 복사합니다. **IdP 토큰 서명 인증서** 텍스트 필드에 붙여넣습니다. 그런 다음, **일반** 탭으로 이동하여 **POST 바인딩 URL** 및 **엔터티 ID** 필드를 각각 앞에서 저장한 **로그인 URL** 및 **Azure AD 식별자** 값으로 채웁니다.
 
6. 페이지 아래쪽에서 **저장** 단추를 클릭합니다. 구성 필드로 구문 분석된 메타데이터 및 인증서 파일의 내용을 볼 수 있습니다. EasySSO for Jira 구성이 완료되었습니다.

7. 최상의 테스트 환경을 위해 **모양 및 느낌** 탭으로 이동하여 **SAML 로그인 단추** 옵션을 선택합니다. 이렇게 하면 특히 Jira 로그인 화면에서 별도의 단추를 사용하여 Azure AD SAML 통합을 엔드투엔드로 테스트할 수 있습니다. 이 단추를 켜놓고 프로덕션 모드의 배치, 색 및 변환을 구성할 수도 있습니다.

    ![모양 및 느낌](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > 문제가 발생하는 경우 [EasySSO 지원 팀](mailto:support@techtime.co.nz)에 문의하세요.

### <a name="create-easysso-for-jira-test-user"></a>EasySSO for Jira 테스트 사용자 만들기

이 섹션에서는 Jira에서 Britta Simon이라는 사용자를 만듭니다. EasySSO for Jira는 기본적으로 **비활성화**된 Just-In-Time 사용자 프로비저닝을 지원합니다. 사용자 프로비저닝을 사용하도록 설정하려면 EasySSO 플러그 인 구성의 일반 섹션에서 **성공적인 로그인 시 사용자 만들기** 옵션을 명시적으로 선택해야 합니다. Jira에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

그러나 사용자가 처음 로그인할 때 자동 사용자 프로비저닝을 사용하지 않으려면, 사용자는 LDAP 또는 Atlassian Crowd와 같이 Jira 인스턴스가 사용하는 백 엔드 사용자 디렉터리에 있어야 합니다.

![사용자 프로비전](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>SSO 테스트 

### <a name="idp-initiated-workflow"></a>IdP 시작 워크플로

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 EasySSO for Jira 타일을 클릭하면 SSO를 설정한 Jira 인스턴스에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

### <a name="sp-initiated-workflow"></a>SP 시작 워크플로

이 섹션에서는 Jira **SAML 로그인** 단추를 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

![사용자 SAML 로그인](media/easysso-for-jira-tutorial/jira-admin-7.png)

이 시나리오에서는 Jira EasySSO 구성 페이지의 **모양 및 느낌** 탭에서 **SAML 로그인 단추**를 사용하도록 설정했다고 가정합니다(위 참조). 기존 세션과의 간섭을 피하려면 브라우저 시크릿 모드로 Jira 로그인 URL을 엽니다. **SAML 로그인** 단추를 클릭하면 Azure AD 사용자 인증 흐름으로 리디렉션됩니다. 성공적으로 완료되면 SAML을 통해 인증된 사용자로 Jira 인스턴스로 다시 리디렉션됩니다.

Azure AD에서 다시 리디렉션되는 경우 다음 화면이 나타날 수 있습니다.

![EasySSO 실패 스크린샷](media/easysso-for-jira-tutorial/jira-admin-8.png)

이 경우 [이 페이지의 지침]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)에 따라 **atlassian-jira.log** 파일에 대한 액세스 권한을 획득해야 합니다. 오류에 대한 세부 정보는 EasySSO 오류 페이지에서 참조 ID에 따라 제공됩니다.

로그 메시지를 이해하기 어려운 경우 [EasySSO 지원 팀](mailto:support@techtime.co.nz)에 문의하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 EasySSO for Jira 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 EasySSO for Jira를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
