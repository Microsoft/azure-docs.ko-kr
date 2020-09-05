---
title: '자습서: SAP HANA와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP HANA 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fc3b5b096e339d0c0f4bca7afc92d7315246fcd4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552291"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>자습서: SAP HANA와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP HANA를 통합하는 방법에 대해 알아봅니다.
SAP HANA를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* SAP HANA에 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SAP HANA에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

SAP HANA와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SSO(Single Sign-On)를 사용하도록 설정된 SAP HANA 구독
- 공용 IaaS, 온-프레미스, Azure VM 또는 Azure의 SAP 큰 인스턴스에서 실행 중인 HANA 인스턴스
- XSA 관리 웹 인터페이스 및 HANA 인스턴스에 설치된 HANA Studio

> [!NOTE]
> 이 자습서의 단계를 테스트할 때 SAP HANA의 프로덕션 환경을 사용하지 않는 것이 좋습니다. 애플리케이션 개발 또는 스테이징 환경에서 통합을 먼저 테스트한 다음, 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAP HANA Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAP HANA에서 **IDP** 시작 SSO를 지원합니다.
* SAP HANA에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-sap-hana-from-the-gallery"></a>갤러리에서 SAP HANA 추가

SAP HANA의 Azure AD 통합을 구성하려면 갤러리의 SAP HANA를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP HANA를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SAP HANA**를 입력하고 결과 패널에서 **SAP HANA**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 SAP HANA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 SAP HANA에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SAP HANA의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP HANA에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SAP HANA Single Sign-On 구성](#configure-sap-hana-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SAP HANA 테스트 사용자 만들기](#create-sap-hana-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP HANA에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SAP HANA에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAP HANA** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    ![SAP HANA 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `HA100`을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [SAP HANA 클라이언트 지원 팀](https://cloudplatform.sap.com/contact.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. SAP HANA 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. **사용자 특성 및 클레임** 대화 상자의 **사용자 특성** 섹션에서 다음 단계를 수행합니다.
 
    a. **편집** 아이콘을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/saphana-tutorial/tutorial_usermail.png)

    ![이미지](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    다. **매개 변수 1** 목록에서 **user.mail**을 선택합니다.

    d. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>SAP HANA Single Sign-On 구성

1. SAP HANA 쪽에 Single Sign-On을 구성하려면 해당 HTTPS 엔드포인트로 이동하여 **HANA XSA 웹 콘솔**에 로그인합니다.

    > [!NOTE]
    > 기본 구성에서는 URL이 요청을 로그온 화면으로 리디렉션하며, 인증된 SAP HANA 데이터베이스 사용자의 자격 증명이 필요합니다. 로그인하는 사용자는 SAML 관리 작업을 수행할 권한이 있어야 합니다.

2. XSA 웹 인터페이스에서 **SAML ID 공급자**로 이동합니다. 여기에서 화면 아래쪽의 **+** 단추를 선택하여 **ID 공급자 정보 추가** 창을 표시합니다. 다음 단계를 수행합니다.

    ![ID 공급자 추가](./media/saphana-tutorial/sap1.png)

    a. **ID 공급자 정보 추가** 창에서 메타데이터 XML(Azure Portal에서 다운로드한)의 콘텐츠를 **메타데이터** 상자에 붙여넣습니다.

    ![ID 공급자 설정 추가](./media/saphana-tutorial/sap2.png)

    b. XML 문서의 콘텐츠가 유효하면 구문 분석 프로세스를 통해 **일반 데이터** 화면 영역의 **제목, 엔터티 ID 및 발급자** 필드에 필요한 정보가 추출됩니다. **대상** 화면 영역의 URL 필드에 필요한 정보도 추출됩니다(예: **기준 URL 및 SingleSignOn URL (*)** 필드).

    ![ID 공급자 설정 추가](./media/saphana-tutorial/sap3.png)

    다. **일반 데이터** 화면 영역의 **이름** 상자에 새 SAML SSO ID 공급자의 이름을 입력합니다.

    > [!NOTE]
    > SAML IDP의 이름은 필수이며 고유해야 합니다. SAP HANA XS 애플리케이션에서 사용할 인증 방법으로 SAML을 선택할 때 표시되는 SAML IDP 목록에 나타납니다. 예를 들어 XS 아티팩트 관리 도구의 **인증** 화면 영역에서 이 작업을 수행할 수 있습니다.

3. **저장**을 선택하여 SAML ID 공급자의 세부 정보를 저장하고 알려진 SAML IDP 목록에 새 SAML IDP를 추가합니다.

    ![저장 단추](./media/saphana-tutorial/sap4.png)

4. HANA Studio **Configuration**(구성) 탭의 시스템 속성 내에서 **saml**을 기준으로 설정을 필터링합니다. 그런 다음 **assertion_timeout**을 **10초**에서 **120초**로 조정합니다.

    ![assertion_timeout 설정](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP HANA에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SAP HANA**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SAP HANA**를 입력하고 선택합니다.

    ![애플리케이션 목록의 SAP HANA 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-sap-hana-test-user"></a>SAP HANA 테스트 사용자 만들기

Azure AD 사용자가 SAP HANA에 로그인할 수 있도록 SAP HANA에 프로비전해야 합니다.
SAP HANA는 기본적으로 사용하도록 설정되는 **Just-In-Time 프로비전**을 지원합니다.

사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.

>[!NOTE]
>사용자가 사용하는 외부 인증을 변경할 수 있습니다. Kerberos와 같은 외부 시스템으로 인증할 수 있습니다. 외부 ID에 대한 자세한 내용은 [도메인 관리자](https://cloudplatform.sap.com/contact.html)에게 문의하세요.

1. 관리자 권한으로 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)를 연 다음 SAML SSO에 DB 사용자를 사용하도록 설정합니다.

    ![사용자 만들기](./media/saphana-tutorial/sap5.png)

2. **SAML** 왼쪽의 보이지 않는 확인란을 선택한 다음 **구성** 링크를 선택합니다.

3. **추가**를 선택하여 SAML IDP를 추가합니다.  적절한 SAML IDP를 선택한 다음 **확인**을 선택합니다.

4. **외부 ID**(이 경우, BrittaSimon)를 추가하거나 **모두**를 선택합니다. 그런 다음, **확인**을 선택합니다.

   > [!Note]
   > **모두** 확인란을 선택하지 않으면 HANA의 사용자 이름이 도메인 접미사 앞에 있는 UPN의 사용자 이름과 정확히 일치해야 합니다. (예: BrittaSimon@contoso.com은 HANA에서 BrittaSimon이 됩니다.)

5. 테스트를 위해 모든 **XS** 역할을 사용자에게 할당합니다.

    ![역할 할당](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 사용 사례에 적합한 권한만 부여해야 합니다.

6. 사용자를 저장합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP HANA 타일을 클릭하면 SSO를 설정한 SAP HANA에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

