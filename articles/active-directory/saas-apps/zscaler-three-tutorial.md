---
title: '자습서: Zscaler Three와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Zscaler Three 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 00585105350e9858bbfd649a41273def162febcb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545780"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>자습서: Zscaler Three와 Azure Active Directory SSO(Single Sign-On) 연결

이 문서에서는 Azure AD(Azure Active Directory)와 Zscaler Three를 연결하는 방법에 대해 알아봅니다. Azure AD와 Zscaler Three를 연결할 경우 다음을 수행할 수 있습니다.

* Zscaler Three에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Zscaler Three에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Zscaler Three SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Zscaler Three는 **SP** 시작 SSO를 지원합니다.

* Zscaler Three는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-zscaler-three-from-the-gallery"></a>갤러리에서 Zscaler Three 추가

Zscaler Three가 Azure AD에 통합되도록 구성하려면 갤러리의 Zscaler Three를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Zscaler Three**를 입력합니다.
1. 결과 패널에서 **Zscaler Three**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Zscaler Three에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Zscaler Three에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Zscaler Three의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zscaler Three에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Zscaler Three SSO 구성](#configure-zscaler-three-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Zscaler Three 테스트 사용자 만들기](#create-zscaler-three-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Three에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zscaler Three** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **로그온 URL** 텍스트 상자에 `https://login.zscalerthree.net/sfc_sso` URL을 입력합니다.

1. Zscaler Three 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도 Zscaler Three 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | 속성 | 원본 특성 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    > [!NOTE]
    > [여기](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Zscaler Three 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Zscaler Three에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Zscaler Three**를 선택합니다.
1. **사용자 및 그룹** 대화 상자의 목록에서 **Britta Simon** 등의 사용자를 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. **역할 선택** 대화 상자의 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

    ![이미지](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Zscaler Three SSO 구성

1. Zscaler Three 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Zscaler Three 설정**을 클릭하면 Zscaler Three 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Zscaler Three에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설치 프로그램](common/setup-sso.png)

3. Zscaler Three를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Zscaler Three 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. **관리 > 인증 > 인증 설정**으로 이동하고 다음 단계를 수행합니다.
   
    ![관리](./media/zscaler-three-tutorial/ic800206.png "관리")

    a. 인증 형식에서 **SAML**을 선택합니다.

    b. **SAML 구성**을 클릭합니다.

5. **SAML 편집** 창에서 다음 단계를 수행하고 저장을 클릭합니다.  
            
    ![사용자 및 인증 관리](./media/zscaler-three-tutorial/ic800208.png "사용자 & 인증 관리")
    
    a. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML 포털 URL** 텍스트 상자에 붙여넣습니다.

    b. **로그인 이름 특성** 텍스트 상자에 **NameID**을 입력합니다.

    다. **업로드**를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서**에 업로드합니다.

    d. **SAML 자동 프로비전 사용**을 선택/해제합니다.

    e. displayName 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **사용자 표시 이름 특성** 텍스트 상자에 **displayName**을 입력합니다.

    f. memberOf 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **그룹 이름 특성** 텍스트 상자에 **memberOf**를 입력합니다.

    g. 부서 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **부서 이름 특성** 텍스트 상자에 **부서**를 입력합니다.

    h. **저장**을 클릭합니다.

6. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![관리](./media/zscaler-three-tutorial/ic800207.png)

    a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화**를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer**를 시작합니다.

2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.   
    
     ![인터넷 옵션](./media/zscaler-three-tutorial/ic769492.png "인터넷 옵션")

3. **연결** 탭을 클릭합니다.   
  
     ![연결](./media/zscaler-three-tutorial/ic769493.png "Connections")

4. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

5. 프록시 서버 섹션에서 다음 단계를 수행합니다.   
   
    ![프록시 서버](./media/zscaler-three-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용**을 선택합니다.

    b. 주소 텍스트 상자에 **gateway.Zscaler Three.net**을 입력합니다.

    다. 포트 텍스트 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.

    e. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

6. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="create-zscaler-three-test-user"></a>Zscaler Three 테스트 사용자 만들기

이 섹션에서는 Zscaler Three에서 B.Simon이라는 사용자를 만듭니다. Zscaler Three는 기본적으로 사용하도록 설정되는 Just-In-Time 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Zscaler Three에 사용자가 아직 없는 경우 Zscaler Three에 액세스하려고 할 때 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Zscaler Three 지원 팀](https://www.zscaler.com/company/contact)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler Three 타일을 클릭하면 SSO를 설정한 Zscaler Three에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Zscaler Three 사용해 보기](https://aad.portal.azure.com/)

