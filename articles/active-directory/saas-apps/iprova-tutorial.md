---
title: '자습서: iProva와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 iProva 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552842"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>자습서: iProva와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 iProva를 통합하는 방법을 알아봅니다. iProva를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* iProva에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 iProva에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* iProva SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* iProva가 **SP**에서 시작된 SSO를 지원

* iProva를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>갤러리에서 iProva 추가

iProva가 Azure AD에 통합되도록 구성하려면 갤러리의 iProva를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **iProva**를 입력합니다.
1. 결과 패널에서 **iProva**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>iProva에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 iProva에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 iProva의 관련 사용자 간에 연결 관계를 설정해야 합니다.

iProva에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[iProva에서 구성 정보 검색](#retrieve-configuration-information-from-iprova)** - 다음 단계를 위한 준비입니다.
1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[iProva 테스트 사용자 만들기](#create-iprova-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 iProva에 만듭니다.
1. **[iProva SSO 구성](#configure-iprova-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="retrieve-configuration-information-from-iprova"></a>iProva에서 구성 정보 검색

이 섹션에서는 iProva에서 정보를 검색하여 Azure AD Single Sign-On을 구성합니다.

1. 웹 브라우저를 열고 다음 URL 패턴을 사용하여 iProva의 **SAML2 정보 페이지**로 이동합니다.

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![iProva SAML2 정보 페이지 보기](media/iprova-tutorial/iprova-saml2-info.png)

1. 다른 브라우저 탭에서 다음 단계를 진행하는 동안 브라우저 탭을 열어 둡니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **iProva** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 상자를 **iProva SAML2 정보** 페이지의 **로그온 URL** 레이블 뒤에 표시된 값으로 채웁니다. 이 페이지는 다른 브라우저 탭에서 계속 열려 있습니다.

    b. **식별자** 상자를 **iProva SAML2 정보** 페이지의 **EntityID** 레이블 뒤에 표시된 값으로 채웁니다. 이 페이지는 다른 브라우저 탭에서 계속 열려 있습니다.

    다. **회신 URL** 상자를 **iProva SAML2 정보** 페이지의 **회신 URL** 레이블 뒤에 표시된 값으로 채웁니다. 이 페이지는 다른 브라우저 탭에서 계속 열려 있습니다.

1. iProva 애플리케이션에는 특정 형식의 SAML 어설션이 필요하기 때문에, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 iProva 애플리케이션에는 아래에서 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 | 원본 특성| 네임스페이스  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

## <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 iProva에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **iProva**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="create-iprova-test-user"></a>iProva 테스트 사용자 만들기

1. **관리자** 계정을 사용하여 iProva에 로그인합니다.

2. **이동** 메뉴를 엽니다.

3. **애플리케이션 관리**를 선택합니다.

4. **사용자 및 사용자 그룹** 패널에서 **사용자**를 선택합니다.

5. **추가**를 선택합니다.

6. **사용자 이름** 상자에 사용자 이름(예: `B.Simon@contoso.com`)을 입력합니다.

7. **전체 이름** 상자에 사용자(예: **B.Simon**)의 전체 이름을 입력합니다.

8. **암호 없음(Single Sign-On 사용)** 옵션을 선택합니다.

9. **이메일 주소** 상자에 사용자의 이메일 주소(예: `B.Simon@contoso.com`)를 입력합니다.

10. 페이지 끝까지 아래로 스크롤하여 **마침**을 선택합니다.

## <a name="configure-iprova-sso"></a>iProva SSO 구성

1. **관리자** 계정을 사용하여 iProva에 로그인합니다.

2. **이동** 메뉴를 엽니다.

3. **애플리케이션 관리**를 선택합니다.

4. **시스템 설정** 패널에서 **일반**을 선택합니다.

5. **편집**을 선택합니다.

6. **액세스 제어**까지 아래로 스크롤합니다.

    ![iProva 액세스 제어 설정](media/iprova-tutorial/iprova-accesscontrol.png)

7. **Users are automatically logged on with their network accounts**(사용자가 자신의 네트워크 계정으로 자동 로그인됨) 설정을 찾아서 **Yes, authentication via SAML**(예, SAML을 통한 인증)로 변경합니다. 추가 옵션이 표시됩니다.

8. **설정**을 선택합니다.

9. **다음**을 선택합니다.

10. 페더레이션 데이터를 URL에서 다운로드할지 아니면 파일에서 업로드할지 묻는 메시지가 iProva에 표시됩니다. **URL에서** 옵션을 선택합니다.

    ![Azure AD 메타데이터 다운로드](media/iprova-tutorial/iprova-download-metadata.png)

11. “Azure AD Single Sign-On 구성” 섹션의 마지막 단계에서 저장한 메타데이터 URL을 붙여넣습니다.

12. 화살표 모양의 단추를 선택하여 Azure AD에서 메타데이터를 다운로드합니다.

13. 다운로드가 완료되면 확인 메시지, **Valid Federation Data file downloaded**(유효한 페더레이션 데이터 파일이 다운로드됨)가 표시됩니다.

14. **다음**을 선택합니다.

15. **Test login**(로그인 테스트) 옵션은 건너뛰고 **다음**을 선택합니다.

16. **Claim to use**(사용할 클레임) 드롭다운에서 **windowsaccountname**을 선택합니다.

17. **마침**을 선택합니다.

18. 이제 **Edit general settings**(일반 설정 편집) 화면으로 돌아갑니다. 페이지 맨 아래까지 아래로 스크롤하고 **확인**을 선택하여 구성을 저장합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 iProva 타일을 클릭하면 SSO를 설정한 iProva에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 iProva 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 iProva를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)