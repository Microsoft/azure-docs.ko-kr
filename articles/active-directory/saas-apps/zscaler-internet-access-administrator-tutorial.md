---
title: '자습서: Zscaler Internet Access Administrator와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Zscaler Internet Access Administrator 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 0cbcfb65d7c7ac71b17e38dd72545a3d8fd2f162
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545883"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>자습서: Zscaler Internet Access Administrator와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zscaler Internet Access Administrator를 통합하는 방법을 알아봅니다.
Zscaler Internet Access Administrator를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Zscaler Internet Access Administrator에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Zscaler Internet Access Administrator에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Zscaler Internet Access Administrator와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Zscaler Internet Access Administrator 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Zscaler Internet Access Administrator 지원 **IDP** 시작 SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>갤러리에서 Zscaler Internet Access Administrator 추가

Zscaler Internet Access Administrator의 Azure AD 통합을 구성하려면 갤러리의 Zscaler Internet Access Administrator를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zscaler Internet Access Administrator를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zscaler Internet Access Administrator**를 입력하고 결과 패널에서  **Zscaler Internet Access Administrator**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Zscaler Internet Access Administrator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Zscaler Internet Access Administrator에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Zscaler Internet Access Administrator의 관련 사용자 간에 연결이 형성되어야 합니다.

Zscaler Internet Access Administrator에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Zscaler Internet Access Administrator Single Sign-On 구성](#configure-zscaler-internet-access-administrator-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-on 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Zscaler Internet Access Administrator 테스트 사용자 만들기](#create-zscaler-internet-access-administrator-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Internet Access Administrator에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Zscaler Internet Access Administrator에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zscaler Internet Access Administrator** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Zscaler Internet Access Administrator 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에 요구 사항에 따라 URL을 입력합니다.

    | ID |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. **회신 URL** 텍스트 상자에 요구 사항에 따라 URL을 입력합니다.

    | 회신 URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정 페이지**에서 **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![특성 링크](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | Name  | 원본 특성  |
    | ---------| ------------ |
    | 역할     | user.assignedroles |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./common/new-save-attribute.png)
    
    ![이미지](./common/new-attribute-details.png)

    b. **원본 특성** 목록에서 특성 값을 선택합니다.

    다. **Ok**를 클릭합니다.

    d. **저장**을 클릭합니다.

    > [!NOTE]
    > [여기](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Zscaler Internet Access Administrator 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Zscaler Internet Access Administrator Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Zscaler Internet Access Administrator UI에 로그인합니다.

2. **관리 > 관리자 관리**로 이동하여 다음 단계를 수행하고 [저장]을 클릭합니다.

    ![관리](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "관리")

    a. **SAML 인증 사용**을 선택합니다.

    b. **업로드**를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서**에 업로드합니다.

    다. 필요에 따라 추가 보안을 위해 **발급자** 세부 정보를 추가하여 SAML 응답의 발급자를 확인합니다.

3. 관리자 UI에서 다음 단계를 수행합니다.

    ![관리](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화**를 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler Internet Access Administrator에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택하고, **모든 애플리케이션**을 선택한 다음, **Zscaler Internet Access Administrator**를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler Internet Access Administrator**를 입력하고 선택합니다.

    ![애플리케이션 목록의 Zscaler Internet Access Administrator 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Zscaler Internet Access Administrator 테스트 사용자 만들기

이 섹션은 Zscaler Internet Access Administrator에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Zscaler Internet Access는 관리자 SSO에 대한 Just-In-Time 프로비전을 지원하지 않습니다. 관리자 계정은 수동으로 만들어야 합니다.
관리자 계정을 만드는 방법에 대한 단계는 Zscaler 설명서를 참조하세요.

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler Internet Access Administrator 타일을 클릭하면 SSO를 설정한 Zscaler Internet Access Administrator UI에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
