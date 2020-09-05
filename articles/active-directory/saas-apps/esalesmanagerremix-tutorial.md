---
title: '자습서: Azure Active Directory와 E Sales Manager Remix 통합 | Microsoft Docs'
description: Azure Active Directory 및 E Sales Manager Remix 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: dd5a200d1c17f0417b2ee88f7f905ee6de3cfe62
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555392"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory와 E Sales Manager Remix 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 E Sales Manager Remix를 통합하는 방법을 알아봅니다.

Azure AD를 E Sales Manager Remix와 통합하면 다음과 같은 혜택이 있습니다.

- E Sales Manager Remix에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 E Sales Manager Remix에 자동으로 로그인(Single Sign-On 또는 SSO)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

E Sales Manager Remix와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- E Sales Manager Remix SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트 하는 경우 프로덕션 환경을 사용 *하지* 않는 것이 좋습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 E Sales Manager Remix추가
* Azure AD Single Sign-on 구성 및 테스트

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>갤러리에서 E Sales Manager Remix 추가
E Sales Manager Remix와의 Azure AD 통합을 구성하려면 다음을 수행하여 갤러리의 E Sales Manager Remix를 관리되는 SaaS 앱 목록에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택 합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    !["엔터프라이즈 애플리케이션" 창][2]
    
1. 새 애플리케이션을 추가하려면 창 위쪽에 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **E Sales Manager Remix**를 입력하고 결과 목록에서 **E Sales Manager Remix**를 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 E Sales Manager Remix에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 E Sales Manager Remix 사용자와 이 사용자에 대응하는 Azure AD의 사용자에 대해 알고 있어야 합니다. 즉, Azure AD 사용자와 E Sales Manager Remix의 동일한 사용자 간에 연결 관계가 형성되어야 합니다.

E Sales Manager Remix에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 5개 섹션에서 구성 요소를 완료해야 합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

다음을 수행하여 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 E Sales Manager Remix 애플리케이션에서 Single Sign-On을 구성합니다.

1. Azure Portal의 **E Sales Manager Remix** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![“Single Sign-On” 링크][4]

1. **Single Sign-On** 창의 **Single Sign-On 모드** 상자에서 **SAML 기반 로그온**을 선택합니다.
 
    ![“Single Sign-On” 창](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. **E Sales Manager Remix 도메인 및 URL**에서 다음을 수행합니다.

    ![E Sales Manager Remix 도메인 및 URL Single Sign-On 정보](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. **로그온 URL** 상자에 다음 형식으로 URL을 입력 합니다. *https:// \<Server-Based-URL> / \<sub-domain> /esales-pc*

    b. **식별자** 상자에 다음 형식으로 URL을 입력 합니다. *https:// \<Server-Based-URL> / \<sub-domain> / *.

    다. 이 자습서의 뒷부분에서 사용할 수 있게 **식별자** 값을 적어둡니다.
    
    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트합니다. 값을 얻으려면 [E Sales Manager Remix 고객 지원 팀](mailto:esupport@softbrain.co.jp)에 문의하세요.

1. **SAML 서명 인증서** 아래에서 **인증서(Base64)** 를 선택한 후 사용자의 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서(Base64) 다운로드 링크](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. **기타 모든 사용자 특성 보기 및 편집** 확인란을 선택하고 **emailaddress** 특성을 선택합니다.
    
    ![사용자 특성 창](./media/esalesmanagerremix-tutorial/configure1.png)

    **특성 편집** 창이 열립니다.

1. **Namespace** 및 **Name** 값을 복사합니다. 패턴에서 값을 생성 *\<Namespace>/\<Name>* 하 고 나중에이 자습서에서 사용할 수 있도록 저장 합니다.

    ![특성 편집 창](./media/esalesmanagerremix-tutorial/configure2.png)

1. **E Sales Manager Remix 구성**에서 **E Sales Manager Remix 구성**을 선택합니다.

    ![E Sales Manager Remix 구성](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **로그온 구성** 창이 열립니다.

1. **빠른 참조** 섹션에서 로그아웃 URL 및 SAML Single Sign-On 서비스 URL을 복사합니다.

1. **저장**을 선택합니다.

    ![저장 단추](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. E Sales Manager Remix 애플리케이션에 관리자로 로그인합니다.

1. 오른쪽 상단에서 **관리자 메뉴**를 선택합니다.

    !["관리자 메뉴" 명령](./media/esalesmanagerremix-tutorial/configure4.png)

1. 왼쪽 창에서 **시스템 설정**  >  **외부 시스템**에 대 한 협조를 선택 합니다.

    ![“시스템 설정” 및 “외부 시스템 협력” 링크](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. **외부 시스템 협력** 창에서 **SAML**을 선택합니다.

    !["외부 시스템 협력" 창](./media/esalesmanagerremix-tutorial/configure6.png)

1. **SAML 인증 설정**에서 다음을 수행합니다.

    !["SAML 인증 설정" 섹션](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. **PC 버전** 확인란을 선택합니다.
    
    b. **협업 항목** 섹션의 드롭다운 목록에서 **이메일**을 선택합니다.

    다. **협업 항목** 상자에 이전에 Azure Portal에서 복사한 클레임 값을 붙여넣습니다(즉, **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`**).

    d. **발급자(엔터티 ID)** 상자에 이전에 Azure Portal의 **E Sales Manager Remix 도메인 및 URL** 섹션에서 복사한 식별자 값을 붙여넣습니다.

    e. Azure Portal에서 다운로드한 인증서를 업로드하려면 **파일 선택**을 선택합니다.

    f. **ID 공급자 로그인 URL** 상자에 이전에 Azure Portal에서 복사한 SAML Single Sign-On 서비스 URL을 붙여넣습니다.

    g. **ID 공급자 로그아웃 URL** 상자에 이전에 Azure Portal에서 복사한 로그아웃 URL 값을 붙여넣습니다.

    h. **설정 완료**를 선택합니다.

> [!TIP]
> 앱을 설정할 때 [Azure Portal](https://portal.azure.com)에서 이전 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory**  >  **엔터프라이즈 응용 프로그램** 섹션에서 앱을 추가한 후에 **Single sign-on** 탭을 선택 하 고 맨 아래에 있는 **구성** 섹션에서 포함 된 설명서에 액세스 합니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![Azure AD 테스트 사용자 만들기][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 링크](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. 현재 사용자 목록을 표시 하려면 **사용자 및 그룹**  >  **모든 사용자**를 선택 합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. **모든 사용자** 창 상단에서 **추가**를 선택합니다.

    ![추가 단추](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **사용자** 창이 열립니다.

1. **사용자** 창에서 다음을 수행합니다.

    ![사용자 창](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시 되는 값을 확인 합니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix 테스트 사용자 만들기

1. E Sales Manager Remix 애플리케이션에 관리자로 로그온합니다.

1. 오른쪽 상단 메뉴에서 **관리자 메뉴**를 선택합니다.

    ![E Sales Manager Remix 구성](./media/esalesmanagerremix-tutorial/configure4.png)

1. **회사의**  >  **부서 및 직원의 설정 유지 관리**를 선택한 다음 **등록 된 직원**을 선택 합니다.

    !["등록된 직원" 탭](./media/esalesmanagerremix-tutorial/user1.png)

1. **새 직원 등록** 섹션에서 다음을 수행합니다.
    
    !["새 직원 등록" 섹션](./media/esalesmanagerremix-tutorial/user2.png)

    a. **직원 이름** 상자에 사용자의 이름을 입력합니다(예: **Britta**).

    b. 나머지 필수 필드를 완료합니다.
    
    다. SAML을 사용하도록 설정하면 관리자는 로그인 페이지에서 로그인할 수 없습니다. **관리자 로그인** 확인란을 선택하여 관리자에게 사용자에 대한 로그인 권한을 부여합니다.

    d. **등록**을 선택합니다.

1. 나중에 관리자 권한으로 로그인하려면 관리자 권한이 있는 사용자로 로그인하고 오른쪽 상단에 있는 **관리자 메뉴**를 선택합니다.

    !["관리자 메뉴" 명령](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 사용자 Britta Simon에게 E Sales Manager Remix에 대한 액세스 권한을 부여합니다. 이렇게 하려면 다음을 수행합니다. 

![사용자 역할 할당][200] 

1. Azure Portal에서 **애플리케이션** 보기를 열고 **디렉터리** 보기로 이동한 후 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    !["엔터프라이즈 애플리케이션" 및 "모든 애플리케이션" 링크][201] 

1. **애플리케이션** 목록에서 **E Sales Manager Remix**를 선택합니다.

    ![E Sales Manager Remix 링크](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. 왼쪽 창에서 **사용자 및 그룹**을 선택 합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가**를 선택한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 창의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

1. **선택** 단추를 누릅니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 E Sales Manager Remix 타일을 선택하면 E Sales Manager Remix 애플리케이션에 자동으로 로그인됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

