---
title: '자습서: Zendesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Zendesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: jeedes
ms.openlocfilehash: c7d452803d15bab77df8e85a861de914a5ed08d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>자습서: Zendesk와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zendesk를 통합하는 방법에 대해 알아봅니다. Azure AD와 Zendesk를 통합하면 다음을 수행할 수 있습니다.

* Zendesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Zendesk에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Zendesk SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Zendesk에서 **SP** 시작 SSO를 지원합니다.
* Zendesk에서 [**자동** 사용자 프로비전](zendesk-provisioning-tutorial.md)을 지원합니다.
* Zendesk를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>갤러리에서 Zendesk 추가

Zendesk의 Azure AD 통합을 구성하려면 갤러리의 Zendesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Zendesk**를 입력합니다.
1. 결과 패널에서 **Zendesk**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Zendesk에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Zendesk에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Zendesk의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zendesk에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Zendesk SSO 구성](#configure-zendesk-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Zendesk 테스트 사용자 만들기](#create-zendesk-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Zendesk에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zendesk** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.zendesk.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<subdomain>.zendesk.com` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://<subdomain>.zendesk.com/access/saml` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트합니다. 이러한 값을 얻으려면 [Zendesk 클라이언트 지원 팀](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Zendesk 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 필수 SAML 특성은 없지만, 필요에 따라 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

    > [!NOTE]
    > 기본적으로 Azure AD에 없는 특성을 추가하려면 확장 특성을 사용합니다. **Zendesk**에서 허용하는 전체 SAML 특성 목록을 가져오려면 [User attributes that can be set in SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)(SAML에서 설정할 수 있는 사용자 특성)을 클릭하세요.

1. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

1. **SAML 서명 인증서** 섹션에서 **지문 값**을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

1. **Zendesk 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Zendesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Zendesk**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-zendesk-sso"></a>Zendesk SSO 구성

1. **Zendesk** 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![이미지](./media/target-process-tutorial/install_extension.png)

1. 브라우저에 확장을 추가한 후 **Zendesk 설정**을 클릭하면 Zendesk 애플리케이션으로 이동됩니다. 응용 프로그램에서 관리자 자격 증명을 입력하여 Zendesk에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Zendesk를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Zendesk 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

1. **Admin**을 클릭합니다.

1. **설정**을 클릭하고 왼쪽 탐색 창에서 **보안**을 클릭합니다.

1. **보안** 페이지에서 다음 단계를 수행합니다.

    ![보안](./media/zendesk-tutorial/ic773089.png "보안")

    ![Single Sign-On](./media/zendesk-tutorial/ic773090.png "SSO(Single sign-on)")

    a. **관리자 및 에이전트** 탭을 클릭합니다.

    b. **SSO(Single Sign-On) 및 SAML**을 선택하고**SAML**을 선택합니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML SSO URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 인증서의 **지문** 값을 **인증서 지문** 텍스트 상자에 붙여넣습니다.

    f. **저장**을 클릭합니다.

### <a name="create-zendesk-test-user"></a>Zendesk 테스트 사용자 만들기

이 섹션은 Zendesk에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Zendesk는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](Zendesk-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

> [!NOTE]
> **최종 사용자** 계정은 로그인할 때 자동으로 프로비전됩니다. **에이전트** 및 **관리자** 계정은 로그인하기 전에 **Zendesk**에서 수동으로 프로비전해야 합니다.

1. **Zendesk** 테넌트에 로그인합니다.

2. **고객 목록** 탭을 선택합니다.

3. **사용자** 탭을 선택하고 **추가**를 클릭합니다.

    ![사용자 추가](./media/zendesk-tutorial/ic773632.png "사용자 추가")
4. 프로비전하려는 기존 Azure AD 계정의 **이름**과 **메일**을 입력하고 **저장**을 클릭합니다.

    ![새 사용자](./media/zendesk-tutorial/ic773633.png "새 사용자")

> [!NOTE]
> 다른 Zendesk 사용자 계정 생성 도구 또는 Zendesk에서 제공하는 APIs를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zendesk 타일을 클릭하면 SSO를 설정한 Zendesk에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Zendesk 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Zendesk를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [사용자 프로비저닝 구성](zendesk-provisioning-tutorial.md)