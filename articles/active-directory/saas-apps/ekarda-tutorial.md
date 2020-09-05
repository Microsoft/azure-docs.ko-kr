---
title: '자습서: Ekarda와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Ekarda 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: b1cf45fd57e159993cdb3a677c505911d013122e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>자습서: Ekarda와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Ekarda를 통합하는 방법에 대해 알아봅니다. Azure AD와 Ekarda를 통합하면 다음을 수행할 수 있습니다.

* Ekarda에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Ekarda에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Ekarda SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Ekarda에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Ekarda에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

* Ekarda가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ekarda-from-the-gallery"></a>갤러리에서 Ekarda 추가

Ekarda가 Azure AD에 통합되도록 구성하려면 갤러리의 Ekarda를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Ekarda**를 입력합니다.
1. 결과 패널에서 **Ekarda**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Ekarda용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Ekarda에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Ekarda의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Ekarda에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Ekarda SSO 구성](#configure-ekarda-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Ekarda 테스트 사용자 만들기](#create-ekarda-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Ekarda에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Ekarda** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.
    
    a. **메타데이터 파일 업로드**를 클릭합니다.

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    다. 메타데이터 파일이 성공적으로 업로드되면 Ekarda 섹션 텍스트 상자에서 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

1. **서비스 공급자 메타데이터 파일**이 없는 경우 **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Ekarda 클라이언트 지원 팀](mailto:contact@ekarda.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **인증서(Base64)** 를 컴퓨터에 다운로드하고 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Ekarda 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Ekarda에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Ekarda**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-ekarda-sso"></a>Ekarda SSO 구성

1. 다른 웹 브라우저 창에서 Ekarda 회사 사이트에 관리자로 로그인합니다.

1. **관리자** -> **내 계정**을 클릭합니다.

    ![Ekarda 구성](./media/ekarda-tutorial/ekarda.png)    

1. 페이지 하단을 보면 **SAML 설정** 섹션이 있는데, 이 섹션에서 SAML 통합을 구성할 것입니다.
1. 다음 페이지에서 아래 단계를 수행합니다.

    ![Ekarda 구성](./media/ekarda-tutorial/ekarda1.png)

    a. **서비스 공급자 메타데이터** 링크를 클릭하여 컴퓨터에 파일로 저장합니다.

    b. **SAML 사용** 확인란을 선택합니다.

    다. Azure Portal에서 복사한 **엔터티 ID** 값을 **IDP 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그인 URL** 값을 **IDP 로그인 URL** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그아웃 URL** 값을 **IDP 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    f. Azure Portal에서, 다운로드한 **인증서(Base64)** 를 메모장으로 열고, 내용을 **IDP x509 인증서** 텍스트 상자에 붙여넣습니다.

    g. **옵션** 섹션에서 **SLO 사용**을 선택합니다.

    h. **업데이트**를 클릭합니다.

### <a name="create-ekarda-test-user"></a>Ekarda 테스트 사용자 만들기

이 섹션에서는 Ekarda에서 B.Simon이라는 사용자를 만듭니다. Ekarda는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Ekarda에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Ekarda 타일을 클릭하면 SSO를 설정한 Ekarda에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Ekarda 사용해보기](https://aad.portal.azure.com/)

- [Ekarda의 엔터프라이즈 eCard 솔루션](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise)을 사용하여 회사 로고가 포함된 eCards를 클라이언트 및 동료에게 보낼 직원을 원하는 수만큼 프로비전할 수 있습니다. [Ekarda를 SSO 솔루션](https://support.ekarda.com/#SSO-Implementation)으로 프로비전하는 것에 대해 자세히 알아보세요.

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Ekarda를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
