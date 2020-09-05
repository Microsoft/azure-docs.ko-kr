---
title: '자습서: Sage Intacct와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Sage Intacct 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: jeedes
ms.openlocfilehash: 79a77d3c55ee4ac545712329f0054123bec8fcae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535845"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>자습서: Azure Active Directory와 Sage Intacct 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sage Intacct를 통합하는 방법에 대해 알아봅니다. Azure AD와 Sage Intacct를 통합하는 경우 다음을 수행할 수 있습니다.

* Sage Intacct에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Sage Intacct에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Sage Intacct SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Sage Intacct는 **IDP** 시작 SSO를 지원합니다.
* Sage Intacct가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sage-intacct-from-the-gallery"></a>갤러리에서 Sage Intacct 추가

Sage Intacct의 Azure AD 통합을 구성하려면 갤러리의 Sage Intacct를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Sage Intacct**를 입력합니다.
1. 결과 패널에서 **Sage Intacct**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Sage Intacct에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Sage Intacct에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Sage Intacct의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sage Intacct에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
2. **[Sage Intacct SSO 구성](#configure-sage-intacct-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Sage Intacct 테스트 사용자 만들기](#create-sage-intacct-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Sage Intacct에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Sage Intacct** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **회신 URL** 텍스트 상자에서 `https://www.intacct.com/ia/acct/sso_response.phtml` URL을 입력합니다.

1. Sage Intacct 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Sage Intacct 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성 및 클레임** 대화 상자에서 다음 단계를 수행하여 아래 표와 같이 SAML 토큰 특성을 추가합니다.

    | 특성 이름  |  원본 특성|
    | ---------------| --------------- |
    | 회사 이름 | **Sage Intacct 회사 ID** |
    | name | 이 값은 **Sage Intacct 테스트 사용자 만들기 섹션**에서 입력하는 Sage Intacct **사용자 ID**와 동일해야 합니다. 이 내용은 자습서의 뒷부분에서 설명합니다. |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력하거나 선택합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Sage Intacct 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Sage Intacct에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Sage Intacct**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sage-intacct-sso"></a>Sage Intacct SSO 구성

1. 다른 웹 브라우저 창에서 Sage Intacct 회사 사이트에 관리자로 로그인합니다.

1. **회사** 탭을 클릭하고 **회사 정보**를 클릭합니다.

    ![회사](./media/intacct-tutorial/ic790037.png "회사")

1. **보안** 탭을 클릭한 다음 **편집**을 클릭합니다.

    ![보안](./media/intacct-tutorial/ic790038.png "보안")

1. **Single Sign-On** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign On](./media/intacct-tutorial/ic790039.png "Single Sign On")

    a. **Single Sign-On 사용**을 선택합니다.

    b. **ID 공급자 유형**으로 **SAML 2.0**을 선택합니다.

    다. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **발급자 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    e. **Base-64**로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음, **인증서** 상자에 붙여넣습니다.

    f. **저장**을 클릭합니다.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct 테스트 사용자 만들기

Azure AD 사용자가 Sage Intacct에 로그인할 수 있도록 설정하려면 Sage Intacct로 프로비저닝되어야 합니다. Sage Intacct의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Sage Intacct** 테넌트에 로그인합니다.

1. **회사** 탭을 클릭하고 **사용자**를 클릭합니다.

    ![사용자](./media/intacct-tutorial/ic790041.png "사용자")

1. **추가** 탭을 클릭합니다.

    ![추가](./media/intacct-tutorial/ic790042.png "추가")

1. **사용자 정보** 섹션에서 다음 단계를 수행합니다.

    ![사용자 정보](./media/intacct-tutorial/ic790043.png "사용자 정보")

    a. **사용자 정보** 섹션으로 프로비전하려는 Azure AD 계정의 **사용자 ID**, **성**, **이름**, **전자 메일 주소**, **직함** 및 **전화 번호**를 입력합니다.

    > [!NOTE]
    > 위 스크린샷의 **사용자 ID**와 Azure Portal의 **사용자 특성** 섹션에 있는 **이름** 특성과 매핑되는 **원본 특성** 값이 동일해야 합니다.

    b. 프로비전하려는 Azure AD 계정의 **관리자 권한**을 선택합니다.

    다. **저장**을 클릭합니다. 
    
    d. Azure AD 계정 보유자에게 전자 메일이 발송되며 여기에 포함된 링크를 클릭하여 계정을 확인하면 계정이 활성화됩니다.

1. **Single Sign-On** 탭을 클릭하고 아래 스크린샷의 **페더레이션된 SSO 사용자 ID**와 Azure Portal의 **사용자 특성** 섹션에 있는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`와 매핑되는 **원본 특성** 값이 동일해야 합니다.

    ![사용자 정보](./media/intacct-tutorial/ic790044.png "사용자 정보")

> [!NOTE]
> Azure AD 사용자 계정을 프로비저닝하려면 다른 Sage Intacct 사용자 계정 생성 도구 또는 Sage Intacct에서 제공한 API를 사용하면 됩니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Sage Intacct 타일을 클릭하면 SSO를 설정한 Sage Intacct에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
