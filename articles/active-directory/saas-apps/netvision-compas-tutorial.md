---
title: '자습서: Netvision Compas와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Netvision Compas 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 8f92ff60dacd78687207c7523504182521700a7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554501"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>자습서: Netvision Compas와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Netvision Compas를 통합하는 방법에 대해 알아봅니다. Azure AD와 Netvision Compas를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Netvision Compas에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Netvision Compas에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Netvision Compas SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Netvision Compas에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Netvision Compas가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>갤러리에서 Netvision Compas 추가

Netvision Compas가 Azure AD에 통합되도록 구성하려면 갤러리의 Netvision Compas를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Netvision Compas**를 입력합니다.
1. 결과 패널에서 **Netvision Compas**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Netvision Compas에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Netvision Compas에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Netvision Compas의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Netvision Compas에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Netvision Compas SSO 구성](#configure-netvision-compas-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Netvision Compas 테스트 사용자 구성](#configure-netvision-compas-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Netvision Compas에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Netvision Compas** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<TENANT>.compas.cloud/Identity/Saml20` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 해당 값을 얻으려면 [Netvision Compas 클라이언트 지원 팀](mailto:contact@net.vision)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 메타데이터 파일을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)



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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Netvision Compas에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Netvision Compas**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-netvision-compas-sso"></a>Netvision Compas SSO 구성

이 섹션에서는 **Netvision Compas**에서 SAML SSO를 사용하도록 설정합니다.
1. 관리 계정을 사용하여 **Netvision Compas**에 로그인하고 관리 영역에 액세스합니다.

    ![관리 영역](media/netvision-compas-tutorial/admin.png)

1. **시스템** 영역을 찾아서 **ID 공급자**를 선택합니다.

    ![관리 IDP](media/netvision-compas-tutorial/admin-idps.png)

1. **추가** 동작을 선택하여 Azure AD를 새 IDP로 등록합니다.

    ![IDP 추가](media/netvision-compas-tutorial/idps-add.png)

1. **공급자 형식**으로 **SAML**을 선택합니다.
1. **표시 이름** 및 **설명** 필드에 의미 있는 값을 입력합니다.
1. **사용 가능한 사용자** 목록에서 사용자를 선택하고 **선택한 항목 추가** 단추를 선택하여 **Netvision Compas** 사용자를 IDP에 할당합니다. 프로비저닝 절차를 수행하는 동안에도 사용자를 IDP에 할당할 수 있습니다.
1. **메타데이터** SAML 옵션의 경우 **파일 선택** 단추를 클릭하고 이전에 컴퓨터에 저장된 메타데이터 파일을 선택합니다.
1. **저장**을 클릭합니다.

    ![IDP 편집](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Netvision Compas 테스트 사용자 구성

이 섹션에서는 SSO에 Azure AD를 사용하도록 **Netvision Compcompas**에서 기존 사용자를 구성합니다.
1. 회사에서 정의한 **Netvision Compas** 사용자 프로비저닝 절차를 따르거나 기존 사용자 계정을 편집합니다.
1. 사용자 프로필을 정의하는 동안 사용자의 **이메일(개인용)** 주소가 Azure AD 사용자 이름(username@companydomain.extension)과 일치하는지 확인합니다. `B.Simon@contoso.com`)을 입력합니다.

    ![사용자 편집](media/netvision-compas-tutorial/user-config.png)

Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 Azure AD Single Sign-On 구성을 테스트합니다.

### <a name="using-the-access-panel-idp-initiated"></a>액세스 패널(IDP 시작)을 사용합니다.

액세스 패널에서 Netvision Compas 타일을 클릭하면 SSO를 설정한 Netvision Compas에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Netvision Compas(SP 시작)에 직접 액세스합니다.

1. **Netvision Compas** URL에 액세스합니다. `https://tenant.compas.cloud`)을 입력합니다.
1. **Netvision Compas** 사용자 이름을 입력하고 **다음**을 선택합니다.

    ![로그인 사용자](media/netvision-compas-tutorial/login-user.png)

1. **(선택 사항)** **Netvision Compas** 내에서 사용자에게 여러 IDP가 할당된 경우 사용 가능한 IDP 목록이 표시됩니다. 이전에 **Netvision Compas**에서 구성한 Azure AD IDP를 선택합니다.

    ![로그인 선택](media/netvision-compas-tutorial/login-choose.png)

1. 인증을 수행하기 위해 Azure AD로 리디렉션됩니다. 성공적으로 인증되면 SSO를 설정한 **Netvision Compas**에 자동으로 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Netvision Compas 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
