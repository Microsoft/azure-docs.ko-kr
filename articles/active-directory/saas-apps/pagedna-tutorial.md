---
title: '자습서: PageDNA와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 PageDNA 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 543085eaea993f6e464a501ffc93befd7589a009
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554136"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>자습서: PageDNA와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 PageDNA를 통합하는 방법에 대해 알아봅니다.

PageDNA를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서 PageDNA에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 PageDNA에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

PageDNA와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* Single Sign-On을 사용하도록 설정한 PageDNA 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 PageDNA를 통합합니다.

PageDNA에서 지원하는 기능은 다음과 같습니다.

* SP 시작 SSO(Single Sign-On)

* Just-In-Time 사용자 프로비저닝

## <a name="add-pagedna-from-the-azure-marketplace"></a>Azure Marketplace에서 PageDNA 추가

PageDNA의 Azure AD 통합을 구성하려면 Azure Marketplace의 PageDNA를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com?azure-portal=true)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 옵션](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 새 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 옵션](common/add-new-app.png)

1. 검색 상자에 **PageDNA**를 입력합니다. 검색 결과에서 **PageDNA**를 선택한 다음, **추가**를 선택하여 애플리케이션을 추가합니다.

    ![결과 목록의 PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 PageDNA에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 PageDNA의 관련 사용자 간에 연결을 설정해야 합니다.

PageDNA에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[PageDNA Single Sign-On 구성](#configure-pagedna-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[PageDNA 테스트 사용자 만들기](#create-a-pagedna-test-user)** - Britta Simon이라는 Azure AD 사용자와 연결된 PageDNA의 Britta Simon이라는 사용자가 있습니다.
1. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

PageDNA에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure portal](https://portal.azure.com/)의 **PageDNA** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 옵션](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집**(연필 모양 아이콘)을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 창에서 다음 단계를 수행합니다.

    ![PageDNA 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    1. **로그온 URL** 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. **식별자(엔터티 ID)** 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [PageDNA 지원 팀](mailto:success@pagedna.com)에 문의합니다. Azure Portal의 **기본 SAML 구성** 창에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하여 지정된 옵션에서 **인증서(원시)** 를 다운로드하고, 컴퓨터에 저장합니다.

    ![인증서(원시) 다운로드 옵션](common/certificateraw.png)

1. **PageDNA 설정** 창에서 필요한 URL를 복사합니다.

   * **로그인 URL**
   * **Azure AD 식별자**
   * **로그아웃 URL**

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA Single Sign-On 구성

PageDNA 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 인증서(원시) 및 적절히 복사한 URL을 [PageDNA 지원 팀](mailto:success@pagedna.com)으로 보냅니다. PageDNA 팀이 양쪽 모두에서 SAML SSO 연결이 올바르게 설정되어 있는지 확인합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**   > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 "모든 사용자" 옵션](common/users.png)

1. 화면의 위쪽에서 **+새 사용자**를 선택합니다.

    ![새 사용자 옵션](common/new-user.png)

1. **사용자** 창에서 다음 단계를 수행합니다.

    ![사용자 창](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **BrittaSimon\@\<yourcompanydomain>.\<extension>** 을 입력합니다. 예를 들어 **BrittaSimon\@contoso.com**입니다.

    1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 사용자 Britta Simon에게 PageDNA에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **PageDNA**를 선택합니다.

    ![Enterprise 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **PageDNA**를 선택합니다.

    ![애플리케이션 목록의 PageDNA](common/all-applications.png)

1. 왼쪽 창의 **관리** 아래에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 옵션](common/users-groups-blade.png)

1. **+사용자 추가**를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

1. **사용자 및 그룹**창의 **사용자** 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에서 **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에게 적합한 역할을 선택합니다. 창의 아래쪽에서 **선택**을 선택합니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-pagedna-test-user"></a>PageDNA 테스트 사용자 만들기

이제 Britta Simon이라는 사용자가 PageDNA에 만들어집니다. 이 사용자를 만들기 위해 아무 작업도 수행할 필요가 없습니다. PageDNA는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. Britta Simon이라는 사용자가 PageDNA에 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 My Apps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

My Apps 포털에서**PageDNA**를 선택하면 SSO를 설정한 PageDNA 구독에 자동으로 로그인됩니다. My Apps 포털에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [SaaS 애플리케이션과 Azure Active Directory를 통합하는 방법에 대한 자습서의 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

