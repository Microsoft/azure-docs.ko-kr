---
title: '자습서: Supermood와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Supermood 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639827"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>자습서: Supermood와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Supermood를 통합하는 방법에 대해 알아봅니다.

Supermood를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Supermood에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Supermood에 자동으로 로그인(Single Sign-on)하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Supermood와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Supermood Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Supermood 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-supermood-from-the-gallery"></a>갤러리에서 Supermood 추가
Supermood의 Azure AD 통합을 구성하려면 갤러리의 Supermood를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Supermood를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **Supermood**를 입력하고 결과 패널에서 **Supermood**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Supermood](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Supermood에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Supermood 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Supermood의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Supermood에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Supermood 테스트 사용자 만들기](#create-a-supermood-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Supermood에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Supermoodl 애플리케이션에서 Single Sign-On을 구성합니다.

**Supermood에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Supermood** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. **Supermood 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Supermood 도메인 및 URL Single Sign-On 정보](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. **고급 URL 설정 표시**를 선택합니다.

    b. **IDP** 시작 모드로 애플리케이션을 구성하려는 경우 **릴레이 상태** 텍스트 상자에 다음 URL을 입력합니다. `https://supermood.co/auth/sso/saml20`

    다. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 **로그온 URL** 텍스트 상자에 다음 URL을 입력합니다. `https://supermood.co/app/#!/loginv2`

1. Supermood 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ---------------| --------------- |    
    | firstname | user.givenname |
    | Lastname | user.surname |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스**를 비워 둡니다.
    
    d. **확인**을 클릭합니다.

1. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/supermood-tutorial/tutorial_general_400.png)

1. 보안 관리자 권한으로 Supermood.co 관리 패널로 이동합니다.

1. **내 계정**(왼쪽 아래)을 클릭하고 **SSO(Single Sign On)** 을 클릭합니다.

    ![인증서 단일](./media/supermood-tutorial/tutorial_supermood_single.png)
1. **SAML 2.0 구성**에서 **이메일 도메인에 대한 SAML 2.0 구성 추가**를 클릭합니다.

    ![인증서 추가](./media/supermood-tutorial/tutorial_supermood_add.png)

1. **이메일 도메인에 대한 SAML 2.0 구성 추가** 섹션에서 다음 단계를 수행합니다.

    ![인증서 SAML](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. **이 ID 공급자에 대한 이메일 도메인** 텍스트 상자에서 사용자의 도메인을 입력합니다.

    b. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL 사용** 텍스트 상자에 붙여넣습니다.

    다. **추가**를 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/supermood-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/supermood-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/supermood-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/supermood-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-supermood-test-user"></a>Supermood 테스트 사용자 만들기

이 섹션에서는 Supermood에서 Britta Simon이라는 사용자를 만듭니다. Supermood는 Just-In-Time 프로비전을 지원합니다. Supermood에서 구성하는 동안 추가된 도메인에 이메일이 속해 있는 사용자의 경우 기본적으로 사용하도록 설정되어 있습니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 Supermood에 액세스하는 동안 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우  [Supermood 지원 팀](mailto:hello@supermood.fr)에 문의하세요.


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Supermood에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Supermood에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Supermood**를 선택합니다.

    ![애플리케이션 목록의 Supermood 링크](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Supermood 타일을 클릭하면 Supermood 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

