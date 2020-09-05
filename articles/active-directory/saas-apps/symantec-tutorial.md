---
title: '자습서: Symantec WSS(Web Security Service)와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Symantec WSS(Web Security Service) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 7e2087fdd5568156a96645b25cb105cc5f3ca6b7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544947"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>자습서: Symantec WSS(Web Security Service)와 Azure Active Directory 통합

이 자습서에서는 WSS가 SAML 인증을 사용하여 Azure AD에서 프로비전된 최종 사용자를 인증하고 사용자 또는 그룹 수준 정책 규칙을 적용할 수 있도록 Symantec WSS(Web Security Service) 계정을 Azure AD(Azure Active Directory) 계정과 통합하는 방법에 대해 알아봅니다.

Symantec WSS(Web Security Service)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- WSS 계정에서 사용하는 모든 최종 사용자와 그룹을 Azure AD Portal에서 관리합니다.

- 최종 사용자가 자신의 Azure AD 자격 증명을 사용하여 WSS에서 자신을 인증할 수 있습니다.

- WSS 계정에 정의된 사용자 및 그룹 수준 정책 규칙을 적용합니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Symantec WSS(Web Security Service)와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Symantec WSS(Web Security Service) Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Symantec WSS(Web Security Service)에서 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>갤러리에서 Symantec WSS(Web Security Service) 추가

Symantec WSS(Web Security Service)가 Azure AD에 통합되도록 구성하려면 갤러리에서 Symantec WSS(Web Security Service)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Symantec WSS(Web Security Service)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Symantec WSS(Web Security Service)** 를 입력하고, 결과 패널에서 **Symantec WSS(Web Security Service)** 를 선택한 다음 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Symantec WSS(Web Security Service)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Symantec WSS(Web Security Service)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Symantec WSS(Web Security Service)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Symantec WSS(Web Security Service)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **Symantec WSS(Web Security Service) Single Sign-On 구성** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Symantec WSS(Web Security Service) 테스트 사용자 만들기](#create-symantec-web-security-service-wss-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Symantec WSS(Web Security Service)에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Symantec WSS(Web Security Service)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Symantec WSS(Web Security Service)** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![Symantec WSS(Web Security Service) 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에 URL을 입력합니다. `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. **회신 URL** 텍스트 상자에서 `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost` URL을 입력합니다.

    > [!NOTE]
    > **식별자** 및 **회신 URL** 값이 어떤 이유로 작동하지 않는 경우 [Symantec WSS(Web Security Service) 클라이언트 지원 팀](https://www.symantec.com/contact-us)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Symantec WSS(Web Security Service) Single Sign-On 구성

Symantec WSS(Web Security Service) 쪽에 Single Sign-On을 구성하려면 WSS 온라인 설명서를 참조하세요. 다운로드한 **페더레이션 메타데이터 XML** 파일을 WSS 포털로 가져와야 합니다. WSS 포털 구성과 관련하여 도움이 필요하면 [Symantec WSS(Web Security Service) 지원 팀](https://www.symantec.com/contact-us)에 문의하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Symantec WSS(Web Security Service)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Symantec WSS(Web Security Service)** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Symantec WSS(Web Security Service)** 를 입력하고 선택합니다.

    ![애플리케이션 목록의 Symantec WSS(Web Security Service) 연결](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec WSS(Web Security Service) 테스트 사용자 만들기

이 섹션에서는 Symantec WSS(Web Security Service)에서 Britta Simon이라는 사용자를 만듭니다. WSS 포털에서 해당하는 최종 사용자 이름을 수동으로 만들 수도 있고, Azure AD에 프로비전된 사용자/그룹이 몇 분 후(15분 이내) WSS 포털에 동기화될 때까지 기다릴 수도 있습니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 웹 사이트 찾아보기에 사용되는 최종 사용자 컴퓨터의 공용 IP 주소 또한 Symantec WSS(Web Security Service) 포털에서 프로비전해야 합니다.

> [!NOTE]
> 머신의 공용 IP 주소를 얻으려면 [여기](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)를 클릭하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

SAML 인증에 Azure AD를 사용하도록 WSS 계정을 구성했으니, 이번 섹션에서는 Single Sign-On 기능을 테스트하겠습니다.

WSS로 트래픽을 프록시하도록 웹 브라우저를 구성한 후 웹 브라우저를 열고 사이트로 이동하려고 시도하면 Azure 로그인 페이지로 리디렉션됩니다. Azure AD에서 프로비전된 테스트 최종 사용자(즉, BrittaSimon)의 자격 증명 및 연결된 암호를 입력합니다. 인증되면 선택한 웹 사이트로 이동할 수 있습니다. WSS 쪽에 BrittaSimon이 특정 사이트로 이동할 수 없게 차단하는 정책 규칙을 만드는 경우 사용자 BrittaSimon으로 해당 사이트로 이동하려고 시도하면 WSS 차단 페이지가 나타납니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

