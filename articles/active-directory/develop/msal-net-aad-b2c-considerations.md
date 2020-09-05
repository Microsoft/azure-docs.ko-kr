---
title: Azure AD B2C 및 MSAL.NET
titleSuffix: Microsoft identity platform
description: .NET 용 Microsoft Authentication Library (MSAL.NET)와 함께 Azure AD B2C를 사용할 때의 고려 사항
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: ed3e9da628ab779ab47673fa2ce728c5c25539be
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166436"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET를 사용 하 여 소셜 id로 사용자 로그인

[Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)를 사용 하 여 소셜 id로 사용자를 로그인 하는 데 MSAL.NET를 사용할 수 있습니다. Azure AD B2C은 정책의 개념을 중심으로 작성 되었습니다. MSAL.NET에서는 정책을 지정 하 여 권한을 제공 하는 것으로 해석 됩니다.

- 공용 클라이언트 응용 프로그램을 인스턴스화하는 경우 정책을 기관 일부로 지정 해야 합니다.
- 정책을 적용 하려는 경우 `AcquireTokenInteractive` 매개 변수를 허용 하는의 재정의를 호출 `authority` 합니다.

이 문서는 MSAL.NET 3.x에 적용 됩니다. MSAL.NET 2.x의 경우 GitHub의 MSAL.NET Wiki에서 [MSAL 2.x에](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) 대 한 자세한 Azure AD B2C을 참조 하세요.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 테 넌 트 및 정책에 대 한 권한

Azure AD B2C의 인증 기관 형식은 다음과 같습니다.`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`-Azure AD B2C 테 넌 트와 호스트의 이름입니다. 예를 들면 *contosob2c.b2clogin.com*입니다.
- `tenant`-Azure AD B2C 테 넌 트의 도메인 이름 또는 디렉터리 (테 넌 트) ID입니다. 예를 들어 *contosob2c.onmicrosoft.com* 또는 GUID가 각각입니다.
- `policyName`-적용할 사용자 흐름 또는 사용자 지정 정책의 이름입니다. 예를 들어 *b2c_1_susi*와 같은 등록/로그인 정책이 있습니다.

Azure AD B2C 기관에 대 한 자세한 내용은 [리디렉션 url을 b2clogin.com로 설정](../../active-directory-b2c/b2clogin.md)을 참조 하세요.

## <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

`WithB2CAuthority()`응용 프로그램 개체를 만들 때를 호출 하 여 권한을 제공 합니다.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>정책을 적용 하는 토큰 획득

공용 클라이언트 응용 프로그램에서 Azure AD B2C로 보호 되는 API에 대 한 토큰을 획득 하려면 권한으로 재정의를 사용 해야 합니다.

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

위의 코드 조각에서 다음을 수행 합니다.

- `policy`Azure AD B2C 사용자 흐름 또는 사용자 지정 정책 (예:)의 이름을 포함 하는 문자열입니다 `PolicySignUpSignIn` .
- `ParentActivityOrWindow`는 Android (활동)에 필요 하며 iOS의 Microsoft Windows 및 UIViewController와 같은 부모 UI를 지 원하는 다른 플랫폼의 경우 선택 사항입니다. UI 대화 상자에 대 한 자세한 내용은 MSAL Wiki의 [Withparentactivityorwindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) 를 참조 하십시오.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`는 지정 된 정책에 대 한 계정을 찾는 메서드입니다. 다음은 그 예입니다. 

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

사용자 흐름 또는 사용자 지정 정책 적용 (예: 사용자가 프로필을 편집 하거나 암호를 다시 설정 하는 등)은 현재를 호출 하 여 수행 됩니다 `AcquireTokenInteractive` . 이러한 두 정책의 경우 반환 된 토큰/인증 결과를 사용 하지 않습니다.

## <a name="profile-edit-policies"></a>프로필 편집 정책

사용자가 소셜 id를 사용 하 여 로그인 한 다음 프로필을 편집할 수 있도록 하려면 프로필 편집 정책 Azure AD B2C 적용 합니다.

이렇게 하려면 `AcquireTokenInteractive` 해당 정책에 대 한 권한을 사용 하 여를 호출 합니다. 사용자가 이미 로그인 되어 있고 활성 쿠키 세션이 있으므로를 사용 `Prompt.NoPrompt` 하 여 계정 선택 대화 상자가 표시 되지 않도록 합니다.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>리소스 소유자 암호 자격 증명 (ROPC)

ROPC 흐름에 대 한 자세한 내용은 [리소스 소유자 암호 자격 증명 부여를 사용 하 여 로그인](v2-oauth-ropc.md)을 참조 하세요.

응용 프로그램에서 사용자의 암호를 입력 하 라는 메시지가 안전 하지 않으므로 ROPC 흐름을 사용 **하지 않는** 것이 좋습니다. 이 문제에 대 한 자세한 내용은 [암호의 증가 하는 문제에 대 한 해결책은 무엇 인가요?를](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)참조 하세요.

ROPC flow에서 사용자 이름/암호를 사용 하 여 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

- 최신 id의 핵심 개념: 공유 암호를 가로챌 수 있으므로 암호를 fished 하거나 재생할 수 있습니다. 정의에 따라 ropc는 암호 없는 흐름과 호환 되지 않습니다.
- MFA를 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
- 사용자는 SSO (Single Sign-On)를 사용할 수 없습니다.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC 흐름 구성

Azure AD B2C 테 넌 트에서 새 사용자 흐름을 만들고 **ROPC를 사용 하 여 로그인** 을 선택 하 여 사용자 흐름에 ropc를 사용 하도록 설정 합니다. 자세한 내용은 [리소스 소유자 암호 자격 증명 흐름 구성](../../active-directory-b2c/configure-ropc.md)을 참조 하세요.

`IPublicClientApplication`메서드를 포함 합니다 `AcquireTokenByUsernamePassword` .

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

이 `AcquireTokenByUsernamePassword` 메서드는 다음 매개 변수를 사용 합니다.

- 액세스 토큰을 가져올 *범위* 입니다.
- *사용자 이름*입니다.
- 사용자의 SecureString *암호* 입니다.

### <a name="limitations-of-the-ropc-flow"></a>ROPC 흐름의 제한 사항

ROPC 흐름은 사용자가 전자 메일 주소 또는 사용자 이름을 사용 하 여 Azure AD B2C에 등록 한 **로컬 계정에만 작동**합니다. Azure AD B2C (Facebook, Google 등)에서 지 원하는 외부 id 공급자에 페더레이션 하는 경우이 흐름이 작동 하지 않습니다.

## <a name="google-auth-and-embedded-webview"></a>Google 인증 및 포함 된 웹 보기

Google을 id 공급자로 사용 하는 경우 Google [에서 포함 된 웹 보기 인증](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)을 허용 하지 않으므로 시스템 브라우저를 사용 하는 것이 좋습니다. 현재는 `login.microsoftonline.com` Google을 사용 하는 신뢰할 수 있는 인증 기관 이며 포함 된 웹 보기를 사용할 수 있습니다. 그러나 `b2clogin.com` 는 Google을 사용 하 여 신뢰할 수 있는 기관이 아니므로 사용자는 인증할 수 없습니다.

변경 사항이 있는 경우이 [문제](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) 에 대 한 업데이트를 제공 합니다.

## <a name="token-caching-in-msalnet"></a>MSAL.NET의 토큰 캐싱

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C의 알려진 문제

MSAL.NET는 [토큰 캐시](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)를 지원 합니다. 토큰 캐싱 키는 IdP (id 공급자)에서 반환 하는 클레임을 기반으로 합니다.

현재 MSAL.NET는 토큰 캐시 키를 작성 하는 데 두 가지 클레임이 필요 합니다.

- `tid`(Azure AD 테 넌 트 ID)
- `preferred_username`

일부 소셜 id 공급자 (Facebook, Google 및 기타)가 Azure AD B2C 반환 하는 토큰으로 반환 하기 때문에 Azure AD B2C 시나리오에서는 이러한 클레임이 모두 누락 될 수 있습니다.

이러한 시나리오의 증상은 `Missing from the token response` `preferred_username` Azure AD B2C에서 발급 한 토큰의 클레임 값에 액세스할 때 MSAL.NET가를 반환 한다는 것입니다. MSAL은의 값을 사용 하 여 `Missing from the token response` `preferred_username` 라이브러리 간에 캐시 간 호환성을 유지 합니다.

### <a name="workarounds"></a>해결 방법

#### <a name="mitigation-for-missing-tenant-id"></a>누락 된 테 넌 트 ID에 대 한 완화

제안 된 해결 방법은 위에 설명 된 [정책에 따라 캐싱](#acquire-a-token-to-apply-a-policy) 을 사용 하는 것입니다.

또는 `tid` Azure AD B2C에서 [사용자 지정 정책을](../../active-directory-b2c/custom-policy-get-started.md) 사용 하는 경우 클레임을 사용할 수 있습니다. 사용자 지정 정책은 [클레임 변환을](../../active-directory-b2c/claims-transformation-technical-profile.md)사용 하 여 응용 프로그램에 추가 클레임을 반환할 수 있습니다.

#### <a name="mitigation-for-missing-from-the-token-response"></a>"토큰 응답에 없음"에 대 한 완화

한 가지 옵션은 대신 클레임을 사용 하는 것입니다 `name` `preferred_username` . `name`Azure AD B2C에서 발급 한 ID 토큰에 클레임을 포함 하려면 사용자 흐름을 구성할 때 **표시 이름** 을 선택 합니다.

사용자 흐름에서 반환 되는 클레임을 지정 하는 방법에 대 한 자세한 내용은 [자습서: Azure AD B2C에서 사용자 흐름 만들기](../../active-directory-b2c/tutorial-create-user-flows.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure AD B2C 응용 프로그램에 대 한 MSAL.NET를 대화형으로 토큰을 가져오는 방법에 대 한 자세한 내용은 다음 샘플에서 제공 됩니다.

| 샘플 | 플랫폼 | Description|
|------ | -------- | -----------|
|[b2c-xamarin-네이티브](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | MSAL.NET를 사용 하 여 Azure AD B2C를 통해 사용자를 인증 한 후 반환 된 토큰을 사용 하 여 web API에 액세스 하는 Xamarin Forms 앱입니다.|