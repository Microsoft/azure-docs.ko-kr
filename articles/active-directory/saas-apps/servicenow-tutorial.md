---
title: '자습서: ServiceNow와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 ServiceNow 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: jeedes
ms.openlocfilehash: 9748c772466db73fc49b1291ffe2ce7d13da1d8c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540221"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>자습서: ServiceNow와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 ServiceNow를 통합하는 방법에 대해 알아봅니다. Azure AD와 ServiceNow를 통합하면 다음 작업을 할 수 있습니다.

* ServiceNow에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 ServiceNow에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* ServiceNow SSO(Single Sign-On)가 설정된 구독
* ServiceNow의 경우 ServiceNow의 인스턴스 또는 테넌트, Calgary 버전 이상
* ServiceNow Express의 경우 ServiceNow Express의 인스턴스, Helsinki 버전 이상
* ServiceNow 테넌트에서 [여러 공급자 Single Sign-On 플러그 인](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)을 사용하도록 설정해야 합니다. [서비스 요청을 제출](https://hi.service-now.com)하면 됩니다.
* 자동 구성은 ServiceNow에 대한 다중 공급자 플러그 인을 사용하도록 설정합니다.
* ServiceNow Classic(모바일) 애플리케이션을 설치하려면 적절한 스토어로 이동한 다음, ServiceNow Classic 애플리케이션을 검색합니다. 그리고 다운로드합니다.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

* ServiceNow에서 **SP** 시작 SSO를 지원합니다.

* ServiceNow에서 [자동화된 사용자 프로비저닝](servicenow-provisioning-tutorial.md)을 지원합니다.

* ServiceNow를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* SSO를 설정할 수 있는 Azure AD를 사용하여 ServiceNow Classic(모바일) 애플리케이션을 구성할 수 있습니다. Azure AD는 Android 및 iOS 사용자를 지원합니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

## <a name="add-servicenow-from-the-gallery"></a>갤러리에서 ServiceNow 추가

ServiceNow의 Azure AD 통합을 구성하려면 갤러리의 ServiceNow를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. 회사 또는 학교 계정을 사용하거나 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ServiceNow**를 입력합니다.
1. 결과 패널에서 **ServiceNow**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 ServiceNow에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ServiceNow의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ServiceNow에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
    1. [ServiceNow Express에 대해 Azure AD SSO 구성](#configure-azure-ad-sso-for-servicenow-express) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [ServiceNow 구성](#configure-servicenow) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. [ServiceNow 테스트 사용자 만들기](#create-servicenow-test-user) - Azure AD 표현과 연결된 B. Simon에 해당하는 사용자를 ServiceNow에 만듭니다.
    1. [ServiceNow Express SSO 구성](#configure-servicenow-express-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.  
3. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.
4. [ServiceNow Classic(모바일)에 대한 SSO 테스트](#test-sso-for-servicenow-classic-mobile)를 수행하여 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ServiceNow** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 펜 아이콘을 선택하여 설정을 편집합니다.

   ![펜 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL**에 `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 에 `https://<instance-name>.service-now.com` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL**에 다음 URL 중 하나를 입력합니다.

    | 회신 URL|
    |----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 자습서 뒷부분에 설명된 실제 로그온 URL 및 식별자로 업데이트해야 합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. 

   ![다운로드가 강조 표시된 SAML 서명 인증서 섹션의 스크린샷](common/certificatebase64.png)

   a. 복사 단추를 선택하여 **앱 페더레이션 메타데이터 URL**을 복사한 다음, 메모장에 붙여넣습니다. 이 URL은 이 자습서의 뒷부분에서 사용됩니다.

    b. **다운로드**를 선택하여 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

1. **ServiceNow 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![URL이 강조 표시된 ServiceNow 설정 섹션의 스크린샷](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름**에 `B.Simon`를 입력합니다.  
   1. **사용자 이름**으로 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 ServiceNow에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **ServiceNow**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹**을 선택합니다.

   ![사용자 및 그룹이 강조 표시된 관리 섹션의 스크린샷](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![[사용자 추가]가 강조 표시된 [사용자 및 그룹]의 스크린샷](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, **선택**을 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, **선택**을 선택합니다.
1. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>ServiceNow Express에 대해 Azure AD SSO 구성

1. [Azure Portal](https://portal.azure.com/)의 **ServiceNow** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On이 강조 표시된 ServiceNow 애플리케이션 통합 페이지의 스크린샷](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![SAML이 강조 표시된 Single Sign-On 방법 선택의 스크린샷](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 펜 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![펜 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL**에 `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 에 `https://<instance-name>.service-now.com` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL**에 다음 URL 중 하나를 입력합니다.

    | 회신 URL |
    |-----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 자습서 뒷부분에 설명된 실제 로그온 URL 및 식별자로 업데이트해야 합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하고, 지정된 옵션 중에서 요구 사항에 맞는 **인증서(Base64)** 를 다운로드합니다. 인증서를 컴퓨터에 저장합니다.

    ![다운로드가 강조 표시된 SAML 서명 인증서 섹션의 스크린샷](common/certificatebase64.png)

6. Azure AD에서 SAML 기반 인증용 ServiceNow를 자동으로 구성하도록 할 수 있습니다. 이 서비스를 사용하도록 설정하려면 **ServiceNow 설정** 섹션으로 이동하고, **단계별 지침 보기**를 선택하여 **로그온 구성** 창을 엽니다.

    ![[단계별 지침 보기]가 강조 표시된 [ServiceNow 설정] 섹션의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. **로그온 구성** 양식에서 ServiceNow 인스턴스 이름, 관리자 이름 및 관리자 암호를 입력합니다. **지금 구성**을 선택합니다. 입력하는 관리 사용자 이름에 ServiceNow의 **security_admin** 역할이 할당되어 있어야 이 절차를 수행할 수 있습니다. 그렇지 않고 Azure AD를 SAML ID 공급자로 사용하도록 ServiceNow를 수동으로 구성하려면 **수동으로 Single Sign-On 구성**을 선택합니다. 빠른 참조 섹션에서 **로그아웃 URL, Azure AD 식별자 및 로그인 URL**을 복사합니다.

    ![[지금 구성]이 강조 표시된 [로그온 구성] 양식의 스크린샷](./media/servicenow-tutorial/configure.png "앱 URL 구성")

## <a name="configure-servicenow"></a>ServiceNow 구성

1. ServiceNow 애플리케이션에 관리자 권한으로 로그온합니다.

1. 다음 단계를 따라 **통합 - 여러 공급 기업 Single Sign-On 설치 관리자** 플러그 인을 활성화합니다.

    a. 왼쪽 창의 검색 상자에서 **시스템 정의** 섹션을 검색한 다음, **플러그 인**을 선택합니다.

    ![[시스템 정의] 및 [플러그 인]이 강조 표시된 [시스템 정의] 섹션의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_03.png "플러그 인 활성화")

    b. **통합 - 여러 공급 기업 Single Sign-On 설치 관리자**를 검색합니다.

     ![[통합 - 여러 공급자 Single Sign-On 설치 관리자]가 강조 표시된 [시스템 플러그 인] 페이지의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_04.png "플러그 인 활성화")

    다. 플러그 인을 선택합니다. 마우스 오른쪽 단추를 클릭하고 **활성화/업그레이드**를 선택합니다.

     ![[활성화/업그레이드]가 강조 표시된 플러그 인 오른쪽 클릭 메뉴의 스크린샷](./media/servicenow-tutorial/tutorial_activate.png "플러그 인 활성화")

    d. **활성화**를 선택합니다.

     ![[활성화]가 강조 표시된 [플러그 인 활성화] 대화 상자의 스크린샷](./media/servicenow-tutorial/tutorial_activate1.png "플러그 인 활성화")

1. 왼쪽 창의 검색 창에서 **여러 공급자 SSO** 섹션을 검색한 다음, **속성**을 선택합니다.

    ![[여러 공급자 SSO] 및 [속성] 섹션이 강조 표시된 여러 공급자 SSO 섹션의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_06.png "앱 URL 구성")

1. **여러 공급자 SSO 속성** 대화 상자에서 다음 단계를 수행합니다.

    ![[여러 공급자 SSO 속성] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694981.png "앱 URL 구성")

    * **여러 공급자 SSO 사용**을 **예**로 선택합니다.
  
    * **모든 ID 공급자에서 사용자 테이블로 사용자 자동 가져오기 사용**을 **예**로 선택합니다.

    * **여러 공급자 SSO 통합에 디버그 로깅 사용**을 **예**로 선택합니다.

    * **...하는 사용자 테이블의 필드**에 **이메일**을 입력합니다.
  
    * **저장**을 선택합니다.

1. ServiceNow를 자동 또는 수동으로 구성할 수 있습니다. ServiceNow를 자동으로 구성하려면 다음 단계를 따릅니다.

    1. Azure Portal의 **ServiceNow** Single-Sign On 페이지로 돌아갑니다.

    1. ServiceNow에 대한 원클릭 구성 서비스가 제공됩니다. 이 서비스를 사용하도록 설정하려면 **ServiceNow 구성** 섹션으로 이동하고, **ServiceNow 구성**을 선택하여 **로그온 구성** 창을 엽니다.

        ![[단계별 지침 보기]가 강조 표시된 [ServiceNow 설정]의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. **로그온 구성** 양식에서 ServiceNow 인스턴스 이름, 관리자 이름 및 관리자 암호를 입력합니다. **지금 구성**을 선택합니다. 입력하는 관리 사용자 이름에 ServiceNow의 **security_admin** 역할이 할당되어 있어야 이 절차를 수행할 수 있습니다. 그렇지 않고 Azure AD를 SAML ID 공급자로 사용하도록 ServiceNow를 수동으로 구성하려면 **수동으로 Single Sign-On 구성**을 선택합니다. 빠른 참조 섹션에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

        ![[지금 구성]이 강조 표시된 [로그온 구성] 양식의 스크린샷](./media/servicenow-tutorial/configure.png "앱 URL 구성")

    1. ServiceNow 애플리케이션에 관리자 권한으로 로그온합니다.

       * 자동 구성의 경우 필요한 모든 설정이 **ServiceNow** 쪽에서 구성되지만, **X.509 인증서**는 기본적으로 사용되지 않습니다. ServiceNow의 ID 공급자에 직접 매핑해야 합니다. 다음 단계를 수행하세요.

         1. 왼쪽 창의 검색 창에서 **여러 공급자 SSO** 섹션을 검색한 다음, **ID 공급자**를 선택합니다.

            ![[ID 공급자]가 강조 표시된 [여러 공급자 SSO] 섹션의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_07.png "Single Sign-On 구성")

         1. 자동으로 생성된 ID 공급자를 선택합니다.

            ![자동으로 생성된 ID 공급자가 강조 표시된 [ID 공급자]의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_08.png "Single Sign-On 구성")

         1.  **ID 공급자** 섹션에서 다음 단계를 수행합니다.

             ![[ID 공급자] 섹션의 스크린샷](./media/servicenow-tutorial/automatic_config.png "Single Sign-On 구성")

               * **이름**에 구성의 이름을 입력합니다(예: **Microsoft Azure 페더레이션된 Single Sign-On**).

               * 텍스트 상자에 입력된 **ID 공급자의 SingleLogoutRequest** 값을 제거합니다.

               * **ServiceNow 홈페이지** 값을 복사하여 Azure Portal의 **ServiceNow 기본 SAML 구성** 섹션에 있는 **로그온 URL**에 붙여넣습니다.

                  > [!NOTE]
                  > ServiceNow 인스턴스 홈 페이지의 URL은 **ServieNow 테넌트 URL**과 **/navpage.do**가 연결된 형태입니다(예: `https://fabrikam.service-now.com/navpage.do`).

              * **엔터티 ID/발급자** 값을 복사하여 Azure Portal의 **ServiceNow 기본 SAML 구성** 섹션에 있는 **식별자**에 붙여넣습니다.

              * **NameID 정책**이 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 값으로 설정되어 있는지 확인합니다. 

         1. 아래쪽의 **X.509 인증서** 섹션으로 스크롤하여 **편집**을 선택합니다.

             ![[편집]이 강조 표시된 [X.509 인증서] 섹션의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_09.png "Single Sign-On 구성")

         1. 인증서를 선택하고, 오른쪽 화살표 아이콘을 선택하여 인증서 추가

            ![인증서 및 오른쪽 화살표 아이콘이 강조 표시된 [컬렉션]의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_11.png "Single Sign-On 구성")

          1. **저장**을 선택합니다.

          1. 페이지의 오른쪽 위 모서리에서 **연결 테스트**를 선택합니다.

             ![[연결 테스트]가 강조 표시된 페이지의 스크린샷](./media/servicenow-tutorial/tutorial_activate2.png "플러그 인 활성화")

             > [!NOTE]
             > 테스트 연결이 실패하고 이 연결을 활성화할 수 없는 경우 ServiceNow는 재정의 스위치를 제공합니다. **검색 탐색**에 **Sys_properties.LIST**를 입력하면 새 시스템 속성 페이지가 열립니다. 여기서 이름이 **glide.authenticate.multisso.test.connection.mandatory**인 **datatype**을 **True/False**로 새 속성을 만든 다음, **값**을 **False**로 설정해야 합니다.

             > ![[테스트 결과] 페이지의 스크린샷](./media/servicenow-tutorial/testconnection-fail.png "Single Sign-On 구성")
        
          1. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다. 그러면 다음 페이지가 표시됩니다. **SSO 로그아웃 테스트 결과** 오류가 발생할 수 있습니다. 오류를 무시하고 **활성화**를 선택합니다.

             ![[테스트 결과] 페이지의 스크린샷](./media/servicenow-tutorial/servicenowactivate.png "Single Sign-On 구성")
  
1. **ServiceNow**를 수동으로 구성하려면 다음 단계를 따릅니다.

    1. ServiceNow 애플리케이션에 관리자 권한으로 로그온합니다.

    1. 왼쪽 창에서 **ID 공급자**를 선택합니다.

        ![[ID 공급자]가 강조 표시된 [여러 공급자 SSO]의 스크린샷](./media/servicenow-tutorial/tutorial_servicenow_07.png "Single Sign-On 구성")

    1. **ID 공급자** 대화 상자에서 **새로 만들기**를 선택합니다.

        ![[새로 만들기]가 강조 표시된 [ID 공급자] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694977.png "Single Sign-On 구성")

    1. **ID 공급자** 대화 상자에서 **SAML**을 선택합니다.

        ![[SAML]이 강조 표시된 [ID 공급자] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694978.png "Single Sign-On 구성")

    1. **ID 공급자 메타데이터 가져오기**에서 다음 단계를 수행합니다.

        ![[URL] 및 [가져오기]가 강조 표시된 [ID 공급자 메타데이터 가져오기]의 스크린샷](./media/servicenow-tutorial/idp.png "Single Sign-On 구성")

        1. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL**을 입력합니다.

        1. **가져오기**를 선택합니다.

    1. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

        ![[ID 공급자]의 스크린샷](./media/servicenow-tutorial/ic7694982.png "Single Sign-On 구성")

        * **이름**에 구성의 이름을 입력합니다(예: **Microsoft Azure 페더레이션된 Single Sign-On**).

        * 텍스트 상자에 입력된 **ID 공급자의 SingleLogoutRequest** 값을 제거합니다.

        * **ServiceNow 홈페이지** 값을 복사합니다. Azure Portal의 **ServiceNow 기본 SAML 구성** 섹션에 있는 **로그온 URL**에 붙여넣습니다.

            > [!NOTE]
            > ServiceNow 인스턴스 홈 페이지의 URL은 **ServieNow 테넌트 URL**과 **/navpage.do**가 연결된 형태입니다(예: `https://fabrikam.service-now.com/navpage.do`).

        * **엔터티 ID/발급자** 값을 복사합니다. Azure Portal의 **ServiceNow 기본 SAML 구성** 섹션에 있는 **식별자**에 붙여넣습니다.

        * **NameID 정책**이 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 값으로 설정되어 있는지 확인합니다.

        * **고급**을 선택합니다. **사용자 필드**에 **이메일**을 입력합니다.

            > [!NOTE]
            > Azure AD 사용자 ID(사용자 계정 이름) 또는 이메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. 이렇게 하려면 Azure Portal에서 **ServiceNow** > **특성** > **Single Sign-On** 섹션으로 이동한 다음, 원하는 필드를 **nameidentifier** 특성에 매핑합니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.

        * 페이지의 오른쪽 위 모서리에서 **연결 테스트**를 선택합니다.

          > [!NOTE]
          > 테스트 연결이 실패하고 이 연결을 활성화할 수 없는 경우 ServiceNow는 재정의 스위치를 제공합니다. **검색 탐색**에 **Sys_properties.LIST**를 입력하면 새 시스템 속성 페이지가 열립니다. 여기서 이름이 **glide.authenticate.multisso.test.connection.mandatory**인 **datatype**을 **True/False**로 새 속성을 만든 다음, **값**을 **False**로 설정해야 합니다.

          > ![[테스트 결과] 페이지의 스크린샷](./media/servicenow-tutorial/testconnection-fail.png "Single Sign-On 구성")

        * 자격 증명을 입력하라는 메시지가 표시되면 입력합니다. 그러면 다음 페이지가 표시됩니다. **SSO 로그아웃 테스트 결과** 오류가 발생할 수 있습니다. 오류를 무시하고 **활성화**를 선택합니다.

          ![[테스트 결과] 페이지의 스크린샷](./media/servicenow-tutorial/servicenowactivate.png "Single Sign-On 구성")

### <a name="create-servicenow-test-user"></a>ServiceNow 테스트 사용자 만들기

이 섹션의 목적은 ServiceNow에서 B.Simon이라는 사용자를 만드는 것입니다. ServiceNow는 자동 사용자 프로비저닝을 지원하며 기본적으로 사용하도록 설정되어 있습니다.

> [!NOTE]
> 사용자를 수동으로 생성해야 하는 경우 [ServiceNow 클라이언트 지원 팀](https://www.servicenow.com/support/contact-support.html)에 문의하세요.

### <a name="configure-servicenow-express-sso"></a>ServiceNow Express SSO 구성

1. ServiceNow Express 애플리케이션에 관리자 권한으로 로그온합니다.

2. 왼쪽 창에서 **Single Sign-On**을 선택합니다.

    ![[Single Sign-On]이 강조 표시된 ServiceNow Express 애플리케이션의 스크린샷](./media/servicenow-tutorial/ic7694980ex.png "앱 URL 구성")

3. **Single Sign-On** 대화 상자에서 오른쪽 위의 구성 아이콘을 선택하고, 다음 속성을 설정합니다.

    ![[Single Sign-On] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694981ex.png "앱 URL 구성")

    a. **여러 공급자 SSO 사용**을 오른쪽으로 설정/해제합니다.

    b. **여러 공급자 SSO 통합에 대한 디버그 로깅 활성화**를 오른쪽으로 설정/해제합니다.

    다. **...하는 사용자 테이블의 필드**에 **user_name**을 입력합니다.

4. **Single Sign-On** 대화 상자에서 **새 인증서 추가**를 선택합니다.

    ![[새 인증서 추가]가 강조 표시된 [Single Sign-On] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694973ex.png "Single Sign-On 구성")

5. **X.509 인증서** 대화 상자에서 다음 단계를 수행합니다.

    ![[X.509 인증서] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694975.png "Single Sign-On 구성")

    a. **이름**에 구성의 이름을 입력합니다(예: **TestSAML2.0**).

    b. **활성**을 선택합니다.

    다. **형식**으로 **PEM**을 선택합니다.

    d. **유형**으로 **저장소 인증서 신뢰**를 선택합니다.

    e. Azure Portal에서 다운로드한 Base64로 인코딩된 인증서를 메모장에서 엽니다. 인증서 내용을 클립보드에 복사한 다음, **PEM 인증서** 텍스트 상자에 붙여넣습니다.

    f. **업데이트**를 선택합니다.

6. **Single Sign-On** 대화 상자에서 **새 IdP 추가**를 선택합니다.

    ![[새 IdP 추가]가 강조 표시된 [Single Sign-On] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694976ex.png "Single Sign-On 구성")

7. **새 ID 공급자 추가** 대화 상자의 **ID 공급자 구성** 아래에서 다음 단계를 수행합니다.

    ![[새 ID 공급자 추가] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694982ex.png "Single Sign-On 구성")

    a. **이름**에 구성의 이름을 입력합니다(예: **SAML 2.0**).

    b. **ID 공급자 URL**에 Azure Portal에서 복사한 ID 공급자 ID 값을 붙여넣습니다.

    다. **ID 공급자의 AuthnRequest**에 Azure Portal에서 복사한 인증 요청 URL 값을 붙여넣습니다.

    d. **ID 공급자의 SingleLogoutRequest**에 Azure Portal에서 복사한 로그아웃 URL 값을 붙여넣습니다.

    e. **ID 공급자 인증서**로 이전 단계에서 만든 인증서를 선택합니다.

8. **고급 설정**을 선택합니다. **추가 ID 공급자 속성** 아래에서 다음 단계를 수행합니다.

    ![[고급 설정]이 강조 표시된 [새 ID 공급자 추가] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694983ex.png "Single Sign-On 구성")

    a. **IDP의 SingleLogoutRequest에 대한 프로토콜 바인딩**으로 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**를 입력합니다.

    b. **NameID 정책**으로 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**를 입력합니다.

    다. **AuthnContextClassRef 메서드**로 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`를 입력합니다.

    d. **AuthnContextClass 만들기**를 끕니다(선택 취소).

9. **추가 서비스 공급자 속성** 아래에서 다음 단계를 수행합니다.

    ![다양한 속성이 강조 표시된 [새 ID 공급자 추가] 대화 상자의 스크린샷](./media/servicenow-tutorial/ic7694984ex.png "Single Sign-On 구성")

    a. **ServiceNow 홈페이지**로 ServiceNow 인스턴스 홈페이지의 URL을 입력합니다.

    > [!NOTE]
    > ServiceNow 인스턴스 홈 페이지의 URL은 **ServieNow 테넌트 URL**과 **/navpage.do**가 연결된 형태입니다(예: `https://fabrikam.service-now.com/navpage.do`).

    b. **엔터티 ID/발급자**로 ServiceNow 테넌트의 URL을 입력합니다.

    다. **대상 URI**로 ServiceNow 테넌트의 URL을 입력합니다.

    d. **클록 스큐**로 **60**을 입력합니다.

    e. **사용자 필드**에 **이메일**을 입력합니다.

    > [!NOTE]
    > Azure AD 사용자 ID(사용자 계정 이름) 또는 이메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. 이렇게 하려면 Azure Portal에서 **ServiceNow** > **특성** > **Single Sign-On** 섹션으로 이동한 다음, 원하는 필드를 **nameidentifier** 특성에 매핑합니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.

    f. **저장**을 선택합니다.

## <a name="test-sso"></a>SSO 테스트

액세스 패널에서 ServiceNow 타일을 선택하면 SSO를 설정한 ServiceNow에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="test-sso-for-servicenow-classic-mobile"></a>ServiceNow Classic(모바일)에 대한 SSO 테스트

1. **ServiceNow Classic(모바일)** 애플리케이션을 열고 다음 단계를 수행합니다.

    a. 오른쪽 아래 모서리에 있는 더하기 기호를 선택합니다.

    ![더하기 기호가 강조 표시된 ServiceNow 클래식 애플리케이션의 스크린샷](./media/servicenow-tutorial/test03.png)

    b. ServiceNow 인스턴스 이름을 입력하고 **계속**을 선택합니다.

    ![[계속]이 강조 표시된 [인스턴스 추가] 페이지의 스크린샷](./media/servicenow-tutorial/test04.png)

    다. **로그인** 페이지에서 다음 단계를 수행합니다.

    ![[외부 로그인 사용]이 강조 표시된 로그인 페이지의 스크린샷](./media/servicenow-tutorial/test01.png)

    *  **사용자 이름**을 입력합니다(예: B.simon@contoso.com).

    *  **외부 로그인 사용**을 선택합니다. 로그인하는 Azure AD 페이지로 리디렉션됩니다.

    *  자격 증명을 입력합니다. 타사 인증 또는 다른 보안 기능이 설정된 경우 사용자가 그에 따라 적절하게 대응해야 합니다. 애플리케이션 **홈페이지**가 표시됩니다.

        ![애플리케이션 홈페이지의 스크린샷](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비전 구성](servicenow-provisioning-tutorial.md)

- [Azure AD에서 ServiceNow 사용해보기](https://aad.portal.azure.com)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [고급 표시 유형 및 컨트롤을 사용하여 ServiceNow를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
