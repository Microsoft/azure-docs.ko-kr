---
title: '자습서: Boomi와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Boomi 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 8f6c815bf6b8be6d280dcdc60401f7a91e171e66
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>자습서: Boomi와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Boomi를 통합하는 방법에 대해 알아봅니다. Azure AD와 Boomi를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Boomi에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Boomi에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Boomi SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Boomi에서 **IDP** 시작 SSO를 지원합니다.
* Boomi를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>갤러리에서 Boomi 추가

Boomi의 Azure AD 통합을 구성하려면 갤러리의 Boomi를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Boomi**를 입력합니다.
1. 결과 패널에서 **Boomi**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Boomi용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Boomi에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Boomi의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Boomi에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Boomi SSO 구성](#configure-boomi-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Boomi 테스트 사용자 만들기](#create-boomi-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Boomi에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Boomi** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일**이 있고 **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    ![이미지](common/idp-intiated.png)

    > [!Note]
    > 이 자습서의 뒷부분에 설명되어 있는 **Boomi SSO 구성** 섹션에서**서비스 공급자 메타데이터 파일**을 가져올 수 있습니다. **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

1. Boomi 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Boomi 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 |  원본 특성|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Boomi 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Boomi에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Boomi**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-boomi-sso"></a>Boomi SSO 구성

1. 다른 웹 브라우저 창에서 관리자 권한으로 Boomi 회사 사이트에 로그인합니다.

1. **회사 이름**, **설정**으로 이동합니다.

1. **SSO 옵션** 탭을 클릭하고 아래 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. **SAML Single Sign-On 사용** 확인란을 선택합니다.

    b. **가져오기**를 클릭하여 Azure AD에서 다운로드한 인증서를 **ID 공급자 인증서**로 업로드합니다.

    다. **ID 공급자 로그인 URL** 텍스트 상자에서 Azure AD 애플리케이션 구성 창의 **로그인 URL** 값을 입력합니다.

    d. **페더레이션 ID 위치**의 경우 **페더레이션 ID는 FEDERATION_ID 특성 요소에 있음** 라디오 단추를 선택합니다.

    e. **AtomSphere MetaData URL**을 복사하고 선택한 브라우저를 통해 **MetaData URL**로 이동하여 출력을 파일에 저장합니다. Azure Portal의 **기본 SAML 구성** 섹션에서 **MetaData URL**을 업로드합니다.

    f. **저장** 단추를 클릭합니다.

### <a name="create-boomi-test-user"></a>Boomi 테스트 사용자 만들기

Azure AD 사용자가 Boomi에 로그인할 수 있도록 하려면 Boomi로 프로비저닝되어야 합니다. Boomi의 경우 프로비전은 수동 작업입니다.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. Boomi 회사 사이트에 관리자 권한으로 로그인합니다.

1. 로그인 후 **사용자 관리**, **사용자**로 이동합니다.

    ![사용자](./media/boomi-tutorial/tutorial_boomi_001.png "사용자")

1. **+** 아이콘을 클릭하면 **사용자 역할 추가/유지 관리** 대화 상자가 열립니다.

    ![사용자](./media/boomi-tutorial/tutorial_boomi_002.png "사용자")

    ![사용자](./media/boomi-tutorial/tutorial_boomi_003.png "사용자")

    a. **사용자 메일 주소** 텍스트 상자에서 B.Simon@contoso.com과 같은 사용자의 메일 주소를 입력합니다.

    b. **이름** 텍스트 상자에서 사용자의 이름(예: B)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. 사용자의 **페더레이션 ID**를 입력합니다. 각 사용자는 계정 내에서 사용자를 고유하게 식별하는 페더레이션 ID가 있어야 합니다.

    e. **표준 사용자** 역할을 사용자에게 할당합니다. 관리자 역할은 일반적인AtomSphere 액세스 권한과 Single Sign-On 액세스 권한을 모두 제공하므로 이 역할을 할당하지 마세요.

    f. **확인**을 클릭합니다.

    > [!NOTE]
    > 사용자는 해당 암호가 ID 공급자를 통해 관리되므로 AtomSphere 계정에 로그인하는 데 사용할 수 있는 암호가 포함된 시작 알림 메일이 제공되지 않습니다. 다른 Boomi 사용자 계정 생성 도구 또는 Boomi가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Boomi 타일을 클릭하면 SSO를 설정한 Boomi에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD로 Boomi 사용해보기](https://aad.portal.azure.com/)
