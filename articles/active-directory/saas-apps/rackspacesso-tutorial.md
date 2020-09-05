---
title: '자습서: Rackspace SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Rackspace SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: a2c2e7358c32453daf53c40a9322df4fe30642d8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548895"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>자습서: Rackspace SSO와 Azure Active Directory 통합

이 자습서에서는 Rackspace SSO를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.
Rackspace SSO를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Rackspace SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 Rackspace SSO(Single Sign-On)에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Rackspace SSO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Rackspace SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Rackspace SSO는 **SP**에서 시작된 SSO를 지원합니다.

## <a name="adding-rackspace-sso-from-the-gallery"></a>갤러리에서 Rackspace SSO 추가

Rackspace SSO가 Azure AD로 통합되도록 구성하려면 Rackspace SSO를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Rackspace SSO를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Rackspace SSO**를 입력하고, 결과 패널에서 **Rackspace SSO**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Rackspace SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Rackspace SSO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Rackspace에서 Single Sign-On을 사용하는 경우 Rackspace 사용자는 Rackspace 포털에 처음 로그인할 때 자동으로 만들어집니다. 

Rackspace SSO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Rackspace SSO Single Sign-On 구성](#configure-rackspace-sso-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Rackspace 제어판에서 특성 매핑 설정](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - Azure AD 사용자에게 Rackspace 역할을 할당합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Rackspace SSO에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Rackspace SSO** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서, [URL](https://login.rackspace.com/federate/sp.xml)에서 다운로드할 수 있는 **서비스 공급자 메타데이터 파일**을 업로드하고 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![이미지](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![이미지](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 필요한 URL이 자동으로 채워집니다.

    d. **로그온 URL** 텍스트 상자에 `https://login.rackspace.com/federate/` URL을 입력합니다.

    ![Rackspace SSO 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)   

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

이 파일은 Rackspace에 업로드되어 필요한 ID 페더레이션 구성 설정을 채웁니다.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO Single Sign-On 구성

**Rackspace SSO** 쪽에서 Single Sign-On을 구성하려면 다음을 수행합니다.

1. [제어판에 ID 공급자 추가](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)에서 설명서를 참조하세요.
1. 이 설명서가 다음 작업을 수행하는 단계를 안내합니다.
    1. 새 ID 공급자 만들기
    1. 사용자가 로그인할 때 귀사를 식별하기 위해 사용하는 이메일 도메인을 지정합니다.
    1. Azure 제어판에서 이전에 다운로드한 **페더레이션 메타데이터 XML**을 업로드합니다.

이렇게 하면 Azure 및 Rackspace가 연결하는 데 필요한 기본 SSO 설정이 올바르게 구성됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Rackspace SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Rackspace SSO**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Rackspace SSO**를 선택합니다.

    ![애플리케이션 목록의 Rackspace SSO 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Rackspace 제어판에서 특성 매핑 설정

Rackspace는 **특성 매핑 정책**을 사용하여 Single Sign-On 사용자에게 Rackspace 역할 및 그룹을 할당합니다. **특성 매핑 정책**은 Azure AD SAML 클레임을 Rackspace에 필요한 사용자 구성으로 변환합니다. 추가 설명서는 Rackspace [특성 매핑 기본 사항 설명서](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)에서 확인할 수 있습니다. 몇 가지 고려할 사항:

* Azure AD 그룹을 사용하여 다양한 수준의 Rackspace 액세스 권한을 할당하려면 Azure **Rackspace SSO** Single Sign-On 설정에서 그룹 클레임을 사용하도록 설정해야 합니다. 그러면 **특성 매핑 정책**을 사용하여 해당 그룹을 원하는 Rackspace 역할 및 그룹에 일치시킵니다.

    ![그룹 클레임 설정](common/sso-groups-claim.png)

* 기본적으로 Azure AD는 그룹의 이름 대신 SAML 그룹에 있는 Azure AD 그룹의 UID를 보냅니다. 그러나 Azure AD에 온-프레미스 Active Directory를 동기화하는 경우 그룹의 실제 이름을 보내는 옵션이 있습니다.

    ![그룹 클레임 이름 설정](common/sso-groups-claims-names.png)

다음 **특성 매핑 정책** 예제는 다음과 같은 작업을 보여줍니다.
1. Rackspace 사용자의 이름을 `user.name` SAML 클레임으로 설정합니다. 아무 클레임이나 사용할 수 있지만 사용자의 이메일 주소가 포함된 필드로 설정하는 것이 가장 일반적입니다.
1. Azure AD 그룹을 일치시키거나 그룹 이름 또는 그룹 UID에 의해 사용자에 대한 Rackspace 역할 `admin` 및 `billing:admin`을 설정합니다. `roles` 필드에 있는 `"{0}"`의 *대체*를 사용하며, 이는 `remote` 규칙 식의 결과로 바뀝니다.
1. `"{D}"` *기본 대체*를 사용하여 Rackspace가 SAML 교환 시 표준 및 잘 알려진 SAML 클레임을 조회하여 추가 SAML 필드를 검색하게 합니다.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> 정책 파일을 편집할 때 반드시 YAML 구문의 유효성을 검사하는 텍스트 편집기를 사용해야 합니다.

더 많은 예제는 Rackspace [특성 매핑 기본 사항 설명서](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)를 참조하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Rackspace SSO 타일을 클릭하면 SSO를 설정한 Rackspace SSO에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

**Rackspace SSO** Single Sign-On 설정의 **유효성 검사** 단추를 사용할 수도 있습니다.

   ![SSO 유효성 검사 단추](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

