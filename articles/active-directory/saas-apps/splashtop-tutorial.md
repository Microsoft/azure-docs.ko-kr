---
title: '자습서: Splashtop과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Splashtop 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 72c7a24f165d48d3ba2ea0dbcc2b41c818e3f1d7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>자습서: Splashtop과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Splashtop을 통합하는 방법에 대해 알아봅니다. Azure AD와 Splashtop을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Splashtop에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Splashtop에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Splashtop SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Splashtop에서 **SP** 시작 SSO를 지원합니다.

* Splashtop이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>갤러리에서 Splashtop 추가

Splashtop이 Azure AD에 통합되도록 구성하려면 갤러리의 Splashtop을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Splashtop**을 입력합니다.
1. 결과 패널에서 **Splashtop**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Splashtop에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Splashtop에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Splashtop의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Splashtop에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Splashtop SSO 구성](#configure-splashtop-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Splashtop 테스트 사용자 만들기](#create-splashtop-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Splashtop에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Splashtop** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **로그온 URL** 텍스트 상자에 `https://my.splashtop.com/login/sso` URL을 입력합니다.

1. Splashtop 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성 목록을 보여 주지만, **nameidentifier**는 **user.userprincipalname**과 매핑됩니다. TicketManager 애플리케이션에서는 **nameidentifier**가 **user.mail**과 매핑되어야 하기 때문에, **편집** 아이콘을 클릭하여 특성 매핑을 편집하고 특성 매핑을 변경해야 합니다.

    ![이미지](common/edit-attribute.png)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾고, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Splashtop 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Splashtop에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Splashtop**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-splashtop-sso"></a>Splashtop SSO 구성

이 섹션에서는 [Splashtop 웹 포털](https://my.splashtop.com/login)에서 새 SSO 방법을 신청해야 합니다.
1. Splashtop 웹 포털에서 **Account info(계정 정보)**  / **Team(팀)** 탭으로 차례로 이동하고, 아래로 스크롤하여 **Single Sign On** 섹션을 찾습니다. 그런 다음, **Apply for new SSO method(새 SSO 방법 신청)** 를 클릭합니다.

    ![이미지](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. 신청 창에서 **SSO name(SSO 이름)** 을 제공합니다. 예를 들어 [New Azure(새 Azure)]를 선택한 다음, IDP 유형으로 **Azure**를 선택하고, Azure Portal의 Splashtop 애플리케이션에서 복사한 **로그인 URL** 및 **Azure AD 식별자**를 삽입합니다.

    ![이미지](media/splashtop-tutorial/azure-sso-1.png)

1. 인증서 정보의 경우 마우스 오른쪽 단추로 Azure Portal의 Splashtop 애플리케이션에서 다운로드한 인증서 파일을 클릭하고, 메모장에서 편집한 다음, 내용을 복사하여 **Download Certificate (Base64)(인증서(Base64) 다운로드)** 필드에 붙여넣습니다.

    ![이미지](media/splashtop-tutorial/cert-1.png) ![이미지](media/splashtop-tutorial/cert-2.png) ![이미지](media/splashtop-tutorial/azure-sso-2.png)

1. 이것으로 끝입니다. **Save(저장)** 를 클릭합니다. 그러면 Splashtop SSO 유효성 검사 팀에서 사용자에게 확인 정보를 요청한 다음, SSO 방법을 활성화합니다.

### <a name="create-splashtop-test-user"></a>Splashtop 테스트 사용자 만들기

1. SSO 방법이 활성화되면 새로 만든 SSO 방법을 확인하여 **Single Sign On** 섹션에서 사용하도록 설정합니다.

    ![이미지](media/splashtop-tutorial/enable.png)

1. 새로 만든 SSO 방법을 사용하여 테스트 사용자(예: `B.Simon@contoso.com`)를 Splashtop 팀에 초대합니다.

    ![이미지](media/splashtop-tutorial/invite.png)

1. 기존 Splashtop 계정을 SSO 계정으로 변경할 수도 있습니다([지침](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-) 참조).

1. 이것으로 끝입니다. SSO 계정을 사용하여 Splashtop 웹 포털 또는 Splashtop Business 앱에 로그인할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Splashtop 타일을 클릭하면 SSO를 설정한 Splashtop에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Splashtop 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Splashtop을 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)