---
title: '자습서: ContractSafe Saml2 SSO와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 ContractSafe Saml2 SSO 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185627"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>자습서: ContractSafe Saml2 SSO와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ContractSafe Saml2 SSO를 통합하는 방법을 알아봅니다. Azure AD와 ContractSafe Saml2 SSO를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 ContractSafe Saml2 SSO에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 ContractSafe Saml2 SSO에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO를 사용하도록 설정된 ContractSafe Saml2 SSO 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. ContractSafe Saml2 SSO에서 **IDP** 시작 SSO를 지원합니다.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>갤러리에서 ContractSafe Saml2 SSO 추가

ContractSafe Saml2 SSO의 Azure AD 통합을 구성하려면 갤러리의 ContractSafe Saml2 SSO를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ContractSafe Saml2 SSO**를 입력합니다.
1. 결과 패널에서 **ContractSafe Saml2 SSO**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 ContractSafe Saml2 SSO에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ContractSafe Saml2 SSO의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ContractSafe Saml2 SSO에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
   * [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - **B.Simon** 계정을 사용하여 Azure AD SSO를 테스트합니다.
   * [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - **B.Simon**이 Azure AD SSO를 사용할 수 있도록 설정합니다.

1. [ContractSafe Saml2 SSO 구성](#configure-contractsafe-saml2-sso) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
   * [ContractSafe Saml2 SSO 테스트 사용자 만들기](#create-a-contractsafe-saml2-sso-test-user) - **B.Simon**의 Azure AD 표현과 연결된 해당 사용자를 ContractSafe Saml2 SSO에 만듭니다.
2. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ContractSafe Saml2 SSO** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(펜) 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 해당 필드에 다음 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/` 형식을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/` 형식을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [ContractSafe Saml2 SSO 클라이언트 지원 팀](mailto:support@contractsafe.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 형식을 참조할 수도 있습니다.

1. ContractSafe Saml2 SSO에는 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![공통 기본 특성](common/default-attributes.png)

1. 기본 특성 외에도 ContractSafe Saml2 SSO 애플리케이션에는 SAML 응답에서 다시 전달되는 몇 가지 특성이 추가로 필요합니다. 이러한 특성은 미리 채워져 있지만, 요구 사항에 따라 검토할 수 있습니다. 다음 목록에는 추가 속성이 나와 있습니다.

    | 속성 | 원본 특성|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | 이메일 | user.onpremisesuserprincipalname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾습니다. **다운로드**를 선택하여 인증서를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **ContractSafe Saml2 SSO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 **B.Simon**이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 이메일 주소를 `username@companydomain.extension` 형식으로 입력합니다. 예제는 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

## <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 **B.Simon**에게 ContractSafe Saml2 SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **ContractSafe Saml2 SSO**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾은 다음, **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

   ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon**을 선택합니다. 그런 다음, 화면의 아래쪽에 있는 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO 구성

**ContractSafe Saml2 SSO** 쪽에서 SSO를 구성하려면 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML**과 적절히 복사한 URL을 [ContractSafe Saml2 SSO 지원 팀](mailto:support@contractsafe.com)에 보내야 합니다. 팀은 양쪽 모두에서 SAML SSO 연결을 올바르게 설정해야 합니다.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>ContractSafe Saml2 SSO 테스트 사용자 만들기

ContractSafe Saml2 SSO에서 B.Simon이라는 사용자를 만듭니다. [ContractSafe Saml2 SSO 지원 팀](mailto:support@contractsafe.com)과 협력하여 사용자를 ContractSafe Saml2 SSO 플랫폼에 추가합니다. SSO를 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다. 액세스 패널에서 ContractSafe Saml2 SSO 타일을 선택하면 SSO를 설정한 ContractSafe Saml2 SSO에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD를 통해 ContractSafe Saml2 SSO 사용해보기](https://aad.portal.azure.com/)
