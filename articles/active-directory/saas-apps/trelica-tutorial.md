---
title: '자습서: Trelica와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Trelica 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: dcc304febe6fcebe3aba3047d1773e695b80f9e2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551942"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>자습서: Trelica와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Trelica를 연결하는 방법에 대해 알아봅니다.

이러한 통합을 통해 다음을 수행할 수 있습니다.

* Trelica에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Trelica에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)가 설정된 Trelica 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Trelica에서 IDP 시작 SSO를 지원합니다.
* Trelica에서 Just-In-Time 사용자 프로비저닝을 지원합니다.
* Trelica가 구성되면 세션 제어를 적용할 수 있습니다. 이를 통해 조직의 중요한 데이터의 반출 및 반입을 실시간으로 제어할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>갤러리에서 Trelica 추가

Trelica가 Azure AD에 통합되도록 구성하려면 갤러리의 Trelica를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 맨 왼쪽에 있는 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Trelica**를 입력합니다.
1. 검색 결과 패널에서 **Trelica**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Trelica용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Trelica에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Trelica의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Trelica에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Trelica SSO 구성](#configure-trelica-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Trelica 테스트 사용자 만들기](#create-a-trelica-test-user)** - B.Simon에 대응하는 사용자를 Trelica에 만듭니다. 이 대응 사용자는 사용자의 Azure AD 표현에 연결됩니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Trelica** 애플리케이션 통합 페이지에서 **관리** 섹션으로 이동합니다. **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성에 대한 연필 아이콘이 강조 표시된 [SAML로 Single Sign-On 설정] 페이지](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 다음 값을 입력합니다.

    1. **식별자** 상자에 URL **https://app.trelica.com** 을 입력합니다.

    1. **회신 URL** 상자에 URL을 `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` 패턴으로 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL(ACS라고도 함)로 이 값을 업데이트합니다.
    > Trelica에 로그인하고 [SAML ID 공급자 구성 페이지](https://app.trelica.com/Admin/Profile/SAML)(관리자 > 계정 > SAML)로 이동하면 이를 찾을 수 있습니다. **ACS(Assertion Consumer Service) URL** 옆에 있는 복사 단추를 클릭하여 이를 클립보드에 저장하고 Azure AD의 **회신 URL** 텍스트 상자에 붙여넣을 수 있습니다.
    > 질문이 있는 경우 [Trelica 도움말 설명서](https://docs.trelica.com/admin/saml/azure-ad)을 읽거나 [Trelica 클라이언트 지원 팀](mailto:support@trelica.com)에 문의하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![[앱 페더레이션 메타데이터 URL] 옆에 복사 단추가 강조 표시된 [SAML 서명 인증서] 섹션](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 맨 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에서 **B.Simon**을 입력합니다.
   1. **사용자 이름** 필드에 **B.Simon@** _companydomain_ **.** _extension_을 입력합니다. 예들 들어 B.Simon@contoso.com입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Trelica에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Trelica**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션으로 이동하여 **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹이 강조 표시된 [관리] 섹션](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

   ![[사용자 추가]가 강조 표시된 [사용자 및 그룹] 창](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

## <a name="configure-trelica-sso"></a>Trelica SSO 구성

**Trelica** 쪽에서 Single Sign-On을 구성하려면 [SAML ID 공급자 구성 페이지](https://app.trelica.com/Admin/Profile/SAML)(관리자 > 계정 > SAML)로 이동합니다. **새로 만들기** 단추를 클릭합니다. 이름으로 **Azure AD**를 입력하고 메타데이터 유형에 대해 **URL의 메타데이터**를 선택합니다. Azure AD에서 수행한 **앱 페더레이션 메타데이터 URL**을 Trelica의 **메타데이터 URL** 필드에 붙여넣습니다.

질문이 있는 경우 [Trelica 도움말 설명서](https://docs.trelica.com/admin/saml/azure-ad)을 읽거나 [Trelica 클라이언트 지원 팀](mailto:support@trelica.com)에 문의하세요.

### <a name="create-a-trelica-test-user"></a>Trelica 테스트 사용자 만들기

Trelica는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에서는 사용자가 수행할 작업이 없습니다. Trelica에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 My Apps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

내 앱 포털에서 Trelica 타일을 클릭하면 SSO를 설정한 Trelica에 자동으로 로그인됩니다. 내 앱 포털에 대한 자세한 내용은 [내 앱 포털 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Trelica 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Trelica를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
