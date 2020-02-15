---
title: SAML Single Sign-On-비 갤러리 응용 프로그램-Microsoft identity platform | Microsoft Docs
description: Microsoft id 플랫폼 (Azure AD)에서 비 갤러리 응용 프로그램에 대 한 SSO (Single Sign-On) 구성
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159066"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>비 갤러리 응용 프로그램에 대 한 SAML 기반 Single Sign-On 구성

[갤러리 앱](add-gallery-app.md) 또는 [비 갤러리 웹 앱](add-non-gallery-app.md) 을 Azure AD 엔터프라이즈 응용 프로그램에 추가 하면 사용할 수 있는 Single Sign-On 옵션 중 하나는 [SAML 기반 Single Sign-On](what-is-single-sign-on.md#saml-sso)입니다. SAML 프로토콜 중 하나를 사용 하 여 인증 하는 응용 프로그램의 경우 가능 하면 항상 SAML을 선택 합니다. SAML Single Sign-On을 사용하는 Azure AD는 사용자의 Azure AD 계정을 사용하여 애플리케이션에 인증합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. 사용자가 SAML 클레임에서 정의한 규칙에 따라 특정 응용 프로그램 역할에 사용자를 매핑할 수 있습니다. 이 문서에서는 비갤러리 앱에 대한 SAML 기반 Single Sign-On을 구성하는 방법에 대해 설명합니다. 

> [!NOTE]
> 갤러리 앱을 추가하나요? 이에 대한 단계별 설치 지침은 [SaaS 앱 자습서 목록](../saas-apps/tutorial-list.md)에서 확인하세요.

코드를 작성 하지 않고 비 갤러리 응용 프로그램에 대 한 SAML Single Sign-On을 구성 하려면 Azure AD Premium 라이선스와 함께 구독이 있어야 하 고 응용 프로그램에서 SAML 2.0을 지원 해야 합니다. Azure AD 버전에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램을 Azure AD 테 넌 트에 추가 하지 않은 경우 [비 갤러리 앱 추가](add-non-gallery-app.md)를 참조 하세요.

## <a name="step-1-edit-the-basic-saml-configuration"></a>1단계. 기본 SAML 구성 편집

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

2. **Azure Active Directory** > **엔터프라이즈 애플리케이션**으로 차례로 이동하고, 목록에서 애플리케이션을 선택합니다. 
   
   - 애플리케이션을 검색하려면 **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**, **적용**을 차례로 선택합니다. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

3. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

4. **SAML**을 선택합니다. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지가 표시됩니다.

   ![1 단계 기본 SAML 구성 편집](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. 기본 SAML 구성 옵션을 편집하려면 **기본 SAML 구성** 섹션의 오른쪽 위 모서리에 있는 **편집** 아이콘(연필 모양)을 선택합니다.

1. 다음 설정을 입력합니다. 응용 프로그램 공급 업체에서 값을 가져와야 합니다. 필드 값을 추출하려면 수동으로 값을 입력하거나 메타데이터 파일을 업로드할 수 있습니다.

    | 기본 SAML 구성 설정 | SP 시작 | idP 시작 | Description |
    |:--|:--|:--|:--|
    | **식별자 (엔터티 ID)** | 일부 앱의 경우 필수 | 일부 앱의 경우 필수 | 응용 프로그램을 고유 하 게 식별 합니다. Azure AD는 SAML 토큰의 대상 매개 변수로 애플리케이션에 식별자를 보냅니다. 애플리케이션이 식별자의 유효성을 검사해야 합니다. 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다. ' Https://<subdomain>contoso.com ' 패턴을 사용 하는 URL을 입력 합니다. *이 값은 응용 프로그램에서 보낸 **AuthnRequest** (SAML 요청)의 **발급자** 요소로 찾을 수* 있습니다. |
    | **회신 URL** | 필수 | 필수 | 애플리케이션이 SAML 토큰을 수신해야 하는 위치를 지정합니다. 회신 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. 추가 회신 URL 필드를 사용 하 여 여러 회신 Url을 지정할 수 있습니다. 예를 들어 여러 하위 도메인에 대 한 추가 회신 Url이 필요할 수 있습니다. 또는 테스트를 위해 한 번에 여러 회신 Url (로컬 호스트 및 공용 Url)을 지정할 수 있습니다. |
    | **로그온 URL** | 필수 | 지정하지 않음 | 사용자가 이 URL을 열면 서비스 공급자가 Azure AD를 리디렉션하여 사용자를 인증하고 로그온하도록 합니다. Azure AD는 URL을 사용하여 Office 365 또는 Azure AD 액세스 패널에서 애플리케이션을 시작합니다. 비어 있는 경우 Azure AD는 사용자가 Office 365, Azure AD 액세스 패널 또는 Azure AD SSO URL에서 응용 프로그램을 시작할 때 IdP 시작 로그온을 수행 합니다.|
    | **릴레이 상태** | 옵션 | 옵션 | 인증이 완료되면 사용자를 리디렉션할 위치를 애플리케이션에 지정합니다. 일반적으로 이 값은 애플리케이션에 대한 올바른 URL입니다. 그러나 일부 애플리케이션에서는 이 필드를 다르게 사용합니다. 자세한 내용은 애플리케이션 공급 업체에 요청하세요.
    | **로그아웃 URL** | 옵션 | 옵션 | SAML 로그아웃 응답을 애플리케이션에 다시 보내는 데 사용됩니다.

자세한 내용은 [Single SIGN-ON SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)을 참조 하세요.

## <a name="step-2-configure-user-attributes-and-claims"></a>2단계. 사용자 특성 및 클레임 구성 

사용자가 응용 프로그램에 인증할 때 Azure AD는 응용 프로그램을 고유 하 게 식별 하는 사용자에 대 한 정보 (또는 클레임)가 있는 SAML 토큰을 발급 합니다. 기본적으로이 정보는 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함 합니다. 예를 들어 응용 프로그램에 특정 클레임 값 이나 username 이외의 **이름** 형식이 필요한 경우 이러한 클레임을 사용자 지정 해야 할 수 있습니다. 갤러리 앱에 대한 요구 사항은 [애플리케이션 관련 자습서](../saas-apps/tutorial-list.md)에 설명되어 있거나 애플리케이션 공급업체에 문의할 수 있습니다. 사용자 특성 및 클레임을 구성하는 일반적인 단계는 다음과 같습니다.

1. **사용자 특성 및 클레임** 섹션의 오른쪽 위 모서리에서 **편집** 아이콘(연필)을 선택합니다.

   ![2 단계 사용자 특성 및 클레임 구성](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. **이름 식별자 값**을 확인합니다. 기본값은 *user.principalname*입니다. 사용자 ID는 애플리케이션 내에서 각 사용자를 고유하게 식별합니다. 예를 들어 이메일 주소가 사용자 이름 및 고유 식별자 모두인 경우 값을 *user.mail*로 설정합니다.

3. **이름 식별자 값**을 수정하려면 **이름 식별자 값** 필드에 대한 **편집** 아이콘(연필 모양)을 선택합니다. 필요에 따라 식별자 형식과 원본을 적절하게 변경합니다. 자세한 내용은 [NameId 편집](../develop/active-directory-saml-claims-customization.md#editing-nameid)을 참조하세요. 완료되면 변경 내용을 저장합니다. 
 
4. 그룹 클레임을 구성하려면 **클레임에서 반환되는 그룹** 필드에 대한 **편집** 아이콘을 선택합니다. 자세한 내용은 [그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.

5. 클레임을 추가하려면 페이지의 위쪽에서 **새 클레임 추가**를 선택합니다. **이름**을 입력하고 적절한 원본을 선택합니다. **특성** 원본을 선택하는 경우 사용하려는 **원본 특성**을 선택해야 합니다. **번역** 원본을 선택하는 경우 사용하려는 **변환** 및 **매개 변수 1**을 선택해야 합니다. 자세한 내용은 [애플리케이션 관련 클레임 추가](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)를 참조하세요. 완료되면 변경 내용을 저장합니다. 

6. **저장**을 선택합니다. 테이블에 새 클레임이 표시됩니다.

   > [!NOTE]
   > Azure AD에서 애플리케이션에 대한 SAML 토큰을 사용자 지정하는 다른 방법은 다음 리소스를 참조하세요.
   >- Azure Portal를 통해 사용자 지정 역할을 만들려면 [역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)을 참조하세요.
   >- PowerShell을 통해 클레임을 사용자 지정하려면 [클레임 사용자 지정 - PowerShell](../develop/active-directory-claims-mapping.md)을 참조하세요.
   >- 애플리케이션에 대한 선택적 클레임을 구성하도록 애플리케이션 매니페스트를 수정하려면 [선택적 클레임 구성](../develop/active-directory-optional-claims.md)을 참조하세요.
   >- 새로 고침 토큰, 액세스 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정하려면 [토큰 수명 구성](../develop/active-directory-configurable-token-lifetimes.md)을 참조하세요. 또는 Azure AD 조건부 액세스를 통해 인증 세션을 제한하려면 [인증 세션 관리 기능](https://go.microsoft.com/fwlink/?linkid=2083106)을 참조하세요.

## <a name="step-3-manage-the-saml-signing-certificate"></a>3단계. SAML 서명 인증서 관리

Azure AD는 인증서를 사용하여 애플리케이션에 보내는 SAML 토큰에 서명합니다. Azure AD 및 애플리케이션 간 신뢰를 설정하려면 이 인증서가 필요합니다. 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 자세한 내용은 [페더레이션 Single Sign-On에 대 한 인증서 관리](manage-certificates-for-federated-single-sign-on.md) 및 [SAML 토큰의 고급 인증서 서명 옵션](certificate-signing-options.md)을 참조 하세요.

Azure AD에서 활성 인증서를 사용 하는 기본 **Single Sign-on 설정 SAML** 페이지에서 바로 Base64 또는 원시 형식으로 다운로드할 수 있습니다. 또는 응용 프로그램 메타 데이터 XML 파일을 다운로드 하거나 앱 페더레이션 메타 데이터 URL을 사용 하 여 활성 인증서를 가져올 수 있습니다. 인증서 (활성 또는 비활성)를 보거나 만들거나 다운로드 하려면 다음 단계를 수행 합니다.

1. **SAML 서명 인증서** 섹션으로 이동합니다. 

   ![3 단계 SAML 서명 인증서 관리](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. 인증서에 다음이 있는지 확인합니다.

   - *원하는 만료 날짜입니다.* 만료 날짜는 최대 3 년 이후까지 구성할 수 있습니다.
   - *원하는 인증서의 활성 상태입니다.* 상태가 **비활성**이면 상태를 **활성**으로 변경 합니다. 상태를 변경 하려면 원하는 인증서의 행을 마우스 오른쪽 단추로 클릭 하 고 **인증서를 활성 상태로 만들기**를 선택 합니다.
   - *올바른 서명 옵션 및 알고리즘입니다.*
   - *올바른 알림 전자 메일 주소입니다.* 활성 인증서가 만료 날짜에 가까워지면 Azure AD는이 필드에 구성 된 전자 메일 주소로 알림을 보냅니다.

2. 인증서를 다운로드 하려면 Base64 형식, 원시 형식 또는 페더레이션 메타 데이터 XML에 대 한 옵션 중 하나를 선택 합니다. 또한 Azure AD는 **형식의 애플리케이션과 관련된 메타데이터에 액세스할 수 있는**앱 페더레이션 메타데이터 URL`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`을 제공합니다.

3. 인증서를 관리, 만들기 또는 가져오려면 **SAML 서명 인증서** 섹션의 오른쪽 위 모퉁이에 있는 **편집** 아이콘 (연필)을 선택 합니다.

   ![SAML 서명 인증서](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   다음 작업 중 하나를 수행 합니다.

   - 새 인증서를 만들려면 **새 인증서**, **만료 날짜**, **저장**을 차례로 선택합니다. 인증서를 활성화하려면 상황에 맞는 메뉴( **...** ), **인증서를 활성 상태로 만들기**를 차례로 선택합니다.
   - 프라이빗 키 및 pfx 자격 증명을 사용하여 인증서를 업로드하려면 **인증서 가져오기**를 선택하여 해당 인증서로 이동합니다. **PFX 암호**를 입력한 다음, **추가**를 선택합니다.  
   - 고급 인증서 서명 옵션을 구성하려면 다음 옵션을 사용합니다. 이러한 옵션에 대한 설명은 [고급 인증서 서명 옵션](certificate-signing-options.md) 문서를 참조하세요.
      - **서명 옵션** 드롭다운 목록에서 **SAML 응답 서명**, **SAML 어설션 서명** 또는 **SAML 응답 및 어설션 서명**을 선택합니다.
      - **서명 알고리즘** 드롭다운 목록에서 **SHA-1** 또는 **SHA-256**을 선택합니다.
   - 활성 인증서의 만료 날짜가 가까워질 때 추가 사용자에게 알리려면 **알림 이메일 주소** 필드에서 이메일 주소를 입력합니다.

4. 변경한 경우 **SAML 서명 인증서** 섹션의 위쪽에서 **저장** 을 선택 합니다. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4단계. Azure AD를 사용하도록 애플리케이션 설정

**\<applicationName> 설정** 섹션에는 애플리케이션에서 구성해야 하는 값이 나열되어 있으므로 Azure AD를 SAML ID 공급자로 사용합니다. 필요한 값은 애플리케이션에 따라 달라집니다. 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 설명서를 찾으려면 **응용 프로그램 이름 설정 \<>** 제목으로 이동 하 고 단계별 **지침 보기**를 선택 합니다. 설명서는 **로그온 구성** 페이지에 표시 됩니다. 이 페이지에서는 **응용 프로그램 이름 \<설정 >** 제목의 **로그인 URL**, **Azure AD 식별자**및 **로그 아웃 url** 값을 입력 하는 방법을 안내 합니다.

1. **\<applicationName> 설정** 섹션까지 아래로 스크롤합니다. 
   
   ![4 단계 응용 프로그램 설정](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. 필요에 따라 이 섹션의 각 행에서 값을 복사하고, 해당 값을 애플리케이션에 추가하기 위한 애플리케이션 관련 지침을 따릅니다. 갤러리 앱의 경우 **단계별 지침 보기**를 선택하여 설명서를 볼 수 있습니다. 
   - **로그인 URL** 및 **로그아웃 URL** 값은 모두 동일한 엔드포인트로 확인됩니다. 이 엔드포인트는 Azure AD 인스턴스에 대한 SAML 요청 처리 엔드포인트입니다. 
   - **Azure AD 식별자**는 애플리케이션에 발급된 SAML 토큰의 **Issuer**(발급자) 값입니다.
2. 모든 값을 해당 필드에 붙여넣은 다음, **저장**을 선택합니다.

## <a name="step-5-validate-single-sign-on"></a>5단계. Single Sign-On 유효성 검사

Azure AD를 SAML 기반 id 공급자로 사용 하도록 응용 프로그램을 구성한 후에는 설정을 테스트 하 여 Single Sign-On 계정에 대해 작동 하는지 확인할 수 있습니다. 

2. **<applicationName>을(를) 사용하여 Single Sign-On 유효성 검사** 섹션으로 스크롤합니다.

   ![5 단계 Single Sign-On 유효성 검사](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. **유효성 검사**를 선택합니다. 테스트 옵션이 표시됩니다.

4. **현재 사용자로 로그인**을 선택합니다. 

로그온에 성공하면 사용자 및 그룹을 SAML 애플리케이션에 할당할 준비가 된 것입니다.
오류 메시지가 표시되면 다음 단계를 수행합니다.

1. 세부 정보를 **어떤 오류인 것 같습니까?** 상자에 복사하고 붙여넣습니다.

    ![해결 지침 가져오기](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **해결 지침 가져오기**를 선택합니다. 근본 원인 및 해결 지침이 표시됩니다.  이 예제에서 사용자는 애플리케이션에 할당되지 않았습니다.

3. 해결 지침을 읽은 다음, 가능한 경우 문제를 해결합니다.

4. 성공적으로 완료될 때까지 테스트를 다시 실행합니다.

자세한 내용은 [Azure Active Directory의 응용 프로그램에 대 한 SAML 기반 Single Sign-On 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
