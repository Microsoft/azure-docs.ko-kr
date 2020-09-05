---
title: '자습서: Abstract와 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory와 Abstract 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 3f19733f01cf601c1145d5e2bb5deb58db828dc4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538334"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>자습서: Azure Active Directory와 Abstract 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Abstract를 통합하는 방법에 대해 알아봅니다. Azure AD와 Abstract를 통합할 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Abstract에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Abstract에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Abstract SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Abstract에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-abstract-from-the-gallery"></a>갤러리에서 Abstract 추가

Abstract와 Azure AD 통합을 구성하려면 갤러리의 Abstract를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Abstract**를 입력합니다.
1. 결과 패널에서 **Abstract**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Abstract에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Abstract의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Abstract에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Abstract SSO 구성](#configure-abstract-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Abstract 테스트 사용자 만들기](#create-abstract-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Abstract에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Abstract** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 **IDP** 시작 모드로 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://app.abstract.com/signin` URL을 입력합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Abstract SSO 구성

Abstract에서 SSO를 구성하는 데 필요하므로 Azure Portal에서 `App Federation Metadata Url` 및 `Azure AD Identifier`를 검색해야 합니다.

**SAML로 Single Sign-On 설정** 페이지에서 이러한 정보를 찾을 수 있습니다.

* `App Federation Metadata Url`은 **SAML 서명 인증서** 섹션 아래에 있습니다.
* `Azure AD Identifier`는 **Abstract 설정** 섹션에 있습니다.


이제 Abstract에서 SSO를 구성할 준비가 되었습니다.

>[!Note]
>Abstract의 SSO 설정에 액세스하려면 조직 관리자 계정으로 인증해야 합니다.

1. [Abstract 웹앱](https://app.abstract.com/)을 엽니다.
2. 왼쪽 막대의 **사용 권한** 페이지로 이동합니다.
3. **SSO 구성** 섹션에서 **메타데이터 URL** 및  **엔터티 ID**를 입력합니다.
4. 발생할 수 있는 수동 예외를 입력합니다. 수동 예외 섹션에 나열된 이메일은 SSO를 무시하고 이메일 및 암호를 사용하여 로그인할 수 있습니다. 
5. **변경 내용 저장**을 클릭합니다.

>[!Note] 
>수동 예외 목록에서는 기본 이메일 주소를 사용해야 합니다. 목록의 이메일이 사용자의 보조 이메일인 경우 SSO 활성화에 실패합니다. 이런 경우 실패한 계정에 대한 기본 이메일이 포함된 오류 메시지가 표시됩니다. 해당 사용자를 알고 있는 것이 확인된 후 수동 예외 목록에 해당 기본 이메일이 추가됩니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Abstract에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Abstract**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-abstract-test-user"></a>Abstract 테스트 사용자 만들기

Abstract에서 SSO를 테스트하려면 다음을 수행합니다.

1. [Abstract 웹앱](https://app.abstract.com/)을 엽니다.
2. 왼쪽 막대의 **사용 권한** 페이지로 이동합니다.
3. **내 계정 테스트**를 클릭합니다. 테스트가 실패할 경우 [지원 팀에 문의](https://www.abstract.com/help/contact/)하세요.

>[!Note]
>Abstract의 SSO 설정에 액세스하려면 조직 관리자 계정으로 인증해야 합니다.
이 조직 관리자 계정은 Azure Portal에서 Abstract에 할당되어야 합니다.

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Abstract 타일을 클릭하면 SSO를 설정한 Abstract에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

