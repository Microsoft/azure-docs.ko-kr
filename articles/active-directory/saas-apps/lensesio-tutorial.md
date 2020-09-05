---
title: '자습서: Lenses.io와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Lenses.io 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 48a1e50d451abb429e9bc33308909b368283644f
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661455"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>자습서: Lenses.io DataOps 포털과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 [Lenses.io](https://lenses.io/) DataOps 포털을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 Lenses.io를 통합하면 다음을 수행할 수 있습니다.

* Lenses.io 포털에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Lenses에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱을 통합하는 방법에 대해 자세히 알아보려면 [Azure AD를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Lenses 포털 인스턴스. 다양한 [배포 옵션](https://lenses.io/product/deployment/) 중에서 선택할 수 있습니다.
* SSO(Single Sign-On)를 지원하는 Lenses.io [라이선스](https://lenses.io/product/pricing/)

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Lenses.io에서 SP(서비스 공급자) 시작 SSO를 지원합니다.

* Lenses.io가 구성되면 세션 제어를 적용할 수 있습니다. 세션 제어는 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>갤러리에서 Lenses.io 추가

Lenses.io가 Azure AD에 통합되도록 구성하려면 Lenses.io를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Lenses.io**를 입력합니다.
1. 결과 패널에서 **Lenses.io**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Lenses.io에 대한 Azure AD SSO 구성 및 테스트

*B.Simon*이라는 테스트 사용자를 만들어 Lenses.io 포털에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Lenses.io의 관련 사용자 간에 연결 관계를 설정해야 합니다.

다음 단계를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자 및 그룹 만들기](#create-an-azure-ad-test-user-and-group) - B.Simon을 사용하여 Azure AD SSO를 테스트합니다.
    1. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B.Simon이 Azure AD SSO를 사용할 수 있도록 할당합니다.
1. [Lenses.io SSO 구성](#configure-lensesio-sso) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. [Lenses.io 테스트 그룹 권한 만들기](#create-lensesio-test-group-permissions) - B.Simon이 Lenses.io에서 액세스해야 하는 대상을 제어합니다(권한 부여).
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Lenses.io** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집/펜 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성을 편집하기 위한 아이콘을 보여주는 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션의 다음 텍스트 입력 상자에서 값을 입력합니다.

    a. **로그온 URL**: `https://<CUSTOMER_LENSES_BASE_URL>` 패턴을 사용하는 URL을 입력합니다. 예제는 `https://lenses.my.company.com`입니다.

    b. **식별자(엔터티 ID)** : `https://<CUSTOMER_LENSES_BASE_URL>` 패턴을 사용하는 URL을 입력합니다. 예제는 `https://lenses.my.company.com`입니다.

    c. **회신 URL**: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` 패턴을 사용하는 URL을 입력합니다. 예제는 `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`입니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. Lenses 포털 인스턴스의 기본 URL에 대한 실제 로그온 URL, 회신 URL 및 식별자로 업데이트합니다. 자세한 내용은 [Lenses.io SSO 설명서](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)를 참조하세요.

1. **SAML로 Single Sign-On 설정** 페이지에서 **SAML 서명 인증서** 섹션으로 이동합니다. **페더레이션 메타데이터 XML**을 찾은 다음, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크를 보여주는 스크린샷](common/metadataxml.png)

1. **Lenses.io 설정** 섹션에서 다운로드한 XML 파일을 사용하여 Azure SSO에 대해 Lenses를 구성합니다.

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD 테스트 사용자 및 그룹 만들기

Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다. 그런 다음, Lenses에 대한 B.Simon의 액세스를 제어하는 테스트 그룹을 만듭니다.

Lenses에서 그룹 멤버 자격 매핑을 권한 부여에 사용하는 방법은 [Lenses SSO 설명서](https://docs.lenses.io/install_setup/configuration/security.html#id3)에서 확인할 수 있습니다.

**테스트 파일을 만들려면 다음을 수행합니다.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 상자에서 **B.Simon**을 입력합니다.  
   1. **사용자 이름** 상자에서 username@companydomain.extension을 입력합니다. 예들 들어 B.Simon@contoso.com입니다.
   1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시되는 값을 적어 둡니다.
   1. **만들기**를 선택합니다.

**그룹을 만들려면 다음을 수행합니다.**

1. **Azure Active Directory**로 이동한 다음, **그룹**을 선택합니다.
1. 화면의 위쪽에서 **새 그룹**을 선택합니다.
1. **그룹 속성**에서 다음 단계를 수행합니다.
   1. **그룹 유형** 상자에서 **보안**을 선택합니다.
   1. **그룹 이름** 상자에서 **LensesUsers**를 입력합니다.
   1. **만들기**를 선택합니다.
1. **LensesUsers** 그룹을 선택하고, **개체 ID**를 복사합니다(예: f8b5c1ec-45de-4abd-af5c-e874091fb5f7). 이 ID는 Lenses에서 해당 그룹의 사용자를 [올바른 권한](https://docs.lenses.io/install_setup/configuration/security.html#id3)에 매핑하는 데 사용됩니다.  

**테스트 사용자에게 그룹을 할당하려면 다음을 수행합니다.**

1. **Azure Active Directory**로 이동한 다음, **사용자**를 선택합니다.
1. **B.Simon** 테스트 사용자를 선택합니다.
1. **그룹**을 선택합니다.
1. 화면의 위쪽에서 **멤버 자격 추가**를 선택합니다.
1. **LensesUsers**를 검색하여 선택합니다.
1. **선택**을 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Lenses.io에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Lenses.io**를 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크를 보여주는 스크린샷](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다.

   ![사용자 추가 링크를 보여주는 스크린샷](common/add-assign-user.png)

1. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 [사용자] 목록에서 **B.Simon**을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

## <a name="configure-lensesio-sso"></a>Lenses.io SSO 구성

**Lenses.io** 포털에서 SSO를 구성하려면 다운로드한 **페더레이션 메타데이터 XML**을 Lenses 인스턴스에 설치하고 [SSO를 사용하도록 Lenses를 구성](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses)합니다.

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io 테스트 그룹 권한 만들기

1. Lenses에서 그룹을 만들려면 **LensesUsers** 그룹의 **개체 ID**를 사용합니다. 이는 사용자 [만들기 섹션](#create-an-azure-ad-test-user-and-group)에서 복사한 ID입니다.
1. B.Simon에 대해 원하는 권한을 할당합니다.

자세한 내용은 [Azure - Lenses 그룹 매핑](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)을 참조하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Lenses.io 타일을 선택하면 Lenses.io 포털에 자동으로 로그인됩니다. 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Lenses.io 인스턴스에서 SSO 설정](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [SaaS 앱을 Azure AD와 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD를 사용한 애플리케이션 액세스 및 SSO란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD에서 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Lenses.io 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Lenses.io를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
