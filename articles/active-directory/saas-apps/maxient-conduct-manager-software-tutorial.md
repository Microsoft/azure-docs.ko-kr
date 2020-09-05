---
title: '자습서: Maxient Conduct Manager Software와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Maxient Conduct Manager Software 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: dd6872fd8dca3f29b61c6f1dffb5f219abac5cb8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518894"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>자습서: Maxient Conduct Manager Software와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Maxient Conduct Manager Software를 통합하는 방법을 알아봅니다. Azure AD와 Maxient Conduct Manager Software를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 Maxient Conduct Manager Software에서 사용자 인증
* 사용자가 자신의 Azure AD 계정으로 Maxient Conduct Manager Software에 자동으로 로그인되도록 설정합니다.


Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Maxient Conduct Manager Software SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 Maxient Conduct Manager Software에 사용할 수 있도록 Azure AD를 구성합니다.


* Maxient Conduct Manager Software는 **SP 및 IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>갤러리에서 Maxient Conduct Manager Software 추가

Maxient Conduct Manager Software의 Azure AD 통합을 구성하려면 갤러리의 Maxient Conduct Manager Software를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Maxient Conduct Manager Software**를 입력합니다.
1. 결과 패널에서 **Maxient Conduct Manager Software**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Maxient Conduct Manager Software용 Azure AD Single Sign-On 구성 및 테스트

Maxient Conduct Manager Software용 Azure AD SSO 구성 및 테스트 SSO가 작동하려면 Azure AD와 Maxient Conduct Manager Software 간에 연결을 설정해야 합니다.

Maxient Conduct Manager Software를 사용하여 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - Maxient Conduct Manager Software에 사용할 수 있도록 사용자 인증을 지원합니다.
    1. **[Maxient를 사용하도록 모든 사용자 할당](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** - 조직 내 모든 사람이 인증을 수행할 수 있도록 허용합니다.
1. **[Maxient를 사용하여 Azure AD 설정 테스트](#test-with-maxient)** - 구성이 작동하고 올바른 특성이 릴리스되는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Maxient Conduct Manager Software** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1.  **기본 SAML 구성** 섹션에서 애플리케이션은  **IDP** 시작 모드로 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는  **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://cm.maxient.com/<SCHOOLCODE>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. Maxient 구현/지원 담당자와 협력해서 값을 가져옵니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.  Maxient 구현/지원 담당자에게 이 URL을 제공해야 합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Maxient Conduct Manager Software에서 인증을 수행할 수 있도록 모든 사용자 할당

이 섹션에서는 Maxient Conduct Manager Software에서 Azure 시스템을 사용하여 인증을 수행할 수 있도록 모든 계정에 대해 액세스 권한을 부여합니다.  Maxient가 올바르게 작동하려면 이 단계가 **필수** 단계입니다.  Maxient는 Azure AD 시스템을 활용해서 사용자 *인증*을 수행합니다. 사용자에 대한 *권한 부여*는 사용자가 수행하려는 특정 기능에 따라 Maxient 시스템 내에서 수행됩니다. Maxient는 이러한 결정을 내릴 때 해당 디렉터리의 특성을 사용하지 않습니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Maxient Conduct Manager Software**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자에서 모든 사용자(또는 적합한 그룹)를 선택하고 Maxient에서 인증을 수행할 수 있도록 사용자를 **할당**합니다.

## <a name="test-with-maxient"></a>Maxient로 테스트 

지원 티켓이 아직 Maxient 구현/지원 담당자에게 열려 있지 않으면, "캠퍼스 기반 인증/Azure 설정 - \<\<School Name\>\>" 제목을 사용해서 [support@maxient.com](mailto:support@maxient.com)으로 이메일을 전송하세요. 이메일 본문에는 **앱 페더레이션 메타데이터 URL**을 제공하세요. Maxient 직원은 적절한 특성이 릴리스되는지 확인하기 위한 테스트 링크를 제공합니다.  
    
## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD를 통해 Maxient Conduct Manager Software 사용해보기](https://aad.portal.azure.com/)

