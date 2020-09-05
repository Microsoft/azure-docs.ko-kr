---
title: '자습서: Jamf Pro와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Jamf Pro 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.openlocfilehash: 780421d93916c7da7897dfa15d09dc895cf56280
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552665"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>자습서: Jamf Pro와 Azure Active Directory SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Jamf Pro를 통합하는 방법에 대해 알아봅니다. Azure AD와 Jamf Pro를 통합하면 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 Jamf Pro에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Jamf Pro에 자동으로 로그인합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 Jamf Pro 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

* Jamf Pro는 **SP 시작** 및 **IdP 시작** SSO를 지원합니다.
* Jamf Pro를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>갤러리에서 Jamf Pro 추가

Azure AD에 Jamf Pro의 통합을 구성하려면 갤러리의 Jamf Pro를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. 회사/학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 *Jamf Pro*를 입력합니다.
1. 결과 패널에서 **Jamf Pro**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Azure AD에서 Jamf Pro에 대한 SSO 구성 및 테스트

B.Simon이라는 테스트 사용자를 사용하여 Jamf Pro에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Jamf Pro의 관련 사용자 간에 연결 관계를 설정해야 합니다.

이 섹션에서는 Jamf Pro에서 Azure AD SSO를 구성하고 테스트합니다.

1. 사용자가 이 기능을 사용할 수 있도록 [Azure AD에서 SSO를 구성](#configure-sso-in-azure-ad)합니다.
    1. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - 이 사용자는 B.Simon 계정을 사용하여 Azure AD SSO를 테스트합니다.
    1. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B. Simon이 Azure AD에서 SSO를 사용할 수 있도록 합니다.
1. [Jamf Pro에서 SSO 구성](#configure-sso-in-jamf-pro) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. [Jamf Pro 테스트 사용자 만들기](#create-a-jamf-pro-test-user) - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Jamf Pro에 만듭니다.
1. [SSO 구성 테스트](#test-the-sso-configuration) - 구성이 작동하는지 확인합니다.

## <a name="configure-sso-in-azure-ad"></a>Azure AD에서 SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Jamf Pro** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 펜 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집 페이지](common/edit-urls.png)

1. **IdP 시작** 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.jamfcloud.com/saml/metadata` 수식을 사용하는 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<subdomain>.jamfcloud.com/saml/SSO` 수식을 사용하는 URL을 입력합니다.

1. **추가 URL 설정**을 선택합니다. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. Jamf Pro 포털의 **Single Sign On 섹션**에서 실제 식별자 값을 가져옵니다. 이 자습서의 뒷부분에 설명되어 있습니다. 식별자 값에서 실제 하위 도메인 값을 추출하고, 해당 하위 도메인 정보를 로그온 URL 및 회신 URL로 사용할 수 있습니다. 또한 Azure Portal의 **기본 SAML 구성** 섹션에 표시된 수식을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션으로 이동하고, **복사** 단추를 선택하여 **앱 페더레이션 메타데이터 URL**을 복사한 다음, 컴퓨터에 저장합니다.

    ![SAML 서명 인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.
   1. **사용자 이름** 필드에서 [name]@[companydomain].[extension]을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon에게 Jamf Pro에 대한 Simon 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Jamf Pro**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹 선택](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 단추 선택](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 [사용자] 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면의 아래쪽에 있는 **선택** 단추를 선택합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

## <a name="configure-sso-in-jamf-pro"></a>Jamf Pro에서 SSO 구성

1. Jamf Pro 내에서 구성을 자동화하려면 **확장 설치**를 선택하여 **내 앱 보안 로그인 브라우저 확장**을 설치합니다.

    ![내 앱 보안 로그인 브라우저 확장 페이지](common/install-myappssecure-extension.png)

2. 확장이 브라우저에 추가되면 **Jamf Pro 설정**을 선택합니다. Jamf Pro 애플리케이션이 열리면 관리자 자격 증명을 제공하여 로그인합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3~7단계를 자동화합니다.

    ![Jamf Pro의 구성 설정 페이지](common/setup-sso.png)

3. Jamf Pro를 수동으로 설정하려면 새 웹 브라우저 창을 열고, Jamf Pro 회사 사이트에 관리자 권한으로 로그인합니다. 그런 다음, 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위 모서리에서 **설정 아이콘**을 선택합니다.

    ![Jamf Pro에서 설정 아이콘 선택](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **Single Sign-On**을 선택합니다.

    ![Jamf Pro에서 Single Sign-On 선택](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![Jamf Pro의 Single Sign-On 페이지](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **편집**을 선택합니다.

    b. **Enable Single Sign-On Authentication**(Single Sign-On 인증 사용) 확인란을 선택합니다.

  다. **ID 공급자** 드롭다운 메뉴에서 **Azure**를 옵션으로 선택합니다.

  d. **ENTITY ID**(엔터티 ID) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자(엔터티 ID)** 필드에 붙여넣습니다.

> [!NOTE]
> `<SUBDOMAIN>` 필드의 값을 사용하여 Azure Portal의 **기본 SAML 구성** 섹션에서 로그온 URL 및 회신 URL을 완성합니다.

  e. **ID 공급자 메타데이터 원본** 드롭다운 메뉴에서 **메타데이터 URL**을 선택합니다. 표시되는 필드에서 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 붙여넣습니다.

  f. (선택 사항) 토큰 만료 값을 편집하거나 "SAML 토큰 만료 사용 안 함"을 선택합니다.

7. 동일한 페이지에서 **User Mapping**(사용자 매핑) 섹션까지 아래로 스크롤합니다. 그런 다음, 다음 단계를 수행합니다.

    ![Jamf Pro의 Single Sign-On 페이지에 대한 User Mapping 섹션](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. **ID 공급자 사용자 매핑**에 대해 **NameID** 옵션을 선택합니다. 기본적으로 이 옵션은 **NameID**로 설정되지만, 사용자 지정 특성을 정의할 수 있습니다.

    b. **Jamf Pro 사용자 매핑**에 대해 **이메일**을 선택합니다. Jamf Pro는 IdP에서 보낸 SAML 특성을 먼저 사용자별로, 다음으로 그룹별로 매핑합니다. 사용자가 Jamf Pro에 액세스하려고 하면 Jamf Pro는 ID 공급자로부터 사용자에 대한 정보를 가져와서 모든 Jamf Pro 사용자 계정과 대조하여 일치시킵니다. 들어오는 사용자 계정을 찾을 수 없는 경우 Jamf Pro는 해당 계정을 그룹 이름으로 일치시키려고 시도합니다.

    다. `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 값을 **IDENTITY PROVIDER GROUP ATTRIBUTE NAME**(ID 공급자 그룹 특성 이름) 필드에 붙여넣습니다.

    d. 동일한 페이지에서 **보안** 섹션까지 아래로 스크롤하고 **사용자가 Single Sign-on 인증을 무시할 수 있도록 허용**을 선택합니다. 따라서 사용자는 인증을 위해 ID 공급자 로그인 페이지로 리디렉션되지 않고, 대신 Jamf Pro에 직접 로그인할 수 있습니다. 사용자가 ID 공급자를 통해 Jamf Pro에 액세스하려고 시도하는 경우 IdP에서 시작한 SSO 인증 및 권한 부여가 발생합니다.

    e. **저장**을 선택합니다.

### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro 테스트 사용자 만들기

Azure AD 사용자가 Jamf Pro에 로그인할 수 있도록 해당 사용자를 Jamf Pro에 프로비저닝해야 합니다. Jamf Pro에서는 수동으로 프로비저닝합니다.

사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. Jamf Pro 회사 사이트에 관리자 권한으로 로그인합니다.

2. 페이지의 오른쪽 위 모서리에서 **설정** 아이콘을 선택합니다.

    ![Jamf Pro의 설정 아이콘](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **Jamf Pro User Accounts & Groups**(Jamf Pro 사용자 계정 및 그룹)를 선택합니다.

    ![Jamf Pro 설정의 Jamf Pro User Accounts & Groups 아이콘](./media/jamfprosamlconnector-tutorial/user1.png)

4. **새로 만들기**를 선택합니다.

    ![Jamf Pro User Accounts & Groups 시스템 설정 페이지](./media/jamfprosamlconnector-tutorial/user2.png)

5. **Standard 계정 만들기**를 선택합니다.

    ![Jamf Pro User Accounts & Groups 페이지의 Create Standard Account 옵션](./media/jamfprosamlconnector-tutorial/user3.png)

6. **새 계정** 대화 상자에서 다음 단계를 수행합니다.

    ![Jamf Pro 시스템 설정의 New Account 설정 옵션](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **USERNAME**(사용자 이름) 필드에서 테스트 사용자의 전체 이름(`Britta Simon`)을 입력합니다.

    b. 조직에 따라 **ACCESS LEVEL**(액세스 수준), **PRIVILEGE SET**(권한 세트) 및 **ACCESS STATUS**(액세스 상태)에 대한 옵션을 선택합니다.

    다. **FULL NAME**(전체 이름) 필드에서 `Britta Simon`을 입력합니다.

    d. **EMAIL ADDRESS**(이메일 주소) 필드에서 Britta Simon 계정의 이메일 주소를 입력합니다.

    e. **PASSWORD**(암호) 필드에서 사용자의 암호를 입력합니다.

    f. **VERIFY PASSWORD**(암호 확인) 필드에서 사용자의 암호를 다시 입력합니다.

    g. **저장**을 선택합니다.

## <a name="test-the-sso-configuration"></a>SSO 구성 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Jamf Pro 타일을 선택하면 SSO를 구성한 Jamf Pro에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory를 통합하는 방법에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Azure AD에서 Jamf Pro 사용해 보기](https://aad.portal.azure.com/)