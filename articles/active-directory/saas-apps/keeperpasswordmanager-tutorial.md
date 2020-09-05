---
title: '자습서: Keeper Password Manager & Digital Vault와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Keeper Password Manager & Digital Vault 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: c6bd0c130e860a5700256a54c081cc046219b41a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546749"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>자습서: Keeper Password Manager & Digital Vault와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Keeper Password Manager & Digital Vault를 통합하는 방법에 대해 알아봅니다.
Azure AD에 Keeper Password Manager & Digital Vault를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* Keeper Password Manager & Digital Vault에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Keeper Password Manager & Digital Vault에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Keeper Password Manager & Digital Vault와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Keeper Password Manager & Digital Vault Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Keeper Password Manager & Digital Vault에서 **SP** 시작 SSO를 지원합니다.

* Keeper Password Manager & Digital Vault에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

* Keeper Password Manager 및 Digital Vault가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>갤러리에서 Keeper Password Manager & Digital Vault 추가

Keeper Password Manager & Digital Vault의 Azure AD 통합을 구성하려면 갤러리의 Keeper Password Manager & Digital Vault를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Keeper Password Manager 및 Digital Vault**를 입력합니다.
1. 결과 패널에서 **Keeper Password Manager 및 Digital Vault**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Keeper Password Manager 및 Digital Vault에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Keeper Password Manager 및 Digital Vault에서 Azure AD SSO를 구성하고 테스트합니다. SSO과 작동하려면 Azure AD 사용자와 Keeper Password Manager 및 Digital Vault 관련 사용자 간에 연결 관계를 설정해야 합니다.

Keeper Password Manager 및 Digital Vault에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.

    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.

1. **[Keeper Password Manager 및 Digital Vault SSO 구성](#configure-keeper-password-manager--digital-vault-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Keeper Password Manager & Digital Vault 테스트 사용자 만들기](#create-keeper-password-manager--digital-vault-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 Keeper Password Manager & Digital Vault에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Keeper Password Manager 및 Digital Vault** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서  패턴을 사용하는 URL을 입력합니다.
    * **Cloud SSO**의 경우: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **온-프레미스 SSO**의 경우: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. **식별자(엔터티 ID)** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    * **Cloud SSO**의 경우: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * **온-프레미스 SSO**의 경우: `https://<KEEPER_FQDN>/sso-connect`

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.
    * **Cloud SSO**의 경우: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * **온-프레미스 SSO**의 경우: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트합니다. 이러한 값을 얻으려면 [Keeper Password Manager & Digital Vault 클라이언트 지원 팀](https://keepersecurity.com/contact.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Keeper Password Manager & Digital Vault 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

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

이 섹션에서는 B.Simon에게 Keeper Password Manager 및 Digital Vault에 대한 액세스 권한을 부여함으로써 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Keeper Password Manager &amp; Digital Vault**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Keeper Password Manager 및 Digital Vault SSO 구성

**Keeper Password Manager & Digital Vault 구성** 쪽에서 Single Sign-On을 구성하려면 [Keeper 지원 가이드](https://docs.keeper.io/sso-connect-guide/)에서 제공되는 지침에 따릅니다.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Keeper Password Manager & Digital Vault 테스트 사용자 만들기

Keeper Password Manager & Digital Vault에 로그인할 수 있도록 Azure AD 사용자를 설정하려면 Keeper Password Manager & Digital Vault에 프로비전해야 합니다. 애플리케이션이 JIT(Just-in-time) 사용자 프로비저닝을 지원하며 인증 후에 애플리케이션에서 사용자가 자동으로 만들어집니다. 사용자를 수동으로 설정하려는 경우 [Keeper 지원](https://keepersecurity.com/contact.html)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Keeper Password Manager & Digital Vault 타일을 클릭하면 SSO를 설정한 Keeper Password Manager & Digital Vault에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Keeper Password Manager 및 Digital Vault 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
