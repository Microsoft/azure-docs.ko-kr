---
title: '자습서: Qlik Sense Enterprise와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Qlik Sense Enterprise 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: 5344354c05547d6d2a5e2762c70a97cc4222c464
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552359"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>자습서: Azure Active Directory와 Qlik Sense Enterprise 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Qlik Sense Enterprise를 통합하는 방법에 대해 알아봅니다. Qlik Sense Enterprise를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Qlik Sense Enterprise에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Qlik Sense Enterprise에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 다운로드할 수 있습니다.
* Qlik Sense Enterprise SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 
* Qlik Sense Enterprise에서 **SP** 시작 SSO를 지원합니다.
* Qlik Sense Enterprise는 **Just-In-Time 프로비저닝**을 지원합니다.

* Qlik Sense Enterprise가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>갤러리에서 Qlik Sense Enterprise 추가

Qlik Sense Enterprise의 Azure AD 통합을 구성하려면 갤러리의 Qlik Sense Enterprise를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Qlik Sense Enterprise**를 입력합니다.
1. 결과 패널에서 **Qlik Sense Enterprise**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**Britta Simon**이라는 테스트 사용자를 사용하여 Qlik Sense Enterprise에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Qlik Sense Enterprise의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Qlik Sense Enterprise에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Qlik Sense Enterprise SSO 구성](#configure-qlik-sense-enterprise-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Qlik Sense Enterprise 테스트 사용자 만들기](#create-qlik-sense-enterprise-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Qlik Sense Enterprise에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Qlik Sense Enterprise** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. **식별자** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트합니다. 이러한 값은 이 자습서의 뒷부분에서 설명되며, [Qlik Sense Enterprise 클라이언트 지원 팀](https://www.qlik.com/us/services/support)에 문의하여 얻을 수 있습니다. URL의 기본 포트는 443이지만, 조직의 요구 사항에 따라 사용자 지정할 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 지정된 옵션에서 **페더레이션 메타데이터 XML**을 찾아서 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `BrittaSimon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Qlik Sense Enterprise에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Qlik Sense Enterprise**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enterprise SSO 구성

1. Qlik Sense 서버에 업로드할 수 있도록 페더레이션 메타데이터 XML 파일을 준비합니다.

    > [!NOTE]
    > Qlik Sense 서버로 IdP 메타데이터를 업로드하기 전에 Azure AD와 Qlik Sense 서버 간의 적절한 작동을 위해 정보를 제거하도록 파일을 편집해야 합니다.

    ![QlikSense][qs24]

    a. 텍스트 편집기의 Azure Portal에서 다운로드한 FederationMetaData.xml 파일을 엽니다.

    b. **RoleDescriptor**값을 검색합니다.  네 개의 항목(열고 닫는 요소 태그의 두 쌍)이 있습니다.

    다. 파일에서 사이의 RoleDescriptor 태그와 모든 정보를 삭제합니다.

    d. 파일을 저장하고 이 문서의 뒷부분에서 사용하기 위해 가까이 둡니다.

2. 가상 프록시 구성을 만들 수 있는 사용자로 Qlik Sense QMC(Qlik Management Console)로 이동합니다.

3. QMC에서 **가상 프록시** 메뉴 항목을 클릭합니다.

    ![QlikSense][qs6]

4. 화면 아래쪽에서 **새로 만들기** 단추를 클릭합니다.

    ![QlikSense][qs7]

5. 가상 프록시 편집 화면이 나타납니다.  화면 오른쪽은 구성 옵션을 표시하기 위한 메뉴입니다.

    ![QlikSense][qs9]

6. 식별 메뉴 옵션을 선택하여 Azure 가상 프록시 구성에 대한 식별 정보를 입력합니다.

    ![QlikSense][qs8]  

    a. **설명** 필드는 가상 프록시 구성에 대한 친숙한 이름입니다.  설명에 대한 값을 입력합니다.

    b. **접두사** 필드는 Azure AD Single Sign-on으로 Qlik Sense 연결하기 위한 가상 프록시 엔드포인트를 식별합니다.  이 가상 프록시에 대한 고유한 접두사 이름을 입력합니다.

    다. **세션 비활성 시간(분)** 은 이 가상 프록시를 통한 연결에 대한 제한 시간입니다.

    d. **세션 쿠키 헤더 이름**은 인증에 성공한 후 사용자가 받는 Qlik Sense 세션에 대한 세션 식별자를 저장하는 쿠키 이름입니다.  이 이름은 고유해야 합니다.

7. 표시하려면 인증 메뉴 옵션을 클릭합니다.  인증 화면이 나타납니다.

    ![QlikSense][qs10]

    a. **익명 액세스 모드** 드롭다운은 익명 사용자가 가상 프록시를 통해 Qlik Sense에 액세스할 수 있는지를 결정합니다.  기본 옵션은 익명 사용자 없음입니다.

    b. **인증 방법** 드롭다운은 가상 프록시가 사용할 인증 체계를 결정합니다.  드롭다운 목록에서 SAML을 선택합니다.  그 결과 더 많은 옵션이 표시됩니다.

    다. **SAML 호스트 URI 필드**에서 사용자가 이 SAML 가상 프록시를 통해 Qlik Sense에 액세스하기 위해 입력할 호스트 이름을 입력합니다.  호스트 이름은 Qlik Sense 서버의 URI입니다.

    d. **SAML 엔터티 ID**에서 SAML 호스트 URI 필드에 입력한 동일한 값을 입력합니다.

    e. **SAML IdP 메타데이터**는 **Azure AD 구성에서 페더레이션 메타데이터 편집** 섹션이 앞부분에서 편집한 파일입니다.  **IdP 메타데이터를 업로드하기 전에 파일을 편집해야 합니다** .  **파일을 아직 편집하지 않은 경우 위의 지침을 참조하세요.**  파일을 편집한 경우 찾아보기 단추를 클릭하고 편집한 메타데이터 파일을 선택하여 가상 프록시 구성에 업로드합니다.

    f. Azure AD가 Qlik Sense 서버에 전송하는 **UserID**를 나타내는 SAML 특성에 대한 특성 이름 또는 스키마 참조를 입력합니다.  스키마 참조 정보는 Azure 앱 화면 게시 구성에서 사용할 수 있습니다.  이름 특성을 사용하려면 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 선택합니다.

    g. Azure AD 통해 Qlik Sense 서버에 인증하는 경우 사용자에 연결될 **사용자 디렉터리** 에 대한 값을 입력합니다.  하드 코드된 값은 **(대괄호)[]** 로 묶어야 합니다.  Azure AD SAML 어설션에서 전송된 특성을 사용하려면 대괄호 **없이** 이 텍스트 상자에 특성의 이름을 입력합니다.

    h. **SAML 서명 알고리즘** 은 가상 프록시 구성에 대한 서비스 공급자(이 경우 Qlik Sense 서버) 인증서 서명을 설정합니다.  Qlik Sense 서버가 Microsoft Enhanced RSA and AES Cryptographic Provider를 사용하여 생성된 신뢰할 수 있는 인증서를 사용하는 경우 SAML 서명 알고리즘을 **SHA-256**으로 변경합니다.

    i. SAML 특성 매핑 섹션을 통해 그룹과 같은 추가 특성을 보안 규칙에서 사용하기 위해 Qlik Sense로 전송할 수 있습니다.

8. 표시하려면 **부하 분산** 메뉴 옵션을 클릭합니다.  부하 분산 화면이 나타납니다.

    ![QlikSense][qs11]

9. **새 서버 노드 추가** 단추를 클릭하고 Qlik Sense가 부하 분산을 위해 세션을 전송할 엔진 노드 또는 노드를 선택하고 **추가** 단추를 클릭합니다.

    ![QlikSense][qs12]

10. 표시하려면 고급 메뉴 옵션을 클릭합니다. 고급 화면이 나타납니다.

    ![QlikSense][qs13]

    호스트 허용 목록은 Qlik Sense 서버에 연결할 때 허용되는 호스트 이름을 식별합니다.  **Qlik Sense 서버에 연결할 때 사용자가 지정할 호스트 이름을 입력합니다.** 호스트 이름은 https:// 없이 SAML 호스트 URI와 동일한 값입니다.

11. **적용** 단추를 클릭합니다.

    ![QlikSense][qs14]

12. 확인을 클릭하여 가상 프록시에 연결된 프록시가 다시 시작된다는 경고 메시지를 수락합니다.

    ![QlikSense][qs15]

13. 화면 오른쪽에 연결된 항목 메뉴가 나타납니다.  **프록시** 메뉴 옵션을 클릭합니다.

    ![QlikSense][qs16]

14. 프록시 화면이 나타납니다.  맨 아래의 **링크** 단추를 클릭하여 가상 프록시에 프록시를 연결합니다.

    ![QlikSense][qs17]

15. 이 가상 프록시 연결을 지원하는 프록시 노드를 선택하고 **링크** 단추를 클릭합니다.  연결 후 연결된 프록시 아래에 프록시가 나열됩니다.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. 약 5~10초 후에 새로 고침 QMC 메시지가 표시됩니다.  **새로 고침 QMC** 단추를 클릭합니다.

    ![QlikSense][qs20]

17. QMC가 새로 고쳐지면 **가상 프록시** 메뉴 항목을 클릭합니다. 새 SAML 가상 프록시 항목이 화면의 테이블에 나열됩니다.  가상 프록시 항목을 한 번 클릭합니다.

    ![QlikSense][qs51]

18. 화면 맨 아래에서 SP 메타데이터 다운로드 단추가 활성화됩니다.  **SP 메타데이터 다운로드** 단추를 클릭하여 파일에 메타데이터를 저장합니다.

    ![QlikSense][qs52]

19. SP 메타데이터 파일을 엽니다.  **entityID** 및 **AssertionConsumerService** 항목을 관찰합니다.  이러한 값은 Azure AD 애플리케이션 구성의 **ID**, **로그인 URL** 및 **회신 URL**에 해당합니다. Azure AD 애플리케이션 구성의 **Qlik Sense Enterprise 도메인 및 URL** 섹션에 해당 값을 붙여 넣습니다. 일치하지 않는 경우 Azure AD 앱 구성 마법사에서 대체해야 합니다.

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise 테스트 사용자 만들기

Qlik Sense Enterprise는 **Just-In-Time 프로비저닝**을 지원하며, 사용자는 SSO 기능을 사용하므로 Qlik Sense Enterprise의 '사용자' 리포지토리에 자동으로 추가됩니다. 또한 클라이언트는 QMC를 사용하고, Active Directory 등의 LDAP 선택에서 Qlik Sense Enterprise의 사용자를 미리 채울 수 있도록 UDC(사용자 디렉터리 커넥터)를 만들 수 있습니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Qlik Sense Enterprise 타일을 선택하면 SSO를 설정한 Qlik Sense Enterprise 애플리케이션에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
