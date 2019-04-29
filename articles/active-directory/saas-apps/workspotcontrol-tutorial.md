---
title: '자습서: Workspot Control과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Workspot Control 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098677"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>자습서: Workspot Control과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workspot Control을 통합하는 방법에 대해 알아봅니다.

Workspot Control을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Workspot Control에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Workspot Control에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Workspot Control와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Workspot Control Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Workspot Control 컨트롤 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-workspot-control-from-the-gallery"></a>갤러리에서 Workspot Control 컨트롤 추가
Workspot Control의 Azure AD 통합을 구성하려면 갤러리의 Workspot Control을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workspot Control을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Image](./media/workspotcontrol-tutorial/selectazuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![Image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![Image](./media/workspotcontrol-tutorial/a_new_app.png)

4. 검색 상자에 **Workspot Control**을 입력하고 결과 패널에서 **Workspot Control**을 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Workspot Control에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Workspot Control 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Workspot Control의 관련 사용자 간에 연결이 형성되어야 합니다.

Workspot Control에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Workspot Control 테스트 사용자 만들기](#create-a-workspot-control-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Workspot Control에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Workspot Control 응용 프로그램에서 Single Sign-On을 구성합니다.

**Workspot Control에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **Workspot Control** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. **식별자** 텍스트 상자에서 `https://<INSTANCENAME>-saml.workspot.com/saml/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<INSTANCENAME>-saml.workspot.com/saml/assertion` 패턴을 사용하여 URL을 입력합니다.

    다. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

     ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    **로그인 URL** 텍스트 상자에서 `https://<INSTANCENAME>-saml.workspot.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Workspot Control 클라이언트 지원 팀](mailto:support@workspot.com)에 문의하세요. 

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Base64)** 를 컴퓨터에 다운로드하고 저장합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. **Workspot Control 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    이 URL은 다음과 같을 수 있습니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. 다른 웹 브라우저 창에서 Workspot Control에 보안 관리자로 로그인합니다.

8. 페이지의 위쪽에 있는 도구 모음에서  **설치**를 클릭한 다음,  **SAML**로 이동합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. **Security Assertion Markup Language 구성** 페이지에서 다음 단계를 수행합니다.
 
    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. Azure Portal에서 복사한 **Azure Ad 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.   

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 서비스 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 서비스 URL** 텍스트 상자에 붙여넣습니다. 

    d. **파일 업데이트** 단추를 클릭하여 Azure Portal에서 다운로드한 base-64 인코딩 인증서를 X.509 인증서에 업로드합니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-workspot-control-test-user"></a>Workspot Control 테스트 사용자 만들기

Azure AD 사용자가 Workspot Control에 로그인할 수 있도록 하려면 Workspot Control로 프로비전되어야 합니다. Workspot Control의 경우 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 보안 관리자 권한으로 Workspot Control에 로그인합니다.

2. 페이지의 위쪽에 있는 도구 모음에서  **사용자**를 클릭한 다음,  **사용자 추가**로 이동합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. **새 사용자 추가** 페이지에서 다음 단계를 수행합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **simon**)을 입력합니다.

    다. **이메일** 텍스트 상자에 사용자의 이메일 주소(예: **Brittasimon\@contoso.com**)를 입력합니다.

    d. **역할** 드롭다운에서 적절한 사용자 역할을 선택합니다.

    e. **그룹** 드롭다운에서 적절한 사용자 그룹을 선택합니다.

    f. **사용자 추가**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Workspot Control에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. 응용 프로그램 목록에서 **Workspot Control**을 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![Image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Workspot Control 타일을 클릭하면 Workspot Control 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
