---
title: '자습서: HRworks Single Sign-On과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 HRworks Single Sign-On 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c43dfe3ad28417e13196c5ac1e75419906f6eae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>자습서: HRworks Single Sign-On과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 HRworks Single Sign-On을 통합하는 방법에 대해 알아봅니다. HRworks Single Sign-On을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* HRworks Single Sign-On에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 HRworks Single Sign-On에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* HRworks Single Sign-On의 SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* HRworks Single Sign-On에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>갤러리에서 HRworks Single Sign-On 추가

HRworks Single Sign-On의 Azure AD 통합을 구성하려면 갤러리의 HRworks Single Sign-On를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **HRworks Single Sign-On**을 입력합니다.
1. 결과 패널에서 **HRworks Single Sign-On**을 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>HRworks Single Sign-On에 대한 Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 HRworks Single Sign-On에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 HRworks Single Sign-On의 관련 사용자 간에 연결 관계를 설정해야 합니다.

HRworks Single Sign-On에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[HRworks Single Sign-On SSO 구성](#configure-hrworks-single-sign-on-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[HRworks Single Sign-On 테스트 사용자 만들기](#create-hrworks-single-sign-on-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 HRworks Single Sign-On에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **HRworks Single Sign-On** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **로그인 URL** 텍스트 상자에서 `https://login.hrworks.de/?companyId=<companyId>&directssologin=true` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 구하려면 [HRworks Single Sign-On 클라이언트 지원 팀](mailto:nadja.sommerfeld@hrworks.de)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **HRworks Single Sign-On 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 HRworks Single Sign-On에 대한 액세스 권한을 부여하여 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **HRworks Single Sign-On**을 클릭합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-hrworks-single-sign-on-sso"></a>HRworks Single Sign-On SSO 구성

1. HRworks Single Sign-On 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **HRworks Single Sign-On 설정**을 클릭하면 HRworks Single Sign-On 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 HRworks Single Sign-On에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-4단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. HRworks Single Sign-On을 수동으로 설정하려면 새 웹 브라우저 창을 열고 HRworks Single Sign-On 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. 메뉴 모음의 왼쪽에서 **관리자** > **기본** > **보안** > **Single Sign-On**을 클릭하고 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. **Single Sign-On 사용** 확인란을 선택합니다.

    b. **Meta data input method**(메타데이터 입력 방법)에 **XML 메타데이터**를 선택합니다.

    다. **Value for NameID**(NameID 값)에 **Individual NameID identifier**(개별 NameID 식별자)를 선택합니다.

    d. 메모장에서, Azure Portal에서 다운로드한 메타데이터 XML을 열고 해당 콘텐츠를 복사한 다음, **메타데이터** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks Single Sign-On 테스트 사용자 만들기

Azure AD 사용자가 HRworks Single Sign-On에 로그인할 수 있도록 하려면 HRworks Single Sign-On으로 프로비저닝되어야 합니다. HRworks Single Sign-On의 경우 프로비저닝은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. HRworks Single Sign-On에 관리자로 로그인합니다.

1. 메뉴 모음 왼쪽에서 **관리자** > **Persons**(개인) > **Persons**(개인) > **New person**(새로운 개인)을 클릭합니다.

     ![Single Sign-on 구성](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. 팝업에서 **다음**을 클릭합니다.

    ![Single Sign-on 구성](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. **Create new person with country for legal terms**(약관의 국가로 새로운 개인 만들기)에서 **이름**, **성**과 같은 해당 세부 정보를 채우고 **만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 HRworks Single Sign-On 타일을 클릭하면 SSO를 설정한 HRworks Single Sign-On 애플리케이션에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 HRworks Single Sign-On 사용해보기](https://aad.portal.azure.com/)