---
title: '자습서: Tangoe Command Premium Mobile과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Tangoe Command Premium Mobile 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f410fa890df7aac3c3bf4d89468b92e69ba38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869272"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>자습서: Tangoe Command Premium Mobile과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Tangoe Command Premium Mobile을 통합하는 방법에 대해 알아봅니다.

Tangoe Command Premium Mobile을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Tangoe Command Premium Mobile에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Tangoe Command Premium Mobile에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Tangoe Command Premium Mobile과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Tangoe Command Premium Mobile Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Tangoe Command Premium Mobile 추가
1. Azure AD Single Sign-On 구성 및 테스트

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>갤러리에서 Tangoe Command Premium Mobile 추가
Tangoe Command Premium Mobile과 Azure AD 통합을 구성하려면 갤러리의 Tangoe Command Premium Mobile을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Tangoe Command Premium Mobile을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **Tangoe Command Premium Mobile**을 입력하고, 결과 패널에서 **Tangoe Command Premium Mobile**을 선택한 다음 **추가** 단추를 눌러 애플리케이션을 추가합니다.

    ![갤러리에서 Tangoe Command Premium Mobile 추가](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Tangoe Command Premium Mobile에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Tangoe Command Premium Mobile 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Tangoe Command Premium Mobile의 관련 사용자 간에 연결이 설정되어야 합니다.

Tangoe Command Premium Mobile에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Tangoe Command Premium Mobile에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Tangoe Command Premium Mobile 테스트 사용자 만들기](#create-a-tangoe-command-premium-mobile-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Tangoe Command Premium Mobile에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Tangoe Command Premium Mobile 애플리케이션에서 Single Sign-On을 구성합니다.

**Tangoe Command Premium Mobile에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Tangoe Command Premium Mobile** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![SAML 기반 로그온](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. **Tangoe Command Premium Mobile 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Tangoe Command Premium Mobile 도메인 및 URL](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Tangoe Command Premium Mobile 클라이언트 지원 팀](https://www.tangoe.com/contact-us/)에 문의하세요. 

1. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![SAML 서명 인증서 섹션](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![저장 단추](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. **Tangoe Command Premium Mobile** 섹션에서 **Tangoe Command Premium Mobile 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Tangoe Command Premium Mobile 구성 섹션](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. 애플리케이션에 SSO를 구성하려면 [Tangoe Command Premium Mobile 클라이언트 지원 팀](https://www.tangoe.com/contact-us/)에 연락하여 다음 정보를 제공하세요.

   - 다운로드한 메타데이터 파일
   - **SAML 엔터티 ID**
   - **SAML Single Sign-On 서비스 URL**
   - **로그아웃 URL**

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/tangoe-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![사용자 및 그룹 -> 모든 사용자](./media/tangoe-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![사용자 추가](./media/tangoe-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자 페이지](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Tangoe Command Premium Mobile 테스트 사용자 만들기

이 섹션에서는 Tangoe Command Premium Mobile에서 Britta Simon이라는 사용자를 만듭니다. 

Tangoe Command Premium Mobile 애플리케이션에서는 Single Sign On을 수행하기 전에 애플리케이션에서 모든 사용자를 프로비전해야 합니다. 따라서 [Tangoe Command Premium Mobile 클라이언트 지원 팀](https://www.tangoe.com/contact-us/)과 협력하여 모든 사용자를 애플리케이션에 프로비전하세요. 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Tangoe Command Premium Mobile에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Tangoe Command Premium Mobile에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Tangoe Command Premium Mobile**을 선택합니다.

    ![앱 목록의 Tangoe Command Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Tangoe Command Premium Mobile 타일을 클릭하면 Tangoe Command Premium Mobile 애플리케이션에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

