---
title: '자습서: PolicyStat과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 PolicyStat 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 212bd1bc0b15ad985fc8037e99876d78132b2c22
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547939"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>자습서: PolicyStat과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 PolicyStat를 통합하는 방법에 대해 알아봅니다.
PolicyStat를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* PolicyStat에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 PolicyStat(Single Sign-On)에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

PolicyStat와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* PolicyStat Single Sign-On이 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* PolicyStat는 **SP**에서 시작된 SSO를 지원합니다.

* PolicyStat는 **Just In Time** 사용자 프로비전을 지원합니다.

## <a name="adding-policystat-from-the-gallery"></a>갤러리에서 PolicyStat 추가

PolicyStat가 Azure AD에 통합되도록 구성하려면 갤러리의 PolicyStat를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 PolicyStat를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **PolicyStat**를 입력하고 결과 패널에서 **PolicyStat**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 PolicyStat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 사용하여 PolicyStat에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 PolicyStat의 관련 사용자 간에 연결 관계를 설정해야 합니다.

PolicyStat에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[PolicyStat Single Sign-On 구성](#configure-policystat-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
4. **[PolicyStat 테스트 사용자 만들기](#create-policystat-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 PolicyStat에 만듭니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

PolicyStat에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **PolicyStat** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![PolicyStat 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<companyname>.policystat.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<companyname>.policystat.com/saml2/metadata/` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [PolicyStat 클라이언트 지원 팀](http://www.policystat.com/support/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

5. PolicyStat 애플리케이션은 특정 서식에서 SAML 어설션을 예상하므로 SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. PolicyStat 애플리케이션의 경우 위에서 언급한 특성 외에 몇 가지 추가 특성을 SAML 응답에 다시 전달해야 합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | 속성 | 원본 특성 |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.
    
    ![이미지](common/new-save-attribute.png)

    ![이미지](./media/policystat-tutorial/attribute01.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본에 **변환**을 선택합니다.

    e. **변환** 목록에서 해당 행에 표시된 특성 값을 입력합니다.
    
    f. **매개 변수 1** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    g. **저장**을 클릭합니다.

7. **PolicyStat 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-policystat-single-sign-on"></a>PolicyStat Single Sign-On 구성

1. 다른 웹 브라우저 창에서 PolicyStat 회사 사이트에 관리자로 로그인합니다.

2. **관리** 탭을 클릭한 다음 왼쪽 탐색 창에서 **Single Sign-On 구성**을 클릭합니다.
   
    ![관리자 메뉴](./media/policystat-tutorial/ic808633.png "관리자 메뉴")

3. **IDP 메타데이터**를 클릭한 다음, **IDP 메타데이터** 섹션에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/policystat-tutorial/ic808636.png "Single Sign-On 구성")
   
    a. 다운로드한 메타데이터 파일을 열고 내용을 복사한 다음 **Your Identity Provider Metadata**(ID 공급자 메타데이터) 텍스트 상자에 붙여넣습니다.

    b. **변경 내용 저장**을 클릭합니다.

4. **특성 구성**을 클릭한 다음 **특성 구성** 섹션에서 다음 단계를 수행합니다.
   
    a. **사용자 이름 특성** 텍스트 상자에 **uid**를 입력합니다.

    b. **First Name Attribute**(이름 특성) 텍스트 상자에 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** 의 First Name Attribute(이름 특성) 클레임 이름을 입력합니다.

    다. **Last Name Attribute**(성 특성) 텍스트 상자에 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** 의 Last Name Attribute(성 특성) 클레임 이름을 입력합니다.

    d. **이메일 특성** 텍스트 상자에 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** 의 이메일 특성 클레임 이름을 입력합니다.

    e. **변경 내용 저장**을 클릭합니다.

5. **설정** 섹션에서 **Single Sign-On 통합 사용**을 선택합니다.
   
    ![Single Sign-On 구성](./media/policystat-tutorial/ic808634.png "Single Sign-On 구성")


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 PolicyStat에 대한 액세스 권한을 부여하여 자신의 계정에서 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **PolicyStat**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **PolicyStat**를 선택합니다.

    ![애플리케이션 목록의 PolicyStat 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 계정을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-policystat-test-user"></a>PolicyStat 테스트 사용자 만들기

이 섹션에서는 PolicyStat에서 Britta Simon이라는 사용자를 만듭니다. PolicyStat는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. PolicyStat에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!NOTE]
>다른 PolicyStat 사용자 계정 생성 도구 또는 PolicyStat가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 PolicyStat 타일을 클릭하면 SSO를 설정한 PolicyStat에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
