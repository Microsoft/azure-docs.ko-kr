---
title: '자습서: Dropbox Business와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Dropbox Business 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 3c1ff9de1057a5ef551ad941965f64907e4243e5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555742"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>자습서: Dropbox Business와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Dropbox Business를 통합하는 방법에 대해 알아봅니다. Dropbox Business를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Dropbox Business에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Dropbox Business에 자동으로 로그온되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 다운로드할 수 있습니다.
* Dropbox Business SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

* 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Dropbox Business는 **SP** 시작 SSO를 지원합니다.

* Dropbox Business는 [자동 사용자 프로비저닝 및 프로비저닝 해제](dropboxforbusiness-tutorial.md)를 지원합니다.
* Dropbox를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>갤러리에서 Dropbox Business 추가

Dropbox Business가 Azure AD에 통합되도록 구성하려면 갤러리에서 Dropbox Business를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Dropbox Business**를 입력합니다.
1. 결과 패널에서 **Dropbox Business**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**Britta Simon**이라는 테스트 사용자를 사용하여 Dropbox Business에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Dropbox Business의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Dropbox Business에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.    
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Dropbox Business SSO 구성](#configure-dropbox-business-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Dropbox Business 테스트 사용자 만들기](#create-dropbox-business-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 Dropbox Business에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Dropbox Business** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://www.dropbox.com/sso/<id>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 `Dropbox` 값을 입력합니다.

    > [!NOTE]
    > 위의 로그온 URL 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL로 값을 업데이트하게 됩니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Dropbox Business 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL


### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `BrittaSimon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Dropbox Business에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Dropbox Business**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-dropbox-business-sso"></a>Dropbox Business SSO 구성

1. Dropbox Business 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장이 추가된 후 **Dropbox Business 설정**을 클릭하면 Dropbox Business 애플리케이션으로 이동합니다. 여기서 관리자 자격 증명을 제공하여 Dropbox Business에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-8단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Dropbox Business를 수동으로 설정하려면 새 웹 브라우저 창을 열고, Dropbox Business 테넌트로 이동하고, Dropbox Business 테넌트에 로그온합니다. 그러면 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/ic769509.png "Single Sign-On 구성")

4. **사용자 아이콘**을 클릭하고 **설정** 탭을 선택합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure1.png "Single Sign-On 구성")

5. 왼쪽의 탐색 창에서 **관리 콘솔**을 클릭합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure2.png "Single Sign-On 구성")

6. **관리 콘솔**의 왼쪽 탐색 창에서 **설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure3.png "Single Sign-On 구성")

7. **인증** 섹션에서 **Single Sign-On** 옵션을 선택합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure4.png "Single Sign-On 구성")

8. **Single Sign-On** 섹션에서 다음 단계를 수행합니다.  

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure5.png "Single Sign-On 구성")

    a. **Single Sign-On**에 대한 드롭다운에서 옵션으로 **필수**를 선택합니다.

    b. **로그인 URL 추가**를 클릭하고 **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣은 다음, **완료**를 선택합니다.

    ![Single Sign-on 구성](./media/dropboxforbusiness-tutorial/configure6.png "Single Sign-On 구성")

    다. **인증서 업로드**를 클릭하고 Azure Portal에서 다운로드한 **Base64로 인코딩된 인증서 파일**로 이동합니다.

    d. **링크 복사**를 클릭하고 복사한 값을 Azure Portal의 **Dropbox Business 도메인 및 URL** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-dropbox-business-test-user"></a>Dropbox Business 테스트 사용자 만들기

이 섹션에서는 Dropbox Business에서 B.Simon이라는 사용자를 만듭니다. Dropbox Business는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Dropbox Business에 사용자가 없는 경우 인증 후에 새로운 사용자가 생성됩니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Dropbox Business 클라이언트 지원 팀](https://www.dropbox.com/business/contact)에 문의합니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Dropbox Business 타일을 선택하면 SSO를 설정한 Dropbox Business에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD를 사용하여 Dropbox Business 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)