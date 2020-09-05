---
title: '자습서: TOPdesk - Public과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 TOPdesk - Public 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: a18cb583b246c3cfbf09f43b9921497de37a3613
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524272"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>자습서: TOPdesk - Public과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TOPdesk - Public을 통합하는 방법에 대해 알아봅니다.
TOPdesk - Public을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* TOPdesk - Public에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 TOPdesk - Public에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

TOPdesk - Public과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* TOPdesk - Public Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* TOPdesk - Public에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-topdesk---public-from-the-gallery"></a>갤러리에서 TOPdesk-Public 추가

TOPdesk - Public이 Azure AD에 통합되도록 구성하려면 갤러리의 TOPdesk - Public을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TOPdesk - Public을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **TOPdesk - Public**을 입력하고, 결과 패널에서 **TOPdesk - Public**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 TOPdesk-Public](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 TOPdesk - Public에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 TOPdesk - Public의 해당 사용자 간에 연결 관계를 설정해야 합니다.

TOPdesk-Public에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[TOPdesk - Public Single Sign-On 구성](#configure-topdesk---public-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[TOPdesk - Public 테스트 사용자 만들기](#create-topdesk---public-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 TOPdesk - Public에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

TOPdesk - Public에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **TOPdesk - Public** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4.  **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    >[!NOTE]
    >**서비스 공급자 메타데이터 파일**은 이 자습서의 뒷부분에 설명되어 있는 **TOPdesk - Public Single Sign-On 구성** 섹션에서 구할 수 있습니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.
    
    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    ![TOPdesk-Public 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    d. **로그인 URL** 텍스트 상자에서 `https://<companyname>.topdesk.net` 패턴을 사용하여 URL을 입력합니다.

    e. **식별자 URL** 텍스트 상자의 TOPdesk 구성에서 검색할 수 있는 TOPdesk 메타데이터 URL을 입력합니다. `https://<companyname>.topdesk.net/saml-metadata/<identifier>` 패턴을 사용해야 합니다.
    
    f. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > **식별자**와 **회신 URL** 값이 자동으로 채워지지 않으면 수동으로 입력해야 합니다. 식별자의 경우 앞서 언급한 패턴을 따르고 회신 URL은 이 자습서의 뒷부분에 설명되어 있는 **TOPdesk - Public Single Sign-On 구성** 섹션에서 가져옵니다. **로그온 URL** 값은 실제가 아니므로 이 값은 실제 로그온 URL로 업데이트해야 합니다. 이 값을 얻으려면 [TOPdesk - Public 클라이언트 지원 팀](https://help.topdesk.com/saas/enterprise/user/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **TOPdesk - Public 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-topdesk---public-single-sign-on"></a>TOPdesk - Public Single Sign-On 구성

1. **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2. **TOPdesk** 메뉴에서 **설정**을 클릭합니다.
   
    ![설정](./media/topdesk-public-tutorial/ic790598.png "설정")

3. **로그인 설정**을 클릭합니다.
   
    ![로그인 설정](./media/topdesk-public-tutorial/ic790599.png "로그인 설정")

4. **로그인 설정** 메뉴를 확장한 다음 **일반**을 클릭합니다.
   
    ![일반](./media/topdesk-public-tutorial/ic790600.png "일반")

5. **SAML 로그인** 구성 섹션의 **공용** 섹션에서 다음 단계를 수행합니다.
   
    ![기술 설정](./media/topdesk-public-tutorial/ic790601.png "기술 설정")
   
    a. **다운로드** 를 클릭하여 공용 메타데이터 파일을 다운로드한 다음 컴퓨터에 로컬 저장합니다.
   
    b. 다운로드한 메타데이터 파일을 연 다음 **AssertionConsumerService** 노드를 찾습니다.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    다. **AssertionConsumerService** 값을 복사하여 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.      
   
6. 인증서 파일을 만들려면 다음 단계를 수행하십시오.
    
    ![MSSQLSERVER에 대한 프로토콜 속성](./media/topdesk-public-tutorial/ic790606.png "인증서")
    
    a. Azure Portal에서 다운로드한 메타데이터 파일을 엽니다.
    
    b. **fed:ApplicationServiceType**의 **xsi:type**을 가진 **RoleDescriptor** 노드를 확장합니다.
    
    다. **X509Certificate** 노드의 값을 복사합니다.
    
    d. 복사한 **X509Certificate** 값을 컴퓨터에 파일로 로컬 저장합니다.

7. **공용** 섹션에서 **추가**를 클릭합니다.
    
    ![SAML 로그인](./media/topdesk-public-tutorial/ic790625.png "공용")

8. **SAML 구성 도우미** 대화 상자 페이지에서 다음 단계를 수행합니다.
    
    ![SAML 구성 도우미](./media/topdesk-public-tutorial/ic790608.png "SAML 구성 도우미")
    
    a. Azure Portal에서 다운로드한 메타데이터 파일을 업로드하려면 **페더레이션 메타데이터**에서 **찾아보기**를 클릭합니다.

    b. 인증서 파일을 업로드하려면 **인증서(RSA)** 에서 **찾아보기**를 클릭합니다.

    다. TOPdesk 지원팀에서 받은 로고 파일을 업로드하려면 **로고 아이콘**에서 **찾아보기**를 클릭합니다.

    d. **사용자 이름 특성** 텍스트 상자에서 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.

    e. **이름 표시** 텍스트 상자에 구성할 이름을 입력합니다.

    f. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 TOPdesk - Public에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **TOPdesk - Public**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **TOPdesk-Public**을 선택합니다.

    ![애플리케이션 목록의 TOPdesk-Public 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-topdesk---public-test-user"></a>TOPdesk - Public 테스트 사용자 만들기

Azure AD 사용자가 TOPdesk - Public에 로그인할 수 있도록 하려면 TOPdesk - Public에 프로비저닝되어야 합니다. TOPdesk - Public의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면

1. **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2. 위쪽 메뉴에서 **TOPdesk \> 새로 만들기 \> 지원 파일 \> 사람** 순으로 클릭합니다.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. 새로운 사용자 대화 상자에서 다음 단계를 수행합니다.
   
    ![새로운 사람](./media/topdesk-public-tutorial/ic790629.png "새로운 사람")
   
    a. 일반 탭을 클릭합니다.

    b. **성** 텍스트 상자에서 사용자의 성을 입력합니다(예: Simon).
 
    다. 계정에 대한 **사이트** 를 선택합니다.
 
    d. **저장**을 클릭합니다.

> [!NOTE]
> 다른 TOPdesk - Public 사용자 계정 생성 도구 또는 TOPdesk - Public에서 제공하는 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 TOPdesk - Public 타일을 클릭하면 SSO를 설정한 TOPdesk - Public에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
