---
title: '자습서: Shopify Plus와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Shopify Plus 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: 61d2eb05f7953cda16664f6b4e3317feb46aa8ad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>자습서: Shopify Plus와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Shopify Plus를 통합하는 방법에 대해 알아봅니다. Azure AD와 Shopify Plus를 통합하면 다음을 수행할 수 있습니다.

* Shopify Plus에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Shopify Plus에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Shopify Plus SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Shopify Plus에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* Shopify Plus를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>갤러리에서 Shopify Plus 추가

Shopify Plus의 Azure AD 통합을 구성하려면 갤러리의 Shopify Plus를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Shopify Plus**를 입력합니다.
1. 결과 패널에서 **Shopify Plus**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Shopify Plus용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Shopify Plus에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Shopify Plus의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Shopify Plus에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Shopify Plus SSO 구성](#configure-shopify-plus-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Shopify Plus 테스트 사용자 만들기](#create-shopify-plus-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Shopify Plus에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Shopify Plus** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    **회신 URL** 텍스트 상자에 `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://shopify.plus/login` URL을 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 해당 값을 얻으려면 [Shopify Plus 클라이언트 지원 팀](mailto:plus-user-management@shopify.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Shopify Plus 애플리케이션에는 특정 서식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Shopify Plus 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ---- | --------------- |
    | 이메일 | user.mail |

1. **이름 ID** 형식을 **영구**로 변경합니다. **고유한 사용자 ID(이름 ID)** 옵션을 선택한 다음, **이름 식별자** 형식을 선택합니다. 이 옵션에 대해 **영구**를 선택합니다. 변경 내용을 저장합니다.
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 선택하여 **앱 페더레이션 메타데이터 URL**을 복사하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Shopify Plus에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Shopify Plus**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-shopify-plus-sso"></a>Shopify Plus SSO 구성

전체 단계를 보려면 [SAML 통합 설정에 대한 Shopify 설명서](https://help.shopify.com/en/manual/shopify-plus/saml)를 참조하세요.

**Shopify Plus** 쪽에서 Single Sign-On을 구성하려면 Azure Active Directory에서 **앱 페더레이션 메타데이터 URL**을 복사합니다. 그런 다음, [조직 관리자](https://shopify.plus)에 로그인하여 **사용자** > **보안**으로 이동합니다. **Set up configuration**(구성 설정)을 선택한 다음, **ID 공급자 메타데이터 URL** 섹션에 앱 페더레이션 메타데이터 URL을 붙여넣습니다. **추가**를 선택하여 이 단계를 완료합니다.

### <a name="create-shopify-plus-test-user"></a>Shopify Plus 테스트 사용자 만들기

이 섹션에서는 Shopify Plus에 B.Simon이라는 사용자를 만듭니다. **사용자** 섹션으로 돌아가서 이메일과 권한을 입력하여 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="enforce-saml-authentication"></a>SAML 인증 적용

> [!NOTE]
> 통합을 광범위하게 적용하기 전에 개별 사용자를 사용하여 테스트하는 것이 좋습니다.

개별 사용자:
1. Azure AD에서 관리되고 Shopify Plus에서 확인된 이메일 도메인을 사용하여 Shopify Plus의 개별 사용자 페이지로 이동합니다.
1. SAML 인증 섹션에서 **편집**을 선택하고 **필수**를 선택한 다음, **저장**을 선택합니다.
1. idP 시작 및 SP 시작 흐름을 통해 이 사용자가 성공적으로 로그인할 수 있는지 테스트합니다.

이메일 도메인의 모든 사용자:
1. **보안** 페이지로 돌아갑니다.
1. SAML 인증 설정에 대해 **필수**를 선택합니다. 그러면 Shopify Plus 전체에서 해당 이메일 도메인을 사용하는 모든 사용자에게 SAML이 적용됩니다.
1. **저장**을 선택합니다.

> [!IMPORTANT]
> 이메일 도메인에 속하는 모든 사용자에게 SAML을 사용하도록 설정하면 이 애플리케이션을 사용하는 모든 사용자에게 영향을 줍니다. 사용자가 일반 로그인 페이지를 사용하여 로그인할 수 없고, Azure Active Directory를 통해서만 앱에 액세스할 수 있게 됩니다. Shopify는 일반 사용자 이름과 암호를 사용하여 사용자가 로그인할 수 있는 백업 로그인 URL을 제공하지 않습니다. 필요한 경우 Shopify 지원에 문의하여 SAML 기능을 해제할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Shopify Plus 타일을 클릭하면 SSO를 설정한 Shopify Plus에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Shopify Plus 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 제어를 사용하여 Shopify Plus를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
