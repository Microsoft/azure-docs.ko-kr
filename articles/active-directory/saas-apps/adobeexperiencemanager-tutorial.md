---
title: '자습서: Adobe Experience Manager와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adobe Experience Manager 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 1088827ead320007c758d8e843a15328f108c035
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543026"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>자습서: Adobe Experience Manager와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Experience Manager를 통합하는 방법을 알아봅니다.
Adobe Experience Manager를 Azure AD와 통합하면 다음과 같은 혜택이 있습니다.

* Azure AD에서 Adobe Experience Manager에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Adobe Experience Manager에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Adobe Experience Manager와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Adobe Experience Manager Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Adobe Experience Manager에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* Adobe Experience Manager에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>갤러리에서 Adobe Experience Manager 추가

Adobe Experience Manager의 Azure AD 통합을 구성하려면 갤러리의 Adobe Experience Manager를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adobe Experience Manager를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Adobe Experience Manager**를 입력하고 결과 창에서 **Adobe Experience Manager**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Adobe Experience Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 [애플리케이션 이름]에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 [애플리케이션 이름]의 관련 사용자 간에 연결 관계를 설정해야 합니다.

[애플리케이션 이름]에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Adobe Experience Manager Single Sign-On 구성](#configure-adobe-experience-manager-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Adobe Experience Manager 테스트 사용자 만들기](#create-adobe-experience-manager-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Adobe Experience Manager에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

[애플리케이션 이름]에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Adobe Experience Manager** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Adobe Experience Manager 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 상자에서 AEM 서버에 대해서도 정의한 고유한 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<AEM Server Url>/saml_login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 회신 URL 값을 실제 회신 URL로 업데이트합니다. 이 값을 얻으려면 [Adobe Experience Manager 클라이언트 지원 팀](https://helpx.adobe.com/support/experience-manager.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    ![Adobe Experience Manager 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그온 URL** 텍스트 상자에서 Adobe Experience Manager 서버 URL을 입력합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **Adobe Experience Manager 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Adobe Experience Manager Single Sign-On 구성

1. 다른 브라우저 창에서 **Adobe Experience Manager** 관리자 포털을 엽니다.

2. **설정** > **보안** > **사용자**를 선택합니다.

    ![Single Sign-On 저장 단추 구성](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. **관리자** 또는 다른 관련 사용자를 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. **계정 설정** > **TrustStore 관리**를 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. **CER 파일의 인증서 추가**에서 **인증서 파일 선택**을 클릭합니다. Azure Portal에서 다운로드한 인증서 파일을 찾아서 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. 인증서는 TrustStore에 추가됩니다. 인증서의 별칭을 적어둡니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. **사용자** 페이지에서 **인증 서비스**를 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. **계정 설정** > **키 저장소 만들기/관리**를 선택합니다. 암호를 제공하여 키 저장소를 만듭니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. 관리 화면으로 돌아갑니다. **설정** > **작업** > **웹 콘솔**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    구성 페이지가 열립니다.

    ![Single Sign-On 저장 단추 구성](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. **Adobe Granite SAML 2.0 인증 처리기**를 찾습니다. **추가** 아이콘을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. 이 페이지에서 다음 작업을 수행합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. **경로** 상자에 **/** 를 입력합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **IDP URL** 상자에 입력합니다.

    다. **IDP 인증서 별칭** 상자에 TrustStore에서 추가한 **인증서 별칭** 값을 입력합니다.

    d. Azure Portal에서 구성한 고유의 **Azure ID 식별자** 값을 **보안 제공 엔터티 ID** 상자에 입력합니다.

    e. Azure Portal에서 구성한 **회신 URL** 값을 **어설션 소비자 서비스 URL** 상자에 입력합니다.

    f. 키 저장소에서 설정한 **암호**를 **키 저장소 암호** 상자에 입력합니다.

    g. 자신의 사례와 관련된 **이름 ID** 또는 다른 사용자 ID를 **사용자 특성 ID** 상자에 입력합니다.

    h. **CRX 사용자 자동 작성**을 선택합니다.

    i. Azure Portal에서 가져온 고유의 **로그아웃 URL** 값을 **로그아웃 URL** 상자에 입력합니다.

    j. **저장**을 선택합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adobe Experience Manager에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Adobe Creative Cloud**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Adobe Experience Manager**를 선택합니다.

    ![애플리케이션 목록의 Adobe Experience Manager 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager 테스트 사용자 만들기

이 섹션에서는 Adobe Experience Manager에서 Britta Simon이라는 사용자를 만듭니다. **CRX 사용자 자동 작성** 옵션을 선택한 경우 사용자는 인증을 성공한 후에 자동으로 생성됩니다.

사용자를 수동으로 만들려는 경우 [Adobe Experience Manager 지원 팀](https://helpx.adobe.com/support/experience-manager.html) 과 협력하여 Adobe Experience Manager 플랫폼에 사용자를 추가합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adobe Experience Manager 타일을 클릭하면 SSO를 설정한 Adobe Experience Manager 애플리케이션에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
