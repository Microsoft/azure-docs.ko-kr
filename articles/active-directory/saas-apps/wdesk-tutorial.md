---
title: '자습서: Wdesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Wdesk 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603273"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>자습서: Wdesk와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Wdesk를 통합하는 방법에 대해 알아봅니다. Azure AD와 Wdesk를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Wdesk에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Wdesk에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Wdesk SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Wdesk에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

## <a name="add-wdesk-from-the-gallery"></a>갤러리에서 Wdesk 추가

Wdesk의 Azure AD 통합을 구성하려면 갤러리의 Wdesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Wdesk** 를 입력합니다.
1. 결과 패널에서 **Wdesk** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Wdesk에 대한 Azure AD SSO 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 Wdesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Wdesk의 관련 사용자 간에 연결 관계가 설정되어 있어야 합니다.

Wdesk에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Wdesk SSO 구성](#configure-wdesk-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Wdesk 테스트 사용자 만들기](#create-wdesk-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Wdesk에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Wdesk** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. SSO를 구성할 때 WDesk 포털에서 이러한 값을 가져옵니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Wdesk 설정** 섹션에서 요구 사항에 해당하는 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Wdesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Wdesk** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-wdesk-sso"></a>Wdesk SSO 구성

1. 다른 웹 브라우저 창에서 Wdesk에 보안 관리자로 로그인합니다.

1. 왼쪽 아래에서 **관리** 를 클릭하고 **계정 관리자** 를 선택합니다.
 
    ![스크린샷은 관리 메뉴에서 선택한 계정 관리자를 보여줍니다.](./media/wdesk-tutorial/account.png)

1. Wdesk 관리에서 **보안**, **SAML** > **SAML 설정** 으로 이동합니다.

    ![스크린샷은 SAML 탭에서 선택한 SAML 설정을 보여줍니다.](./media/wdesk-tutorial/settings.png)

1. **SAML 사용자 ID 설정** 에서 **SAML 사용자 ID가 Wdesk 사용자 이름** 인지 확인합니다.

    ![스크린샷은 SAML 사용자 ID가 W 데스크 사용자 이름임을 선택할 수 있는 SAML 사용자 ID 설정을 보여줍니다.](./media/wdesk-tutorial/wdesk-username.png)

4. **일반 설정** 에서 **SAML Single Sign On 사용** 을 선택합니다.

    ![스크린샷은 SAML Single Sign-On 사용을 선택할 수 있는 SAML 설정 편집을 보여줍니다.](./media/wdesk-tutorial/user-settings.png)

5. **서비스 공급자 세부 정보** 에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 서비스 공급자 세부 정보를 보여줍니다.](./media/wdesk-tutorial/service-provider.png)

    1. **로그인 URL** 을 복사한 후 Azure Portal의 **로그온 URL** 텍스트 상자에 붙여 넣습니다.

    1. **메타데이터 URL** 을 복사한 후 Azure Portal의 **식별자** 텍스트 상자에 붙여 넣습니다.

    1. **소비자 URL** 을 복사한 후 Azure Portal의 **회신 URL** 텍스트 상자에 붙여 넣습니다.

    1. Azure Portal에서 **저장** 을 클릭하여 변경 내용을 저장합니다.      

1. **IdP 설정 구성** 을 클릭하여 **IdP 설정 편집** 대화 상자를 엽니다. **파일 선택** 을 클릭하여 Azure Portal에서 저장한 **Metadata.xml** 파일을 찾습니다.
    
    ![스크린샷은 메타데이터를 업로드할 수 있는 IdP 설정 편집을 보여줍니다.](./media/wdesk-tutorial/metadata.png)
  
1. **변경 내용 저장** 을 클릭합니다.

    ![스크린샷은 변경 내용 저장 단추를 보여줍니다.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Wdesk 테스트 사용자 만들기

Azure AD 사용자가 Wdesk에 로그인할 수 있도록 하려면 Azure AD 사용자를 Wdesk로 프로비저닝해야 합니다. Wdesk의 경우, 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Wdesk에 보안 관리자로 로그인합니다.

2. **관리** > **계정 관리자** 로 이동합니다.

     ![스크린샷은 관리 메뉴에서 선택한 계정 관리자를 보여줍니다.](./media/wdesk-tutorial/account.png)

3. **피플** 아래에서 **구성원** 을 클릭합니다.

4. 이제 **구성원 추가** 를 클릭하여 **구성원 추가** 대화 상자를 엽니다. 
   
    ![스크린샷은 멤버 추가를 선택할 수 있는 멤버 탭을 보여줍니다.](./media/wdesk-tutorial/create-user-1.png)  

5. **사용자** 텍스트 상자에 b.simon@contoso.com과 같은 사용자의 사용자 이름을 입력하고 **계속** 단추를 클릭합니다.

    ![스크린샷은 사용자를 입력할 수 있는 멤버 추가 대화 상자를 보여줍니다.](./media/wdesk-tutorial/create-user-3.png)

6.  아래와 같이 세부 정보를 입력합니다.
  
    ![스크린샷은 사용자에 대한 기본 정보를 추가할 수 있는 멤버 추가 대화 상자를 보여줍니다.](./media/wdesk-tutorial/create-user-4.png)
 
    a. **이메일** 텍스트 상자에 사용자의 이메일(예: b.simon@contoso.com)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: **B**)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

7. **구성원 저장** 단추를 클릭합니다.  

    ![스크린샷은 멤버 저장 단추가 있는 시작 이메일 보내기를 보여줍니다.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Wdesk 로그온 URL로 리디렉션됩니다.  

* Wdesk 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Wdesk에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Wdesk 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Wdesk에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Wdesk를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
