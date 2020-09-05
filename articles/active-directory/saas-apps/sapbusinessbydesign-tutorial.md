---
title: '자습서: SAP Business ByDesign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Business ByDesign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 6959b7e819ad906f04a18661d6bd4aa2d82f8984
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543377"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>자습서: SAP Business ByDesign과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Business ByDesign을 통합하는 방법에 대해 알아봅니다.
SAP Business ByDesign을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* SAP Business ByDesign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SAP Business ByDesign에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

SAP Business ByDesign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Business ByDesign Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAP Business ByDesign에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>갤러리에서 SAP Business ByDesign 추가

SAP Business ByDesign의 Azure AD 통합을 구성하려면 갤러리의 SAP Business ByDesign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP Business ByDesign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SAP Business ByDesign**을 입력하고 결과 패널에서 **SAP Business ByDesign**을 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 SAP Business ByDesign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 SAP Business ByDesign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SAP Business ByDesign의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Business ByDesign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SAP Business ByDesign Single Sign-On 구성](#configure-sap-business-bydesign-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SAP Business ByDesign 테스트 사용자 만들기](#create-sap-business-bydesign-test-user)** - Azure AD를 대표하여 SAP Business ByDesign에서 Britta Simon에 해당하는 사용자가 있어야 합니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SAP Business ByDesign에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAP Business ByDesign** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SAP Business ByDesign 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<servername>.sapbydesign.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<servername>.sapbydesign.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SAP Business ByDesign 클라이언트 지원 팀](https://www.sap.com/products/cloud-analytics.support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. SAP Business ByDesign 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. **편집** 아이콘을 클릭하여 **이름 식별자 값**을 편집합니다.

    ![이미지](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. **사용자 클레임 관리** 섹션에서 다음 단계를 수행합니다. ![image](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. **변환**을 **원본**으로 선택합니다.

    b. **변환** 드롭다운 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    다. **매개 변수 1** 드롭다운 목록에서 구현에 사용할 사용자 특성을 선택합니다. 예를 들어, EmployeeID를 고유한 사용자 식별자로 사용하고자 하고 ExtensionAttribute2에 특성 값을 저장했다면 user.extensionattribute2를 선택합니다.

    d. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>SAP Business ByDesign Single Sign-On 구성

1. 관리자 권한으로 SAP Business ByDesign 포털에 로그인합니다.

2. **애플리케이션 및 사용자 관리 일반 작업**으로 이동하여 **ID 공급자** 탭을 클릭합니다.

3. **새 ID 공급자**를 클릭하고 Azure Portal에서 다운로드한 메타데이터 XML 파일을 선택합니다. 시스템은 메타데이터를 가져와서 필수 서명 인증서 및 암호화 인증서를 자동으로 업로드합니다.

    ![Single Sign-on 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. **어설션 소비자 서비스 URL**을 SAML 요청에 포함하려면 **어설션 소비자 서비스 URL 포함**을 선택합니다.

5. **Single Sign-on 활성화**를 클릭합니다.

6. 변경 내용을 저장합니다.

7. **내 시스템** 탭을 클릭합니다.

    ![Single Sign-on 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. **Azure AD 로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    ![Single Sign-on 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. 직원이 **수동 ID 공급자 선택**을 선택하여 사용자 ID 및 암호 또는 SSO를 사용하는 로그온 중 하나를 수동으로 선택할 수 있는지 여부를 지정합니다.

10. **SSO URL** 섹션에서 시스템에 로그온하려는 직원이 사용해야 하는 URL을 지정합니다.
    직원에게 전송된 URL 드롭다운 목록에서 다음 옵션 중 하나를 선택할 수 있습니다.

    **SSO가 아닌 URL**

    시스템은 직원에게 정상적인 시스템 URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 없고 대신 암호 또는 인증서를 사용해야 합니다.

    **SSO URL**

    시스템은 직원에게 SSO URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 있습니다. IdP를 통해 인증 요청이 리디렉션됩니다.

    **자동 선택**

    SSO가 활성 상태가 아닌 경우 시스템은 직원에게 정상적인 시스템 URL을 보냅니다. SSO가 활성 상태인 경우 시스템은 직원이 암호를 가지는지 여부를 확인합니다. 암호를 사용할 수 있는 경우 SSO URL와 SSO가 아닌 URL은 직원에게 전송됩니다. 그러나 직원에게 암호가 없는 경우 SSO URL만이 직원에게 전송됩니다.

11. 변경 내용을 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Business ByDesign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SAP Business ByDesign**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SAP Business ByDesign**을 선택합니다.

    ![애플리케이션 목록의 SAP Business ByDesign 연결](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business ByDesign 테스트 사용자 만들기

이 섹션에서는 SAP Business ByDesign에서 Britta Simon이라는 사용자를 만듭니다. [SAP Business ByDesign 클라이언트 지원 팀](https://www.sap.com/products/cloud-analytics.support.html)과 협력하여 SAP Business ByDesign 플랫폼에 사용자를 추가합니다. 

> [!NOTE]
> NameID 값이 SAP Business ByDesign 플랫폼에서 사용자 이름 필드와 일치하는지 확인하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Business ByDesign 타일을 클릭하면 SSO를 설정한 SAP Business ByDesign에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
