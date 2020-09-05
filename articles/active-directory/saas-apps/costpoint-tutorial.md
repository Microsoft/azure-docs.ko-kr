---
title: '자습서: Costpoint와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Costpoint 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 1db1589561968cf1e2974edcee2bfe1cccb4a327
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549251"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>자습서: Costpoint와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Costpoint를 통합하는 방법에 대해 알아봅니다. Azure AD와 Costpoint를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Costpoint에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Costpoint에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Costpoint SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Costpoint는 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="generate-costpoint-metadata"></a>Costpoint 메타데이터 생성

Costpoint SAML SSO 구성은 **DeltekCostpoint711Security.pdf** 가이드에 설명되어 있습니다. Deltek Costpoint 지원 사이트에서 이 가이드를 다운로드 하여 **SAML Single Sign-on Setup**(SAML Single Sign-On 설정) > **Configure SAML Single Sign-on between Costpoint and Microsoft Azure**(Costpoint와 Microsoft Azure 간에 SAML Single Sign-On 구성) 섹션을 참조하세요. 지침에 따라 **Costpoint SP 페더레이션 메타데이터 XML** 파일을 생성합니다. 

![Costpoint 구성 유틸리티](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>갤러리에서 Costpoint 추가

Costpoint를 Azure AD와 통합하려면 먼저 Azure Portal의 갤러리에서 관리형 SaaS 앱 목록에 Costpoint를 추가합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.

1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.

   ![Azure Active Directory 단추](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

   ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.

   ![새 애플리케이션 단추](common/add-new-app.png)

1. **갤러리에서 추가** 섹션의 검색 상자에 **Costpoint**를 입력합니다.

   ![결과 목록의 Costpoint](common/search-new-app.png)

1. 결과 목록에서 **Costpoint**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Costpoint에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Costpoint의 관련 사용자 간에 연결이 형성되어야 합니다.

Costpoint에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
1. **[Costpoint 구성](#configure-costpoint)** - 애플리케이션 쪽에서 SAML SSO 설정을 구성합니다.
1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Costpoint 테스트 사용자 만들기](#create-a-costpoint-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Costpoint에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. **Costpoint** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

   ![Single Sign-On 구성 링크](common/select-sso.png)

1. **기본 SAML 구성** 섹션에서 서비스 공급자 메타데이터 파일이 있는 경우, 다음 단계를 완료합니다. 

   > [!NOTE]
   > 서비스 공급자 메타데이터 파일은 [Costpoint 메타데이터 생성](#generate-costpoint-metadata)에서 가져올 수 있습니다. 이 파일을 사용하는 방법은 자습서의 뒷부분에 설명되어 있습니다.
 
   1. **메타데이터 파일 업로드** 단추를 선택한 다음, Costpoint를 통해 이전에 생성된 **Costpoint SP 페더레이션 메타데이터 XML** 파일을 선택한 후 **추가** 단추를 선택하여 파일을 업로드합니다.

      ![메타데이터 파일 업로드](./media/costpoint-tutorial/upload-metadata.png)
    
   1. 메타데이터 파일이 성공적으로 업로드되면 Costpoint 섹션에서 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

      > [!NOTE]
      > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 요구 사항에 따라 수동으로 값을 입력합니다. **식별자(엔터티 ID)** 및 **회신 URL(Assertion Consumer Service URL)** 이 올바르게 설정되어 있고 **ACS URL**이 **/LoginServlet.cps**로 끝나는 유효한 Costpoint URL인지 확인합니다.

   1. **추가 URL 설정**을 선택합니다. **릴레이 상태**의 경우, `system=[your system]` 패턴을 사용하여 값(예: **system=DELTEKCP**)을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **복사** 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 저장합니다.

   ![SAML 서명 인증서](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint 구성

1. Costpoint 구성 유틸리티로 돌아갑니다. **IdP 페더레이션 메타데이터 XML** 텍스트 상자에 *앱 페더레이션 메타데이터 URL* 파일의 내용을 붙여넣습니다. 

   ![Costpoint 구성 유틸리티](./media/costpoint-tutorial/config-utility-idp.png)

1. **DeltekCostpoint711Security.pdf** 가이드의 지침에 따라 Costpoint SAML 설정을 완료합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 B.Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

   !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

1. **새 사용자**를 선택합니다.

   ![새 사용자 단추](common/new-user.png)

1. **사용자** 속성에서 다음 단계를 완료합니다.

   ![사용자 대화 상자](common/user-properties.png)

   1. **이름** 필드에서 **B.Simon**을 입력합니다.
   
   1. **사용자 이름** 필드에서 `b.simon\@yourcompanydomain.extension`(예: B.Simon@contoso.com)을 입력합니다.
   
   1. **암호 표시** 확인란을 선택한 다음, **암호** 필드에 표시된 값을 적어둡니다.
   
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Costpoint에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.

1. 애플리케이션 목록에서 **Costpoint**를 선택합니다.

1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

   ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon**을 선택합니다. 그런 다음, **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택한 다음, **선택**을 선택합니다.

1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-costpoint-test-user"></a>Costpoint 테스트 사용자 만들기

이 섹션에서는 Costpoint에서 사용자를 만듭니다. 사용자 ID가 **B.SIMON**이고 사용자 이름이 **B.Simon**이라고 가정합니다. [Costpoint 클라이언트 지원 팀](https://www.deltek.com/about/contact-us)과 협력하여 사용자를 Costpoint 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

사용자가 생성된 후 사용자의 **인증 방법**은 **Active Directory**여야 하고, **SAML Single Sign-On** 확인란이 선택되어 있어야 하며, Azure Active Directory의 사용자 이름은 **Active Directory 또는 인증서 ID**여야 합니다(아래 스크린샷 참조).

![Costpoint 사용자](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Costpoint 타일을 선택하면 SSO를 설정했기 때문에 Costpoint 애플리케이션에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 앱을 Azure Active Directory와 통합하는 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
