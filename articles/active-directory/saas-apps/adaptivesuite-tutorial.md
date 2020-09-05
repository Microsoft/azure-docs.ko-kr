---
title: '자습서: Adaptive Insights와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adaptive Insights 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.openlocfilehash: 3471d79875fb34ea0285c086e7d908aa254957dc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537741"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>자습서: Adaptive Insights와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adaptive Insights를 통합하는 방법에 대해 알아봅니다. Azure AD와 Adaptive Insights를 통합하면 다음과 같은 일을 할 수 있습니다.

* Adaptive Insights에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Adaptive Insights에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Adaptive Insights SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Adaptive Insights는 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-adaptive-insights-from-the-gallery"></a>갤러리에서 Adaptive Insights 추가

Adaptive Insights가 Azure AD에 통합되도록 구성하려면 갤러리의 Adaptive Insights를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Adaptive Insights**를 입력합니다.
1. 결과 패널에서 **Adaptive Insights**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Adaptive Insights에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Adaptive Insights의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Adaptive Insights에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Adaptive Insights SSO 구성](#configure-adaptive-insights-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Adaptive Insights 테스트 사용자 만들기](#create-adaptive-insights-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Adaptive Insights에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Adaptive Insights** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://login.adaptiveinsights.com:443/samlsso/<unique-id>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://login.adaptiveinsights.com:443/samlsso/<unique-id>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > Adaptive Insights의 **SAML SSO 설정** 페이지에서 식별자(엔터티 ID)와 응답 URL 값을 가져올 수 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Adaptive Insights 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Adaptive Insights SSO 구성

1. 다른 웹 브라우저 창에서 Adaptive Insights 회사 사이트에 관리자로 로그인합니다.

2. **관리**로 이동합니다.

    ![관리자](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. **사용자 및 역할** 섹션에서 **SAML SSO 설정**을 클릭합니다.

    ![SAML SSO 설정 관리](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO 설정 관리")

4. **SAML SSO 설정** 페이지에서 다음 단계를 수행합니다.

    ![SAML SSO 설정](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO 설정")

    a. **ID 공급자 이름** 텍스트 상자에 구성할 이름을 입력합니다.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **ID 공급자 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 SSO URL** 텍스트 상자에 붙여 넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **ID 공급자 SSO URL** 텍스트 상자에 붙여 넣습니다.

    e. 다운로드한 인증서를 업로드하려면 **파일 선택**을 클릭합니다.

    f. 다음과 같이 선택합니다.

     * **SAML 사용자 ID**에 대해 **사용자의 Adaptive Insights 사용자 이름**을 선택합니다.

     * **SAML 사용자 ID 위치**에 대해 **제목의 NameID에서 사용자 ID**를 선택합니다.

     * **SAML NameID 형식**에 대해 **전자 메일 주소**를 선택합니다.

     * **SAML 사용**에 대해 **SAML SSO 및 Adaptive Insights 직접 로그인 허용**을 선택합니다.

    g. **Adaptive Insights SSO URL**을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자(엔터티 ID)** 및 **회신 URL** 텍스트 상자에 붙여넣습니다.

    h. **저장**을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Adaptive Insights에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Adaptive Insights**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-adaptive-insights-test-user"></a>Adaptive Insights 테스트 사용자 만들기

Azure AD 사용자가 Adaptive Insights에 로그인하려면 Adaptive Insights에 프로비저닝되어야 합니다. Adaptive Insights의 경우 프로비전은 수동 작업입니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. **Adaptive Insights** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **관리**로 이동합니다.

   ![관리자](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. **사용자 및 역할** 섹션에서 **사용자**를 클릭합니다.

   ![사용자 추가](./media/adaptivesuite-tutorial/IC805648.png "사용자 추가")

4. **새 사용자** 섹션에서 다음 단계를 수행합니다.

   ![제출](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. 관련된 텍스트 상자에 프로비저닝할 유효한 Azure Active Directory 사용자의 **이름**, **사용자 이름**, **이메일**, **암호**를 입력합니다.

   b. **역할**을 선택합니다.

   다. **제출**을 클릭합니다.

> [!NOTE]
> 다른 Adaptive Insights 사용자 계정 생성 도구 또는 Adaptive Insights가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adaptive Insights 타일을 클릭하면 SSO를 설정한 Adaptive Insights에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

