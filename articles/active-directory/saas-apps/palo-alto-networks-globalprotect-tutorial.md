---
title: '자습서: Palo Alto Networks - GlobalProtect와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks - GlobalProtect 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: 2b8c74b8a456815400f6d68200ea93f43e3adff0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554053"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>자습서: Palo Alto Networks - GlobalProtect와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks - GlobalProtect를 통합하는 방법에 대해 알아봅니다. Palo Alto Networks - GlobalProtect를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Palo Alto Networks - GlobalProtect에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Palo Alto Networks - GlobalProtect에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Palo Alto Networks - GlobalProtect SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Palo Alto Networks - GlobalProtect에서 **SP** 시작 SSO를 지원합니다.
* Palo Alto Networks - GlobalProtect에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>갤러리에서 Palo Alto Networks - GlobalProtect 추가

Palo Alto Networks - GlobalProtect의 Azure AD 통합을 구성하려면 갤러리의 Palo Alto Networks - GlobalProtect를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Palo Alto Networks - GlobalProtect**를 입력합니다.
1. 결과 패널에서 **Palo Alto Networks - GlobalProtect**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Palo Alto Networks - GlobalProtect에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Palo Alto Networks - GlobalProtect에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Palo Alto Networks - GlobalProtect의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Palo Alto Networks - GlobalProtect에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Palo Alto Networks - GlobalProtect SSO 구성](#configure-palo-alto-networks---globalprotect-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Palo Alto Networks - GlobalProtect 테스트 사용자 만들기](#create-palo-alto-networks---globalprotect-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Palo Alto Networks - GlobalProtect에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Palo Alto Networks - GlobalProtect** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<Customer Firewall URL>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<Customer Firewall URL>/SAML20/SP` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Palo Alto Networks - GlobalProtect 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Palo Alto Networks - GlobalProtect 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Palo Alto Networks - GlobalProtect에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Palo Alto Networks - GlobalProtect**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Palo Alto Networks - GlobalProtect SSO 구성

1. 다른 브라우저 창에서 관리자 권한으로 Palo Alto Networks 방화벽 관리 UI를 엽니다.

2. **디바이스**를 클릭합니다.

    ![Palo Alto Single Sign-On 구성](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 왼쪽 탐색 모음에서 **SAML ID 공급자**를 선택하고 "가져오기"를 클릭하여 메타데이터 파일을 가져옵니다.

    ![Palo Alto Single Sign-On 구성](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 가져오기 창에서 다음 작업 수행

    ![Palo Alto Single Sign-On 구성](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **프로필 이름** 텍스트 상자에 이름(예: Azure AD GlobalProtect)을 제공합니다.

    b. **ID 공급자 메타데이터**에서 **찾아보기**를 클릭하고 Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.

    다. **확인**을 클릭합니다.

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Palo Alto Networks - GlobalProtect 테스트 사용자 만들기

이 섹션에서는 Palo Alto Networks - GlobalProtect에서 B.Simon이라는 사용자를 만듭니다. Palo Alto Networks - GlobalProtect는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Palo Alto Networks - GlobalProtect에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Palo Alto Networks - GlobalProtect 타일을 클릭하면 SSO를 설정한 Palo Alto Networks - GlobalProtect 애플리케이션에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD에서 Palo Alto Networks - GlobalProtect 사용해 보기](https://aad.portal.azure.com/)
