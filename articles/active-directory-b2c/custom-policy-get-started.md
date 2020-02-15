---
title: 사용자 지정 정책 시작-Azure Active Directory B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 시작 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5a0e5846dd541e4997c271aee180b3790efa16e9
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114040"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[사용자 지정 정책은](custom-policy-overview.md) Azure Active Directory B2C (Azure AD B2C) 테 넌 트의 동작을 정의 하는 구성 파일입니다. 이 문서에서는 전자 메일 주소와 암호를 사용한 로콜 계정 등록 또는 로그인을 지원하는 사용자 지정 정책을 만듭니다. ID 공급자를 추가하기 위한 환경도 준비합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 아직 없는 경우 Azure 구독에 연결 된 [Azure AD B2C 테 넌 트를 만듭니다](tutorial-create-tenant.md) .
- Azure AD B2C와 통신할 수 있도록 만든 테 넌 트에 [응용 프로그램을 등록](tutorial-register-applications.md) 합니다.
- Facebook [계정을 사용 하 여 등록 및 로그인 설정](identity-provider-facebook.md) 의 단계를 완료 하 여 facebook 응용 프로그램을 구성 합니다.

## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.
1. 개요 페이지의 **정책**에서 **Id 경험 프레임 워크**를 선택 합니다.

### <a name="create-the-signing-key"></a>서명 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Generate`을 선택합니다.
1. **이름**에 `TokenSigningKeyContainer`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형**으로는 **RSA**를 선택합니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

### <a name="create-the-encryption-key"></a>암호화 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Generate`을 선택합니다.
1. **이름**에 `TokenEncryptionKeyContainer`를 입력합니다. `B2C_1A`_ 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형**으로는 **RSA**를 선택합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="create-the-facebook-key"></a>Facebook 키 만들기

Facebook 응용 프로그램의 [앱 암호](identity-provider-facebook.md) 를 정책 키로 추가 합니다. 이 문서의 필수 구성 요소에 따라 만든 응용 프로그램의 앱 암호를 사용할 수 있습니다.

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. **이름**에 `FacebookSecret`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀**에서 Developers.facebook.com의 Facebook 응용 프로그램의 *앱 암호* 를 입력 합니다. 이 값은 응용 프로그램 ID가 아닌 암호입니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework 애플리케이션 등록

Azure AD B2C를 사용 하려면 로컬 계정 ( *IdentityExperienceFramework*, web API 및 *ProxyIdentityExperienceFramework*)을 사용 하 여 사용자를 등록 하 고 로그인 하는 데 사용 하는 두 개의 응용 프로그램을 등록 하 고 IdentityExperienceFramework 앱에 대 한 위임 된 권한이 있는 네이티브 앱을 등록 해야 합니다. 사용자는 전자 메일 주소 또는 사용자 이름과 암호를 사용 하 여 등록 하 여 테 넌 트에 등록 된 응용 프로그램에 액세스 하 여 "로컬 계정"을 만들 수 있습니다. 로컬 계정은 Azure AD B2C 테 넌 트에만 존재 합니다.

Azure AD B2C 테 넌 트에서 이러한 두 응용 프로그램을 한 번만 등록 해야 합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 애플리케이션 등록

Azure AD B2C 테 넌 트에 응용 프로그램을 등록 하려면 **앱 등록 (레거시)** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. **Azure Active Directory** 개요 메뉴의 **관리**에서 **앱 등록 (레거시)** 를 선택 합니다.
1. **새 애플리케이션 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **애플리케이션 종류**로 **웹앱/API**를 선택합니다.
1. **로그온 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다. 이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.
1. **만들기**를 선택합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **지원 되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택 합니다.
1. **URI 리디렉션**에서 **웹**을 선택 하 고 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`를 입력 합니다. 여기서 `your-tenant-name`는 Azure AD B2C 테 넌 트 도메인 이름입니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로, 범위를 추가 하 여 API를 노출 합니다.

1. **관리**에서 **API 표시**를 선택합니다.
1. **범위 추가**를 선택한 다음, **저장을 선택 하 고 계속** 을 선택 하 여 기본 응용 프로그램 ID URI를 적용 합니다.
1. Azure AD B2C 테 넌 트에서 사용자 지정 정책을 실행 하도록 허용 하는 범위를 만들려면 다음 값을 입력 합니다.
    * **범위 이름**: `user_impersonation`
    * **관리자 동의 표시 이름**: `Access IdentityExperienceFramework`
    * **관리자 동의 설명**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **범위 추가**를 선택합니다.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 애플리케이션 등록

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. **앱 등록 (레거시)** 에서 **새 응용 프로그램 등록**을 선택 합니다.
1. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **애플리케이션 종류**로 **네이티브**를 선택합니다.
1. **리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`는 Azure AD B2C 테넌트입니다.
1. **만들기**를 선택합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.
1. **설정**을 선택한 다음 **필요한 권한**을 선택 하 고 **추가**를 선택 합니다.
1. **API 선택**을 선택 하 고 **IdentityExperienceFramework**를 검색 한 **다음 선택을 클릭 합니다.**
1. **IdentityExperienceFramework 액세스** 옆의 확인란을 선택한 다음 **선택**, **완료**를 차례로 클릭합니다.
1. **권한 부여**를 선택 하 고 **예**를 선택 하 여 확인 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **지원 되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택 합니다.
1. **리디렉션 URI** 아래에서 드롭다운을 사용하여 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
1. **리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`는 Azure AD B2C 테넌트입니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로 응용 프로그램을 공용 클라이언트로 처리 하도록 지정 합니다.

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.** (표시되는 경우)를 선택합니다.
1. **고급 설정**에서 **응용 프로그램을 공용 클라이언트로 처리** 를 사용 하도록 설정 합니다 ( **예**선택).
1. **저장**을 선택합니다.

이제 *IdentityExperienceFramework* 등록에서 앞서 제공한 API 범위에 사용 권한을 부여 합니다.

1. **관리** 아래에서 **API 권한**을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. **내 api** 탭을 선택한 다음 **IdentityExperienceFramework** 응용 프로그램을 선택 합니다.
1. **사용 권한**아래에서 이전에 정의한 **user_impersonation** 범위를 선택 합니다.
1. **권한 추가**를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용**을 선택합니다.
1. 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **수락**을 선택합니다.
1. **새로 고침**을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

* * *

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

사용자 지정 정책은 기술 프로필 및 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 신속 하 게 진행 하기 위해 몇 가지 미리 작성 된 정책과 함께 시작 팩을 제공 합니다. 이러한 각 시작 팩에는 설명 된 시나리오를 수행 하는 데 필요한 최소 수의 기술 프로필 및 사용자 경험 포함 되어 있습니다.

- **Localaccounts** -로컬 계정만 사용 하도록 설정 합니다.
- 사회 보장 **계정** -소셜 (또는 페더레이션) 계정만 사용할 수 있습니다.
- **Socialandlocalaccounts** -로컬 계정과 소셜 계정을 모두 사용할 수 있습니다.
- **SocialAndLocalAccountsWithMFA** -소셜, 로컬 및 multi-factor authentication 옵션을 사용 하도록 설정 합니다.

시작 팩 각각에는 다음이 포함됩니다.

- **기본 파일** -기본 파일을 수정 해야 합니다. 예: *trustframeworkbase.xml*
- **확장 파일** -이 파일은 대부분의 구성이 변경 되는 위치입니다. 예: *trustframeworkextensions.xml*
- **신뢰 당사자 파일** -응용 프로그램에서 호출 하는 작업 관련 파일입니다. 예: *Signu.xml*, *profileedit*.xml, *passwordreset .xml*

이 문서에서는 **Socialandlocalaccounts** 시작 팩에서 XML 사용자 지정 정책 파일을 편집 합니다. XML 편집기가 필요한 경우 간단한 크로스 플랫폼 편집기를 [Visual Studio Code](https://code.visualstudio.com/download)사용해 보세요.

### <a name="get-the-starter-pack"></a>시작 팩 가져오기

GitHub에서 사용자 지정 정책 시작 팩을 가져온 다음, Azure AD B2C 테 넌 트 이름으로 SocialAndLocalAccounts 스타터 pack의 XML 파일을 업데이트 합니다.

1. [.Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 하거나 리포지토리를 복제 합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 모든 파일의 **Socialandlocalaccounts** 디렉터리에서 `yourtenant` 문자열을 Azure AD B2C 테 넌 트의 이름으로 바꿉니다.

    예를 들어 B2C 테 넌 트의 이름이 *contosotenant*인 경우 `yourtenant.onmicrosoft.com`의 모든 인스턴스가 `contosotenant.onmicrosoft.com`됩니다.

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 애플리케이션 ID 추가

확장 파일 *TrustFrameworkExtensions.xml*에 애플리케이션 ID를 추가합니다.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 를 열고 요소 `<TechnicalProfile Id="login-NonInteractive">`찾습니다.
1. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 앞에서 만든 IdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.
1. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 앞에서 만든 ProxyIdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.
1. 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. Azure Portal에서 B2C 테 넌 트에서 **Id 경험 프레임 워크** 메뉴 항목을 선택 합니다.
1. **사용자 지정 정책 업로드**를 선택 합니다.
1. 이 순서로 정책 파일을 업로드 합니다.
    1. *Trustframeworkbase.xml*
    1. *Trustframeworkextensions.xml*
    1. *Signu.xml*
    1. *ProfileEdit .xml*
    1. *PasswordReset .xml*

파일을 업로드 하면 Azure에서 각에 `B2C_1A_` 접두사를 추가 합니다.

> [!TIP]
> XML 편집기에서 유효성 검사를 지 원하는 경우 시작 팩의 루트 디렉터리에 있는 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 스키마에 대해 파일의 유효성을 검사 합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**를 선택 합니다.
1. 사용자 지정 정책의 개요 페이지에서 **응용 프로그램 선택** 에 대해 이전에 등록 한 *webapp1* 이라는 웹 응용 프로그램을 선택 합니다.
1. **회신 URL** 이 `https://jwt.ms`인지 확인 합니다.
1. **지금 실행**을 선택합니다.
1. 전자 메일 주소를 사용 하 여 등록 합니다.
1. **지금 실행** 을 선택 합니다.
1. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 파일에서 `client_id` 값을 Facebook 응용 프로그램 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**를 선택 합니다.
1. **지금 실행** 을 선택 하 고 facebook을 선택 하 여 facebook에 로그인 하 고 사용자 지정 정책을 테스트 합니다.

## <a name="next-steps"></a>다음 단계

다음으로, id 공급자로 Azure Active Directory (Azure AD)를 추가 해 봅니다. 이 시작 가이드에 사용 된 기본 파일에는 Azure AD와 같은 다른 id 공급자를 추가 하는 데 필요한 일부 콘텐츠가 이미 포함 되어 있습니다.

및 id 공급자로 Azure AD를 설정 하는 방법에 대 한 자세한 내용은 [Active Directory B2C 사용자 지정 정책을 사용 하 여 Azure Active Directory 계정으로 등록 및 로그인 설정](identity-provider-azure-ad-single-tenant-custom.md)을 참조 하세요.
