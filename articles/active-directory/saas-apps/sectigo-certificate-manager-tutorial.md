---
title: '자습서: Sectigo Certificate Manager와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Sectigo Certificate Manager 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543281"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>자습서: Sectigo Certificate Manager와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sectigo Certificate Manager를 통합하는 방법을 알아봅니다.

Sectigo Certificate Manager를 Azure AD와 연결하면 다음과 같은 이점을 누릴 수 있습니다.

* Azure AD를 사용하여 Sectigo Certificate Manager에 대해 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자는 자신의 Azure AD 계정으로 Sectigo Certificate Manager에 자동으로 로그인(Single Sign-on)할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Sectigo Certificate Manager와 Azure AD 연결을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Single Sign-On을 사용하도록 설정한 Sectigo Certificate Manager 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 Sectigo Certificate Manager를 통합합니다.

Sectigo Certificate Manager는 다음 기능을 지원합니다.

* **SP 시작 Single Sign-On**
* **IDP 시작 Single Sign-On**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure Portal에서 Sectigo Certificate Manager 추가

Sectigo Certificate Manager를 Azure AD에 통합하려면 관리형 SaaS 앱 목록에 Sectigo Certificate Manager를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 옵션](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 옵션](common/add-new-app.png)

1. 검색 상자에서 **Sectigo Certificate Manager**를 입력합니다. 검색 결과에서 **Sectigo Certificate Manager**를 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 Sectigo Certificate Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Sectigo Certificate Manager에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 Sectigo Certificate Manager의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sectigo Certificate Manager에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

| Task | Description |
| --- | --- |
| **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** | 사용자가 이 기능을 사용할 수 있도록 합니다. |
| **[Sectigo Certificate Manager Single Sign-On 구성](#configure-sectigo-certificate-manager-single-sign-on)** | 애플리케이션에서 Single Sign-On 설정을 구성합니다. |
| **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** | Britta Simon이라는 사용자에 대한 Azure AD Single Sign-On을 테스트합니다. |
| **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** | Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다. |
| **[Sectigo Certificate Manager 테스트 사용자 만들기](#create-a-sectigo-certificate-manager-test-user)** | 사용자 Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Sectigo Certificate Manager에 만듭니다. |
| **[Single Sign-On 테스트](#test-single-sign-on)** | 구성이 작동하는지 확인합니다. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Sectigo Certificate Manager를 사용하여 Azure AD Single Sign-On을 구성합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Sectigo Certificate Manager** 애플리케이션 통합 창에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 옵션](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML** 또는 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집**(연필 모양 아이콘)을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 창에서 *IDP 시작 모드*를 구성하려면 다음 단계를 완료합니다.

    1. **식별자** 상자에 다음 URL 중 하나를 입력합니다.
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. **회신 URL** 상자에 다음 URL 중 하나를 입력합니다.
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. **추가 URL 설정**을 선택합니다.

    1. **릴레이 상태** 상자에 다음 URL 중 하나를 입력합니다.
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager 도메인 및 URL Single Sign-On 정보](common/idp-relay.png)

1.  *SP 시작 모드*로 애플리케이션을 구성하려는 경우 다음 단계를 완료합니다.

    * **로그온 URL** 상자에 다음 URL 중 하나를 입력합니다.
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager 도메인 및 URL Single Sign-On 정보](common/both-signonurl.png)

1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆에 있는 **다운로드**를 선택합니다. 요구 사항에 따라 다운로드 옵션을 선택합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서(Base64) 다운로드 옵션](common/certificatebase64.png)

1. **Sectigo Certificate Manager 설정** 섹션에서 요구 사항에 따라 다음 URL을 복사합니다.

    * 로그인 URL
    * Azure AD 식별자
    * 로그아웃 URL

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sectigo Certificate Manager Single Sign-On 구성

Sectigo Certificate Manager 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 인증서(Base64) 및 복사한 해당 URL을 [Sectigo Certificate Manager 지원 팀](https://sectigo.com/support)으로 보냅니다. Sectigo Certificate Manager 지원 팀은 사용자가 보내는 정보를 사용하여 SAML Single Sign-On 연결이 양쪽에서 제대로 설정되어 있는지 확인합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 모든 사용자 옵션](common/users.png)

1. **새 사용자**를 선택합니다.

    ![새 사용자 옵션](common/new-user.png)

1. **사용자** 창에서 다음 단계를 완료합니다.

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **brittasimon\@\<your-company-domain>.\<extension\>** 를 입력합니다. 예를 들어 **brittasimon\@contoso.com**입니다.

    1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

    ![사용자 창](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Sectigo Certificate Manager에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Sectigo Certificate Manager**를 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Sectigo Certificate Manager**를 선택합니다.

    ![애플리케이션 목록의 Sectigo Certificate Manager](common/all-applications.png)

1. 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 옵션](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. 그런 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

1. **사용자 및 그룹** 창의 사용자 목록에서 **Britta Simon**을 선택합니다. **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에 대한 적절한 역할을 선택합니다. **선택**을 선택합니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Sectigo Certificate Manager 테스트 사용자 만들기

이 섹션에서는 Sectigo Certificate Manager에서 Britta Simon이라는 사용자를 만듭니다. [Sectigo Certificate Manager 지원 팀](https://sectigo.com/support)과 협력하여 Sectigo Certificate Manager 플랫폼에서 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 My Apps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On을 설정한 후에 내 앱 포털에서 **Sectigo Certificate Manager**를 선택하면 Sectigo Certificate Manager에 자동으로 로그인됩니다. My Apps 포털에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](../user-help/my-apps-portal-end-user-access.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


