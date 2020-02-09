---
title: Azure AD B2C (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Azure AD B2C를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083981"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET를 사용 하 여 소셜 id로 사용자 로그인

[Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)를 사용 하 여 소셜 id로 사용자를 로그인 하는 데 MSAL.NET를 사용할 수 있습니다. Azure AD B2C은 정책의 개념을 중심으로 작성 되었습니다. MSAL.NET에서는 정책을 지정 하 여 권한을 제공 하는 것으로 해석 됩니다.

- 공용 클라이언트 응용 프로그램을 인스턴스화할 때 기관에서 정책을 지정 해야 합니다.
- 정책을 적용 하려면 `authority` 매개 변수를 포함 하는 `AcquireTokenInteractive`의 재정의를 호출 해야 합니다.

이 페이지는 MSAL 3.x 용입니다. MSAL 2.x에 관심이 있는 경우 [MSAL 2.x에서 Azure AD B2C 관련 사항을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)참조 하세요.

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 테넌트 및 정책에 대 한 권한

사용할 기관은 다음과 `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` 있습니다.

- `azureADB2CHostname`은 Azure AD B2C 테넌트와 호스트 (예: `{your-tenant-name}.b2clogin.com`)의 이름입니다.
- `tenant`은 Azure AD B2C 테넌트의 전체 이름 (예: `{your-tenant-name}.onmicrosoft.com`) 또는 테넌트의 GUID입니다. 
- 적용할 정책 또는 사용자 흐름의 이름 (예를 들어, 등록/로그인에 대 한 "b2c_1_susi")을 `policyName` 합니다.

Azure AD B2C 기관에 대 한 자세한 내용은이 [설명서](/azure/active-directory-b2c/b2clogin)를 참조 하세요.

## <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

응용 프로그램을 빌드하는 경우 권한을 제공 해야 합니다.

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
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

다음 구문으로 바꿉니다.

- `policy` 이전 문자열 중 하나입니다 (예 `PolicySignUpSignIn`).
- Android (작업)에는 `ParentActivityOrWindow` 필요 하 고, Windows의 windows 및 iOS의 UIViewController와 같이 부모 UI를 지 원하는 다른 플랫폼의 경우 선택 사항입니다. [UI 대화 상자에서](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)자세한 내용을 참조 하세요.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`는 지정 된 정책에 대 한 계정을 찾는 방법입니다. 다음은 그 예입니다.

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

정책 또는 사용자 흐름을 적용 하는 경우 (예를 들어 최종 사용자가 프로필을 편집 하거나 암호를 다시 설정 하는 경우)는 현재 `AcquireTokenInteractive`를 호출 하 여 수행 됩니다. 이러한 두 정책의 경우 반환 된 토큰/인증 결과를 사용 하지 않습니다.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 및 ResetPassword 정책의 특수 한 경우

최종 사용자가 소셜 id를 사용 하 여 로그인 한 다음 프로필을 편집 하는 환경을 제공 하려는 경우 Azure AD B2C 프로필 편집 정책을 적용 합니다. 이 작업을 수행 하는 방법은 해당 정책에 대 한 특정 기관에 `AcquireTokenInteractive`를 호출 하 고, 사용자가 이미 로그인 되어 있고 활성 쿠키 세션이 있기 때문에 계정 선택 대화 상자가 표시 되지 않도록 하는 프롬프트를 `Prompt.NoPrompt`로 설정 하는 것입니다.

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
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하는 ROPC (리소스 소유자 암호 자격 증명)
ROPC 흐름에 대 한 자세한 내용은이 [설명서](v2-oauth-ropc.md)를 참조 하세요.

사용자에 게 암호를 요청 하는 응용 프로그램은 안전 하지 않으므로이 흐름은 사용 하지 않는 것이 **좋습니다** . 이 문제에 대 한 자세한 내용은 [이 문서](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)를 참조 하세요. 

사용자 이름/암호를 사용 하 여 다음과 같은 여러 가지를 제공 합니다.
- 최신 id의 핵심 개념: 암호는 fished를 가져온 후 재생 됩니다. 이 개념은 가로챌 수 있는 공유 암호의 개념입니다. 이는 passwordless와 호환 되지 않습니다.
- MFA를 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
- 사용자가 Single Sign-On을 수행할 수 없습니다.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC 흐름 구성
Azure AD B2C 테넌트에서 새 사용자 흐름을 만들고 **ROPC를 사용 하 여 로그인**을 선택 합니다. 그러면 테넌트에 대해 ROPC 정책이 사용 됩니다. 자세한 내용은 [리소스 소유자 암호 자격 증명 흐름 구성](/azure/active-directory-b2c/configure-ropc) 을 참조 하세요.

`IPublicClientApplication`에는 메서드가 포함 됩니다.
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

이 메서드는를 매개 변수로 사용 합니다.
- 액세스 토큰을 요청 하는 *범위* 입니다.
- *사용자 이름*입니다.
- 사용자의 SecureString *암호* 입니다.

ROPC 정책이 포함 된 기관을 사용 해야 합니다.

### <a name="limitations-of-the-ropc-flow"></a>ROPC 흐름의 제한 사항
 - ROPC flow는 **로컬 계정** (전자 메일 또는 사용자 이름을 사용 하 여 Azure AD B2C에 등록 하는 경우) 에서만 작동 합니다. Azure AD B2C에서 지 원하는 id 공급자 (Facebook, Google 등)에 페더레이션 하는 경우에는이 흐름이 작동 하지 않습니다.

## <a name="google-auth-and-embedded-webview"></a>Google 인증 및 포함 된 웹 보기

Google을 id 공급자로 사용 하는 Azure AD B2C 개발자 인 경우 Google은 [포함 된 웹 보기의 인증](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)을 허용 하지 않으므로 시스템 브라우저를 사용 합니다. 현재 `login.microsoftonline.com`는 Google을 사용 하는 신뢰할 수 있는 기관입니다. 이 권한을 사용 하면 포함 된 웹 보기를 사용할 수 있습니다. 그러나 `b2clogin.com`는 Google을 사용 하 여 신뢰할 수 있는 기관이 아니므로 사용자는 인증할 수 없습니다.

변경 사항이 있는 경우이 [문제](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) 에 대 한 업데이트를 제공 합니다.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>MSAL.Net에서 Azure AD B2C를 사용한 캐싱 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C의 알려진 문제

MSAL.Net는 [토큰 캐시](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)를 지원 합니다. 토큰 캐싱 키는 Id 공급자가 반환 하는 클레임을 기반으로 합니다. 현재 MSAL.Net는 토큰 캐시 키를 작성 하는 데 두 가지 클레임이 필요 합니다.  
- Azure AD 테넌트 ID 인 `tid` 및 
- `preferred_username` 

이러한 두 클레임은 다 수의 Azure AD B2C 시나리오에서 누락 되었습니다. 

고객의 영향은 사용자 이름 필드를 표시 하려고 할 때 값으로 "토큰 응답에서 누락"을 가져오는 것입니다. 이 경우 Azure AD B2C는 소셜 계정 및 Idtoken (외부 id 공급자)의 제한으로 인해 preferred_username의 IdToken에 값을 반환 하지 않기 때문입니다. 사용자가 로컬 계정, Facebook, Google, GitHub 등을 사용 하 여 로그인 할 수 있기 때문에 Azure AD는 사용자가 누구이 고 Azure AD B2C에 대해 사용자가 로그인 할 수 있으므로 preferred_username 값을 반환 합니다. preferred_username에 사용할 Azure AD B2C에는 일관 된 값이 없습니다. MSAL에서 ADAL과의 캐시 호환성을 차단 해제 하려면 IdToken이 preferred_username에 대해 아무 것도 반환 하지 않을 때 Azure AD B2C 계정을 처리할 때 끝에 "토큰 응답에서 누락"을 사용 하기로 결정 했습니다. MSAL은 라이브러리 간에 캐시 호환성을 유지 하기 위해 preferred_username 값을 반환 해야 합니다.

### <a name="workarounds"></a>해결 방법

#### <a name="mitigation-for-the-missing-tenant-id"></a>누락 된 테넌트 ID에 대 한 완화

권장 되는 해결 방법은 정책에의 한 [캐싱을](#acquire-a-token-to-apply-a-policy) 사용 하는 것입니다.

또는 [B2C 사용자 지정 정책을](https://aka.ms/ief)사용 하는 경우 응용 프로그램에 추가 클레임을 반환 하는 기능을 제공 하기 때문에 `tid` 클레임을 사용할 수 있습니다. [클레임 변환](/azure/active-directory-b2c/claims-transformation-technical-profile) 에 대 한 자세한 정보

#### <a name="mitigation-for-missing-from-the-token-response"></a>"토큰 응답에 없음"에 대 한 완화
한 가지 옵션은 "이름" 클레임을 기본 사용자 이름으로 사용 하는 것입니다. 프로세스는이 [B2C > 문서](../../active-directory-b2c/user-flow-overview.md) 에 설명 되어 있습니다. 반환 클레임 열에서 프로필 편집 환경에 성공 후 응용 프로그램으로 다시 보낸 권한 부여 토큰에서 반환 하려는 클레임을 선택 합니다. 예를 들어 표시 이름, 우편 번호를 선택 합니다.

## <a name="next-steps"></a>다음 단계 

Azure AD B2C 응용 프로그램에 대 한 MSAL.NET를 대화형으로 토큰을 가져오는 방법에 대 한 자세한 내용은 다음 샘플에서 제공 됩니다.

| 샘플 | 플랫폼 | Description|
|------ | -------- | -----------|
|[b2c-xamarin-네이티브](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | 간단한 Xamarin Forms 앱은 MSAL.NET를 사용 하 여 Azure AD B2C를 통해 사용자를 인증 하 고 결과 토큰을 사용 하 여 Web API에 액세스 하는 방법을 보여주는.|
