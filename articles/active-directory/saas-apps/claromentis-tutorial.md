---
title: '자습서: Claromentis와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Claromentis 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: dba77045f8aef1799717c6f6af91046b3ca6119e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>자습서: Claromentis와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Claromentis를 통합하는 방법에 대해 알아봅니다. Azure AD와 Claromentis를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Claromentis에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Claromentis에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Claromentis SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Claromentis에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Claromentis에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-claromentis-from-the-gallery"></a>갤러리에서 Claromentis 추가

Claromentis가 Azure AD에 통합되도록 구성하려면 갤러리의 Claromentis를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Claromentis**를 입력합니다.
1. 결과 패널에서 **Claromentis**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Claromentis에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Claromentis에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Claromentis의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Claromentis에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Claromentis SSO 구성](#configure-claromentis-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Claromentis 테스트 사용자 만들기](#create-claromentis-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Claromentis에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Claromentis** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 조직의 요구 사항에 따라 식별자 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 자습서의 뒷부분에서 설명하는 실제 식별자, 회신 URL 및 로그온 URL로 업데이트하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Claromentis 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Claromentis에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Claromentis**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-claromentis-sso"></a>Claromentis SSO 구성

1. 다른 브라우저 창에서 Claromentis 웹 사이트에 관리자 권한으로 로그온합니다.

1. **애플리케이션 아이콘**을 클릭하고 **Admin**(관리자)을 선택합니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config1.png)

1. **Custom Login Handler**(사용자 지정 로그인 처리기) 탭을 선택합니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config2.png)

1. **SAML Config**(SAML 구성)를 선택합니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config3.png)

1. **SAML Config** 탭에서 **Config**(구성) 섹션까지 아래로 스크롤하여 다음 단계를 수행합니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config4.png)

    a. **Technical Contact Name**(기술 담당자 이름) 텍스트 상자에서 기술 담당자의 이름을 입력합니다.

    b. **Technical Contact Email**(기술 담당자 이메일) 텍스트 상자에서 기술 담당자의 이메일 주소를 입력합니다.

    다. **Auth Admin Password**(인증 관리자 암호) 텍스트 상자에서 암호를 입력합니다.

1. **Auth Sources**(인증 원본)까지 아래로 스크롤하여 다음 단계를 수행합니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config5.png)

    a. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IDP** 텍스트 상자에 붙여넣습니다.

    b. 엔터티 ID 값을 **Entity ID**(엔터티 ID) 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 파일을 업로드합니다.

    d. **저장**을 클릭합니다.

1. 이제 **SAML Config** 섹션의 **Identity Provider**(ID 공급자) 섹션 내에 모든 URL이 채워져 있습니다.

    ![Claromentis 구성](./media/claromentis-tutorial/config6.png)

    a. **Identifier (Entity ID)** 식별자(엔터티 ID) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

    b. **Reply URL**(회신 URL) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

    다. **Sign On URL**(로그온 URL) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

### <a name="create-claromentis-test-user"></a>Claromentis 테스트 사용자 만들기

이 섹션에서는 Claromentis에서 B.Simon이라는 사용자를 만듭니다. Claromentis는 Just-In-Time 사용자 프로비저닝을 지원하며, 기본적으로 사용하도록 설정되어 있습니다. 이 섹션에 작업 항목이 없습니다. Claromentis에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Claromentis 타일을 클릭하면 SSO를 설정한 Claromentis에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Claromentis 사용해 보기](https://aad.portal.azure.com/)