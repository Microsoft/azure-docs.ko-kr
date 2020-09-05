---
title: '자습서: Projectplace와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Projectplace 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: cbc65e64dfd18cdc16873a2d82aecadabeec4c28
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553515"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>자습서: Azure Active Directory와 Projectplace 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Projectplace를 통합하는 방법에 대해 알아봅니다. Azure AD와 Projectplace를 통합하면 다음 작업을 할 수 있습니다.

* Projectplace에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Freshservice에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.
* Projectplace에서 사용자를 자동으로 프로비저닝할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Projectplace SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Projectplace에서 **SP 및 IDP** 시작 SSO 및 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-projectplace-from-the-gallery"></a>갤러리에서 Projectplace 추가

Projectplace의 Azure AD 통합을 구성하려면 갤러리의 Projectplace를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Projectplace**를 입력합니다.
1. 결과 패널에서 **Projectplace**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Projectplace에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Projectplace의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Projectplace에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. **[Projectplace 구성](#configure-projectplace)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B. Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Projectplace 테스트 사용자 만들기](#create-projectplace-test-user)** - B. Simon의 Azure AD 표현과 연결된 해당 사용자를 Projectplace에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Projectplace** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **IDP** 시작 모드로 애플리케이션을 구성하려는 경우 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://service.projectplace.com` URL을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 사용자의 요구 사항에 따라 **아이콘**을 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 메모장에 저장합니다.

   ![인증서 다운로드 링크](common/copy-metadataurl.png)

1. **Projectplace 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplace 구성

**Projectplace** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 Url**을 [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)으로 보내야 합니다. 이 팀은 SAML SSO 연결이 양쪽에서 올바르게 설정되도록 보장합니다.

>[!NOTE]
>Single Sign-On 구성을 [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)에서 수행할 수 있습니다. 구성이 완료되는 즉시 알림을 받습니다. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B. Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `BrittaSimon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Projectplace에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Projectplace**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B. Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-projectplace-test-user"></a>Projectplace 테스트 사용자 만들기

>[!NOTE]
>Projectplace에서 사용하도록 설정한 프로비저닝이 있는 경우 이 단계를 건너뛸 수 있습니다. [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)에 프로비저닝을 사용하도록 요청할 수 있습니다. 완료되면 첫 번째 로그인 중 Projectplace에 사용자가 생성됩니다.

Azure AD 사용자가 Percolate에 로그인할 수 있도록 설정하려면 해당 사용자를 Projectplace에 추가해야 합니다. 수동으로 추가해야 합니다.

**사용자 계정을 만들려면 다음 단계를 수행합니다.**

1. **Projectplace** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **People**(사람)로 이동한 다음, **Members**(멤버)를 선택합니다.
   
    ![사람으로 이동한 다음, 멤버 선택](./media/projectplace-tutorial/ic790228.png "사람")

3. **Add Member**(멤버 추가)를 클릭합니다.
   
    ![멤버 추가 선택](./media/projectplace-tutorial/ic790232.png "멤버 추가")

4. **Add Member** 섹션에서 다음 단계를 수행합니다.
   
    ![멤버 추가 섹션](./media/projectplace-tutorial/ic790233.png "새 멤버")
   
    1. **New Members** 텍스트 상자에서 추가하려는 유효한 Azure AD 계정의 이메일 주소를 입력합니다.
   
    1. **보내기**를 선택합니다.

   계정이 활성화되기 전에 해당 계정을 확인하기 위한 링크가 포함된 이메일이 Azure AD 계정 보유자에게 보내집니다.

>[!NOTE]
>또한 Projectplace에서 제공하는 다른 사용자 계정 만들기 도구 또는 API를 사용하여 Azure AD 사용자 계정을 추가할 수 있습니다.


### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Projectplace 타일을 선택하면 SSO를 설정한 Projectplace에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)