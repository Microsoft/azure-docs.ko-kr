---
title: '자습서: CloudPassage와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 CloudPassage 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 34e36be8ee9444b5612901439aa5dca4771f2dda
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>자습서: CloudPassage와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 CloudPassage를 통합하는 방법에 대해 알아봅니다. CloudPassage를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* CloudPassage에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 CloudPassage에 자동으로 로그온(Single Sign-On)되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* CloudPassage SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* CloudPassage에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-cloudpassage-from-the-gallery"></a>갤러리에서 CloudPassage 추가

CloudPassage의 Azure AD 통합을 구성하려면 갤러리의 CloudPassage를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **CloudPassage**를 입력합니다.
1. 결과 패널에서 **CloudPassage**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>CloudPassage에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 CloudPassage에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 CloudPassage의 관련 사용자 간에 연결 관계를 설정해야 합니다.

CloudPassage에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[CloudPassage SSO 구성](#configure-cloudpassage-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[CloudPassage 테스트 사용자 만들기](#create-cloudpassage-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 CloudPassage에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **CloudPassage** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

     a. **로그인 URL** 텍스트 상자에서 `https://portal.cloudpassage.com/saml/init/accountid` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 `https://portal.cloudpassage.com/saml/consume/accountid` 패턴을 사용하여 URL을 입력합니다. CloudPassage 포털의 **Single Sign-On 설정** 섹션에서 **SSO 설치 설명서**를 클릭하여 이 특성의 값을 가져올 수 있습니다.

    ![Single Sign-on 구성](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [CloudPassage 클라이언트 지원 팀](https://www.cloudpassage.com/company/contact/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. CloudPassage 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도 CloudPassage 애플리케이션에는 아래에서 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 | 원본 특성|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | 이메일 |user.mail |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **CloudPassage 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 CloudPassage에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **CloudPassage**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-cloudpassage-sso"></a>CloudPassage SSO 구성

1. 다른 브라우저 창에서 CloudPassage 회사 사이트에 관리자 권한으로 로그온합니다.

1. 위쪽 메뉴에서 **설정**을 클릭한 다음 **사이트 관리**를 클릭합니다. 
   
    ![Single Sign-on 구성][12]

1. **인증 설정** 탭을 클릭합니다. 
   
    ![Single Sign-on 구성][13]

1. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다. 
   
    ![Single Sign-on 구성][14]

    a. **SSO(Single Sign-On) 사용(SSO 설치 설명서)** 확인란을 선택합니다.
    
    b. **Azure AD 식별자**를 **SAML 발급자 URL** 텍스트 상자에 붙여넣습니다.
  
    다. **로그인 URL**을 **SAML 엔드포인트 URL** 텍스트 상자에 붙여넣습니다.
  
    d. **로그아웃 URL**을 **로그아웃 방문 페이지** 텍스트 상자에 붙여 넣습니다.
  
    e. 다운로드한 인증서를 메모장에서 열고, 다운로드한 인증서의 내용을 클립보드에 복사한 다음, **x 509 인증서** 텍스트 상자에 붙여넣습니다.
  
    f. **저장**을 클릭합니다.

### <a name="create-cloudpassage-test-user"></a>CloudPassage 테스트 사용자 만들기

이 섹션은 CloudPassage에서 B.Simon이라는 사용자를 만들기 위한 것입니다.

**CloudPassage에서 B.Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. **CloudPassage** 회사 사이트에 관리자 권한으로 로그인합니다. 

1. 위쪽 도구 모음에서 **설정**을 클릭한 다음 **사이트 관리**를 클릭합니다. 
   
    ![CloudPassage 테스트 사용자 만들기][22] 

1. **사용자** 탭을 클릭한 다음 **새 사용자 추가**를 클릭합니다. 
   
    ![CloudPassage 테스트 사용자 만들기][23]

1. **새 사용자 추가** 섹션에서 다음 단계를 수행합니다. 
   
    ![CloudPassage 테스트 사용자 만들기][24]
    
    a. **이름** 텍스트 상자에 Britta를 입력합니다. 
  
    b. **성** 텍스트 상자에 Simon을 입력합니다.
  
    다. **사용자 이름** 텍스트 상자, **전자 메일** 텍스트 상자 및 **전자 메일 다시 입력** 텍스트 상자에 Azure AD의 Britta 사용자 이름을 입력합니다.
  
    d. **액세스 유형**으로 **Halo 포털 액세스 사용**을 선택합니다.
  
    e. **추가**를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 CloudPassage 타일을 클릭하면 SSO를 설정한 CloudPassage에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 CloudPassage 사용해보기](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

