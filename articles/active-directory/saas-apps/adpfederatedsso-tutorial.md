---
title: '자습서: ADP와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 ADP 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: c170581496241ab3be0fe54bb484549ab4b1f976
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542907"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>자습서: ADP와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 ADP를 통합하는 방법에 대해 알아봅니다. Azure AD와 ADP를 통합하면 다음과 같은 일을 할 수 있습니다.

* ADP에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 ADP에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* ADP SSO(Single Sign-On)이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* ADP에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-adp-from-the-gallery"></a>갤러리에서 ADP 추가

ADP의 Azure AD 통합을 구성하려면 갤러리의 ADP를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ADP**를 입력합니다.
1. 결과 패널에서 **ADP**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>ADP용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 ADP에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ADP의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ADP에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[ADP SSO 구성](#configure-adp-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[ADP 테스트 사용자 만들기](#create-adp-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 ADP에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **ADP** 애플리케이션 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다. 

    ![Single Sign-On 속성](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    b. **사용자 액세스 URL**을 복사하고 자습서의 뒷부분에서 설명되는 **로그온 URL 구성 섹션**에 붙여넣어야 합니다.

    다. **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    d. **사용자가 볼 수 있습니까** 필드 값을 **아니요**로 설정합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ADP** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자(엔터티 ID)** 텍스트 상자에서 `https://fed.adp.com` URL을 입력합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **ADP 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 ADP에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **ADP**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-adp-sso"></a>ADP SSO 구성

**ADP** 측에서 Single Sign-On을 구성하려면 [ADP 웹 사이트](https://adpfedsso.adp.com/public/login/index.fcc)에서 다운로드된 **메타데이터 XML**을 업로드해야 합니다.

> [!NOTE]  
> 이 프로세스는 며칠이 걸릴 수 있습니다.

### <a name="configure-your-adp-services-for-federated-access"></a>페더레이션 액세스에 대한 ADP 서비스 구성

>[!Important]
> ADP 서비스에 대한 페더레이션 액세스를 필요로 하는 직원은 ADP 서비스 앱에 할당되어야 하며 이후에 사용자는 특정 ADP 서비스에 다시 할당되어야 합니다.
ADP 담당자로부터 확인을 받는 즉시 ADP 서비스를 구성하고 특정 ADP 서비스에 대한 사용자 액세스를 제어하도록 사용자를 할당/관리합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ADP**를 입력합니다.
1. 결과 패널에서 **ADP**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.
1. Azure Portal의 **ADP** 애플리케이션 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다.  

    ![Single Sign-On 연결 속성](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    b.  **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    다.  **사용자가 볼 수 있습니까** 필드 값을 **예**로 설정합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ADP** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.

1. **Single Sign-On 방법 선택** 대화 상자에서 **모드**를 **연결됨**으로 선택합니다. 애플리케이션을 **ADP**에 연결하합니다.

    ![연결된 Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. **로그온 URL 구성** 섹션으로 이동하고 다음 단계를 수행합니다.

    ![Single Sign-On 속성](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. 위의 **속성 탭**(기본 ADP 앱)에서 복사한 **사용자 액세스 URL**을 붙여넣습니다.
                                                             
    b. 다음은 다른 **릴레이 상태 URL**을 지원하는 다섯 개의 앱입니다. 특정 애플리케이션에 대한 적절한 **릴레이 상태 URL** 값을 **사용자 액세스 URL**에 수동으로 추가해야 합니다.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **저장** 합니다.

10. ADP 담당자로부터 확인을 받는 즉시 한 명 또는 두 명의 사용자로 테스트를 시작합니다.

    a. ADP 서비스 앱에 적은 수의 사용자를 할당하여 페더레이션 액세스를 테스트합니다.

    b. 사용자가 갤러리에서 ADP 서비스 앱에 액세스하고 해당 ADP 서비스에 액세스할 수 있는 경우 테스트는 성공합니다.
 
11. 성공적인 테스트의 확인 후 페더레이션된 ADP 서비스를 개별 사용자 또는 사용자 그룹에 할당하고(자습서의 뒷부분에서 설명됨) 직원에게 롤아웃합니다.

### <a name="create-adp-test-user"></a>ADP 테스트 사용자 만들기

이 섹션은 ADP에서 B.Simon이라는 사용자를 만들기 위한 것입니다. ADP 계정에 사용자를 추가하려면 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)에 문의하세요. 

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ADP 타일을 클릭하면 SSO를 설정한 ADP에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 ADP 사용해 보기](https://aad.portal.azure.com)
