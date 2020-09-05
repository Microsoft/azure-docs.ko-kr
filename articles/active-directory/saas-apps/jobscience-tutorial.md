---
title: '자습서: Jobscience와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Jobscience 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0de47cc12c461e47d8703861a99d11e7ee72d581
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547072"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>자습서: Jobscience와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Jobscience를 통합하는 방법에 대해 알아봅니다.

Jobscience를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Jobscience에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Jobscience에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱을 통합 하는 방법에 대 한 자세한 내용은 [응용 프로그램 액세스 및 Azure Active Directory Single Sign-On](../manage-apps/what-is-single-sign-on.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

Jobscience와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Jobscience Single Sign-On이 가능하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1 개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Jobscience 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-jobscience-from-the-gallery"></a>갤러리에서 Jobscience 추가
Jobscience가 Azure AD에 통합되도록 구성하려면 갤러리에서 Jobscience를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Jobscience를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **Jobscience**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. 결과 창에서 **Jobscience**를 선택하고 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Jobscience에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 Jobscience 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Jobscience의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Jobscience에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Jobscience에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[AZURE AD Single Sign-on 구성](#configuring-azure-ad-single-sign-on)** -사용자가이 기능을 사용할 수 있도록 합니다.
1. **[AZURE ad 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** -Britta Simon를 사용 하 여 azure ad Single Sign-On를 테스트 하는 데 사용 합니다.
1. **[Jobscience 테스트 사용자 만들기](#creating-a-jobscience-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Jobscience에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-on 테스트](#testing-single-sign-on)** -구성이 작동 하는지 여부를 확인 합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Jobscience 애플리케이션에서 Single Sign-On을 구성합니다.

**Jobscience에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Jobscience** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. **Jobscience 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    **로그온 url** 텍스트 상자에 다음 패턴을 사용 하 여 URL을 입력 합니다.`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값은 [Jobscience 클라이언트 지원 팀](http://www.jobscience.com/support) 또는 앞으로 만들 SSO 프로필에서 참조하세요. 이 내용은 자습서의 뒷부분에 설명되어 있습니다. 
 
1. **SAML 서명 인증서** 섹션에서 **인증서 (Base64)** 를 클릭 한 다음 컴퓨터에 인증서 파일을 저장 합니다.

    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_general_400.png)

1. **Jobscience 구성** 섹션에서 **Jobscience 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션** 에서 **로그 아웃 URL, saml 엔터티 ID 및 Saml SINGLE sign-on 서비스 URL** 을 복사 합니다.

    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Jobscience 회사 사이트에 관리자 권한으로 로그인합니다.

1. **설정**으로 이동합니다.
   
   ![설치](./media/jobscience-tutorial/IC784358.png "설치 프로그램")

1. 왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
   
   ![내 도메인](./media/jobscience-tutorial/ic767825.png "내 도메인")

1. 도메인이 올바르게 설정 되었는지 확인 하려면 "**4 단계 사용자에 게 배포**"에 있는지 확인 하 고 "**내 도메인 설정**"을 검토 합니다.

    ![사용자에 게 배포 된 도메인](./media/jobscience-tutorial/ic784377.png "사용자에 게 배포 된 도메인")

1. Jobscience 회사 사이트에서 **보안 제어**를 클릭한 다음 **Single Sign-On 설정**을 클릭합니다.
    
    ![보안 제어](./media/jobscience-tutorial/ic784364.png "Single Sign On 설정")

1. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 설정](./media/jobscience-tutorial/ic781026.png "Single Sign-On 설정")
    
    a. **SAML 사용**을 선택합니다.

    b. **새로 만들기**를 클릭합니다.

1. **SAML Single Sign-On 설정 편집** 대화 상자에서 다음 단계를 수행합니다.
    
    ![SAML Single Sign-On 설정](./media/jobscience-tutorial/ic784365.png "SAML Single Sign-On 설정")
    
    a. **이름** 텍스트 상자에 구성할 이름을 입력합니다.

    b. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.

    다. **엔터티 ID** 텍스트 상자에 `https://salesforce-jobscience.com`을 입력합니다.

    d. **찾아보기**를 클릭하여 Azure AD 인증서를 업로드합니다.

    e. **SAML ID 유형**으로 **어설션에는 User 개체의 페더레이션 ID가 포함됨**을 선택합니다.

    f. **SAML ID 위치**로 **ID는 Subject 문의 NameIdentifier 요소에 있습니다**를 선택합니다.

    g. **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    h. **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    i. **저장**을 클릭합니다.

1. 왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
    
    ![내 도메인](./media/jobscience-tutorial/ic767825.png "내 도메인")

1. **내 도메인** 페이지의 **로그인 페이지 브랜딩** 섹션에서 **편집**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/jobscience-tutorial/ic767826.png "로그인 페이지 브랜딩")

1. **로그인 페이지 브랜딩** 페이지의 **인증 서비스** 섹션에 **SAML SSO 설정** 이름이 표시됩니다. 이름을 선택하고 **저장**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/jobscience-tutorial/ic784366.png "로그인 페이지 브랜딩")

1. SP에서 시작한 Single Sign-On 로그인 URL을 가져오려면 **보안 제어** 메뉴 섹션에서 **Single Sign-On 설정**을 클릭합니다.

    ![보안 제어](./media/jobscience-tutorial/ic784368.png "Single Sign On 설정")
    
    위 단계에서 만든 SSO 프로필을 클릭합니다. 이 페이지에서는 회사의 Single Sign-On URL을 보여줍니다(예: `https://companyname.my.salesforce.com?so=companyid`.    

> [!TIP]
> 이제 앱을 설정 하는 동안 [Azure Portal](https://portal.azure.com)내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함 된 설명서 기능에 대 한 자세한 내용은 [AZURE AD embedded 설명서]( https://go.microsoft.com/fwlink/?linkid=845985) 를 참조 하세요.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭 합니다.

    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience 테스트 사용자 만들기

Azure AD 사용자가 Jobscience에 로그인할 수 있도록 하려면 Jobscience로 프로비전되어야 합니다. Jobscience의 경우 프로비저닝은 수동 작업입니다.

>[!NOTE]
>다른 Jobscience 사용자 계정 생성 도구 또는 Jobscience가 제공한 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전할 수 있습니다.
>  
        
**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. **Jobscience** 회사 사이트에 관리자로 로그인합니다.

1. 설정으로 이동합니다.
   
   ![설치](./media/jobscience-tutorial/ic784358.png "설치 프로그램")
1. **사용자 관리 \> 사용자**로 이동합니다.
   
   ![사용자](./media/jobscience-tutorial/ic784369.png "사용자")
1. **새 사용자**를 클릭합니다.
   
   ![모든 사용자](./media/jobscience-tutorial/ic784370.png "모든 사용자")
1. **사용자 편집** 대화 상자에서 다음 단계를 수행합니다.
   
   ![사용자 편집](./media/jobscience-tutorial/ic784371.png "사용자 편집")
   
   a. **First Name**(이름) 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.
   
   b. **Last Name**(성) 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.
   
   다. **Alias**(별칭) 텍스트 상자에 사용자의 별칭(예: brittas)을 입력합니다.

   d. **전자 메일** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

   e. **User Name**(사용자 이름) 텍스트 상자에 사용자의 사용자 이름(예: Brittasimon@contoso.com)을 입력합니다.

   f. **Nick Name**(애칭) 텍스트 상자에 사용자의 애칭(예: Simon)을 입력합니다.

   g. **저장**을 클릭합니다.

    
> [!NOTE]
> Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Jobscience에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Jobscience에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Jobscience**를 선택합니다.

    ![Single Sign-on 구성](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Jobscience 타일을 클릭하면 Jobscience 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

