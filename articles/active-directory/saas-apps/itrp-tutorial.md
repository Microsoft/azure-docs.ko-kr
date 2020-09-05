---
title: '자습서: ITRP와 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 ITRP 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d7a7c4f7d9a9ba2ef574519a41aae3dd6e28e4fb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552801"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>자습서: ITRP와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ITRP를 통합하는 방법에 대해 알아봅니다.
이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 ITRP에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ITRP에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

ITRP와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On을 사용하도록 설정된 ITRP 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* ITRP에서 SP 시작 SSO를 지원합니다.

## <a name="add-itrp-from-the-gallery"></a>갤러리에서 ITRP 추가

ITRP가 Azure AD에 통합되도록 설정하려면 갤러리의 ITRP를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **ITRP**를 입력합니다. 검색 결과에서 **ITRP**를 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 ITRP에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 ITRP의 해당 사용자 간에 연결 관계를 설정해야 합니다.

ITRP에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[ITRP Single Sign-On을 구성](#configure-itrp-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[ITRP 테스트 사용자를 만듭니다](#create-an-itrp-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

ITRP에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 ITRP 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![기본 SAML 구성 대화 상자](common/sp-identifier.png)

    1. **로그온 URL** 상자에 다음 패턴으로 URL을 입력합니다.
    
       `https://<tenant-name>.itrp.com`

    1. **식별자(엔터티 ID)** 상자에 다음 패턴으로 URL을 입력합니다.

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > 이러한 값은 자리 표시자입니다. 실제 로그온 URL 및 식별자를 사용해야 합니다. 이 값을 얻으려면 [ITRP 지원 팀](https://www.4me.com/support/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 대화 상자에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML 서명 인증서** 섹션에서 **편집** 아이콘을 선택하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-certificate.png)

6. **SAML 서명 인증서** 섹션에서 **지문** 값을 복사하고 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

7. **ITRP 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-itrp-single-sign-on"></a>ITRP Single Sign-On 구성

1. 새 웹 브라우저 창에서 ITRP 회사 사이트에 관리자 권한으로 로그인합니다.

1. 창 위쪽에서 **Settings**(설정) 아이콘을 선택합니다.

    ![설정 아이콘](./media/itrp-tutorial/ic775570.png "설정 아이콘")

1. 왼쪽 창에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](./media/itrp-tutorial/ic775571.png "Single Sign-On 선택")

1. **Single Sign-On** 구성 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 섹션](./media/itrp-tutorial/ic775572.png "Single Sign-On 섹션")

    ![Single Sign-On 섹션](./media/itrp-tutorial/ic775573.png "Single Sign-On 섹션")

    1. **사용**을 선택합니다.

    1. Azure Portal에서 복사한 **로그아웃 URL**을 **원격 로그아웃 URL** 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 **로그인 URL**을 **SAML SSO URL** 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 인증서의 **지문** 값을 **Certificate fingerprint**(인증서 지문) 상자에 붙여넣습니다.

    1. **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 화면의 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에 **BrittaSimon@\<yourcompanydomain>\<extension>.** 을 입력합니다. (예: BrittaSimon@contoso.com)

    1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ITRP에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **ITRP**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **ITRP**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고, 창 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 창 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-an-itrp-test-user"></a>ITRP 테스트 사용자 만들기

Azure AD 사용자가 ITRP에 로그인할 수 있도록 설정하려면 해당 사용자를 ITRP에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. ITRP 테넌트에 로그인합니다.

1. 창 위쪽에서 **Records**(레코드) 아이콘을 선택합니다.

    ![레코드 아이콘](./media/itrp-tutorial/ic775575.png "레코드 아이콘")

1. 메뉴에서 **People**(사람)을 선택합니다.

    ![사용자 선택](./media/itrp-tutorial/ic775587.png "사용자 선택")

1. 더하기 기호( **+** )를 선택하여 새 사람을 추가합니다.

    ![더하기 기호 선택](./media/itrp-tutorial/ic775576.png "더하기 기호 선택")

1. **Add New Person**(새 사람 추가) 대화 상자에서 다음 단계를 수행합니다.

    ![새 사람 추가 대화 상자](./media/itrp-tutorial/ic775577.png "새 사람 추가 대화 상자")

    1. 추가하려는 유효한 Azure AD 계정의 이름과 이메일 주소를 입력합니다.

    1. **저장**을 선택합니다.

> [!NOTE]
> ITRP에서 제공하는 사용자 계정 생성 도구 또는 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 ITRP 타일을 선택하면 SSO를 설정한 ITRP 인스턴스에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
