---
title: '자습서: Wdesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Wdesk 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>자습서: Wdesk와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Wdesk를 통합하는 방법에 대해 알아봅니다. Azure AD와 Wdesk를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Wdesk에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Wdesk에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Wdesk SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Wdesk는 **SP** 및 **IDP** 시작 SSO를 지원합니다.
* Wdesk를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>갤러리에서 Wdesk 추가

Wdesk의 Azure AD 통합을 구성하려면 갤러리의 Wdesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Wdesk**를 입력합니다.
1. 결과 패널에서 **Wdesk**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Wdesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Wdesk의 관련 사용자 간에 연결 관계가 설정되어 있어야 합니다.

Wdesk에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Wdesk SSO 구성](#configure-wdesk-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Wdesk 테스트 사용자 만들기](#create-wdesk-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Wdesk에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Wdesk에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Wdesk** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Wdesk 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    ![Wdesk 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. SSO를 구성할 때 WDesk 포털에서 이러한 값을 가져옵니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Wdesk 설정** 섹션에서 요구 사항에 해당하는 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Wdesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **Wdesk**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Wdesk**를 선택합니다.

    ![애플리케이션 목록의 Wdesk 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-wdesk-sso"></a>Wdesk SSO 구성

1. 다른 웹 브라우저 창에서 Wdesk에 보안 관리자로 로그인합니다.

2. 왼쪽 아래에서 **관리**를 클릭하고 **계정 관리자**를 선택합니다.
 
     ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Wdesk 관리에서 **보안**, **SAML** > **SAML 설정**으로 이동합니다.

    ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. **일반 설정**에서 **SAML Single Sign On 사용**을 선택합니다.

    ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. **서비스 공급자 세부 정보**에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. **로그인 URL**을 복사한 후 Azure Portal의 **로그온 URL** 텍스트 상자에 붙여 넣습니다.
   
      b. **메타데이터 URL**을 복사한 후 Azure Portal의 **식별자** 텍스트 상자에 붙여 넣습니다.
       
      다. **소비자 URL**을 복사한 후 Azure Portal의 **회신 URL** 텍스트 상자에 붙여 넣습니다.
   
      d. Azure Portal에서 **저장**을 클릭하여 변경 내용을 저장합니다.      

6. **IdP 설정 구성**을 클릭하여 **IdP 설정 편집** 대화 상자를 엽니다. **파일 선택**을 클릭하여 Azure Portal에서 저장한 **Metadata.xml** 파일을 찾습니다.
    
    ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. **변경 내용 저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesk 테스트 사용자 만들기

Azure AD 사용자가 Wdesk에 로그인할 수 있도록 하려면 Azure AD 사용자를 Wdesk로 프로비저닝해야 합니다. Wdesk의 경우, 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Wdesk에 보안 관리자로 로그인합니다.

2. **관리** > **계정 관리자**로 이동합니다.

     ![Single Sign-on 구성](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. **피플** 아래에서 **구성원**을 클릭합니다.

4. 이제 **구성원 추가**를 클릭하여 **구성원 추가** 대화 상자를 엽니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/wdesk-tutorial/createuser1.png)  

5. **사용자** 텍스트 상자에 brittasimon@contoso.com과 같은 사용자의 사용자 이름을 입력하고 **계속** 단추를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/wdesk-tutorial/createuser3.png)

6.  아래와 같이 세부 정보를 입력합니다.
  
    ![Azure AD 테스트 사용자 만들기](./media/wdesk-tutorial/createuser4.png)
 
    a. **이메일** 텍스트 상자에 사용자의 이메일(예: brittasimon@contoso.com)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

7. **구성원 저장** 단추를 클릭합니다.  

    ![Azure AD 테스트 사용자 만들기](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Wdesk 타일을 클릭하면 SSO가 설정된 Wdesk에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)