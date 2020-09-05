---
title: '자습서: IamIP Patent Platform과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 IamIP Patent Platform 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: 1a2139b7a0f7d51b6c759a98c93c5250666f15a9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525142"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>자습서: IamIP Patent Platform과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 IamIP Patent Platform을 통합하는 방법에 대해 알아봅니다. Azure AD와 IamIP Patent Platform을 통합하면 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 IamIP Patent Platform에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 IamIP Patent Platform에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 IamIP Patent Platform 구독

## <a name="tutorial-description"></a>자습서 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

IamIP Patent Platform에서 SP 시작 및 IDP 시작 SSO를 지원합니다.

IamIP Patent Platform이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>갤러리에서 IamIP Patent Platform 추가

IamIP Patent Platform이 Azure AD에 통합되도록 구성하려면 갤러리의 IamIP Patent Platform을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **IamIP Patent Platform**을 입력합니다.
1. 결과 패널에서 **IamIP Patent Platform**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>IamIP Patent Platform에 대한 Azure AD SSO 구성 및 테스트

B.Simon이라는 테스트 사용자를 사용하여 IamIP Patent Platform에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 IamIP Patent Platform의 해당 사용자 간에 연결 관계를 설정해야 합니다.

IamIP Patent Platform에서 Azure AD SSO를 구성하고 테스트하려면 다음과 같은 개략적인 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 설정합니다.
    * **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
    * **[테스트 사용자에게 액세스 권한 부여](#grant-access-to-the-test-user)** - 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 설정합니다.

1. **[IamIP Patent Platform SSO 구성](#configure-iamip-patent-platform-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[IamIP Patent Platform 테스트 사용자 만들기](#create-iamip-patent-platform-test-user)** - 사용자의 Azure AD 표현에 해당하는 사용자를 만듭니다.

1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **IamIP Patent Platform** 애플리케이션 통합 페이지에 있는 **관리** 섹션에서 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성에 대한 연필 아이콘](common/edit-urls.png)

1. 서비스 공급자 메타데이터 파일이 있고 IDP 시작 모드에서 SSO를 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 선택합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. 폴더 단추를 선택하고, 메타데이터 파일을 선택한 다음, **업로드**를 선택합니다.

    ![폴더 및 업로드 단추](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 섹션에 자동으로 채워집니다.

    ![식별자 및 회신 URL 값](common/idp-intiated.png)

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 요구 사항에 따라 값을 수동으로 입력합니다.

1. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, 다음 단계를 수행합니다.

    **로그온 URL** 상자에서 **https:\//patents.iamip.com/login-user**를 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(원시)** 에 대한 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificateraw.png)

1. **IamIP Patent Platform 설정** 섹션에서 요구 사항에 따라 하나 이상의 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 완료합니다.
   1. **이름** 상자에서 **B.Simon**을 입력합니다.  
   1. **사용자 이름** 상자에 \<username>@\<companydomain>.\<extension>을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="grant-access-to-the-test-user"></a>테스트 사용자에게 액세스 권한 부여

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 해당 사용자에게 IamIP Patent Platform에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **IamIP Patent Platform**을 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹 선택](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon**을 선택하고, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

## <a name="configure-iamip-patent-platform-sso"></a>IamIP Patent Platform SSO 구성

IamIP Patent Platform 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 원시 인증서와 적절히 복사한 URL을 [IamIP Patent Platform 지원 팀](mailto:info@iamip.com)에 보내야 합니다. 팀에서 양쪽의 SAML SSO 연결을 모두 올바르게 구성합니다.

### <a name="create-iamip-patent-platform-test-user"></a>IamIP Patent Platform 테스트 사용자 만들기

[IamIP Patent Platform 지원 팀](mailto:info@iamip.com)과 협력하여 B.Simon이라는 사용자를 IamIP Patent Platform에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 IamIP Patent Platform 타일을 클릭하면 SSO를 설정한 IamIP Patent Platform에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 IamIP Patent Platform 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
