---
title: '자습서: SilkRoad Life Suite와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SilkRoad Life Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 49dd333454f0eb92f5fb0dddc40390ec1baa91c5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525570"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>자습서: SilkRoad Life Suite와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SilkRoad Life Suite를 통합하는 방법에 대해 알아봅니다.
SilkRoad Life Suite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서 사용자의 SilkRoad Life Suite에 대한 액세스 권한을 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 SilkRoad Life Suite에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

SilkRoad Life Suite와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SilkRoad Life Suite Single-Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SilkRoad Life Suite는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>갤러리에서 SilkRoad Life Suite 추가

SilkRoad Life Suite가 Azure AD에 통합되도록 구성하려면 갤러리의 SilkRoad Life Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SilkRoad Life Suite를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SilkRoad Life Suite**를 입력하고 결과 패널에서 **SilkRoad Life Suite**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록에서 SilkRoad Life Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 SilkRoad Life Suite에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SilkRoad Life Suite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SilkRoad Life Suite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SilkRoad Life Suite Single Sign-On 구성](#configure-silkroad-life-suite-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SilkRoad Life Suite 테스트 사용자 만들기](#create-silkroad-life-suite-test-user)** - Britta Simon이라는 Azure AD 사용자와 연결된 SilkRoad Life Suite 사용자를 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SilkRoad Life Suite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SilkRoad Life Suite** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    > [!NOTE]
    > 이 자습서의 뒷부분에서 설명할 **서비스 공급자 메타데이터 파일**을 가져오겠습니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![이미지](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![이미지](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    ![이미지](common/sp-identifier-reply.png)

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

    d. **로그인 URL** 텍스트 상자에서 `https://<subdomain>.silkroad-eng.com/Authentication/` 패턴을 사용하여 URL을 입력합니다.

5. **서비스 공급자 메타데이터 파일**이 없으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SilkRoad Life Suite 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    a. **로그인 URL** 텍스트 상자에서 `https://<subdomain>.silkroad-eng.com/Authentication/` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다.

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    다. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [SilkRoad Life Suite 클라이언트 지원 팀](https://www.silkroad.com/locations/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **SilkRoad Life Suite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>SilkRoad Life Suite Single Sign-On 구성

1. SilkRoad 회사 사이트에 관리자로 로그인합니다.

    > [!NOTE]
    > Microsoft Azure AD와 페더레이션을 구성하기 위한 SilkRoad Life 인증 애플리케이션에 대한 액세스를 얻으려면 SilkRoad 지원 또는 SilkRoad 서비스 담당자에게 문의하세요.

1. **서비스 공급자**로 이동한 다음 **페더레이션 세부 정보**를 클릭합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. **페더레이션 메타데이터 다운로드**를 클릭한 후, 컴퓨터에 메타데이터 파일을 저장 합니다. 다운로드한 페더레이션 메타데이터를 Azure Portal의 **기본 SAML 구성** 섹션에서 **서비스 공급자 메타데이터 파일**로 사용합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. **SilkRoad** 애플리케이션에서 **인증 원본**을 클릭합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. **인증 원본 추가**를 클릭합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. **인증 원본 추가** 섹션에서 다음 단계를 수행합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. **옵션2 - 메타데이터 파일**에서 **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.
  
    b. **파일 데이터를 사용하여 ID 공급자 만들기**를 클릭합니다.

1. **인증 원본** 섹션에서 **편집**을 클릭합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. **인증 원본 편집** 대화 상자에서 다음 단계를 수행합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. **사용**을 **예**로 선택합니다.

    b. **EntityId** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. **IdP 설명** 텍스트 상자에 구성에 대한 설명을 입력합니다(예: *Azure AD SSO*).

    d. **메타데이터 파일** 텍스트 상자에서, Azure Portal에서 다운로드한 **메타데이터** 파일을 업로드합니다.
  
    e. **IdP 이름** 텍스트 상자에 구성에 적용되는 이름을 입력합니다(예: *Azure SP*).
  
    f. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 서비스 URL** 텍스트 상자에 붙여넣습니다.

    g. Azure Portal에서 복사한 **로그인 URL** 값을 **로그온 서비스 URL** 텍스트 상자에 붙여넣습니다.

    h. **저장**을 클릭합니다.

1. 다른 모든 인증 원본을 사용하지 않도록 설정합니다.

    ![Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SilkRoad Life Suite에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SilkRoad Life Suite**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SilkRoad Life Suite**를 선택합니다.

    ![애플리케이션 목록의 SilkRoad Life Suite 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad Life Suite 테스트 사용자 만들기

이 섹션에서는 SilkRoad Life Suite에서 Britta Simon이라는 사용자를 만듭니다. [SilkRoad Life Suite 클라이언트 지원 팀](https://www.silkroad.com/locations/)과 함께 작업하여 SilkRoad Life Suite 플랫폼에서 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SilkRoad Life Suite 타일을 클릭하면 SSO를 설정한 SilkRoad Life Suite에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
