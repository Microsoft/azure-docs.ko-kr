---
title: '자습서: SAP Cloud for Customer와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 SAP Cloud for Customer 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.openlocfilehash: f9fd458ea19fa0dad2f630f94a67d5e1db96cee3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>자습서: SAP Cloud for Customer와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Cloud for Customer를 연결하는 방법에 대해 알아봅니다. SAP Cloud for Customer를 Azure AD와 연결하면 다음을 수행할 수 있습니다.

* Azure AD에서 SAP Cloud for Customer에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP Cloud for Customer에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Cloud for Customer SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SAP Cloud for Customer에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>갤러리에서 SAP Cloud for Customer 추가

SAP Cloud for Customer의 Azure AD 통합을 구성하려면 갤러리의 SAP Cloud for Customer를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Cloud for Customer**를 입력합니다.
1. 결과 패널에서 **SAP Cloud for Customer**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>SAP Cloud for Customer에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 SAP Cloud for Customer에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Cloud for Customer의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Cloud for Customer에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAP Cloud for Customer SSO 구성](#configure-sap-cloud-for-customer-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Cloud for Customer 테스트 사용자 만들기](#create-sap-cloud-for-customer-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Cloud for Customer에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAP Cloud for Customer** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<server name>.crm.ondemand.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<server name>.crm.ondemand.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SAP Cloud for Customer 클라이언트 지원 팀](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. SAP Cloud for Customer 애플리케이션은 특정 형식의 SAML 어설션을 예상하며, 따라서 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. **사용자 특성 및 클레임** 대화 상자의 **사용자 특성** 섹션에서 다음 단계를 수행합니다.

    a. **편집** 아이콘을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![이미지](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. **변환**을 **원본**으로 선택합니다.

    다. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    d. **매개 변수 1** 목록에서 구현에 사용할 사용자 특성을 선택합니다.
    예를 들어, EmployeeID를 고유한 사용자 식별자로 사용하고자 하고 ExtensionAttribute2에 특성 값을 저장했다면 user.extensionattribute2를 선택합니다.

    e. **저장**을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **SAP Cloud for Customer 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Cloud for Customer에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **SAP Cloud for Customer**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP Cloud for Customer SSO 구성

1. 새 웹 브라우저 창을 열고, SAP Cloud for Customer 회사 사이트에 관리자 권한으로 로그인합니다.

2. 메뉴의 왼쪽에서  **ID 공급자** > **회사 ID 공급자** > **추가**를 클릭하고 팝업에서 **Azure AD**와 같은 ID 공급자 이름을 추가하고, **저장**을 클릭한 다음, **SAML 2.0 구성**을 클릭합니다.

    ![SAP 구성](./media/sap-customer-cloud-tutorial/configure01.png)

3. **SAML 2.0 구성** 섹션에서 다음 단계를 수행합니다.

    ![SAP 구성](./media/sap-customer-cloud-tutorial/configure02.png)

    a. **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 페더레이션 메타데이터 XML 파일을 업로드합니다.

    b. XML 파일이 성공적으로 업로드되면 아래 값이 자동으로 채워지므로 **저장**을 클릭합니다.

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP Cloud for Customer 테스트 사용자 만들기

Azure AD 사용자가 SAP Cloud for Customer에 로그인할 수 있도록 설정하려면 SAP Cloud for Customer에 프로비저닝되어야 합니다. SAP Cloud for Customer에서 프로비저닝은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. SAP Cloud for Customer에 보안 관리자로 로그인합니다.

2. 메뉴의 왼쪽에서  **사용자 및 권한 부여** > **사용자 관리** > **사용자 추가**를 클릭합니다.

    ![SAP 구성](./media/sap-customer-cloud-tutorial/configure03.png)

3. **새 사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![SAP 구성](./media/sap-customer-cloud-tutorial/configure04.png)

    a. **이름** 텍스트 상자에 **B**와 같은 이름을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **이메일** 텍스트 상자에 사용자의 이메일(예: `B.Simon@contoso.com`)을 입력합니다.

    d. **로그인 이름** 텍스트 상자에 사용자의 이름(예: **B.Simon**)을 입력합니다.

    e. 요구 사항에 따라 **사용자 유형**을 선택합니다.

    f. 요구 사항에 따라 **계정 활성화** 옵션을 선택합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Cloud for Customer 타일을 클릭하면 SSO를 설정한 SAP Cloud for Customer에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 SAP Cloud for Customer 사용해보기](https://aad.portal.azure.com/)

