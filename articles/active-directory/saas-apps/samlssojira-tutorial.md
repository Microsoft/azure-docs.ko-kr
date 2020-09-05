---
title: '자습서: SAML SSO for Jira by Resolution GmbH와 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory 및 SAML SSO for Jira by resolution GmbH 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 44077c2685ce78b7ba1ea140a34dd75dcf5c7c46
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543468"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>자습서: SAML SSO for Jira by resolution GmbH와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)로 SAML SSO for Jira by resolution GmbH를 설정하는 방법을 알아봅니다.
SAML SSO for Jira by resolution GmbH와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

* Jira with the SAML SSO plugin by resolution GmbH에 로그인할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 SAML SSO for Jira by resolution GmbH(Single Sign-On)를 사용하여 Azure AD 계정을 통해 Jira에 자동으로 로그인되도록 할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 통합 및 SAML SSO for Jira by resolution GmbH를 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAML SSO for Jira by resolution GmbH Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAML SSO for Jira by resolution GmbH는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Single Sign-On에 대한 엔터프라이즈 애플리케이션 추가

Azure AD에서 Single Sign-On을 설정하려면 새로운 엔터프라이즈 애플리케이션을 추가해야 합니다. 갤러리에는 이를 위해 미리 구성된 애플리케이션 **SAML SSO for Jira by resolution GmbH**가 있습니다.

**갤러리에서 SAML SSO for Jira by resolution GmbH를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 클릭합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위에 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SAML SSO for Jira by resolution GmbH**를 입력하고, 결과 패널에서 **SAML SSO for Jira by resolution GmbH**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다. 또한 엔터프라이즈 앱의 이름을 변경할 수 있습니다.

     ![결과 목록의 SAML SSO for Jira by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>SAML SSO 플러그 인 및 Azure AD를 사용하여 Single Sign-On 구성 및 테스트

이 섹션에서는 Azure AD 사용자를 위해 Jira에 대한 Single Sign-On을 테스트하고 구성합니다. **Britta Simon**이라는 테스트 사용자에 대해 수행됩니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SAML SSO for Jira by resolution GmbH의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. **[Single Sign-On에 대해 Azure AD 엔터프라이즈 애플리케이션 구성](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - Single Sign-On에 대해 Azure AD 엔터프라이즈 애플리케이션 구성
2. **[Jira 인스턴스의 SAML SSO 플러그 인 구성](#configure-the-saml-sso-plugin-of-your-jira-instance)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Azure AD에서 테스트 사용자를 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Azure 쪽에서 Single Sign-On을 사용하도록 테스트 사용자를 설정합니다.
1. **[Jira에서 테스트 사용자 만들기](#create-the-test-user-also-in-jira)** - Jira에서 Azure AD 테스트 사용자에 해당하는 테스트 사용자를 만듭니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Single Sign-On에 대해 Azure AD 엔터프라이즈 애플리케이션 구성

이 섹션에서는 Azure Portal에서 Single Sign-On을 설정합니다.

Jira by resolution GmbH에 대해 SAML SSO를 사용하는 Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 방금 만든 **SAML SSO for Jira by resolution GmbH** 엔터프라이즈 애플리케이션에서 왼쪽 패널에 있는 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택**에서 **SAML** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. 그런 다음, **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 애플리케이션을 **IDP** 시작 모드로 구성하려는 경우 다음 단계를 수행합니다.

    ![SAML SSO for Jira by resolution GmbH 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    다. 애플리케이션을 **SP** 시작 모드로 구성하려는 경우 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![SAML SSO for Jira by resolution GmbH 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 식별자, 회신 URL 및 로그온 URL의 경우 **\<server-base-url>** 를 Jira 인스턴스의 기본 URL로 바꿉니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다. 문제가 있으면 [SAML SSO for Jira by resolution GmbH 클라이언트 지원팀](https://www.resolution.de/go/support)에 문의하세요.

4. **SAML을 사용하여 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Jira 인스턴스의 SAML SSO 플러그 인 구성 

1. 다른 웹 브라우저 창에서 Jira 인스턴스에 관리자로 로그인합니다.

2. 오른쪽에 있는 코그를 마우스로 가리키고 **앱 관리**를 클릭합니다.
    
    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon1.png)

3. 관리자 액세스 페이지로 리디렉션되면 **암호**를 입력하고 **확인** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon2.png)

4. 일반적으로 Jira에서 Atlassian Marketplace로 리디렉션됩니다. 그렇지 않은 경우 왼쪽 패널에서 **새 앱 찾기**를 클릭합니다. **SAML SSO(Single Sign-On) for JIRA**를 검색하고 **설치** 단추를 클릭하여 SAML 플러그 인을 설치합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/store.png)

5. 플러그 인 설치가 시작됩니다. 완료되면 **닫기** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/store-2.png)

    ![Single Sign-on 구성](./media/samlssojira-tutorial/store-3.png)

6. 그런 다음, **관리**를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/store-4.png)
    
8. 다음으로 **구성**을 클릭하여 방금 설치된 플러그 인을 구성합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/store-5.png)

9. **SAML SingleSignOn 플러그인 구성** 마법사에서 **새 IDP 추가**를 클릭하여 새 ID 공급 기업으로 Azure AD를 구성합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon4.png) 

10. **SAML ID 공급 기업 선택** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon5a.png)
 
    a. **Azure AD**를 IdP 유형으로 설정합니다.
    
    b. ID 공급 기업의 **이름**을 추가합니다(예: Azure AD).
    
    다. ID 공급 기업의 **설명**(선택 사항)을 추가합니다(예: Azure AD).
    
    d. **다음**을 클릭합니다.
    
11. **ID 공급자 구성** 페이지에서 **다음**을 클릭합니다.
 
    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon5b.png)

12. **SAML IdP 메타데이터 가져오기** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon5c.png)

    a. **메타데이터 XML 파일 선택** 단추를 클릭하고 전에 다운로드한 **페더레이션 메타데이터 XML** 파일을 선택합니다.

    b. **가져오기** 단추를 클릭합니다.
     
    다. 가져오기가 완료될 때까지 잠시 대기합니다.  
     
    d. **다음** 단추를 클릭합니다.
    
13. **사용자 ID 특성 및 변환** 페이지에서 **다음** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon5d.png)
    
14. **사용자 만들기 및 업데이트** 페이지에서 **저장 및 다음**을 클릭하여 설정을 저장합니다.
    
    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon6a.png)
    
15. **설정 테스트** 페이지에서 **테스트 건너뛰기 및 수동으로 구성**을 클릭하여 현재 사용자 테스트를 건너뜁니다. 이는 다음 섹션에서 수행되며 Azure Portal에서 몇 가지 설정이 필요합니다.
    
    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon6b.png)
    
16. **확인**을 클릭하여 경고를 건너뜁니다.
    
    ![Single Sign-on 구성](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다. 사용자를 사용하여 Single Sign-On을 테스트합니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 맨 위에 있는 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자 속성**에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에서 **Britta Simon**을 입력합니다.
  
    b. **사용자 이름** 필드에 <b>BrittaSimon@contoso.com</b>을 입력합니다

    다. **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Single Sign-On을 사용하도록 엔터프라이즈 애플리케이션에 Britta Simon을 추가합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다. 

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 이 자습서의 시작 부분에서 만든 엔터프라이즈 애플리케이션을 검색합니다. 자습서의 단계를 수행하는 경우 **SAML SSO for Jira by resolution GmbH**라고 합니다. 다른 이름을 지정한 경우 해당 이름을 검색합니다.

    ![애플리케이션 목록의 SAML SSO for Jira by resolution GmbH 링크](common/all-applications.png)

3. 왼쪽 패널에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-the-test-user-also-in-jira"></a>Jira에서 테스트 사용자 만들기

Azure AD 사용자가 SAML SSO for Jira by resolution GmbH에 로그인하도록 설정하려면 SAML SSO for Jira by resolution GmbH에 프로비저닝되어야 합니다. 이 자습서의 경우 프로비저닝을 직접 수행해야 합니다. 그러나 SAML SSO plugin by resolution에 사용할 수 있는 다른 프로비저닝 모델도 있습니다(예: **Just In Time** 프로비저닝). [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all)에서 해당 설명서를 참조하세요. 질문이 있는 경우 [resolution 지원 팀](https://www.resolution.de/go/support)에 지원을 요청하세요.

**사용자 계정을 수동으로 프로비저닝하려면 다음 단계를 수행합니다.**

1. Jira 인스턴스에 관리자로 로그인합니다.

2. 택한 코그 위로 마우스를 가져가고 **사용자 관리**를 선택합니다.

   ![직원 추가](./media/samlssojira-tutorial/user1.png)

3. 관리자 액세스 페이지로 리디렉션되면 **암호**를 입력하고 **확인** 단추를 클릭합니다.

    ![직원 추가](./media/samlssojira-tutorial/user2.png) 

4. **사용자 관리** 탭 섹션에서 **사용자 만들기**를 클릭합니다.

    ![직원 추가](./media/samlssojira-tutorial/user3-new.png) 

5. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다. Azure AD에서와 같이 사용자를 정확하게 만들어야 합니다.

    ![직원 추가](./media/samlssojira-tutorial/user4-new.png) 

    a. **이메일 주소** 텍스트 상자에 사용자의 이메일 주소를 입력합니다(<b>BrittaSimon@contoso.com</b>).

    b. **전체 이름** 텍스트 상자에 사용자의 전체 이름을 입력합니다. **Britta Simon**.

    다. **사용자 이름** 텍스트 상자에 사용자의 이메일 주소를 입력합니다(<b>BrittaSimon@contoso.com</b>). 

    d. **암호** 텍스트 상자에 사용자의 암호를 입력합니다.

    e. **사용자 만들기**를 클릭하여 사용자 만들기를 완료합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAML SSO for Jira by resolution GmbH 타일을 클릭하면 SSO를 설정한 SAML SSO for Jira by resolution GmbH에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

`https://<server-base-url>/plugins/servlet/samlsso`로 이동하는 경우 Single Sign-On을 테스트할 수도 있습니다. **\<server-base-url>** 를 Jira 인스턴스의 기본 URL로 바꿉니다.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Jira에 대한 Single Sign-On 리디렉션 사용

이전 섹션에서 설명한 대로, 현재 두 가지 방법으로 Single Sign-On을 트리거할 수 있습니다. **Azure Portal**을 사용하거나 **Jira 인스턴스에 대한 특별 링크**를 사용하는 것입니다. SAML SSO plugin by resolution GmbH를 사용하면 **Jira 인스턴스를 가리키는 모든 URL에 액세스**하기만 하면 Single Sign-On을 트리거할 수 있습니다.

기본적으로 Jira에 액세스하는 모든 사용자는 플러그 인에서 옵션을 활성화한 후 Single Sign-On으로 리디렉션됩니다.

SSO 리디렉션을 활성화하려면 **Jira 인스턴스**에서 다음을 수행합니다.

1. Jira에서 SAML SSO 플러그 인의 구성 페이지에 액세스합니다.
1. 왼쪽 패널에서 **리디렉션**을 클릭합니다.

   ![왼쪽 탐색에서 리디렉션 링크를 강조 표시하는 Jira SAML SingleSignOn 플러그 인 구성 페이지의 부분 스크린샷.](./media/samlssojira-tutorial/ssore1.png)

1. **SSO 리디렉션 사용**을 선택합니다.

   ![선택한 "SSO 리디렉션 사용" 확인란이 강조 표시된 Jira SAML SingleSignOn 플러그 인 구성 페이지의 부분 스크린샷.](./media/samlssojira-tutorial/ssore2.png) 

1. 오른쪽 위 모서리의 **설정 저장** 단추를 누릅니다.

옵션을 활성화한 후에도 `https://\<server-base-url>/login.jsp?nosso`로 이동하여 **nosso 사용** 옵션을 선택하면 계속 사용자 이름/암호 프롬프트에 도달할 수 있습니다. 항상 그렇듯이, **\<server-base-url>** 를 기본 URL로 바꿉니다.


## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

