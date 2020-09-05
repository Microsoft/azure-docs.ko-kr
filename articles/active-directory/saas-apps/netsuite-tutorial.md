---
title: '자습서: NetSuite와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 NetSuite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: c5be6ef39a3e980852aa3373dbc54da63aa3983e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554518"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>자습서: NetSuite와 Azure AD SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 NetSuite를 연결하는 방법에 대해 알아봅니다. Azure AD와 NetSuite를 연결하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 NetSuite에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 NetSuite에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* NetSuite SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

NetSuite는 다음을 지원합니다.

* IDP 시작 SSO
* JIT(Just-In-Time) 사용자 프로비저닝
* [자동화된 사용자 프로비저닝](NetSuite-provisioning-tutorial.md)
* NetSuite를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-netsuite-from-the-gallery"></a>갤러리에서 NetSuite 추가

NetSuite가 Azure AD에 통합되도록 구성하려면 다음을 수행하여 갤러리에서 NetSuite를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사/학교 계정 또는 개인 Microsoft 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **NetSuite**를 입력합니다.
1. 결과 패널에서 **NetSuite**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 NetSuite에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 NetSuite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

NetSuite에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    * [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - B.Simon 사용자를 사용하여 Azure AD Single Sign-On을 테스트합니다.  
    * [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B. Simon 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [NetSuite SSO 구성](#configure-netsuite-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * [NetSuite 테스트 사용자 만들기](#create-the-netsuite-test-user) - B.Simon 사용자의 Azure AD 표현과 연결된 해당 사용자를 NetSuite에 만듭니다.
1. [SSO 테스트](#test-sso)는 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **NetSuite** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 창에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 창에서 **기본 SAML 구성** 옆에 있는 **편집**("연필") 아이콘을 선택합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에서 URL을 다음 형식 중 하나로 입력합니다.

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Netsuite 구성 아래의 8 단계 자습서 뒷부분에서 설명하는 Netsuite 구성 섹션에서 **<`Instance ID`>** 값을 가져옵니다. 정확한 도메인(이 경우 system.na0.netsuite.com)을 찾을 수 있습니다.

        ![Single Sign-On 구성](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > 이전 URL의 값은 실제 값이 아닙니다. 이 값을 실제 회신 URL로 업데이트합니다. 해당 값을 얻으려면 [NetSuite 클라이언트 지원 팀](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 형식을 참조할 수도 있습니다.

1. NetSuite 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 NetSuite 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 | 원본 특성 |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > 계정 특성의 값은 실제 값이 아닙니다. 이 값은 이 자습서의 뒷부분에서 설명한 대로 업데이트됩니다.

1. SAML로 Single Sign-On 설정 페이지의 SAML 서명 인증서 섹션에서 페더레이션 메타데이터 XML을 찾고, 다운로드를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **NetSuite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.

1. 화면 위쪽에서 **새 사용자**를 선택합니다.

1. **사용자** 속성 창에서 다음 단계를 수행합니다.

   a. **이름** 상자에서 **B.Simon**을 입력합니다.  
   b. **사용자 이름** 상자에서 username@companydomain.extension(예: B.Simon@contoso.com)을 입력합니다.  
   다. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.  
   d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon 사용자에게 NetSuite에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **NetSuite**를 선택합니다.
1. 개요 창에서 **관리** 섹션을 찾은 다음, **사용자 및 그룹** 링크를 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 추가" 단추](common/add-assign-user.png)

1. **사용자 및 그룹** 창의 **사용자** 드롭다운 목록에서 **B.Simon**을 선택한 다음, 화면의 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 다음을 수행합니다.

   a. **역할 선택** 창의 드롭다운 목록에서 사용자에게 적합한 역할을 선택합니다.  
   b. 화면의 아래쪽에서 **선택** 단추를 선택합니다.
1. **할당 추가** 창에서 **할당** 단추를 선택합니다.

## <a name="configure-netsuite-sso"></a>NetSuite SSO 구성

1. 브라우저에서 새 탭을 열고, 관리자 권한으로 NetSuite 회사 사이트에 로그인합니다.

2. 위쪽 탐색 모음에서 **설정**을 선택한 다음, **회사** > **기능 사용**을 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-setupsaml.png)

3. 페이지의 가운데에 있는 도구 모음에서 **SuiteCloud**를 클릭합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **인증 관리** 아래에서 **SAML Single Sign-On** 확인란을 선택하여 NetSuite에서 SAML Single Sign-On 옵션을 사용하도록 설정합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 왼쪽 탐색 모음에서 **설정**을 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-setup.png)

6. **설정 작업** 목록에서 **통합**을 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-integration.png)

7. **인증 관리** 아래에서 **SAML Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-saml.png)

8. **SAML 설정** 창의 **NetSuite 구성** 아래에서 다음을 수행합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **기본 인증 방법** 확인란을 선택합니다.

    b. **SAMLV2 ID 공급자 메타데이터** 아래에서 **IDP 메타데이터 파일 업로드**를 선택한 다음, **찾기**를 선택하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    다. **제출**을 선택합니다.

9. 위쪽의 NetSuite 탐색 모음에서 **설정**을 선택한 다음, **회사** > **회사 정보**를 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-com.png)

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-account-id.png)

    b. 오른쪽 열의 **회사 정보** 창에서 **계정 ID** 값을 복사합니다.

    다. NetSuite 계정에서 복사한 **계정 ID**를 Azure AD의 **특성 값** 상자에 붙여넣습니다.

10. 사용자가 NetSuite에 Single Sign-On을 수행하려면 먼저 NetSuite에 적절한 권한을 할당해야 합니다. 이러한 권한을 할당하려면 다음을 수행합니다.

    a. 왼쪽 탐색 모음에서 **설정**을 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-setup.png)

    b. 왼쪽 창에서 **사용자/역할**, **역할 관리**를 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-manage-roles.png)

    다. **새 역할**을 선택합니다.

    d. 새 역할의 **이름**을 입력합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-new-role.png)

    e. **저장**을 선택합니다.

    f. 위쪽 탐색 모음에서 **권한**을 선택합니다. 그런 다음, **설정**을 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-sso.png)

    g. **SAML Single Sign-On**, **추가**를 차례로 선택합니다.

    h. **저장**을 선택합니다.

    i. 위쪽 탐색 모음에서 **설정**, **설정 관리자**를 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-setup.png)

    j. 왼쪽 창에서 **사용자/역할**, **사용자 관리**를 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 테스트 사용자, **편집**, **액세스** 탭을 차례로 선택합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-edit-user.png)

    l. **역할** 창에서 사용자가 만든 적절한 역할을 할당합니다.

    ![Single Sign-On 구성](./media/NetSuite-tutorial/ns-add-role.png)

    m. **저장**을 선택합니다.

### <a name="create-the-netsuite-test-user"></a>NetSuite 테스트 사용자 만들기

이 섹션에서는 NetSuite에서 B.Simon이라는 사용자를 만듭니다. NetSuite는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에는 작업 항목이 없습니다. NetSuite에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 NetSuite 타일을 선택하면 SSO를 설정한 NetSuite에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Azure AD로 NetSuite 사용해보기](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 NetSuite를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)