---
title: '자습서: ThirdLight와 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 ThirdLight 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3193df0848c0d67935faa0020465e79bd50e2151
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544981"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>자습서: ThirdLight와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ThirdLight를 통합하는 방법에 대해 알아봅니다. 이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 ThirdLight에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ThirdLight에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱의 통합에 대해 자세히 알아보려면 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

ThirdLight와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On을 사용하도록 설정된 ThirdLight 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* ThirdLight에서 SP 시작 SSO를 지원합니다.

## <a name="add-thirdlight-from-the-gallery"></a>갤러리에서 ThirdLight 추가

ThirdLight가 Azure AD에 통합되도록 설정하려면 갤러리의 ThirdLight를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **ThirdLight**를 입력합니다. 검색 결과에서 **ThirdLight**를 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 ThirdLight에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 ThirdLight의 해당 사용자 간에 연결 관계를 설정해야 합니다.

ThirdLight에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[ThirdLight Single Sign-On을 구성](#configure-thirdlight-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[ThirdLight 테스트 사용자를 만듭니다](#create-a-thirdlight-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

ThirdLight에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 ThirdLight 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![기본 SAML 구성 대화 상자](common/sp-identifier.png)

    1. **로그온 URL** 상자에 다음 패턴으로 URL을 입력합니다.
    
          `https://<subdomain>.thirdlight.com/`

    1. **식별자(엔터티 ID)** 상자에 다음 패턴으로 URL을 입력합니다.

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > 이러한 값은 자리 표시자입니다. 실제 로그온 URL 및 식별자를 사용해야 합니다. 이 값을 얻으려면 [ThirdLight 지원 팀](https://www.thirdlight.com/support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 대화 상자에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **페더레이션 메타데이터 XML** 옆의 **다운로드** 링크를 선택하고, 해당 파일을 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **ThirdLight 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-thirdlight-single-sign-on"></a>ThirdLight Single Sign-On 구성

1. 새 웹 브라우저 창에서 ThirdLight 회사 사이트에 관리자 권한으로 로그인합니다.

1. **Configuration(구성)**  > **System Administration(시스템 관리)**  > **SAML2**로 차례로 이동합니다.

    ![시스템 관리](./media/thirdlight-tutorial/ic805843.png "시스템 관리")

1. SAML2 구성 섹션에서 다음 단계를 수행합니다.
  
    ![SAML2 구성 섹션](./media/thirdlight-tutorial/ic805844.png "SAML2 구성 섹션")

    1. **SAML2 Single Sign-On 사용**을 선택합니다.

    1. **Source for IdP Metadata**(IdP 메타데이터에 대한 원본) 아래에서 **Load IdP Metadata from XML**(XML에서 IdP 메타데이터 로드)을 선택합니다.

    1. 이전 섹션의 Azure Portal에서 다운로드한 메타데이터 파일을 엽니다. 파일의 내용을 복사하여 **IdP Metadata XML**(IdP 메타데이터 XML) 상자에 붙여넣습니다.

    1. **Save SAML2 settings**(SAML2 설정 저장)를 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 창 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **BrittaSimon@\<yourcompanydomain>\<extension>.** 을 입력합니다. (예: BrittaSimon@contoso.com)

    1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ThirdLight에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **ThirdLight**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **ThirdLight**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고, 창 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 창 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-thirdlight-test-user"></a>ThirdLight 테스트 사용자 만들기

Azure AD 사용자가 ThirdLight에 로그인할 수 있도록 설정하려면 해당 사용자를 ThirdLight에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. ThirdLight 회사 사이트에 관리자 권한으로 로그인합니다.

1. **사용자** 탭으로 이동합니다.

1. **사용자 및 그룹**을 선택합니다.

1. **새 사용자 추가**를 선택합니다.

1. 프로비저닝하려는 유효한 Azure AD 계정의 사용자 이름, 이름 또는 설명 및 이메일 주소를 입력합니다. [새 멤버]의 [미리 설정] 또는 [그룹]을 선택 합니다.

1. **만들기**를 선택합니다.

> [!NOTE]
> ThirdLight에서 제공하는 사용자 계정 생성 도구 또는 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 ThirdLight 타일을 선택하면 SSO를 설정한 ThirdLight 인스턴스에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
