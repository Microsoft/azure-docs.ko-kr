---
title: '자습서: Spotinst와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 Spotinst 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 3b8297175c24aac132fd7d83580e0889e0da4730
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587959"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>자습서: Spotinst와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Spotinst를 통합하는 방법에 대해 알아봅니다. Azure AD와 Spotinst를 통합하면 다음을 수행할 수 있습니다.

* Spotinst에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Spotinst에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Spotinst SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Spotinst에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-spotinst-from-the-gallery"></a>갤러리에서 Spotinst 추가

Spotinst의 Azure AD 통합을 구성하려면 갤러리의 Spotinst를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Spotinst**를 입력합니다.
1. 결과 패널에서 **Spotinst**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Spotinst용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Spotinst에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Spotinst의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Spotinst에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Spotinst SSO 구성](#configure-spotinst-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Spotinst 테스트 사용자 만들기](#create-spotinst-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Spotinst에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Spotinst** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. IDP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 완료합니다.

   1. **회신 URL**이 https://console.spotinst.com/auth/saml 로 설정되어 있는지 확인합니다.
   1. **릴레이 상태**에 Spotinst 조직 ID를 입력합니다. 이 ID는 **SSO** 탭에서도 확인할 수 있습니다.
   1. **로그온 URL**은 비어 있어야 합니다.

1. **저장**을 클릭합니다.

1. Spotinst 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Spotinst 애플리케이션에는 아래에서 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Spotinst 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Spotinst에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Spotinst**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-spotinst-sso"></a>Spotinst SSO 구성

1. 다른 웹 브라우저 창에서 Spotinst에 보안 관리자로 로그인합니다.

2. 화면 오른쪽 위에 있는 **사용자 아이콘**을 클릭하고 **설정**을 클릭합니다.

    ![Spotinst 설정](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. 위쪽의 **보안** 탭을 클릭한 후 **ID 공급자**를 선택하고 다음 단계를 수행합니다.

    ![Spotinst 보안](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. 인스턴스에 대한 **릴레이 상태** 값을 복사한 다음, Azure Portal에서 **기본 SAML 구성** 섹션의 **릴레이 상태** 텍스트 상자에 붙여넣습니다.

    b. **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 xml 파일을 업로드합니다.

    다. **저장**을 클릭합니다.

### <a name="create-spotinst-test-user"></a>Spotinst 테스트 사용자 만들기

이 섹션은 Spotinst에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

1. **SP** 시작 모드로 애플리케이션을 구성한 경우 다음 단계를 수행합니다.

   a. 다른 웹 브라우저 창에서 Spotinst에 보안 관리자로 로그인합니다.

   b. 화면 오른쪽 위에 있는 **사용자 아이콘**을 클릭하고 **설정**을 클릭합니다.

    ![Spotinst 설정](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    다. **사용자**를 클릭하고 **사용자 추가**를 선택합니다.

    ![Spotinst 설정](./media/spotinst-tutorial/adduser1.png)

    d. 사용자 추가 섹션에서 다음 단계를 수행합니다.

    ![Spotinst 설정](./media/spotinst-tutorial/adduser2.png)

    * **전체 이름** 텍스트 상자에 **BrittaSimon**과 같은 사용자의 전체 이름을 입력합니다.

    * **이메일** 텍스트 상자에 사용자의 이메일 주소(예: `brittasimon\@contoso.com`)를 입력합니다.

    * **조직 역할, 계정 역할 및 계정**에 대해 조직별 세부 정보를 선택합니다.

2. **IDP** 시작 모드에서 애플리케이션을 구성한 경우 이 섹션에 작업 항목이 없습니다. Spotinst는 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정합니다. 새 사용자가 아직 존재하지 않는 경우 Spotinst에 액세스하는 동안 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Spotinst 타일을 클릭하면 SSO를 설정한 Spotinst에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Spotinst 사용해보기](https://aad.portal.azure.com/)

