---
title: '자습서: Palo Alto Networks - Admin UI와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks - Admin UI 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 8bd41034d6d4cfa444ae4c0711fd46cb2924d009
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554086"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>자습서: Palo Alto Networks - Admin UI와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks - Admin UI를 통합하는 방법에 대해 알아봅니다.
Azure AD에 Palo Alto Networks - Admin UI를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* Palo Alto Networks - Admin UI에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Palo Alto Networks - Admin UI에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Palo Alto Networks - Admin UI와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Palo Alto Networks - Admin UI Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Palo Alto Networks - Admin UI에서 **SP** 시작 SSO를 지원합니다.
* Palo Alto Networks - Admin UI에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>갤러리에서 Palo Alto Networks - Admin UI 추가

Palo Alto Networks - Admin UI의 Azure AD 통합을 구성하려면 갤러리의 Palo Alto Networks - Admin UI를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Palo Alto Networks - Admin UI**를 입력합니다.
1. 결과 패널에서 **Palo Alto Networks - Admin UI**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **B.Simon**이라는 테스트 사용자를 기반으로 하여 Palo Alto Networks - Admin UI에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Palo Alto Networks - Admin UI의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Palo Alto Networks - Admin UI에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Palo Alto Networks - Admin UI SSO 구성](#configure-palo-alto-networks---admin-ui-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Palo Alto Networks - Admin UI 테스트 사용자 만들기](#create-palo-alto-networks---admin-ui-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Palo Alto Networks - Admin UI에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Palo Alto Networks - Admin UI에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Palo Alto Networks - Admin UI** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

1. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<Customer Firewall FQDN>/php/login.php` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에서 `https://<Customer Firewall FQDN>:443/SAML20/SP` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS` 형식의 ACS(Assertion Consumer Service) URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [Palo Alto Networks - Admin UI 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.
    >
    > **식별자**와 **회신 URL**에 포트 443이 필요합니다. Palo Alto Firewall에 이러한 값이 하드 코딩되어 있기 때문입니다. 포트 번호를 제거하면 로그인하는 동안 오류가 발생합니다.

    > **식별자**와 **회신 URL**에 포트 443이 필요합니다. Palo Alto Firewall에 이러한 값이 하드 코딩되어 있기 때문입니다. 포트 번호를 제거하면 로그인하는 동안 오류가 발생합니다.

1. Palo Alto Networks - Admin UI 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

   > [!NOTE]
   > 특성 값은 예시일 뿐이므로 *username* 및 *adminrole*에 적절한 값을 매핑하세요. 또한 선택적 특성인 *accessdomain*이 있으며, 이 특성은 방화벽에서 특정 가상 시스템에 대한 관리자 액세스를 제한하는 데 사용됩니다.

1. 위에서 언급한 특성 외에도 Palo Alto Networks - Admin UI 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name |  원본 특성|
    | --- | --- |
    | 사용자 이름 | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > 특성에 대한 자세한 내용은 다음 문서를 참조하세요.
    > * [Admin UI(adminrole)에 대한 관리 역할 프로필](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI(accessdomain)에 대한 디바이스 액세스 도메인](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Palo Alto Networks - Admin UI 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Palo Alto Networks - Admin UI에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Palo Alto Networks - Admin UI**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Palo Alto Networks - Admin UI SSO 구성

1. 새 창에서 관리자 권한으로 Palo Alto Networks Firewall Admin UI를 엽니다.

2. **디바이스** 탭을 선택합니다.

    ![디바이스 탭](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 왼쪽 창에서 **SAML ID 공급자**를 선택한 후 **가져오기**를 선택하여 메타데이터 파일을 가져옵니다.

    ![메타데이터 파일 가져오기 단추](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **SAML ID 공급자 서버 프로필 가져오기** 창에서 다음을 수행합니다.

    !["SAML ID 공급자 서버 프로필 가져오기" 창](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. **프로필 이름** 상자에 이름(예: **AzureAD Admin UI**)을 제공합니다.

    b. **ID 공급자 메타데이터** 아래에서 **찾아보기**를 선택하고 앞서 Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.

    다. **ID 공급자 인증서의 유효성 검사** 확인란을 선택 취소합니다.

    d. **확인**을 선택합니다.

    e. 방화벽의 구성을 커밋하려면 **커밋**을 선택합니다.

5. 왼쪽 창에서 **SAML ID 공급자**를 선택한 후 이전 단계에서 만든 SAML ID 공급자 프로필(예: **AzureAD Admin UI**)을 선택합니다.

    ![SAML ID 공급자 프로필](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. **SAML ID 공급자 서버 프로필** 창에서 다음을 수행합니다.

    !["SAML ID 공급자 서버 프로필" 창](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. **ID 공급자 SLO URL** 상자에서 이전에 가져온 SLO URL을 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` URL로 바꿉니다.
  
    b. **확인**을 선택합니다.

7. Palo Alto Networks Firewall Admin UI에서 **디바이스**를 선택한 후 **관리자 역할**을 선택합니다.

8. **추가** 단추를 선택합니다.

9. **관리자 역할 프로필** 창의 **이름** 상자에 관리자 역할의 이름을 제공합니다(예: **fwadmin**). 이 관리자 역할 이름은 ID 공급자가 보낸 SAML 관리자 역할 특성 이름과 일치해야 합니다. 관리자 역할 이름과 값은 Azure Portal의 **사용자 특성** 섹션에 만들어졌습니다.

    ![Palo Alto Networks 관리자 역할 구성](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Firewall Admin UI에서 **디바이스**를 선택한 후 **인증 프로필**을 선택합니다.

11. **추가** 단추를 선택합니다.

12. **인증 프로필** 창에서 다음을 수행합니다. 

    !["인증 프로필" 창](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. **이름** 상자에 이름(예: **AzureSAML_Admin_AuthProfile**)을 제공합니다.

    b. **형식** 드롭다운 목록에서 **SAML**을 선택합니다. 

    다. **IdP 서버 프로필** 드롭다운 목록에서 적절한 SAML ID 공급자 서버 프로필(예: **AzureAD Admin UI**)을 선택합니다.

    다. **단일 로그아웃 사용** 확인란을 선택합니다.

    d. **관리자 역할 특성** 상자에 특성 이름(예: **adminrole**)을 입력합니다.

    e. **고급** 탭을 선택한 후 **허용 목록**에서 **추가**를 선택합니다.

    ![고급 탭의 추가 단추](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. **모두** 확인란을 선택하거나 이 프로필로 인증될 수 있는 사용자 및 그룹을 선택합니다.  
    사용자를 인증할 때 방화벽은 연결된 사용자 이름 또는 그룹이 이 목록에 있는 항목과 일치하는지 비교합니다. 항목을 추가하지 않은 경우 사용자가 인증될 수 없습니다.

    g. **확인**을 선택합니다.

13. 관리자가 Azure를 통해 SAML SSO를 사용하도록 허용하려면 **디바이스** > **설정**을 선택합니다. **설정** 창에서 **관리** 탭을 선택한 후 **인증 설정**에서 **설정** ("기어") 단추를 선택합니다.

    ![설정 단추](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. [인증 프로필] 창에서 만든 SAML 인증 프로필(예: **AzureSAML_Admin_AuthProfile**)을 선택합니다.

    ![인증 프로필 필드](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. **확인**을 선택합니다.

16. 구성을 커밋하려면 **커밋**을 선택합니다.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto Networks - Admin UI 테스트 사용자 만들기

Palo Alto Networks - Admin UI는 Just-In-Time 사용자 프로비저닝을 지원합니다. 사용자가 아직 없을 경우 인증 성공 후 시스템에 사용자가 자동으로 생성됩니다. 사용자를 만들기 위해 별다른 조치를 취할 필요가 없습니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Palo Alto Networks - Admin UI 타일을 클릭하면 SSO를 설정한 Palo Alto Networks - Admin UI 애플리케이션에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Palo Alto Networks - Admin UI 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Palo Alto Networks - Admin UI를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
