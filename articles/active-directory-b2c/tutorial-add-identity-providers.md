---
title: '자습서: 앱에 ID 공급자 추가'
titleSuffix: Azure AD B2C
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C의 애플리케이션에 ID 공급자를 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f9abf9105da773ec5f8321c0f8e70e20516618c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922152"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가

애플리케이션에서 사용자가 다른 ID 공급자로 로그인하도록 허용할 수 있습니다. ‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다. Azure Portal을 사용하여 Azure AD B2C(Azure Active Directory B2C)에서 지원하는 ID 공급자를 [사용자 흐름](user-flow-overview.md)에 추가할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * ID 공급자 애플리케이션 만들기
> * Facebook 및 Azure Active Directory에 있는 테넌트에 ID 공급자 추가
> * 사용자 흐름에 ID 공급자 추가

일반적으로 애플리케이션에서 하나의 ID 공급자만 사용하지만, ID 공급자를 더 추가할 수 있습니다. 이 자습서에서는 Azure AD ID 공급자 및 Facebook ID 공급자를 애플리케이션에 추가하는 방법을 보여 줍니다. 애플리케이션에 두 ID 공급자를 추가하는 것은 선택 사항입니다. [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md) 또는 [Twitter](identity-provider-twitter.md)와 같은 다른 ID 공급자를 추가할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).

## <a name="create-applications"></a>애플리케이션 만들기

ID 공급자 애플리케이션은 Azure AD B2C 테넌트와 통신할 수 있도록 하는 식별자와 키를 제공합니다. 이 자습서 섹션에서는 테넌트에 ID 공급자를 추가하기 위해 식별자와 키를 가져오는 Azure AD 애플리케이션 및 Facebook 애플리케이션을 만듭니다. ID 공급자 중 하나만 추가하는 경우 해당 공급자에 대한 애플리케이션만 만들면 됩니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

Azure AD에서 사용자 로그인을 허용하려면 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예들 들어 `Azure AD B2C App`입니다.
1. 이 애플리케이션에서 **이 조직 디렉터리의 계정만** 선택을 수락합니다.
1. **리디렉션 URI**의 경우, **Web**의 값을 수락하고 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예들 들어 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`입니다.

    이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.

1. **등록**을 선택한 다음, 이후 단계에서 사용하는 **애플리케이션(클라이언트) ID**를 기록합니다.
1. 애플리케이션 메뉴의 **관리**에서 **인증서 및 비밀**을 선택한 다음, **새 클라이언트 암호**를 선택합니다.
1. 클라이언트 암호에 대한 **설명**을 입력합니다. 예들 들어 `Azure AD B2C App Secret`입니다.
1. 만료 기간을 선택합니다. 이 애플리케이션의 경우 **1년** 선택을 수락합니다.
1. **추가**를 선택한 다음, 이후 단계에서 사용하는 새 클라이언트 암호 값을 기록합니다.

### <a name="create-a-facebook-application"></a>Facebook 애플리케이션 만들기

Azure AD B2C의 ID 공급자로 Facebook 계정을 사용하려면 Facebook에서 애플리케이션을 만들어야 합니다. Facebook 계정이 없는 경우 [https://www.facebook.com/](https://www.facebook.com/)에서 얻을 수 있습니다.

1. Facebook 계정 자격 증명으로 [개발자용 Facebook](https://developers.facebook.com/)에 로그인합니다.
1. 아직 등록하지 않은 경우 Facebook 개발자로 등록해야 합니다. 이를 수행하려면 페이지의 오른쪽 위에서 **시작**을 선택하고 Facebook의 정책에 동의한 후 등록 단계를 완료합니다.
1. **내 앱**을 선택한 다음, **앱 만들기**를 선택합니다.
1. **표시 이름** 및 유효한 **연락처 전자 메일**을 제공합니다.
1. **앱 ID 만들기**를 클릭합니다. Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.
1. **설정** > **기본**을 선택합니다.
1. `Business and Pages` 등의 **범주**를 선택합니다. 이 값은 Facebook의 경우 필수이지만 Azure AD B2C에서는 사용되지 않습니다.
1. 페이지의 맨 아래에서 **플랫폼 추가**를 선택한 후 **웹 사이트**를 선택합니다.
1. **사이트 URL**에 `https://your-tenant-name.b2clogin.com/`을 입력합니다. 여기서 `your-tenant-name`은 실제 테넌트의 이름으로 바꾸세요.
1. **개인 정보 취급 방침 URL**의 URL(예: `http://www.contoso.com/`)을 입력합니다. 개인정보처리방침 URL은 애플리케이션에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
1. **변경 내용 저장**을 선택합니다.
1. 페이지의 맨 위에서 **앱 ID** 값을 기록합니다.
1. **앱 비밀** 옆에 있는 **표시**를 선택하고 해당 값을 기록합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 앱 ID 및 앱 비밀을 모두 사용합니다. **앱 비밀**은 안전하게 저장해야 하는 중요한 보안 자격 증명입니다.
1. **제품** 옆에 있는 더하기 기호를 선택한 다음, **Facebook 로그인**에 있는 **설정**을 선택합니다.
1. 왼쪽 메뉴의 **Facebook 로그인**에서 **설정**을 선택합니다.
1. **유효한 OAuth 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. 페이지 아래쪽에 있는 **변경 내용 저장**을 선택합니다.
1. Azure AD B2C에서 Facebook 애플리케이션을 사용할 수 있도록 하려면 페이지 오른쪽 위에 있는 **상태** 선택기를 클릭한 후 **설정**하여 공용 애플리케이션으로 만들고 **확인**을 클릭합니다. 이때 상태가 **개발**에서 **라이브**로 변경됩니다.

## <a name="add-the-identity-providers"></a>ID 공급자 추가

추가하려는 ID 공급자에 대한 애플리케이션을 만든 후 ID 공급자를 테넌트에 추가합니다.

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory ID 공급자 추가

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **ID 공급자**를 선택한 다음, **새 OpenID Connect 공급자**를 선택합니다.
1. **이름**을 입력합니다. 예를 들어 *Contoso Azure AD*를 입력합니다.
1. **메타데이터 URL**에 대해 다음 URL을 입력합니다. 여기서 `your-AD-tenant-domain`은 Azure AD 테넌트의 도메인 이름으로 바꿉니다.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    예들 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`입니다.

1. **Client ID**에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호**에 입력합니다.
1. **범위**, **응답 유형** 및 **응답 모드**에 대한 기본값을 그대로 둡니다.
1. 선택적으로 **Domain_hint**의 값을 입력합니다. 예를 들어 *ContosoAD*입니다. [도메인 힌트](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)는 애플리케이션의 인증 요청에 포함되는 지시문입니다. 페더레이션된 IdP 로그인 페이지로 사용자를 빠르게 보내는 데 사용할 수 있습니다. 또는 다중 테넌트 애플리케이션에서 테넌트에 대한 브랜딩 Azure AD 로그인 페이지로 사용자를 바로 보내는 데 사용될 수 있습니다.
1. **ID 공급자 클레임 매핑**에서 다음 클레임 매핑 값을 입력합니다.

    * **사용자 ID**: *oid*
    * **표시 이름**: *name*
    * **지정된 이름**: *given_name*
    * **성**: *family_name*
    * **이메일**: *unique_name*

1. **저장**을 선택합니다.

### <a name="add-the-facebook-identity-provider"></a>Facebook ID 공급자 추가

1. **ID 공급자**를 선택한 다음, **Facebook**을 선택합니다.
1. **이름**을 입력합니다. 예를 들어 *Facebook*입니다.
1. **클라이언트 ID**에 대해 이전에 만든 Facebook 애플리케이션의 앱 ID를 입력합니다.
1. **클라이언트 암호**에는 기록했던 앱 비밀을 입력합니다.
1. **저장**을 선택합니다.

## <a name="update-the-user-flow"></a>사용자 흐름 업데이트

필수 조건의 일부로 완료한 자습서에서 등록 및 로그인을 위한 사용자 흐름(*B2C_1_signupsignin1*)을 만들었습니다. 이 섹션에서는 *B2C_1_signupsignin1* 사용자 흐름에 ID 공급자를 추가합니다.

1. **사용자 흐름**, *B2C_1_signupsignin1* 사용자 흐름을 차례로 선택합니다.
2. **ID 공급자**를 선택한 다음, 추가한 **Facebook** 및 **Contoso Azure AD** ID 공급자를 선택합니다.
3. **저장**을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
1. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행**을 선택한 다음, 이전에 추가한 ID 공급자로 로그인합니다.
1. 추가한 다른 ID 공급자에 대해 1-3단계를 반복합니다.

로그인 작업이 성공하면 다음과 같이 디코딩된 토큰을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ID 공급자 애플리케이션 만들기
> * 테넌트에 ID 공급자 추가
> * 사용자 흐름에 ID 공급자 추가

다음으로, 애플리케이션에서 ID 환경의 일부로 사용자에게 표시되는 페이지의 UI를 사용자 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md)
