---
title: '자습서: Soloinsight-CloudGate SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Soloinsight-CloudGate SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545066"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>자습서: Soloinsight-CloudGate SSO와 Azure Active Directory 통합

이 자습서에서는 Soloinsight-CloudGate SSO를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Soloinsight CloudGate SSO를 Azure AD와 통합하는 경우에 다음을 수행할 수 있습니다.

* Soloinsight-CloudGate SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Soloinsight-CloudGate SSO에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 다운로드할 수 있습니다.
* Soloinsight-CloudGate SSO SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Soloinsight-CloudGate SSO에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>갤러리에서 Soloinsight-CloudGate SSO 추가

Soloinsight-CloudGate SSO가 Azure AD로 통합되도록 구성하려면 Soloinsight-CloudGate SSO를 갤러리에서 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Soloinsight-CloudGate SSO**를 입력합니다.
1. 결과 패널에서 **Soloinsight CloudGate SSO**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

테스트 사용자 **Britta Simon**을 사용하여 Soloinsight-CloudGate SSO로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Soloinsight-CloudGate SSO의 관련 사용자 간에 연결이 형성되어야 합니다.

Soloinsight-CloudGate SSO에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. **[Soloinsight-CloudGate SSO 구성](#configure-soloinsight-cloudgate-sso)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Soloinsight-CloudGate SSO 테스트 사용자 만들기](#create-soloinsight-cloudgate-sso-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Soloinsight-CloudGate SSO에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Soloinsight-CloudGate SSO** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    1. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.sigateway.com/login` 패턴을 사용하는 URL을 입력합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<SUBDOMAIN>.sigateway.com/process/sso` 패턴을 사용하는 URL을 입력합니다.

   > [!NOTE]
   > 이러한 값은 실제 값이 아닙니다. 이러한 값을 이 자습서 뒷부분의 **Soloinsight-CloudGate SSO Single Sign-On 구성** 섹션에 설명되어 있는 실제 로그온 URL 및 식별자로 업데이트합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Soloinsight-CloudGate SSO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO 구성

1. Soloinsight-CloudGate SSO 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Soloinsight-CloudGate SSO 설정**클릭하여 Soloinsight-CloudGate SSO 애플리케이션으로 이동합니다. 여기에서 관리자 자격 증명을 입력하여 Soloinsight-CloudGate SSO에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-8단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Soloinsight-CloudGate SSO를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Soloinsight-CloudGate SSO 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. 기본 SAML을 구성할 때 Azure Portal에 붙여넣을 값을 얻으려면 자격 증명을 사용하여 CloudGate 웹 포털에 로그인한 다음, SSO 설정(**홈>관리>시스템 설정>일반**)에 액세스합니다.

    ![CloudGate SSO 설정](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML 소비자 URL**

    * **Saml 소비자 URL** 및 **리디렉션 URL** 필드에 대해 제공되는 링크를 복사하여 각각 **식별자(엔터티 ID)** 및 **회신 URL** 필드의 Azure Portal **기본 SAML 구성** 섹션에 붙여넣습니다.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML 서명 인증서**

    * Azure Portal SAML 서명 인증서 목록에서 다운로드한 인증서(Base64) 파일의 원본으로 이동하여 마우스 오른쪽 단추로 클릭합니다. 목록에서 **메모장++를 사용하여 편집** 옵션을 선택합니다. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 인증서(Base64) 메모장++ 파일의 콘텐츠를 복사합니다.

        ![인증서 복사](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * 복사한 콘텐츠를 CloudGate 웹 포털 SSO 설정 **인증서** 필드에 붙여넣고 [저장] 단추를 클릭합니다.

        ![인증서 포털](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **기본 그룹**

    * CloudGate 웹 포털의 **기본 그룹** 옵션 드롭다운 목록에서 **비즈니스 관리자**를 선택합니다.

        ![기본 그룹](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD 식별자 및 로그인 URL**

    * Azure Portal **Soloinsight-CloudGate SSO 설정** 구성에서 복사한 **로그인 URL**은 CloudGate 웹 포털 SSO 설정 섹션에 입력할 것입니다.

    * Azure Portal의 **로그인 URL** 링크를 CloudGate 웹 포털 **AD 로그인 URL** 필드에 붙여넣습니다.

    * Azure Portal의 **Azure AD 식별자** 링크를 CloudGate 웹 포털 **AD 식별자** 필드에 붙여넣습니다.

        ![Ad 로그인](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `BrittaSimon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Soloinsight-CloudGate SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Soloinsight-CloudGate SSO**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO 테스트 사용자 만들기

테스트 사용자를 만들려면 CloudGate 웹 포털의 주 메뉴에서 **직원**을 선택하고 [새 직원 추가] 양식을 작성합니다. 테스트 사용자에게 할당할 권한 수준은 **비즈니스 관리자**입니다. 필수 필드를 모두 채웠으면 **만들기**를 클릭합니다.

![직원 테스트](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Soloinsight-CloudGate SSO 타일을 선택하면 SSO를 설정한 Soloinsight-CloudGate SSO에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)