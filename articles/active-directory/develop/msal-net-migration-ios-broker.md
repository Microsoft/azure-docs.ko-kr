---
title: 브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator를 사용 하는 Xamarin iOS 앱을 ADAL.NET에서 MSAL.NET로 마이그레이션하는 방법에 대해 알아봅니다.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177434"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Microsoft Authenticator를 사용 하는 iOS 응용 프로그램을 ADAL.NET에서 MSAL.NET로 마이그레이션

Azure Active Directory Authentication Library for .NET (ADAL.NET) 및 iOS broker를 사용 하 고 있습니다. 이제 릴리스 4.3 이후 iOS에서 broker를 지 원하는 .NET 용 [Microsoft Authentication Library](msal-overview.md) (MSAL.NET)로 마이그레이션할 시간입니다.

어디서 시작해야 합니까? 이 문서는 ADAL에서 MSAL으로 Xamarin iOS 앱을 마이그레이션하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 구성 요소
이 문서에서는 iOS broker와 통합 된 Xamarin iOS 앱이 이미 있다고 가정 합니다. 그렇지 않으면 MSAL.NET로 직접 이동 하 여 해당 위치에서 broker 구현을 시작 합니다. 새 응용 프로그램을 사용 하 여 MSAL.NET에서 iOS broker를 호출 하는 방법에 대 한 자세한 내용은 [이 설명서](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)를 참조 하세요.

## <a name="background"></a>배경

### <a name="what-are-brokers"></a>Broker 란?

브로커는 Android 및 iOS에서 Microsoft가 제공 하는 응용 프로그램입니다. IOS 및 Android의 [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) 앱 및 android의 Intune 회사 포털 앱을 참조 하세요.

사용 하도록 설정 합니다.

- Single Sign-On
- 장치 식별-일부 [조건부 액세스 정책](../conditional-access/overview.md)에 필요 합니다. 자세한 내용은 [장치 관리](../conditional-access/concept-conditional-access-conditions.md#device-platforms)를 참조 하세요.
- 응용 프로그램 id 확인-일부 엔터프라이즈 시나리오에도 필요 합니다. 자세한 내용은 [INTUNE MAM (모바일 응용 프로그램 관리)](/intune/mam-faq)을 참조 하세요.

## <a name="migrate-from-adal-to-msal"></a>ADAL에서 MSAL으로 마이그레이션

### <a name="step-1-enable-the-broker"></a>1 단계: broker 사용

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
ADAL.NET에서 broker 지원은 인증 별 컨텍스트를 기준으로 설정 되었습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 다음을 설정 해야 합니다.

`useBroker` broker를 호출 하는 생성자의 true에 대 한 플래그입니다 `PlatformParameters` .

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
또한 플랫폼별 코드에서이 예제의 iOS에 대 한 페이지 렌더러에서 `useBroker`
true로 플래그 지정:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

그런 다음 토큰 가져오기 호출에 매개 변수를 포함 합니다.
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource,
                              ClientId,
                              new Uri(RedirectURI),
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
MSAL.NET에서 broker 지원은 PublicClientApplication 기준으로 설정 됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 사용 하도록 설정 하려면

`WithBroker()` broker를 호출 하기 위해 매개 변수 (기본적으로 true로 설정)

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
토큰 획득 호출에서 다음을 수행 합니다.
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2 단계: UIViewController () 설정
ADAL.NET에서의 일부로 UIViewController를 전달 `PlatformParameters` 했습니다. 1 단계의 예제를 참조 하십시오. MSAL.NET에서 개발자에 게 더 많은 유연성을 제공 하기 위해 개체 창이 사용 되지만 일반 iOS 사용에는 필요 하지 않습니다. Broker를 사용 하려면 broker에서 응답을 보내고 받기 위해 개체 창을 설정 합니다.
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
UIViewController는 다음으로 전달 됩니다.

`PlatformParameters` iOS 특정 플랫폼에서

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET에서 iOS에 대 한 개체 창을 설정 하는 두 가지 작업을 수행 합니다.

1. 에서를 `AppDelegate.cs` `App.RootViewController` 새으로 설정 `UIViewController()` 합니다.
이 할당을 통해 broker에 대 한 호출을 사용 하 여 UIViewController가 있는지 확인할 수 있습니다. 올바르게 설정 되지 않은 경우 다음 오류가 발생할 수 있습니다. `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. AcquireTokenInteractive 호출에서를 사용 하 여 `.WithParentActivityOrWindow(App.RootViewController)` 사용할 개체 창에 대 한 참조를 전달 합니다.

**예:**

`App.cs`의 경우
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`의 경우
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
토큰 획득 호출에서 다음을 수행 합니다.
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3 단계: 콜백을 처리 하도록 AppDelegate 업데이트
ADAL 및 MSAL은 모두 broker를 호출 하 고, broker는 클래스의 메서드를 통해 응용 프로그램을 다시 호출 합니다 `OpenUrl` `AppDelegate` . 자세한 내용은 [이 설명서](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)를 참조 하세요.

ADAL.NET와 MSAL.NET 사이에는 변경 내용이 없습니다.

### <a name="step-4-register-a-url-scheme"></a>4 단계: URL 구성표 등록
ADAL.NET 및 MSAL.NET는 Url을 사용 하 여 broker를 호출 하 고 broker 응답을 앱으로 다시 반환 합니다. 다음과 `Info.plist` 같이 앱에 대 한 파일에 URL 체계를 등록 합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
URL 구성표는 앱에 고유 합니다.
</td><td>
이

`CFBundleURLSchemes` 이름에 포함 되어야 합니다.

`msauth.`

접두사로 사용한 다음 `CFBundleURLName`

예: `$"msauth.(BundleId")`

```csharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> 이 URL 구성표는 broker에서 응답을 받을 때 앱을 고유 하 게 식별 하는 데 사용 되는 리디렉션 URI의 일부가 됩니다.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5 단계: LSApplicationQueriesSchemes 섹션에 broker 식별자 추가

ADAL.NET 및 MSAL.NET는 모두 `-canOpenURL:` broker가 장치에 설치 되어 있는지 확인 하는 데 사용 됩니다. 다음과 같이 info.plist 파일의 LSApplicationQueriesSchemes 섹션에 iOS broker의 올바른 식별자를 추가 합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
사용

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
사용

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>6 단계: Azure Portal에 리디렉션 URI 등록

ADAL.NET 및 MSAL.NET 모두 broker를 대상으로 하는 경우 리디렉션 URI에 대 한 추가 요구 사항을 추가 합니다. Azure Portal에서 리디렉션 URI를 응용 프로그램에 등록 합니다.
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

예제:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

예제:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Azure Portal에서 리디렉션 URI를 등록 하는 방법에 대 한 자세한 내용은 [7 단계: 앱 등록에 리디렉션 Uri 추가](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration)를 참조 하세요.

### <a name="step-7-set-the-entitlementsplist"></a>**7 단계: info.plist 설정**

Info.plist 파일에서 키 집합 액세스를 사용 하도록 설정 *합니다* .

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

키 집합 액세스를 사용 하는 방법에 대 한 자세한 내용은 키 [집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[MSAL.NET를 사용 하 여 Xamarin iOS 관련 고려 사항](msal-net-xamarin-ios-considerations.md)에 대해 알아봅니다.