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
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449326"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>자습서: Zscaler Internet Access Administrator와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zscaler Internet Access Administrator를 통합하는 방법을 알아봅니다. Zscaler Internet Access Administrator를 Azure AD와 통합하는 경우 다음을 수행할 수 있습니다.

* Zscaler Internet Access Administrator에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Zscaler Internet Access Administrator에 자동으로 로그온되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Zscaler Internet Access Administrator SSO(Single Sign-On)가 설정된 구독.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Zscaler Internet Access Administrator에서 **IDP** 시작 SSO를 지원합니다.

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>갤러리에서 Zscaler Internet Access Administrator 추가

Zscaler Internet Access Administrator의 Azure AD 통합을 구성하려면 갤러리의 Zscaler Internet Access Administrator를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Zscaler Internet Access Administrator** 를 입력합니다.
1. 결과 패널에서 **Zscaler Internet Access Administrator** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Zscaler Internet Access Administrator에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Zscaler Internet Access Administrator에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Zscaler Internet Access Administrator의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Zscaler Internet Access Administrator에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[Zscaler Internet Access Administrator SSO 구성](#configure-zscaler-internet-access-administrator-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Zscaler Internet Access Administrator 테스트 사용자 만들기](#create-zscaler-internet-access-administrator-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Internet Access Administrator에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Zscaler Internet Access Administrator** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자** 텍스트 상자에 요구 사항에 따라 다음 URL 중 하나를 입력합니다.

    | ID |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. **회신 URL** 텍스트 상자에 요구 사항에 따라 다음 URL 중 하나를 입력합니다.

    | 회신 URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정 페이지** 에서 **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![특성 링크](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | Name  | 원본 특성  |
    | ---------| ------------ |
    | 역할 | user.assignedroles |

    a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    b. **원본 특성** 목록에서 특성 값을 선택합니다.

    다. **Ok** 를 클릭합니다.

    d. **저장** 을 클릭합니다.

    > [!NOTE]
    > [여기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Zscaler Internet Access Administrator 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Zscaler Internet Access Administrator에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Zscaler Internet Access Administrator** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 위에서 설명한 대로 역할을 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Zscaler Internet Access Administrator SSO 구성

1. 다른 웹 브라우저 창에서 Zscaler Internet Access Administrator UI에 로그인합니다.

2. **관리 > 관리자 관리** 로 이동하여 다음 단계를 수행하고 [저장]을 클릭합니다.

    ![스크린샷은 SAML 인증 사용, SSL 인증서 업로드 및 발급자 지정 옵션이 있는 관리자 관리를 보여줍니다.](./media/zscaler-internet-access-administrator-tutorial/management.png "관리")

    a. **SAML 인증 사용** 을 선택합니다.

    b. **업로드** 를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서** 에 업로드합니다.

    다. 필요에 따라 추가 보안을 위해 **발급자** 세부 정보를 추가하여 SAML 응답의 발급자를 확인합니다.

3. 관리자 UI에서 다음 단계를 수행합니다.

    ![스크린샷은 단계를 수행할 수 있는 관리자 UI를 보여줍니다.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화** 를 클릭합니다.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Zscaler Internet Access Administrator 테스트 사용자 만들기

이 섹션은 Zscaler Internet Access Administrator에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Zscaler Internet Access는 관리자 SSO에 대한 Just-In-Time 프로비전을 지원하지 않습니다. 관리자 계정은 수동으로 만들어야 합니다.
관리자 계정을 만드는 방법에 대한 단계는 Zscaler 설명서를 참조하세요.

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Zscaler Internet Access Administrator에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Zscaler Internet Access Administrator 타일을 클릭하면 SSO를 설정한 Zscaler Internet Access Administrator에 자동으로 로그온됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Zscaler Internet Access Administrator가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
