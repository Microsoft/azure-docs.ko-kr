---
title: '자습서: SiteIntel과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 SiteIntel 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: f681dd2931300ec00fd6388b4636015c87f38170
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525096"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>자습서: SiteIntel과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SiteIntel을 통합하는 방법에 대해 알아봅니다. Azure AD와 SiteIntel을 통합하면 다음을 수행할 수 있습니다.

* SiteIntel에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 SiteIntel에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SiteIntel SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SiteIntel은 SP 시작 SSO와 IdP 시작 SSO를 지원합니다.
* SiteIntel이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>갤러리에서 SiteIntel 추가

SiteIntel과 Azure AD의 통합을 구성하려면 갤러리의 SiteIntel을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 상자에 **SiteIntel**을 입력합니다.
1. 결과 목록에서 **SiteIntel**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>SiteIntel에 대한 Azure AD Single Sign-On 구성 및 테스트

*B.Simon*이라는 테스트 사용자를 사용하여 SiteIntel에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SiteIntel의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SiteIntel에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.  

    a. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - 사용자 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.  

    b. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - 사용자 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.

1. **[SiteIntel SSO 구성](#configure-siteintel-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.

    * **[SiteIntel 테스트 사용자 만들기](#create-a-siteintel-test-user)** - 사용자 B.Simon의 Azure AD 표현과 연결된 이 사용자의 대응을 SiteIntel에 만듭니다.

1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SiteIntel** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 옆에 있는 **편집**(연필 아이콘)을 선택합니다.

   ![“SAML로 Single Sign-On 설정” 창의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션을 IdP 시작 모드로 구성하려면 다음을 수행합니다.

    a. **식별자** 상자에 다음과 같은 형식으로 URL을 입력합니다. `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. **회신 URL** 상자에 다음과 같은 형식으로 URL을 입력합니다. `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    다. **릴레이 상태** 상자에 다음과 같은 형식으로 URL을 입력합니다. `https://<CLIENT>.siteintel.com`

1. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고 다음을 수행합니다.

   * **로그온 URL** 상자에 다음과 같은 형식으로 URL을 입력합니다. `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL, 로그온 URL 및 릴레이 상태로 업데이트합니다. 이러한 값을 가져오려면 [SiteIntel 클라이언트 지원 팀](mailto:support@intalytics.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **복사** 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL** 상자의 URL을 복사합니다.

    !["앱 페더레이션 메타데이터 URL" 복사 단추의 스크린샷](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.
1. 창 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음을 수행합니다.

   a. **이름** 상자에서 **B.Simon**을 입력합니다.  

   b. **사용자 이름** 상자에 사용자 이름을 `username@companydomain.extension` 형식으로 입력합니다(예: `B.Simon@contoso.com`).

   다. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

   d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 사용자 B.Simon에게 SiteIntel에 대한 액세스 권한을 부여하여 Azure Single Sign-On을 사용할 수 있도록 합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. **애플리케이션** 목록에서 **SiteIntel**을 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   ![“사용자 및 그룹” 링크의 스크린샷](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![“사용자 추가” 단추의 스크린샷](common/add-assign-user.png)

1. **사용자 및 그룹** 창에서 **B.Simon**을 선택한 다음, 화면의 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에게 적합한 역할을 선택한 다음, **선택** 단추를 선택합니다.
1. **할당 추가** 창에서 **할당** 단추를 선택합니다.

## <a name="configure-siteintel-sso"></a>SiteIntel SSO 구성

SiteIntel 쪽에서 Single Sign-On을 구성하려면 **앱 페더레이션 메타데이터 URL** 상자에서 복사한 URL을 [SiteIntel 지원 팀](mailto:support@intalytics.com)으로 보냅니다. 그러면 지원 팀에서 SAML SSO 연결이 양쪽에 제대로 설정되도록 이 값을 설정합니다.

### <a name="create-a-siteintel-test-user"></a>SiteIntel 테스트 사용자 만들기

이 섹션에서는 SiteIntel에서 *Britta Simon*이라는 사용자를 만듭니다. SiteIntel 플랫폼에서 사용자를 추가하려면  [SiteIntel 지원 팀](mailto:support@intalytics.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **SiteIntel** 타일을 선택하면 SSO를 설정한 SiteIntel에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Azure AD를 사용하여 SiteIntel 사용해 보기](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [고급 표시 유형 및 컨트롤을 사용하여 SiteIntel을 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
