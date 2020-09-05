---
title: '자습서: HubSpot과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 HubSpot 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 51e27526b10bcdd74d6e2bffb8bf620d7b022aac
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551471"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>자습서: HubSpot과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 HubSpot을 통합하는 방법에 대해 알아봅니다.

HubSpot을 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* Azure AD를 사용하여 HubSpot에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자는 자신의 Azure AD 계정으로 HubSpot에 자동으로 로그인(Single Sign-on)할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

HubSpot과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Single Sign-On을 사용하도록 설정한 HubSpot 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 HubSpot을 통합합니다.

HubSpot에서 지원하는 기능은 다음과 같습니다.

* **SP 시작 Single Sign-On**
* **IDP 시작 Single Sign-On**

## <a name="add-hubspot-in-the-azure-portal"></a>Azure Portal에서 HubSpot 추가

HubSpot을 Azure AD와 통합하려면 관리형 SaaS 앱 목록에 HubSpot을 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 옵션](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 옵션](common/add-new-app.png)

1. 검색 상자에 **HubSpot**을 입력합니다. 검색 결과에서 **HubSpot**을 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 HubSpot에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 HubSpot의 관련 사용자 간에 연결 관계를 설정해야 합니다.

HubSpot에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

| Task | Description |
| --- | --- |
| **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** | 사용자가 이 기능을 사용할 수 있도록 합니다. |
| **[HubSpot Single Sign-On 구성](#configure-hubspot-single-sign-on)** | 애플리케이션에서 Single Sign-On 설정을 구성합니다. |
| **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** | Britta Simon이라는 사용자에 대한 Azure AD Single Sign-On을 테스트합니다. |
| **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** | Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다. |
| **[HubSpot 테스트 사용자 만들기](#create-a-hubspot-test-user)** | Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 HubSpot에 만듭니다. |
| **[Single Sign-On 테스트](#test-single-sign-on)** | 구성이 작동하는지 확인합니다. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 HubSpot을 사용하여 Azure AD Single Sign-On을 구성합니다.

1. [Azure Portal](https://portal.azure.com/)의 **HubSpot** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 옵션](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML** 또는 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집**(연필 모양 아이콘)을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 창에서 *IDP 시작 모드*를 구성하려면 다음 단계를 완료합니다.

    1. **식별자** 상자에 https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\> 패턴을 사용하는 URL을 입력합니다.

    1. **회신 URL** 상자에 https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\> 패턴을 사용하는 URL을 입력합니다.

    ![HubSpot 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    > [!NOTE]
    > URL 서식을 지정하기 위해 Azure Portal의 **기본 SAML 구성** 창에 표시된 패턴을 참조할 수도 있습니다.

1. *SP 시작* 모드로 애플리케이션을 구성하려는 경우 다음을 수행합니다.

    1. **추가 URL 설정**을 선택합니다.

    1. **로그온 URL** 상자에 **https:\//app.hubspot.com/login**을 입력합니다.

    ![추가 URL 설정 옵션](common/metadata-upload-additional-signon.png)

1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆에 있는 **다운로드**를 선택합니다. 요구 사항에 따라 다운로드 옵션을 선택합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서(Base64) 다운로드 옵션](common/certificatebase64.png)

1. **HubSpot 설정** 섹션에서 요구 사항에 따라 다음 URL을 복사합니다.

    * 로그인 URL
    * Azure AD 식별자
    * 로그아웃 URL

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot Single Sign-On 구성

1. 브라우저에서 새 탭을 열고 HubSpot 관리자 계정으로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에서 **설정** 아이콘을 선택합니다.

    ![HubSpot의 설정 아이콘](./media/hubspot-tutorial/config1.png)

1. **계정 기본값**을 선택합니다.

    ![HubSpot이 계정 기본값 옵션](./media/hubspot-tutorial/config2.png)

1. 아래로 스크롤하여 **보안** 섹션으로 이동한 후 **설정**을 선택합니다.

    ![HubSpot의 설정 옵션](./media/hubspot-tutorial/config3.png)

1. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.

    1. **대상 그룹 URl(서비스 공급자 엔터티 ID)** 상자에서 **복사**를 선택하여 값을 복사합니다. Azure Portal의 **기본 SAML 구성** 창에서 **식별자** 상자에 해당 값을 붙여넣습니다.

    1. **로그인 URl, ACS, 받는 사람 또는 리디렉션** 상자에서 **복사**를 선택하여 값을 복사합니다. Azure Portal의 **기본 SAML 구성** 창에서 **회신 URL** 상자에 해당 값을 붙여넣습니다.

    1. HubSpot의 **ID 공급자 식별자 또는 발급자 URL** 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    1. HubSpot의 **ID 공급자 Single Sign-On URL** 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. Windows 메모장에서 다운로드한 인증서(Base64) 파일을 엽니다. 파일 콘텐츠를 선택한 후 복사합니다. 그런 다음, HubSpot에서 **X.509 인증서** 상자에 붙여넣습니다.

    1. **확인**을 선택합니다.

        ![HubSpot의 Single Sign-On 설정 섹션](./media/hubspot-tutorial/config4.png)

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

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 HubSpot에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **HubSpot**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **HubSpot**을 선택합니다.

    ![애플리케이션 목록의 HubSpot](common/all-applications.png)

1. 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 옵션](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. 그런 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

1. **사용자 및 그룹** 창의 사용자 목록에서 **Britta Simon**을 선택합니다. **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에 대한 적절한 역할을 선택합니다. **선택**을 선택합니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-hubspot-test-user"></a>HubSpot 테스트 사용자 만들기

Azure AD 사용자가 HubSpot에 로그인하려면 HubSpot에 프로비저닝되어야 합니다. HubSpot의 경우, 수동으로 프로비저닝합니다.

HubSpot에서 사용자 계정을 프로비저닝하려면

1. HubSpot 회사 사이트에 관리자로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에서 **설정** 아이콘을 선택합니다.

    ![HubSpot의 설정 아이콘](./media/hubspot-tutorial/config1.png)

1. **사용자 및 팀**을 선택합니다.

    ![HubSpot의 사용자 및 팀 옵션](./media/hubspot-tutorial/user1.png)

1. **사용자 만들기**를 선택합니다.

    ![HubSpot에서 사용자 만들기 옵션](./media/hubspot-tutorial/user2.png)

1. **메일 추가** 상자에 brittasimon\@contoso.com 형식으로 사용자의 메일 주소를 입력하고 **다음**을 선택합니다.

    ![HubSpot의 사용자 만들기 섹션에 있는 메일 주소 추가 상자](./media/hubspot-tutorial/user3.png)

1. **사용자 만들기** 섹션에서 각 탭을 선택합니다. 각 탭에서 사용자에 대해 관련 옵션 및 권한을 설정합니다. 그다음에 **다음**을 선택합니다.

    ![HubSpot의 사용자 만들기 섹션에 포함된 탭](./media/hubspot-tutorial/user4.png)

1. 사용자에게 초대를 보내려면 **보내기**를 선택합니다.

    ![HubSpot의 보내기 옵션](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 사용자는 초대를 수락하면 활성화됩니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 My Apps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On을 설정한 후에 내 앱 포털에서 **HubSpot**을 선택하면 HubSpot에 자동으로 로그인됩니다. My Apps 포털에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](../user-help/my-apps-portal-end-user-access.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
