---
title: '자습서: NS1 SSO for Azure와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 NS1 SSO for Azure 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: d0fee044506a9a19e09478ef8d70b3719ecc167a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554261"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>자습서: NS1 SSO for Azure와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 NS1 SSO for Azure를 통합하는 방법에 대해 알아봅니다. Azure AD와 Single NS1 SSO for Azure를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 NS1 SSO for Azure에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 NS1 SSO for Azure에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* NS1 SSO for Azure SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* NS1 SSO for Azure에서 SP 및 IDP 시작 SSO를 지원합니다.
* NS1 SSO for Azure가 구성되면 세션 제어를 적용할 수 있습니다. 이를 통해 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>갤러리에서 NS1 SSO for Azure 추가

NS1 SSO for Azure가 Azure AD에 통합되도록 구성하려면 갤러리의 NS1 SSO for Azure를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **NS1 SSO for Azure**를 입력합니다.
1. 결과 패널에서 **NS1 SSO for Azure**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>NS1 SSO for Azure에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 NS1 SSO for Azure에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 NS1 SSO for Azure의 관련 사용자 간에 연결 관계를 설정합니다.

NS1 SSO for Azure를 사용하여 Azure AD SSO를 구성하고 테스트하는 일반적인 단계는 다음과 같습니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.

    a. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.

    b. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[NS1 SSO for Azure SSO 구성](#configure-ns1-sso-for-azure-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.

    a. **[NS1 SSO for Azure 테스트 사용자 만들기](#create-an-ns1-sso-for-azure-test-user)** - B.Simon과 대응되는 사용자를 NS1 SSO for Azure에 만듭니다. 이 대응 사용자는 사용자의 Azure AD 표현에 연결됩니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **NS1 SSO for Azure** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://api.nsone.net/saml/metadata` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://api.nsone.net/saml/sso/<ssoid>` 패턴을 사용하는 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에서 `https://my.nsone.net/#/login/sso` URL을 입력합니다.

    > [!NOTE]
    > 회신 URL 값은 실제 값이 아닙니다. 실제 회신 URL로 회신 URL 값을 업데이트합니다. 이 값을 얻으려면 [NS1 SSO for Azure 클라이언트 지원 팀](mailto:techops@nsone.net)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. NS1 SSO for Azure 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 이러한 특성의 값은 애플리케이션 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 연필 아이콘을 선택하여 **사용자 특성** 대화 상자를 엽니다.

    ![연필 아이콘이 강조 표시된 사용자 특성 및 클레임 섹션의 스크린샷](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. 특성 이름을 선택하여 클레임을 편집합니다.

    ![특성 이름이 강조 표시된 사용자 특성 및 클레임 섹션의 스크린샷](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. **변환**을 선택합니다.

    ![변환이 강조 표시된 클레임 관리 섹션의 스크린샷](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. **변환 관리** 섹션에서 다음 단계를 수행합니다.

    ![다양한 필드가 강조 표시된 변환 관리 섹션의 스크린샷](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. **변환**으로 **ExactMailPrefix()** 를 선택합니다.

    1. **매개 변수 1**로 **user.userprincipalname**을 선택합니다.

    1. **추가**를 선택합니다.

    1. **저장**을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 선택합니다. 그러면 **앱 페더레이션 메타데이터 URL**이 복사되어 컴퓨터에 저장됩니다.

    ![복사 단추가 강조 표시된 SAML 서명 인증서의 스크린샷](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.

   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 필드에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 NS1 SSO for Azure에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **NS1 SSO for Azure**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹이 강조 표시된 관리 섹션의 스크린샷](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가가 강조 표시된 사용자 및 그룹 페이지의 스크린샷](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO for Azure SSO 구성

NS1 SSO for Azure 쪽에서 Single Sign-On을 구성하려면 앱 페더레이션 메타데이터 URL을 [NS1 SSO for Azure 지원 팀](mailto:techops@nsone.net)에 보내야 합니다. 해당 팀에서 SAML SSO 연결이 양쪽에 제대로 설정되도록 구성합니다.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>NS1 SSO for Azure 테스트 사용자 만들기

이 섹션에서는 NS1 SSO for Azure에서 B.Simon이라는 사용자를 만듭니다. NS1 SSO for Azure 지원 팀과 협력하여 사용자를 NS1 SSO for Azure 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 NS1 SSO for Azure 타일을 클릭하면 SSO를 설정한 NS1 SSO for Azure에 자동으로 로그인됩니다. 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 NS1 SSO for Azure 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)