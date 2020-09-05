---
title: '자습서: Mitel Connect와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Mitel Connect 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: 63f1d1d40d8aff21641f3fa4ee10a289de40800d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552611"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>자습서: Mitel MiCloud Connect 또는 CloudLink 플랫폼과 Azure Active Directory 통합

이 자습서에서는 Mitel Connect 앱을 사용하여 Mitel MiCloud Connect 또는 CloudLink 플랫폼을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 설명합니다. Mitel Connect 앱은 Azure 갤러리에서 얻을 수 있습니다. MiCloud Connect 또는 CloudLink 플랫폼을 Azure AD와 통합하면 다음과 같은 이점을 누릴 수 있습니다.

* 엔터프라이즈 자격 증명을 사용하여 Azure AD에서 MiCloud Connect 앱 및 CloudLink 앱에 액세스할 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 MiCloud Connect 또는 CloudLink에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

Azure 구독이 없는 경우 Mitel MiCloud Connect 또는 CloudLink 플랫폼과 Azure AD의 통합을 시작하기 전에 [체험 계정을 만드세요](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 구성 요소

MiCloud Connect와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* 구성하려는 애플리케이션에 따라 Mitel MiCloud Connect 계정 또는 Mitel CloudLink 계정

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 Azure AD SSO(Single Sign-On)를 구성하고 테스트합니다.

* Mitel Connect는 **SP** 시작 SSO를 지원합니다.
* Mitel Connect가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>갤러리에서 Mitel Connect 추가

Mitel Connect가 Azure AD에 통합되도록 구성하려면 Azure Portal에서 갤러리의 Mitel Connect를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 Azure **Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 필드에 **Mitel Connect**를 입력하고, 결과 패널에서 **Mitel Connect**를 선택한 다음, **추가**를 선택합니다.

     ![결과 목록의 Mitel Connect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **_Britta Simon_** 이라는 테스트 사용자를 기반으로 하여 MiCloud Connect 또는 CloudLink 플랫폼에서 Azure AD SSO를 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 포털의 사용자와 Mitel 플랫폼의 해당 사용자 간에 연결을 설정해야 합니다. MiCloud Connect 또는 CloudLink 플랫폼에서 Azure AD SSO를 구성하고 테스트하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.
* MiCloud Connect에서 Azure AD SSO 구성 및 테스트
* CloudLink 플랫폼에서 Azure AD SSO 구성 및 테스트

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>MiCloud Connect에서 Azure AD SSO 구성 및 테스트

MiCloud Connect에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법은 다음과 같습니다.

1. **[Azure AD를 통해 SSO를 사용하도록 MiCloud Connect 구성](#configure-micloud-connect-for-sso-with-azure-ad)** - 사용자가 이 기능을 사용하고 애플리케이션 쪽에서 SSO 설정을 구성할 수 있도록 설정합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
4. **[Mitel MiCloud Connect 테스트 사용자 만들기](#create-a-mitel-micloud-connect-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 MiCloud Connect 계정에 만듭니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Azure AD를 통해 SSO를 사용하도록 MiCloud Connect 구성

이 섹션에서는 Azure Portal에서 MiCloud Connect에 대해 Azure AD Single Sign-On을 사용하도록 설정하고 Azure AD를 통해 SSO를 허용하도록 MiCloud Connect 계정을 구성합니다.

Azure AD용 SSO를 사용하여 MiCloud Connect를 구성하려면 Azure Portal과 Mitel 계정 포털을 나란히 여는 것이 가장 쉽습니다. 일부 정보는 Azure Portal에서 Mitel 계정 포털로 복사하고, 일부는 Mitel 계정 포털에서 Azure Portal로 복사해야 합니다.


1. [Azure Portal](https://portal.azure.com/)에서 구성 페이지를 여는 방법은 다음과 같습니다.

    1. **Mitel Connect** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

       ![Single Sign-On 구성 링크](common/select-sso.png)

    1. **Single Sign-On 방법 선택** 대화 상자에서 **SAML**을 선택합니다.
    
       ![Single Sign-On 선택 모드](common/select-saml-option.png)
    
       SAML 기반 로그온 페이지가 표시됩니다.

2. Mitel 계정 포털에서 구성 페이지를 여는 방법은 다음과 같습니다.

    1. **전화 시스템** 메뉴에서 **추가 기능**을 선택합니다.

    1. **Single Sign-On**의 오른쪽에서 **활성화** 또는 **설정**을 선택합니다.
    
    Connect Single Sign-on Settings(Connect Single Sign-On 설정) 대화 상자가 표시됩니다.
    
3. **Enable Single Sign-On**(Single Sign-On 사용) 확인란을 선택합니다.
    
    ![Single Sign-On 사용 확인란이 선택된 Mitel Connect Single Sign-On 설정 페이지를 보여주는 스크린샷](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Azure Portal의 **기본 SAML 구성** 섹션에서 **편집** 아이콘을 선택합니다.
   
    ![이미지](common/edit-urls.png)

    기본 SAML 구성 대화 상자가 표시됩니다.

5.  Mitel 계정 포털의 **Mitel Identifier (Entity ID)**(Mitel 식별자(엔터티 ID)) 필드에서 URL을 복사하여 Azure Portal의 **식별자(엔터티 ID)** 필드에 붙여넣습니다.

6. Mitel 계정 포털의 **Reply URL (Assertion Consumer Service URL)**(회신 URL(Assertion Consumer Service URL)) 필드에서 URL을 복사하여 Azure Portal의 **회신 URL(Assertion Consumer Service URL)** 필드에 붙여넣습니다.

   ![이미지](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. **로그온 URL** 텍스트 상자에서 다음 URL 중 하나를 입력합니다.

    1. **https://portal.shoretelsky.com** - Mitel 계정 포털을 기본 Mitel 애플리케이션으로 사용합니다.
    1. **https://teamwork.shoretel.com** - Teamwork를 기본 Mitel 애플리케이션으로 사용합니다.

    > [!NOTE]
    > 기본 Mitel 애플리케이션은 사용자가 [액세스 패널]에서 Mitel Connect 타일을 선택할 때 액세스되는 애플리케이션입니다. 또한 이 애플리케이션은 Azure AD에서 테스트를 설정할 때 액세스되는 애플리케이션이기도 합니다.

8. Azure Portal의 **기본 SAML 구성** 대화 상자에서 **저장**을 선택합니다.

9. Azure Portal의 **SAML 기반 로그온** 페이지에 있는 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆의 **다운로드**를 선택하여 **서명 인증서**를 다운로드하고 컴퓨터에 저장합니다.

    ![이미지](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. 텍스트 편집기에서 서명 인증서 파일을 열고, 파일의 모든 데이터를 복사한 다음, 데이터를 Mitel 계정 포털의 **Signing Certificate**(서명 인증서) 필드에 붙여넣습니다. 

      ![이미지](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Azure Portal의 **SAML 기반 로그인** 페이지에 있는 **Mitel Connect 설정** 섹션에서 다음을 수행합니다.

     1. **로그인 URL** 필드에서 URL을 복사하여 Mitel 계정 포털의 **Sign-in URL**(로그인 URL) 필드에 붙여넣습니다.

     1. **Azure AD 식별자** 필드에서 URL을 복사하여 Mitel 계정 포털의 **Entity ID**(엔터티 ID) 필드에 붙여넣습니다.
         
         ![이미지](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Mitel 계정 포털의 **Connect Single Sign-On 설정** 대화 상자에서 **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 필드에서 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 필드에 brittasimon@\<yourcompanydomain\>.\<extension\>을 입력합니다.  정의합니다(예: BrittaSimon@contoso.com).

    1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Mitel Connect에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Mitel Connect**를 선택합니다.

    ![애플리케이션 목록의 Mitel Connect 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **Britta Simon**을 선택한 다음, 화면 아래쪽에서 **선택**을 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면 아래쪽에서 **선택**을 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel MiCloud Connect 테스트 사용자 만들기

이 섹션에서는 MiCloud Connect 계정에서 Britta Simon이라는 사용자를 만듭니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

Mitel 계정 포털에 사용자를 추가하는 방법에 대한 자세한 내용은 Mitel 기술 자료의 [사용자 추가](https://oneview.mitel.com/s/article/Adding-a-User-092815) 문서를 참조하세요.

다음 세부 정보를 사용하여 사용자를 MiCloud Connect 계정에 만듭니다.

* **이름:** Britta Simon
* **비즈니스 이메일 주소:** `brittasimon@<yourcompanydomain>.<extension>`   
  (예: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **사용자 이름:** `brittasimon@<yourcompanydomain>.<extension>`  
  (예: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). 사용자의 사용자 이름은 일반적으로 사용자의 비즈니스 이메일 주소와 동일합니다.)

> [!NOTE]
> 사용자의 MiCloud Connect 사용자 이름은 Azure에서 사용자의 이메일 주소와 동일해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Mitel Connect 타일을 선택하면 **로그온 URL** 필드에서 기본값으로 구성한 MiCloud Connect 애플리케이션에 로그인하도록 자동으로 리디렉션됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>CloudLink 플랫폼에서 Azure AD SSO 구성 및 테스트

이 섹션에서는 Azure Portal에서 CloudLink 플랫폼에 Azure AD SSO를 사용하도록 설정하는 방법 및 Azure AD를 사용하여 Single Sign-On을 허용하도록 CloudLink 플랫폼 계정을 구성하는 방법을 설명합니다.

CloudLink 플랫폼에 Azure AD Single Sign-On을 사용하도록 구성하려면 Azure Portal의 일부 정보를 CloudLink 계정 포털로 또는 그 반대로 복사해야 하므로 Azure Portal과 CloudLink 계정 포털을 나란히 열어 놓는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com/)에서 구성 페이지를 여는 방법은 다음과 같습니다.

    1. **Mitel Connect** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

       ![Single Sign-On 구성 링크](common/select-sso.png)

    1. **Single Sign-On 방법 선택** 대화 상자에서 **SAML**을 선택합니다.

       ![Single Sign-On 선택 모드](common/select-saml-option.png)
    
       **SAML 기반 로그온** 페이지가 열리고 **기본 SAML 구성** 섹션이 표시됩니다.

       ![이미지](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. CloudLink 계정 포털에서 **Azure AD Single Sign-on** 구성 패널에 액세스하려면 다음을 수행합니다.

    1. 통합을 사용하도록 설정하려는 고객 계정의 **계정 정보** 페이지로 이동합니다.

    1. **통합** 섹션에서 **+ 새로 추가**를 선택합니다. 팝업 화면에 **통합** 패널이 표시됩니다.

    1. **타사** 탭을 선택합니다. 지원되는 타사 애플리케이션 목록이 표시됩니다. **Azure AD Single Sign On**과 연결된 **추가** 단추를 선택하고, **완료**를 선택합니다.

       ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       **Azure AD Single Sign-On**은 고객 계정에 대해 사용하도록 설정되며, **계정 정보** 페이지의 **통합** 섹션에 추가됩니다.   

   1. **설정 완료**를 선택합니다.
    
      ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      **Azure AD Single Sign On** 구성 패널이 열립니다.
      
       ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel은 **선택적 Mitel 자격 증명** 섹션의 **Mitel 자격 증명 사용(선택 사항**) 확인란을 선택하지 않는 것이 좋습니다. 사용자가 Single Sign-On 옵션 외에도 Mitel 자격 증명을 사용하여 CloudLink 애플리케이션에 로그인할 수 있게 하려는 경우에만 이 확인란을 선택하세요.

3. Azure Portal의 **SAML 기반 로그온** 페이지에 있는 **기본 SAML 구성** 섹션에서 **편집** 아이콘을 선택합니다. **기본 SAML 구성** 패널이 열립니다.

    ![이미지](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. CloudLink 계정 포털의 **Mitel 식별자(엔터티 ID)** 필드에서 URL을 복사하여 Azure Portal의 **식별자(엔터티 ID)** 필드에 붙여넣습니다.

 5. CloudLink 계정 포털의 **회신 URL(Assertion Consumer Service URL)** 필드에서 URL을 복사하여 Azure Portal의 **회신 URL(Assertion Consumer Service URL)** 필드에 붙여넣습니다.  
    
    ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. **로그온 URL** 텍스트 상자에 URL `https://accounts.mitel.io`를 입력하여 CloudLink 계정 포털을 기본 Mitel 애플리케이션으로 사용합니다.
     
     ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > 기본 Mitel 애플리케이션은 사용자가 [액세스 패널]에서 Mitel Connect 타일을 선택할 때 열리는 애플리케이션입니다. 또한 사용자가 Azure AD에서 테스트 설정을 구성할 때 액세스되는 애플리케이션이기도 합니다.

7. **기본 SAML 구성** 대화 상자에서 **저장**을 선택합니다.

8. Azure Portal의 **SAML 기반 로그온** 페이지에 있는 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆의 **다운로드**를 선택하여 **서명 인증서**를 다운로드합니다. 인증서를 컴퓨터에 저장합니다.
  
    ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. 텍스트 편집기에서 서명 인증서 파일을 열고, 파일의 모든 데이터를 복사한 다음, 데이터를 CloudLink 계정 포털의 **서명 인증서** 필드에 붙여넣습니다.  

    > [!NOTE]
    > 인증서가 여러 개 있는 경우 하나씩 붙여넣는 것이 좋습니다. 
       
    ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Azure Portal의 **SAML 기반 로그인** 페이지에 있는 **Mitel Connect 설정** 섹션에서 다음을 수행합니다.

     1. **로그인 URL** 필드에서 URL을 복사하여 CloudLink 계정 포털의 **로그인 URL** 필드에 붙여넣습니다.

     1. **Azure AD 식별자** 필드의 URL을 복사하여 CloudLink 계정 포털의 **IDP 식별자(엔터티 ID)** 필드에 붙여넣습니다.
     
        ![이미지](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. CloudLink 계정 포털의 **Azure AD Single Sign-On** 패널에서 **저장**을 선택합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 필드에서 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 필드에 brittasimon@\<yourcompanydomain\>.\<extension\>을 입력합니다.  정의합니다(예: BrittaSimon@contoso.com).

    1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Mitel Connect에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Mitel Connect**를 선택합니다.

    ![애플리케이션 목록의 Mitel Connect 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **Britta Simon**을 선택한 다음, 화면 아래쪽에서 **선택**을 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면 아래쪽에서 **선택**을 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-cloudlink-test-user"></a>CloudLink 테스트 사용자 만들기

이 섹션에서는 CloudLink 플랫폼에서 **_Britta Simon_** 이라는 테스트 사용자를 만드는 방법을 설명합니다. 사용자가 Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

CloudLink 계정 포털에서 사용자를 추가하는 방법에 대한 자세한 내용은 [CloudLink 계정 설명서](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html)의 **_사용자 관리_** 를 참조하세요.

다음 세부 정보를 사용하여 CloudLink 계정 포털에서 사용자를 만듭니다.

* 이름: Britta Simon
* 이름: Britta
* 성: Simon
* 전자 메일: BrittaSimon@contoso.com

> [!NOTE]
> 사용자의 CloudLink 이메일 주소는 Azure Portal의 **사용자 계정 이름**과 동일해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 [액세스 패널]을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Mitel Connect 타일을 선택하면 **로그온 URL** 필드에서 기본값으로 구성한 CloudLink 애플리케이션에 로그인하도록 자동으로 리디렉션됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)