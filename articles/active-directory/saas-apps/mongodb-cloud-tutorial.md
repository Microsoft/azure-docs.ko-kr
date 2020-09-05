---
title: '자습서: MongoDB Cloud와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 MongoDB Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 6a59df062adf9b5c2e511db29cb8601d4a4df70e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554569"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>자습서: MongoDB Cloud와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 MongoDB Cloud와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다. Azure AD와 MongoDB Cloud를 통합하면 다음을 수행할 수 있습니다.

* MongoDB Cloud, MongoDB Atlas, MongoDB Community, MongoDB 대학 및 MongoDB 지원에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 MongoDB Cloud에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 MongoDB Cloud 조직은 [사용 가능한 클러스터](https://www.mongodb.com/cloud)에 등록할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* MongoDB Cloud에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.
* MongoDB Cloud에서 **Just In Time** 사용자 프로비저닝을 지원합니다.
* MongoDB Cloud를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. 자세한 내용은 [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)를 참조하세요.

## <a name="add-mongodb-cloud-from-the-gallery"></a>갤러리에서 MongoDB Cloud 추가

MongoDB Cloud가 Azure AD에 통합되도록 구성하려면 갤러리에서 MongoDB Cloud를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **MongoDB Cloud**를 입력합니다.
1. 결과에서 **MongoDB Cloud**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>MongoDB Cloud용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 MongoDB Cloud에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 MongoDB Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

MongoDB Cloud에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [MongoDB Cloud SSO 구성](#configure-mongodb-cloud-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. [MongoDB Cloud 테스트 사용자 만들기](#create-a-mongodb-cloud-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 MongoDB Cloud에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **MongoDB Cloud** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. **회신 URL** 텍스트 상자에서 `https://auth.mongodb.com/sso/saml2/<Customer_Unique>` 패턴을 사용하는 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://cloud.mongodb.com/sso/<Customer_Unique>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 가져오려면 [MongoDB Cloud 클라이언트 지원 팀](https://support.mongodb.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. MongoDB Cloud 애플리케이션에는 SAML 어설션이 특정 형식이어야 하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![기본 특성의 스크린샷](common/default-attributes.png)

1. 위의 특성 외에도 MongoDB Cloud 애플리케이션에는 SAML 응답에서 다시 전달되는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | 속성 | 원본 특성|
    | ---------------| --------- |
    | 이메일 | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾습니다. **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![다운로드 링크가 강조 표시된 SAML 서명 인증서 섹션의 스크린샷](common/metadataxml.png)

1. **MongoDB Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![URL이 강조 표시된 Mongo DB Cloud 설정 섹션의 스크린샷](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, 암호를 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 MongoDB Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **MongoDB Cloud**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹이 강조 표시된 관리 섹션의 스크린샷](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가가 강조 표시된 사용자 및 그룹 페이지의 스크린샷](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택합니다. 화면 하단에서 **선택**을 누릅니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 화면 하단에서 **선택**을 누릅니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

## <a name="configure-mongodb-cloud-sso"></a>MongoDB Cloud SSO 구성

MongoDB 클라우드 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 복사한 적절한 URL이 필요합니다. MongoDB Cloud 조직의 페더레이션 애플리케이션도 구성해야 합니다. [MongoDB Cloud 설명서](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/)의 지침을 따릅니다. 문제가 있는 경우 [MongoDB Cloud 지원 팀](https://support.mongodb.com/)에 문의하세요.

### <a name="create-a-mongodb-cloud-test-user"></a>MongoDB Cloud 테스트 사용자 만들기

MongoDB Cloud는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 수행할 추가 작업이 없습니다. MongoDB Cloud에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 MongoDB Cloud 타일을 선택하면 SSO를 설정한 MongoDB Cloud에 자동으로 로그인됩니다. 자세한 내용은 [내 앱 포털에서 앱에 로그인하여 시작](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure에서 MongoDB Atlas 등록](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mongodb_atlas_may_2020?tab=Overview)

- [Azure AD로 MongoDB Cloud 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 MongoDB Cloud 보호](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

