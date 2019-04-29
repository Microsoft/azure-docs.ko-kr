---
title: '자습서: Veracode와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Veracode 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e80a2e554d31ad85ddb1111f84a96e1814c3969f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638823"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>자습서: Veracode와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Veracode를 통합하는 방법에 대해 알아봅니다.

Veracode를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Veracode에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Veracode에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Veracode와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Veracode Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Veracode 추가
1. Azure AD Single Sign-On 구성 및 테스트

## <a name="add-veracode-from-the-gallery"></a>갤러리에서 Veracode 추가
Veracode의 Azure AD 통합을 구성하려면 갤러리의 Veracode를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Veracode를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **Veracode**를 입력하고 결과 패널에서 **Veracode**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Veracode](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Veracode에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Veracode 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Veracode의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Veracode에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Veracode에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Veracode 테스트 사용자 만들기](#create-a-veracode-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Veracode에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Veracode 애플리케이션에서 Single Sign-On을 구성합니다.

**Veracode에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Veracode** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. **Veracode 도메인 및 URL** 섹션에서 앱이 Azure와 이미 사전 통합되었으므로 사용자는 아무 단계도 수행할 필요가 없습니다. 

    ![Configure Single Sign-On](./media/veracode-tutorial/tutorial_veracode_url.png)

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. 이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Veracode에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

    Veracode 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 할당 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![특성](./media/veracode-tutorial/tutorial_veracode_attr.png "특성")

1. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | 이메일 |User.mail |
    
    a. 위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    
    ![특성](./media/veracode-tutorial/tutorial_veracode_addattr.png "특성")
    
    ![특성](./media/veracode-tutorial/tutorial_veracode_addattr1.png "특성")
    
    b. **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **특성 값** 텍스트 상자에서 해당 행에 표시된 특성 값을 선택합니다.
    
    d. **Ok**를 클릭합니다.

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/veracode-tutorial/tutorial_general_400.png)

1. **Veracode 구성** 섹션에서 **Veracode 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID**를 복사합니다.

    ![Veracode 구성](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. 다른 웹 브라우저 창에서 Veracode 회사 사이트에 관리자 권한으로 로그인합니다.

1. 위쪽 메뉴에서 **설정**을 클릭한 다음 **관리자**를 클릭합니다.
   
    ![관리](./media/veracode-tutorial/ic802911.png "관리")

1. **SAML** 탭을 클릭합니다.

1. **조직 SAML 설정** 섹션에서 다음 단계를 수행합니다.
   
    ![관리](./media/veracode-tutorial/ic802912.png "관리")
   
    a.  **발급자** 텍스트 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.
    
    b. Azure Portal에서 다운로드한 인증서를 업로드하려면 **파일 선택**을 클릭합니다.
   
    다. **자체 등록 사용**을 선택합니다.

1. **자체 등록 설정** 섹션에서 다음 단계를 수행하고 **저장**을 클릭합니다.
   
    ![관리](./media/veracode-tutorial/ic802913.png "관리")
   
    a. **새 사용자 활성화**에 대해 **활성화 필요 없음**을 선택합니다.
   
    b. **사용자 데이터 업데이트**에 대해 **기본 설정 Veracode 사용자 데이터**를 선택합니다.
   
    다. **SAML 특성 세부 정보**에 대해 다음을 선택합니다.
      * **사용자 역할**
      * **정책 관리자**
      * **검토자**
      * **보안 팀장**
      * **경영자**
      * **제출자**
      * **작성자**
      * **모든 검색 형식**
      * **팀 멤버 자격**
      * **기본 팀**

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/veracode-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/veracode-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/veracode-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/veracode-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-veracode-test-user"></a>Veracode 테스트 사용자 만들기
Azure AD 사용자가 Veracode에 로그인할 수 있도록 하려면 Veracode로 프로비전되어야 합니다. Veracode의 경우 프로비전은 자동 작업입니다. 작업 항목이 없습니다. 필요한 경우 첫 번째 Single Sign-On 시도에서 사용자가 자동으로 생성될 수 있습니다.

> [!NOTE]
> 다른 Veracode 사용자 계정 생성 도구 또는 Veracode가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Veracode에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Veracode에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Veracode**를 선택합니다.

    ![애플리케이션 목록의 Veracode 연결](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Veracode 타일을 클릭하면 Veracode 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

