---
title: '자습서: Palo Alto Networks Captive Portal과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks Captive Portal 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 1dad0ecc80302ae6b48d420664723a3a03fc9ea5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554025"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>자습서: Palo Alto Networks Captive Portal과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks Captive Portal을 통합하는 방법에 대해 알아봅니다.

Azure AD에 Palo Alto Networks Captive Portal을 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* Azure AD에서 Palo Alto Networks Captive Portal에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자 Azure AD 계정을 사용하여 Palo Alto Networks Captive Portal에서 사용자를 자동으로 로그인할 수 있습니다.
* 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

Palo Alto Networks Captive Portal과 Azure AD를 통합하려면 다음 항목이 필요합니다.

* Azure Active Directory 구독 Azure AD가 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
* Palo Alto Networks Captive Portal SSO(Single Sign-on)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Palo Alto Networks Captive Portal에서는 다음 시나리오를 지원합니다.

* **IDP 시작 Single Sign-On**
* **Just-In-Time 사용자 프로비전**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>갤러리에서 Palo Alto Networks Captive Portal 추가

시작하려면 갤러리에서 관리되는 SaaS 앱 목록에 Palo Alto Networks Captive Portal을 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![메뉴에서 엔터프라이즈 애플리케이션 옵션](common/enterprise-applications.png)

3. **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Palo Alto Networks Captive Portal**을 입력합니다. 검색 결과에서 **Palo Alto Networks - Captive Portal**을 선택한 다음, **추가**를 선택합니다.

     ![결과 목록의 Palo Alto Networks - Captive Portal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

*Britta Simon*이라는 테스트 사용자를 기반으로 하여 Palo Alto Networks Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 Palo Alto Networks Captive Portal의 동일한 사용자 간에 연결 관계를 설정해야 합니다. 

Palo Alto Networks Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 작업을 완료합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** : 사용자가 이 기능을 사용하도록 설정합니다.
2. **[Palo Alto Networks Captive Portal Single Sign-On 구성](#configure-palo-alto-networks-captive-portal-single-sign-on)** : 애플리케이션에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** : *Britta Simon* 사용자로 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** : Britta Simon이 Azure AD Single Sign-On을 사용하도록 설정합니다.
5. **Palo Alto Networks Captive Portal 테스트 사용자 만들기**: Azure AD 사용자에게 연결된 Palo Alto Networks Captive Portal에서 해당하는 *Britta Simon* 사용자를 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** : 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

먼저 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Palo Alto Networks - Captive Portal** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 창에서 **SAML**을 선택합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 창에서 연필 **편집** 아이콘을 선택합니다.

    ![연필 모양의 편집 아이콘](common/edit-urls.png)

4. **기본 SAML 구성** 창에서 다음 단계를 완료합니다.

    ![Palo Alto Networks Captive Portal 기본 SAML 구성 창](common/idp-intiated.png)

   1. **식별자**에 대해 `https://<customer_firewall_host_name>/SAML20/SP` 패턴이 있는 URL을 입력합니다.

   2. **회신 URL**에 대해 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 패턴이 있는 URL을 입력합니다.

      > [!NOTE]
      > 실제 식별자 및 회신 URL로 이 단계의 자리 표시자 값을 업데이트합니다. 실제 값을 얻으려면 [Palo Alto Networks Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의하세요.

5. **SAML 서명 인증서** 섹션의 **페더레이션 메타데이터 XML** 옆에서 **다운로드**를 선택합니다. 다운로드한 파일을 컴퓨터에 저장합니다.

    ![페더레이션 메타데이터 XML 다운로드 링크](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Palo Alto Networks Captive Portal Single Sign-On 구성

다음으로 Palo Alto Networks Captive Portal에서 Single Sign-On을 설정합니다.

1. 다른 브라우저 창에서 Palo Alto Networks 웹 사이트에 관리자로 로그인합니다.

2. **디바이스** 탭을 선택합니다.

    ![Palo Alto Networks 웹 사이트 디바이스 탭](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 메뉴에서 **SAML ID 공급 기업**을 선택한 다음, **가져오기**를 선택합니다.

    ![가져오기 단추](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **SAML ID 공급 기업 서버 프로필 가져오기** 대화 상자에서 다음 단계를 완료합니다.

    ![Palo Alto Networks Single Sign-on 구성](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **프로필 이름**에 대해 **AzureAD-CaptivePortal**과 같은 이름을 입력합니다.
    
    2. **ID 공급 기업 메타데이터** 옆에서 **찾아보기**를 선택합니다. Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.
    
    3. **확인**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

다음으로 Azure Portal에서 *Britta Simon*이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 창에서 다음 단계를 완료합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름**에 대해 **BrittaSimon**을 입력합니다.
  
    2. **사용자 이름**에 **BrittaSimon\@\<your_company_domain\>** 을 입력합니다. 예를 들어 **BrittaSimon\@contoso.com**입니다.

    3. **암호**에 대해 암호를 입력합니다. 입력한 암호의 기록을 유지하는 것이 좋습니다. **암호 표시** 확인란을 선택하여 암호를 표시할 수 있습니다.

    4. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

다음으로 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Palo Alto Networks Captive Portal에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Palo Alto Networks - Captive Portal**을 입력한 다음, 애플리케이션을 선택합니다.

    ![애플리케이션 목록의 Palo Alto Networks - Captive Portal 링크](common/all-applications.png)

3. 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택합니다. 그런 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 창의 **사용자** 목록에서 **Britta Simon**을 선택합니다. **선택**을 선택합니다.

6. SAML 어설션에 역할 값을 추가하려면 **역할 선택** 창에서 사용자에 대한 관련 역할을 선택합니다. **선택**을 선택합니다.

7. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks Captive Portal 테스트 사용자 만들기

다음으로 Palo Alto Networks Captive Portal에 *Britta Simon*이라는 사용자를 만듭니다. Palo Alto Networks Captive Portal은 기본적으로 사용하도록 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션의 작업을 완료하지 않아도 됩니다. Palo Alto Networks Captive Portal에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Palo Alto Networks Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

Palo Alto Networks Captive Portal은 Windows VM의 방화벽 뒤에서 설치됩니다. Palo Alto Networks Captive Portal에서 Single Sign-On을 테스트하려면 RDP(원격 데스크톱 프로토콜)를 사용하여 Windows VM에 로그인합니다. RDP 세션에서 브라우저를 열고 웹 사이트로 이동합니다. SSO URL이 열리고 인증하라는 메시지가 표시됩니다. 인증이 완료되면 웹 사이트에 액세스할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 다음 문서를 참조하세요.

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

