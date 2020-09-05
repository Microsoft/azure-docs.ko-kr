---
title: '자습서: ZPA(Zscaler Private Access)와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 ZPA(Zscaler Private Access) 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.openlocfilehash: 8c7347aabb3aa6f122ea82a46ad8a09f53e271bf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545690"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>자습서: ZPA(Zscaler Private Access)와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ZPA(Zscaler Private Access)를 통합하는 방법에 대해 알아봅니다. ZPA(Zscaler Private Access)와 Azure AD를 통합하는 경우 다음을 수행할 수 있습니다.

* ZPA(Zscaler Private Access)에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 ZPA(Zscaler Private Access)에 자동으로 로그인(Single Sign-On)되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* ZPA(Zscaler Private Access) SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. ZPA(Zscaler Private Access)는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>갤러리에서 ZPA(Zscaler Private Access) 추가

ZPA(Zscaler Private Access)의 Azure AD 통합을 구성하려면 갤러리의 ZPA(Zscaler Private Access)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ZPA(Zscaler Private Access)** 를 입력합니다.
1. 결과 패널에서 **ZPA(Zscaler Private Access)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

테스트 사용자 **Britta Simon**을 사용하여 ZPA(Zscaler Private Access)로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ZPA(Zscaler Private Access)의 관련 사용자 간에 링크 관계를 설정해야 합니다.

ZPA(Zscaler Private Access)에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. **[ZPA(Zscaler Private Access) 구성](#configure-zscaler-private-access-zpa)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[ZPA(Zscaler Private Access) 테스트 사용자 만들기](#create-zscaler-private-access-zpa-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ZPA(Zscaler Private Access)에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ZPA(Zscaler Private Access)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    1. **로그온 URL** 텍스트 상자에서 `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>` 패턴을 사용하는 URL을 입력합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에서 `https://samlsp.private.zscaler.com/auth/metadata` URL을 입력합니다.

    > [!NOTE]
    > **로그온 URL** 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [ZPA(Zscaler Private Access) 클라이언트 지원 팀](https://help.zscaler.com/zpa-submit-ticket)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

1. **ZPA(Zscaler Private Access) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>ZPA(Zscaler Private Access) 구성

1. ZPA(Zscaler Private Access) 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **ZPA(Zscaler Private Access) 설정**을 클릭하면 ZPA(Zscaler Private Access) 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 ZPA(Zscaler Private Access)에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. ZPA(Zscaler Private Access)를 수동으로 설정하려면 새 웹 브라우저 창을 열고 ZPA(Zscaler Private Access) 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 상단에서 **Administration**(관리)을 클릭하고 **AUTHENTICATION**(인증) 섹션으로 이동한 다음, **IdP Configuration**(IdP 구성)을 클릭합니다.

    ![Zscaler Private Access 관리자 관리](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. 상단 오른쪽 모서리에서 **Add IdP Configuration**(IdP 구성 추가)을 클릭합니다. 

    ![Zscaler Private Access 관리자 idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. **IdP 구성 추가** 페이지에서 다음 단계를 수행합니다.
 
    ![Zscaler Private Access 관리자 선택](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. **Select File**(파일 선택)을 클릭하여 **IdP Metadata File Upload**(IdP 메타데이터 파일 업로드) 필드의 Azure AD에서 다운로드된 메타데이터 파일을 업로드합니다.

    b. 그러면 Azure AD에서 **IdP 메타데이터**를 읽어와서 아래와 같이 모든 필드 정보가 채워집니다.

    ![Zscaler Private Access 관리자 구성](./media/zscalerprivateaccess-tutorial/config.png)

    다. **Domains**(도메인) 필드에서 도메인을 선택합니다.
    
    d. **저장**을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ZPA(Zscaler Private Access)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **ZPA(Zscaler Private Access)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-zscaler-private-access-zpa-test-user"></a>ZPA(Zscaler Private Access) 테스트 사용자 만들기

이 섹션에서는 ZPA(Zscaler Private Access)에서 Britta Simon이라는 사용자를 만듭니다. [ZPA(Zscaler Private Access) 지원 팀](https://help.zscaler.com/zpa-submit-ticket)에 문의하여 ZPA(Zscaler Private Access) 플랫폼에 사용자를 추가하세요.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 ZPA(Zscaler Private Access) 타일을 선택하면 SSO를 설정한 ZPA(Zscaler Private Access)에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)