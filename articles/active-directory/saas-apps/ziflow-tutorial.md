---
title: '자습서: Azure Active Directory와 Ziflow 통합| Microsoft Docs'
description: Azure Active Directory와 Ziflow 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175e678365016bafd3d18f590a5434c32ac9fadd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60797958"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>자습서: Ziflow와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Ziflow를 통합하는 방법에 대해 알아봅니다.

Ziflow를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Ziflow에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Ziflow에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Ziflow와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Ziflow Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Ziflow 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-ziflow-from-the-gallery"></a>갤러리에서 Ziflow 추가
Ziflow의 Azure AD 통합을 구성하려면 갤러리의 Ziflow를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Ziflow를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Ziflow**를 입력하고 결과 패널에서 **Ziflow**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Ziflow](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Ziflow에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Ziflow 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Ziflow의 관련 사용자 간에 연결이 형성되어야 합니다.

Ziflow에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Ziflow 테스트 사용자 만들기](#create-a-ziflow-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Ziflow에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Ziflow 애플리케이션에서 Single Sign-On을 구성합니다.

**Ziflow에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Ziflow** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. **Ziflow 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Ziflow 도메인 및 URL Single Sign-On 정보](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. **식별자** 텍스트 상자에서 `urn:auth0:ziflow-production:<UniqueID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 위의 값은 실제가 아닙니다. 식별자 및 로그온 URL의 고유한 ID 값을 자습서 뒷부분에 설명된 실제 값으로 업데이트하게 됩니다.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/ziflow-tutorial/tutorial_general_400.png)

6. **Ziflow 구성** 섹션에서 **Ziflow 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Ziflow 구성](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. 다른 웹 브라우저 창에서 Ziflow에 보안 관리자로 로그인합니다.

8. 오른쪽 위 모서리에서 Avatar를 클릭한 다음 **계정 관리**를 클릭합니다.

    ![Ziflow 구성 관리](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. 왼쪽 위에서 **Single Sign On**을 클릭합니다.

    ![Ziflow 구성 기호](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![Ziflow 구성 단일](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. **유형**으로 **SAML2.0**을 선택합니다.

    b. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 다운로드한 base 64 인코딩 인증서를 **X509 서명 인증서**에 업로드합니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. **식별자 공급자에 대한 구성 설정** 섹션에서 강조 표시된 고유 ID 값을 복사하고, 식별자 및 로그온 URL과 함께 Azure Portal의 **Ziflow 도메인 및 URL 섹션**에 추가합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/ziflow-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/ziflow-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/ziflow-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/ziflow-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
  
### <a name="create-a-ziflow-test-user"></a>Ziflow 테스트 사용자 만들기

Azure AD 사용자가 Ziflow에 로그인할 수 있도록 하려면 Ziflow로 프로비전되어야 합니다. Ziflow의 경우, 수동으로 프로비전합니다.

사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. 보안 관리자 권한으로 Ziflow에 로그인합니다.

2. 맨 위의 **사람**으로 이동합니다.

    ![Ziflow 구성 사람](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. **추가**를 클릭한 후 **사용자 추가**를 클릭합니다.

    ![Ziflow 구성 사용자 추가](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. **사용자 추가** 팝업에서 다음 단계를 수행합니다.

    ![Ziflow 구성 사용자 추가](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. **전자 메일** 텍스트 상자에 사용자의 전자 메일(예: brittasimon@contoso.com)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. Ziflow 역할을 선택합니다.

    e. **사용자 1명 추가**를 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Ziflow에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Ziflow에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

2. 애플리케이션 목록에서 **Ziflow**를 선택합니다.

    ![애플리케이션 목록의 Ziflow 링크](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Ziflow 타일을 클릭하면 Ziflow 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

