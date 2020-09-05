---
title: '자습서: Raketa와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Raketa 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: f5a01d74d22d6ea010d80ef1ef8ce1a53fbd4422
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548816"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>자습서: Raketa와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Raketa를 통합하는 방법에 대해 알아봅니다. Azure AD와 Raketa를 통합하면 다음을 수행할 수 있습니다.

* Raketa에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Raketa에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Raketa SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Raketa에서 **SP** 시작 SSO를 지원합니다.
* Raketa가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>갤러리에서 Raketa 추가

Raketa의 Azure AD 통합을 구성하려면 갤러리의 Raketa를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스[1]를 선택합니다.

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. **엔터프라이즈 애플리케이션**[2]으로 이동한 다음, **모든 애플리케이션**[3]을 선택합니다.

1. 새 애플리케이션을 추가하려면 **새 애플리케이션**[4]을 선택합니다. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. **갤러리에서 추가**[5] 섹션의 검색 상자[6]에 **Raketa**를 입력합니다.

1. 결과 패널[7]에서 **Raketa**를 선택한 다음, **추가** 단추[8]를 클릭합니다. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Raketa용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Raketa에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Raketa의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Raketa에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Raketa SSO 구성](#configure-raketa-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Raketa 테스트 사용자 만들기](#create-raketa-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Raketa에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Raketa** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**[9]을 선택합니다.

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. **Single Sign-On 방법 선택** 페이지에서[9] **SAML**[10]을 선택합니다.

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**[11]에 대한 편집/펜 아이콘을 클릭하여 설정을 편집합니다.

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    1. **식별자(엔터티 ID)** [12] 및 **로그온 URL**[14] 텍스트 상자에 URL `https://raketa.travel/`을 입력합니다.

    1. **회신 URL** 텍스트 상자[13]에 `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` 패턴을 사용하여 URL을 입력합니다.  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 이 값을 가져오려면 [Raketa 클라이언트 지원 팀](mailto:help@raketa.travel)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾고, **다운로드**[15]를 선택하여 인증서를 컴퓨터에 다운로드하고 저장합니다.

1. **Raketa 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    1. 로그인 URL [16] – 사용자를 인증 시스템으로 리디렉션하는 데 사용되는 권한 부여 웹 페이지 URL입니다.

    1. Azure AD 식별자 [17] – Azure AD 식별자입니다.

    1. 로그아웃 URL [18] – 로그아웃 후 사용자를 리디렉션하는 데 사용되는 웹 페이지 URL입니다.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**[1], **사용자**[19], **모든 사용자**[20]를 차례로 선택합니다.

1. 다음과 같이 화면의 위쪽에서 **새 사용자**[21]를 선택합니다.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. **사용자** 속성에서 다음 단계를 수행합니다.

   1. **사용자 이름** 필드[22]에 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.

   1. **이름** 필드[23]에 `B.Simon`을 입력합니다.

   1. **암호 표시** 확인란[25]을 선택한 다음, **암호** 상자[24]에 표시된 값을 적어둡니다.

   1. **만들기**[26]를 클릭합니다. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Raketa에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**[2], **모든 애플리케이션**[3]을 차례로 선택합니다.

1. 애플리케이션 목록에서 **Raketa**[27]를 선택합니다.  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹**[28]을 선택합니다. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. **사용자 추가**[29]를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**[30]을 선택합니다.

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**[31]을 선택한 다음, 화면 아래쪽에서 **선택**[32] 단추를 클릭합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추[33]를 클릭합니다. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Raketa SSO 구성

**Raketa** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **인증서(Base64)** 와 적절히 복사한 URL을 [Raketa 지원 팀](mailto:help@raketa.travel)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-raketa-test-user"></a>Raketa 테스트 사용자 만들기

이 섹션에서는 Raketa에서 B.Simon이라는 사용자를 만듭니다. Raketa 플랫폼에서 사용자를 추가하려면 [Raketa 지원 팀](mailto:help@raketa.travel)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Raketa 타일을 클릭하면 SSO를 설정한 Raketa에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Raketa 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Raketa를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)