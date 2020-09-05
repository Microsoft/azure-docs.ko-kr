---
title: '자습서: Velpic SAML과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 Velpic SAML 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 85dfa6b57171ce68c3d962bac27016434d587080
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532656"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>자습서: Velpic SAML과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Velpic SAML을 연결하는 방법에 대해 알아봅니다. Vtiger SAML을 Azure AD와 연결하면 다음을 수행할 수 있습니다.

* Velpic SAML에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Velpic SAML에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Velpic SAML SSO(Single Sign-On)를 사용하도록 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Velpic SAML은 **SP** 시작 SSO를 지원합니다.

## <a name="adding-velpic-saml-from-the-gallery"></a>갤러리에서 Velpic SAML 추가

Velpic SAML의 Azure AD 통합을 구성하려면 갤러리의 Velpic SAML을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Velpic SAML**을 입력합니다.
1. 결과 패널에서 **Velpic SAML**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Velpic SAML에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Velpic SAML에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Velpic SAML의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Velpic SAML에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Velpic SAML SSO 구성](#configure-velpic-saml-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Velpic SAML 테스트 사용자 만들기](#create-velpic-saml-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Velpic SAML에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Velpic SAML** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<sub-domain>.velpicsaml.net` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://auth.velpic.com/saml/v2/<entity-id>/login` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > Velpic SAML 쪽에서 SSO 플러그 인을 구성한 경우 로그온 URL은 Velpic SAML 팀에서 제공하고 식별자 값을 사용할 수 있습니다. Velpic SAML 애플리케이션 페이지에서 해당 값을 복사하고 여기에 붙여넣어야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Velpic SAML 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Velpic SAML에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Velpic SAML**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-velpic-saml-sso"></a>Velpic SAML SSO 구성

1. Velpic SAML 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Velpic SAML 설정**을 클릭하면 Velpic SAML 애플리케이션으로 이동됩니다. 여기에서 관리자 자격 증명을 입력하여 Velpic SAML에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-8단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Velpic SAML을 수동으로 설정하려면 새 웹 브라우저 창을 열고 Velpic SAML 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. **관리** 탭을 클릭하고 **플러그 인** 단추를 클릭해야 하는 **통합** 섹션으로 이동하여 로그인을 위한 새 플러그 인을 만듭니다.

    ![플러그 인](./media/velpicsaml-tutorial/velpic_1.png)

5. **‘플로그 인 추가’** 단추를 클릭합니다.
    
    ![플러그 인](./media/velpicsaml-tutorial/velpic_2.png)

6. 플러그 인 추가 페이지에서 **SAML** 타일을 클릭합니다.
    
    ![플러그 인](./media/velpicsaml-tutorial/velpic_3.png)

7. 새 SAML 플러그 인의 이름을 입력하고 **'추가'** 단추를 클릭합니다.

    ![플러그 인](./media/velpicsaml-tutorial/velpic_4.png)

8. 세부 정보를 다음과 같이 입력합니다.

    ![플러그 인](./media/velpicsaml-tutorial/velpic_5.png)

    a. **이름** 텍스트 상자에 SAML 플러그 인의 이름을 입력합니다.

    b. **발급자 URL** 텍스트 상자에 Azure Portal의 **로그온 구성** 창에서 복사한 **Azure AD ID**를 붙여넣습니다.

    다. **공급자 메타데이터 구성**에 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드합니다.

    d. **'새 사용자 자동 만들기'** 확인란을 활성화하여 SAML JIT(Just-In-Time) 프로비전을 활성화하도록 선택할 수도 있습니다. 사용자자 Velpic에 없는 경우 이 플래그는 활성화되지 않으며 Azure에서 로그인에 실패합니다. 플래그가 활성화된 경우 사용자는 로그인 시 자동으로 Velpic으로 프로비전됩니다. 

    e. 텍스트 상자에서 **Single Sign-On URL**을 복사하고 Azure Portal에 붙여 넣습니다.
    
    f. **저장**을 클릭합니다.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML 테스트 사용자 만들기

애플리케이션은 JIT(Just-In-Time) 사용자 프로비전을 지원하므로 이 단계는 일반적으로 필요하지 않습니다. 자동 사용자 프로비전이 활성화되지 않은 경우 수동 사용자 만들기는 아래에서 설명한 대로 실행될 수 있습니다.

Velpic SAML 회사 사이트에 관리자로 로그인하고 다음 단계를 수행합니다.
    
1. 관리 탭을 클릭하고 사용자 섹션으로 이동한 다음 새로 만들기 단추를 클릭하여 사용자를 추가합니다.

    ![사용자 추가](./media/velpicsaml-tutorial/velpic_7.png)

2. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![사용자](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. **First Name** 텍스트 상자에 B의 이름을 입력합니다.

    b. **성** 텍스트 상자에 성 Simon을 입력합니다.

    다. **사용자 이름** 텍스트 상자에 B.Simon의 사용자 이름을 입력합니다.

    d. **이메일** 텍스트 상자에 B.Simon@contoso.com 계정의 이메일 주소를 입력합니다.

    e. 나머지 정보는 선택 사항이며 필요한 경우 채울 수 있습니다.
    
    f. **저장**을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

1. 액세스 패널에서 Velpic SAML 타일을 클릭할 때 Velpic SAML 애플리케이션의 로그인 페이지를 가져와야 합니다. 로그인 페이지에 **'Azure AD를 사용하여 로그인'** 단추가 표시됩니다.

    ![플러그 인](./media/velpicsaml-tutorial/velpic_6.png)

1. **'Azure AD를 사용하여 로그인'** 단추를 클릭하여 Azure AD 계정을 사용하여 Velpic에 로그인합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD를 사용하여 Velpic SAML 체험](https://aad.portal.azure.com/)

