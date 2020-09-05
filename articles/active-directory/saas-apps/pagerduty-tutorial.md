---
title: '자습서: PagerDuty와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 PagerDuty 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2bc486987dd25d899728af4861c0b71ef14d9f4a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554090"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>자습서: PagerDuty와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 PagerDuty를 통합하는 방법을 알아봅니다. Azure AD와 PagerDuty를 통합하면 다음을 수행할 수 있습니다.

* PagerDuty에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 PagerDuty에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* PagerDuty SSO(Single Sign-On)가 설정된 구독입니다.

> [!NOTE]
> Azure AD에서 MFA 또는 Passwordless 인증을 사용하는 경우 SAML 요청에서 AuthnContext 값을 해제합니다. 그렇지 않으면 Azure AD는 AuthnContext의 불일치에 대한 오류를 throw하고 토큰을 애플리케이션으로 다시 보내지 않습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* PagerDuty에서 **SP** 시작 SSO를 지원합니다.
* PagerDuty를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>갤러리에서 PagerDuty 추가

PagerDuty가 Azure AD에 통합되도록 구성하려면 갤러리의 PagerDuty를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **PagerDuty**를 입력합니다.
1. 결과 패널에서 **PagerDuty**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>PagerDuty용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 PagerDuty에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 PagerDuty의 관련 사용자 간에 연결 관계를 설정해야 합니다.

PagerDuty에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[PagerDuty SSO 구성](#configure-pagerduty-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[PagerDuty 테스트 사용자 만들기](#create-pagerduty-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 PagerDuty에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **PagerDuty** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.pagerduty.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<tenant-name>.pagerduty.com` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://<tenant-name>.pagerduty.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트합니다. 이러한 값을 얻으려면 [PagerDuty 클라이언트 지원 팀](https://www.pagerduty.com/support/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **PagerDuty 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 PagerDuty에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **PagerDuty**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-pagerduty-sso"></a>PagerDuty SSO 구성

1. 다른 웹 브라우저 창에서 Pagerduty 회사 사이트에 관리자로 로그인합니다.

2. 위쪽의 메뉴에서 **계정 설정**을 클릭합니다.

    ![계정 설정](./media/pagerduty-tutorial/ic778535.png "계정 설정")

3. **Single Sign-On**을 클릭합니다.

    ![Single Sign-On](./media/pagerduty-tutorial/ic778536.png "SSO(Single sign-on)")

4. **SSO(Single Sign-On) 사용** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 사용](./media/pagerduty-tutorial/ic778537.png "Single Sign-On 사용")

    a. Azure Portal에서 다운로드한 Base 64로 인코딩된 인증서를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음, **X.509 인증서** 텍스트 상자에 붙여넣습니다.
  
    b. **로그인 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL**을 붙여넣습니다.
  
    다. **로그아웃 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그아웃 URL**을 붙여넣습니다.

    d. **Allow username/password login**(사용자 이름/암호 로그인 허용)을 선택합니다.

    e. **Require EXACT authentication context comparison**(정확하게 일치 인증 컨텍스트 비교 필요) 확인란을 선택합니다.

    f. **변경 내용 저장**을 클릭합니다.

### <a name="create-pagerduty-test-user"></a>PagerDuty 테스트 사용자 만들기

Azure AD 사용자가 PagerDuty에 로그인할 수 있도록 하려면 이 사용자를 PagerDuty로 프로비저닝하여야 합니다. PagerDuty의 경우 프로비전은 수동 작업입니다.

> [!NOTE]
> 다른 PagerDuty 사용자 계정 생성 도구 또는 PagerDuty에서 제공하는 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전할 수 있습니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Pagerduty** 테넌트에 로그인합니다.

2. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

3. **사용자 추가**를 클릭합니다.
   
    ![사용자 추가](./media/pagerduty-tutorial/ic778539.png "사용자 추가")

4.  **팀 초대** 대화 상자에서 다음 단계를 수행합니다.
   
    ![팀 초대](./media/pagerduty-tutorial/ic778540.png "팀 초대")

    a. 사용자의 **성과 이름**을 입력합니다(예: **B.Simon**). 
   
    b. 사용자의 **이메일 주소**(예: **b.simon\@contoso.com**)를 입력합니다.
   
    다. **추가**를 클릭한 다음 **초대장 보내기**를 클릭합니다.
   
    > [!NOTE]
    > 추가된 모든 사용자는 PagerDuty 계정을 만들도록 초대를 받게 됩니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 PagerDuty 타일을 클릭하면 SSO를 설정한 PagerDuty에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 PagerDuty 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 PagerDuty를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

