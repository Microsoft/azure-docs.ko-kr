---
title: '자습서: Azure Active Directory와 Zscaler ZSCloud 통합 | Microsoft Docs'
description: Azure Active Directory와 Zscaler ZSCloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 21a6d6f728b11c3dee166921538bb844d1e72f70
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545821"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>자습서: Azure Active Directory와 Zscaler ZSCloud 통합

이 자습서에서는 Zscaler ZSCloud를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.
Zscaler ZSCloud를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* Zscaler ZSCloud에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Zscaler ZSCloud에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Zscaler ZSCloud와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Zscaler ZSCloud Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Zscaler ZSCloud는 **SP** 시작 SSO를 지원합니다.

* Zscaler ZSCloud는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>갤러리에서 Zscaler ZSCloud 추가

Zscaler ZSCloud와 Azure AD의 통합을 구성하려면 갤러리의 Zscaler ZSCloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zscaler ZSCloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zscaler ZSCloud**를 입력하고 결과 패널에서 **Zscaler ZSCloud**를 선택하고 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Zscaler ZSCloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Zscaler ZSCloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Zscaler ZSCloud Single Sign-On 구성](#configure-zscaler-zscloud-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Zscaler ZSCloud 테스트 사용자 만들기](#create-zscaler-zscloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Zscaler ZSCloud에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zscaler ZSCloud** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Zscaler ZSCloud 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    **로그온 URL** 텍스트 상자에 사용자가 ZScaler ZSCloud 애플리케이션에 로그인하는 데 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이 값을 실제 로그인 URL로 업데이트해야 합니다. 이 값을 얻으려면 [Zscaler ZSCloud 클라이언트 지원 팀](https://help.zscaler.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. Zscaler ZSCloud 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도, Zscaler ZSCloud 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.
    
    | 속성 | 원본 특성 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.
    
    f. **저장**을 클릭합니다.

    > [!NOTE]
    > [여기](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Zscaler ZSCloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Zscaler ZSCloud Single Sign-On 구성

1. Zscaler ZSCloud 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Zscaler ZSCloud 설정**을 클릭하면 Zscaler ZSCloud 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Zscaler ZSCloud에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![SSO 설정](common/setup-sso.png)

3. Zscaler ZSCloud를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Zscaler ZSCloud 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. **관리 > 인증 > 인증 설정**으로 이동하고 다음 단계를 수행합니다.
   
    ![관리](./media/zscaler-zscloud-tutorial/ic800206.png "관리")

    a. 인증 형식에서 **SAML**을 선택합니다.

    b. **SAML 구성**을 클릭합니다.

5. **SAML 편집** 창에서 다음 단계를 수행하고 저장을 클릭합니다.  
            
    ![사용자 및 인증 관리](./media/zscaler-zscloud-tutorial/ic800208.png "사용자 & 인증 관리")
    
    a. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML 포털 URL** 텍스트 상자에 붙여넣습니다.

    b. **로그인 이름 특성** 텍스트 상자에 **NameID**을 입력합니다.

    다. **업로드**를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서**에 업로드합니다.

    d. **SAML 자동 프로비전 사용**을 선택/해제합니다.

    e. displayName 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **사용자 표시 이름 특성** 텍스트 상자에 **displayName**을 입력합니다.

    f. memberOf 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **그룹 이름 특성** 텍스트 상자에 **memberOf**를 입력합니다.

    g. 부서 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **부서 이름 특성** 텍스트 상자에 **부서**를 입력합니다.

    h. **저장**을 클릭합니다.

6. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![관리](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화**를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer**를 시작합니다.

2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.   
    
     ![인터넷 옵션](./media/zscaler-zscloud-tutorial/ic769492.png "인터넷 옵션")

3. **연결** 탭을 클릭합니다.   
  
     ![연결](./media/zscaler-zscloud-tutorial/ic769493.png "Connections")

4. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

5. 프록시 서버 섹션에서 다음 단계를 수행합니다.   
   
    ![프록시 서버](./media/zscaler-zscloud-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용**을 선택합니다.

    b. 주소 텍스트 상자에 **gateway.Zscaler ZSCloud.net**을 입력합니다.

    다. 포트 텍스트 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.

    e. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

6. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler ZSCloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Zscaler ZSCloud**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler ZSCloud**를 선택합니다.

    ![애플리케이션 목록의 Zscaler ZSCloud 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 목록에서 **Britta Simon** 등의 사용자를 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. **역할 선택** 대화 상자의 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

    ![이미지](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >기본 액세스 역할은 프로비저닝을 중단하므로 지원되지 않으며, 따라서 사용자를 할당하는 동안 기본 역할을 선택할 수 없습니다.

### <a name="create-zscaler-zscloud-test-user"></a>Zscaler ZSCloud 테스트 사용자 만들기

이 섹션에서는 Zscaler ZSCloud에서 Britta Simon이라는 사용자를 만듭니다. Zscaler ZSCloud는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Zscaler ZSCloud에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Zscaler ZSCloud 지원 팀](https://help.zscaler.com/)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler ZSCloud 타일을 클릭하면 SSO를 설정한 Zscaler ZSCloud에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

