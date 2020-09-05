---
title: '자습서: Kontiki와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Kontiki 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: cb652cfdae0ce785d6a076b8b1592d9f700b3383
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549977"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>자습서: Kontiki와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Kontiki를 통합하는 방법에 대해 알아봅니다.

Kontiki를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD를 사용하여 Kontiki에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자는 해당 Azure AD 계정으로 Kontiki에 자동으로 로그온(Single Sign-On)할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Kontiki와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Single Sign-On을 사용하도록 설정된 Kontiki 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 다음, Azure AD에 Kontiki를 통합합니다.

Kontiki에서 지원하는 기능은 다음과 같습니다.

* **SP 시작 Single Sign-On**
* **Just-In-Time 사용자 프로비전**

## <a name="add-kontiki-in-the-azure-portal"></a>Azure Portal에서 Kontiki 추가

Kontiki를 Azure AD와 통합하려면 관리형 SaaS 앱 목록에 Kontiki를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 옵션](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 옵션](common/add-new-app.png)

1. 검색 상자에서 **Kontiki**를 입력합니다. 검색 결과에서 **Kontiki**를 선택한 다음, **추가**를 선택합니다.

    ![결과 목록의 Kontiki](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Kontiki에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 Kontiki의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Kontiki에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

| Task | Description |
| --- | --- |
| **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** | 사용자가 이 기능을 사용할 수 있도록 합니다. |
| **[Kontiki Single Sign-On 구성](#configure-kontiki-single-sign-on)** | 애플리케이션에서 Single Sign-On 설정을 구성합니다. |
| **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** | Britta Simon이라는 사용자에 대한 Azure AD Single Sign-On을 테스트합니다. |
| **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** | Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다. |
| **[Kontiki 테스트 사용자 만들기](#create-a-kontiki-test-user)** | Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Kontiki에 만듭니다. |
| **[Single Sign-On 테스트](#test-single-sign-on)** | 구성이 작동하는지 확인합니다. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Kontiki를 사용하여 Azure AD Single Sign-On을 구성합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Kontiki** 애플리케이션 통합 창에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 옵션](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML** 또는 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집**(연필 모양 아이콘)을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 창의 **로그온 URL** 텍스트 상자에서 `https://<companyname>.mc.eval.kontiki.com` 패턴의 URL을 입력합니다.

    ![Kontiki 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    > [!NOTE]
    > 사용할 올바른 값을 얻으려면 [Kontiki 클라이언트 지원 팀](https://customersupport.kontiki.com/enterprise/contactsupport.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 옆에 있는 **다운로드**를 선택합니다. 요구 사항에 따라 다운로드 옵션을 선택합니다. 인증서를 컴퓨터에 저장합니다.

    ![페더레이션 메타데이터 XML 인증서 다운로드 옵션](common/metadataxml.png)

1. **Kontiki 설정** 섹션에서 요구 사항에 따라 다음 URL을 복사합니다.

    * 로그인 URL
    * Azure AD 식별자
    * 로그아웃 URL

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki Single Sign-On 구성

[Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) 쪽에서 Single Sign-On을 구성하려면 다운로드한 페더레이션 메타데이터 XML과 Azure Portal에서 복사한 관련 URL을 Kontiki 지원 팀으로 보내야 합니다. Kontiki 지원 팀은 사용자가 보내는 정보를 사용하여 SAML Single Sign-On 연결이 양쪽에서 제대로 설정되었는지 확인합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 모든 사용자 옵션](common/users.png)

1. **새 사용자**를 선택합니다.

    ![새 사용자 옵션](common/new-user.png)

1. **사용자** 창에서 다음 단계를 완료합니다.

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **brittasimon\@\<your-company-domain>.\<extension>** 를 입력합니다. 예를 들어 **brittasimon\@contoso.com**입니다.

    1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

    ![사용자 창](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Kontiki에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Kontiki**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Kontiki**를 선택합니다.

    ![애플리케이션 목록의 Kontiki](common/all-applications.png)

1. 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 옵션](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. 그런 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

1. **사용자 및 그룹** 창의 사용자 목록에서 **Britta Simon**을 선택합니다. **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에 대한 적절한 역할을 선택합니다. **선택**을 선택합니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-kontiki-test-user"></a>Kontiki 테스트 사용자 만들기

Kontiki에서 사용자 프로비저닝을 구성할 수 있는 작업 항목이 없습니다. 할당된 사용자가 My Apps 포털을 사용하여 Kontiki에 로그인하려고 하면 Kontiki에서 해당 사용자가 있는지 여부를 확인합니다. 사용자 계정이 없으면 Kontiki는 사용자 계정을 자동으로 만듭니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 My Apps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On이 설정되면 My Apps 포털에서 **Kontiki**를 선택하면 Kontiki에 자동으로 로그인됩니다. My Apps 포털에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](../user-help/my-apps-portal-end-user-access.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
