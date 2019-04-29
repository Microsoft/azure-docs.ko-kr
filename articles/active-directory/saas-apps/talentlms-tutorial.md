---
title: '자습서: TalentLMS와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 TalentLMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa78ec2b5623dfd010a8fe5709916a47e221a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732333"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>자습서: TalentLMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TalentLMS를 통합하는 방법에 대해 알아봅니다.

TalentLMS를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- TalentLMS에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 TalentLMS에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

TalentLMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- TalentLMS Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 TalentLMS 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-talentlms-from-the-gallery"></a>갤러리에서 TalentLMS 추가
TalentLMS가 Azure AD에 통합되도록 구성하려면 갤러리의 TalentLMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TalentLMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **TalentLMS**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. 결과 패널에서 **TalentLMS**를 선택하고 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 TalentLMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 TalentLMS 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 TalentLMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TalentLMS에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

TalentLMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[TalentLMS 테스트 사용자 만들기](#creating-a-talentlms-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 TalentLMS에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 TalentLMS 애플리케이션에서 Single Sign-On을 구성합니다.

**TalentLMS에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **TalentLMS** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. **TalentLMS 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<tenant-name>.TalentLMSapp.com`

    b. **식별자** 텍스트 상자에서 `http://<tenant-name>.talentlms.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [TalentLMS 클라이언트 지원 팀](https://www.talentlms.com/contact)에 문의하세요. 
 
1. **SAML 서명 인증서** 섹션에서 인증서의 **지문** 값을 복사합니다.

    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_general_400.png)

1. **TalentLMS 구성** 섹션에서 **TalentLMS 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. 다른 웹 브라우저 창에서 TalentLMS 회사 사이트에 관리자로 로그인합니다.

1. **계정 및 설정** 섹션에서 **사용자** 탭을 클릭합니다.
   
    ![계정 & 설정](./media/talentlms-tutorial/IC777296.png "계정 & 설정")

1. **SSO(Single Sign-On)** 를 클릭합니다.

1. Single Sign-On 섹션에서 다음 단계를 수행 합니다.
   
    ![Single Sign-On](./media/talentlms-tutorial/IC777297.png "Single Sign-On")   

    a. **SSO 통합 형식** 목록에서 **SAML 2.0**을 선택합니다.

    b. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **ID 공급자(IDP)** 텍스트 상자에 붙여넣습니다.
 
    다. Azure Portal의 **지문** 값을 **Certificate Fingerprint**(인증서 지문) 텍스트 상자에 붙여넣습니다.    

    d.  Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **Remote sign-in URL**(원격 로그인 URL) 텍스트 상자에 붙여넣습니다.
 
    e. Azure Portal에서 복사한 **로그아웃 URL** 값을 **Remote sign-out URL**(원격 로그아웃 URL) 텍스트 상자에 붙여넣습니다.

    f. 다음을 입력합니다. 

    * **TargetedID**(대상 ID) 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.
     
    * **이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.
    
    * **성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`을 입력합니다.
    
    * **메일** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.
    
1. **저장**을 클릭합니다.
 
> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-talentlms-test-user"></a>TalentLMS 테스트 사용자 만들기

Azure AD 사용자가 TalentLMS에 로그인할 수 있도록 하려면 TalentLMS로 프로비전되어야 합니다. TalentLMS의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 자신의 **TalentLMS** 테넌트에 로그인합니다.

1. **사용자**를 클릭한 후 **사용자 추가**를 클릭합니다.

1. **사용자 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![사용자 추가](./media/talentlms-tutorial/IC777299.png "사용자 추가")  

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.
 
    다. **이메일 주소** 텍스트 상자에 사용자의 메일 주소(예: **brittasimon\@contoso.com**)를 입력합니다.

    d. **사용자 추가**를 클릭합니다.

>[!NOTE]
>다른 TalentLMS 사용자 계정 생성 도구 또는 TalentLMS가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 TalentLMS에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 TalentLMS에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **TalentLMS**를 선택합니다.

    ![Configure Single Sign-On](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 TalentLMS 타일을 클릭하면 TalentLMS 애플리케이션에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png

