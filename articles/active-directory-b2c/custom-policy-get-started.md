---
title: 사용자 지정 정책 시작
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 시작하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54d92da469625a3b81d0027558ec14166d916b80
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163189"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[사용자 지정 정책](custom-policy-overview.md)은 Azure AD B2C(Azure Active Directory B2C) 테넌트의 동작을 정의하는 구성 파일입니다. 이 문서에서는 전자 메일 주소와 암호를 사용한 로콜 계정 등록 또는 로그인을 지원하는 사용자 지정 정책을 만듭니다. ID 공급자를 추가하기 위한 환경도 준비합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.
- 생성된 테넌트에 [애플리케이션을 등록](tutorial-register-applications.md)하여 Azure AD B2C와 통신할 수 있도록 합니다.
- [Facebook 계정을 사용한 가입 및 로그인 설정](identity-provider-facebook.md)의 단계를 완료하여 Facebook 애플리케이션을 구성합니다. Facebook 애플리케이션은 사용자 지정 정책을 사용하는 데 필요하지 않지만 이 연습에서 사용자 지정 정책에 소셜 로그인을 사용하도록 설정하는 방법을 보여 주기 위해 사용되었습니다.

## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색하고 선택합니다.
1. 개요 페이지의 **정책**에서 **Identity Experience Framework**를 선택합니다.

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

Facebook 애플리케이션의 [앱 비밀](identity-provider-facebook.md)을 정책 키로 추가합니다. 이 문서의 사전 요구 사항에 따라 만든 애플리케이션의 앱 비밀을 사용할 수 있습니다.

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. **이름**에 `FacebookSecret`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀**에 Facebook 애플리케이션의 *앱 비밀*(developers.facebook.com)을 입력합니다. 이 값은 애플리케이션 ID가 아닌 비밀입니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework 애플리케이션 등록

Azure AD B2C를 사용하려면 로컬 계정으로 사용자를 가입 및 로그인시키는 데 사용되는 IdentityExperienceFramework 앱에 대한 위임된 권한을 가진 *IdentityExperienceFramework*(웹 API) 및 *ProxyIdentityExperienceFramework*(네이티브 앱)라는 두 개의 애플리케이션을 등록해야 합니다. 사용자는 이메일 주소 또는 사용자 이름과 암호로 가입하고 테넌트에 등록된 애플리케이션에 액세스하여 "로컬 계정"을 만들 수 있습니다. 로컬 계정은 Azure AD B2C 테넌트에만 존재합니다.

Azure AD B2C 테넌트에 이러한 두 애플리케이션을 한 번만 등록해야 합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 애플리케이션 등록

Azure AD B2C 테 넌 트에 응용 프로그램을 등록 하려면 **앱 등록** 환경을 사용할 수 있습니다.

1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택합니다.
1. **리디렉션 URI**에서 **웹**을 선택한 후 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로, 범위를 추가하여 API를 노출합니다.

1. 왼쪽 메뉴의 **관리**에서 **API 노출**을 선택 합니다.
1. **범위 추가**를 선택한 다음, **저장 후 계속**을 선택하여 기본 애플리케이션 ID URI를 적용합니다.
1. Azure AD B2C 테넌트에서 사용자 지정 정책을 실행하도록 허용하는 범위를 만들도록 다음 값을 입력합니다.
    * **범위 이름**: `user_impersonation`
    * **관리자 동의 표시 이름**: `Access IdentityExperienceFramework`
    * **관리자 동의 설명**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **범위 추가**를 선택합니다.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 애플리케이션 등록

1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택합니다.
1. **리디렉션 URI** 아래에서 드롭다운을 사용하여 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
1. **리디렉션 URI**에 `myapp://auth`를 입력합니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로, 애플리케이션을 퍼블릭 클라이언트로 처리하도록 지정합니다.

1. 왼쪽 메뉴의 **관리** 아래에서 **인증**을 선택합니다.
1. **고급 설정**에서 **애플리케이션을 퍼블릭 클라이언트로 처리**(**예** 선택)를 사용하도록 설정합니다. 애플리케이션 매니페스트에 **"allowPublicClient": true**가 설정되었는지 확인합니다. 
1. **저장**을 선택합니다.

이제 앞서 *IdentityExperienceFramework* 등록 시 제공한 API 범위에 권한을 부여합니다.

1. 왼쪽 메뉴의 **관리**에서 **API 권한**을 선택 합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. **내 API** 탭을 선택한 후 **IdentityExperienceFramework** 애플리케이션을 선택합니다.
1. **권한**에서 이전에 정의한 **user_impersonation** 범위를 선택합니다.
1. **권한 추가**를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용**을 선택합니다.
1. 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **수락**을 선택합니다.
1. **새로 고침**을 선택 하 고 "다음에 대해 권한 부여 ..."를 확인 합니다. 범위 offline_access, openid connect 및 user_impersonation에 대 한 **상태** 아래에 나타납니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

* * *

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

사용자 지정 정책은 기술 프로필 및 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 세트입니다. 신속한 진행을 위해 몇 가지 미리 작성된 정책이 포함된 시작 팩이 제공됩니다. 이러한 각 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.

- **LocalAccounts** - 로컬 계정만 사용할 수 있습니다.
- **SocialAccounts** - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
- **SocialAndLocalAccounts** - 로컬 계정과 소셜 계정을 모두 사용할 수 있습니다.
- **SocialAndLocalAccountsWithMFA** - 소셜, 로컬 및 다단계 인증 옵션을 사용할 수 있습니다.

시작 팩 각각에는 다음이 포함됩니다.

- **기본 파일** - 몇 가지 수정이 필요합니다. 예제: *TrustFrameworkBase.xml*
- **확장 파일** - 이 파일은 대부분의 구성이 변경되었습니다. 예제: *TrustFrameworkExtensions.xml*
- **신뢰 당사자 파일** - 애플리케이션에서 호출하는 작업 관련 파일입니다. 예제: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

이 문서에서는 **SocialAndLocalAccounts** 시작 팩에서 XML 사용자 지정 정책 파일을 편집합니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).

### <a name="get-the-starter-pack"></a>시작 팩 가져오기

GitHub에서 사용자 지정 정책 시작 팩을 가져온 다음, Azure AD B2C 테넌트 이름으로 SocialAndLocalAccounts 시작 팩의 XML 파일을 업데이트합니다.

1. [.zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)하거나 리포지토리를 복제합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** 디렉터리의 모든 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다.

    예를 들어 B2C 테넌트의 이름이 *contosotenant*인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosotenant.onmicrosoft.com`이 됩니다.

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 애플리케이션 ID 추가

확장 파일 *TrustFrameworkExtensions.xml*에 애플리케이션 ID를 추가합니다.

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 을 열고 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
1. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 IdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.
1. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 ProxyIdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.
1. 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. Azure Portal에서 B2C 테넌트의 **Identity Experience Framework** 메뉴 항목을 선택합니다.
1. **사용자 지정 정책 업로드**를 선택합니다.
1. 이 순서로 정책 파일을 업로드합니다.
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

파일을 업로드하면 Azure에서 각 파일에 `B2C_1A_` 접두사를 추가합니다.

> [!TIP]
> XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 디렉터리에 있는 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 스키마에 대해 파일의 유효성을 검사합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**을 선택합니다.
1. 사용자 지정 정책의 개요 페이지에 있는 **애플리케이션 선택**에서 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다.
1. **회신 URL** `https://jwt.ms`인지 확인합니다.
1. **지금 실행**을 선택합니다.
1. 이메일 주소를 사용하여 가입합니다.
1. **지금 실행**을 다시 선택합니다.
1. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

[사전 요구 사항](#prerequisites)에 설명된 것처럼, Facebook 은 사용자 지정 정책을 사용하는 데 필요하지 *않지만* 여기서는 사용자 지정 정책에서 페더레이션된 소셜 로그인을 사용하도록 설정하는 방법을 보여 주기 위해 사용되었습니다.

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 파일에서 `client_id`의 값을 Facebook 애플리케이션 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**을 선택합니다.
1. **지금 실행**을 선택하고 Facebook을 선택하여 Facebook에 로그인하고 사용자 지정 정책을 테스트합니다.

## <a name="next-steps"></a>다음 단계

다음으로, Azure AD(Azure Active Directory)를 ID 공급자로 추가해 봅니다. 이 시작 가이드에서 사용된 기본 파일에는 이미 다른 ID 공급자(예: Azure AD)를 추가하는 데 필요한 내용 중 일부가 포함되어 있습니다. Id 공급자로 Azure AD를 설정 하는 방법에 대 한 자세한 내용은 [Active Directory B2C 사용자 지정 정책을 사용 하 여 Azure Active Directory 계정으로 등록 및 로그인 설정](identity-provider-azure-ad-single-tenant-custom.md)을 참조 하세요. 

사용자 지정 정책을 사용 하 여 ISV 통합을 구현 하는 방법에 대 한 자세한 내용은 [파트너 갤러리](partner-gallery.md) 를 참조 하세요. 
