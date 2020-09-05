---
title: 웹 브라우저 사용(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)에서 Xamarin Android를 사용하는 경우 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bf51f39a789b91a4cb0b88eb8bb1f2989bec7358
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165824"
---
# <a name="using-web-browsers-msalnet"></a>웹 브라우저 사용(MSAL.NET)

대화형 인증에는 웹 브라우저가 필요합니다. 기본적으로 MSAL.NET은 Xamarin.iOS 및 Xamarin.Android에서 [시스템 웹 브라우저](#system-web-browser-on-xamarinios-xamarinandroid)를 지원합니다. 그러나 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 및 [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 앱의 요구 사항(UX, SSO(Single Sign-On) 필요, 보안)에 따라 [포함된 웹 브라우저를 사용하도록 설정할 수도 있습니다](#enable-embedded-webviews-on-ios-and-android). 그뿐만 아니라 Chrome 또는 Android에서 Chrome 사용자 지정 탭을 지원하는 브라우저의 유무에 따라 어떤 웹 브라우저를 사용할지 [동적으로 선택](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)할 수도 있습니다. MSAL.NET은 .NET Core 데스크톱 애플리케이션에서 시스템 브라우저만 지원합니다.

## <a name="web-browsers-in-msalnet"></a>MSAL.NET의 웹 브라우저

### <a name="interaction-happens-in-a-web-browser"></a>웹 브라우저에서 상호 작용이 발생함

토큰을 대화형으로 가져올 때 대화 상자의 콘텐츠는 라이브러리가 아니라 STS(보안 토큰 서비스)에 의해 제공됨을 이해하는 것이 중요합니다. 인증 엔드포인트는 상호 작용을 제어하는 일부 HTML 및 JavaScript를 다시 보내며, 이와 같은 언어는 웹 브라우저나 웹 컨트롤에서 렌더링됩니다. STS가 HTML 상호 작용을 처리할 수 있도록 하면 다음과 같은 많은 장점이 있습니다.

- 암호(입력한 경우)는 애플리케이션 또는 인증 라이브러리에 의해 저장되지 않습니다.
- 다른 ID 공급자에 대한 리디렉션을 사용하도록 설정합니다(예: 회사 학교 계정, MSAL을 사용한 개인 계정 또는 Azure AD B2C를 사용한 소셜 계정으로 로그인).
- 예를 들어 인증 단계(Windows Hello 핀을 입력하거나 휴대폰이나 휴대폰의 인증 앱에서 호출됨) 중에 사용자가 [MFA(다단계 인증)](../authentication/concept-mfa-howitworks.md)를 수행하도록 하여 STS 컨트롤 조건부 액세스를 허용합니다. 필요한 다단계 인증이 아직 설정되지 않은 경우 사용자는 동일한 대화 상자에서 적시에 설정할 수 있습니다.  사용자는 휴대폰 번호를 입력한 다음, 안내에 따라 인증 애플리케이션을 설치하고 QR 태그를 스캔하여 계정을 추가합니다. 서버를 기반으로 하는 이와 같은 상호 작용은 유용한 경험입니다.
- 암호가 만료된 경우 사용자가 동일한 대화 상자에서 암호를 변경할 수 있습니다(이전 암호 및 새 암호에 대한 추가 필드 제공).
- 테넌트의 브랜딩 또는 Azure AD 테넌트 관리자/애플리케이션 소유자가 제어하는 애플리케이션(이미지)을 사용하도록 설정할 수 있습니다.
- 사용자가 인증 직후에 애플리케이션에서 자신의 이름으로 리소스/범위에 액세스하도록 동의할 수 있습니다.

### <a name="embedded-vs-system-web-ui"></a>포함된 UI와 시스템 웹 UI 비교

MSAL.NET은 다중 프레임워크 라이브러리이며 UI 컨트롤에서 브라우저를 호스트하는 프레임워크 관련 코드가 있습니다. 예를 들어 .NET 클래식에서는 WinForms를 사용하고, Xamarin에서는 네이티브 모바일 컨트롤을 사용합니다. 이 컨트롤을 `embedded` 웹 UI라고 합니다. 또는 MSAL.NET이 시스템 OS 브라우저를 시작할 수도 있습니다.

일반적으로 플랫폼 기본값을 사용하는 것이 좋으며, 보통은 시스템 브라우저가 기본값입니다. 시스템 브라우저는 이전에 로그인한 사용자를 기억하는 데 더 뛰어납니다. 이 동작을 변경해야 하는 경우 `WithUseEmbeddedWebView(bool)`를 사용하세요.

### <a name="at-a-glance"></a>개요

| 프레임워크        | 포함된 | 시스템 | 기본값 |
| ------------- |-------------| -----| ----- |
| .NET 클래식     | 예 | 예^ | 포함된 |
| .NET Core     | 예 | 예^ | 시스템 |
| .NET Standard | 예 | 예^ | 시스템 |
| UWP | 예 | 예 | 포함된 |
| Xamarin.Android | 예 | 예  | 시스템 |
| Xamarin.iOS | 예 | 예  | 시스템 |
| Xamarin.Mac| 예 | 예 | 포함된 |

^ “http://localhost” 리디렉션 URI가 필요함

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS, Xamarin.Android의 시스템 웹 브라우저

기본적으로 MSAL.NET은 Xamarin.iOS, Xamarin.Android 및 .NET Core에서 시스템 웹 브라우저를 지원합니다. UI를 제공하는 모든 플랫폼(즉, .NET Core 제외)의 경우 웹 브라우저 컨트롤을 포함하는 라이브러리에서 대화 상자를 제공합니다. 또한 MSAL.NET은 .NET 데스크톱에는 포함된 웹 보기를 사용하고, UWP 플랫폼에는 WAB를 사용합니다. 그러나 Xamarin iOS 및 Xamarin Android 애플리케이션에는 **시스템 웹 브라우저**가 기본적으로 활용됩니다. iOS에서는 운영 체제의 버전(iOS12, iOS11 및 이전 버전)에 따라 사용할 웹 보기도 선택됩니다.

시스템 브라우저를 사용하면 브로커(회사 포털/인증자) 없이 다른 애플리케이션 및 웹 애플리케이션과 SSO 상태를 공유할 수 있는 큰 장점이 있습니다. 기본적으로 시스템 브라우저가 MSAL.NET에서 Xamarin iOS 및 Xamarin Android 플랫폼에 사용되었습니다. 이와 같은 플랫폼에서는 시스템 웹 브라우저가 전체 화면을 차지하고 사용자 환경이 개선되기 때문입니다. 시스템 웹 보기는 대화 상자와 구별되지 않습니다. 그러나 iOS에서는 사용자가 브라우저에서 애플리케이션을 다시 호출하도록 동의해야 할 수 있어서 성가실 수 있습니다.

## <a name="system-browser-experience-on-net-core"></a>.NET Core의 시스템 브라우저 환경

MSAL.NET은 .NET Core에서 시스템 브라우저를 별도의 프로세스로 시작합니다. MSAL.NET은 이 브라우저를 제어할 수 없지만 사용자가 인증을 완료하면 MSAL.NET에서 URI를 가로챌 수 있는 방식으로 웹 페이지가 리디렉션됩니다.

다음을 지정하여 .NET 클래식용으로 작성된 앱에서 이 브라우저를 사용하도록 구성할 수도 있습니다.

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET은 사용자가 브라우저를 벗어나거나 그냥 닫아버리는 경우 검색할 수 없습니다. 이 기법을 사용하는 앱은 시간 제한을 정의하는 것이 좋습니다(`CancellationToken`을 통해). 사용자에게 암호를 변경하거나 다단계 인증을 수행하라는 메시지가 표시되는 경우를 고려하여 최소한 몇 분의 시간 제한을 사용하는 것이 좋습니다.

### <a name="how-to-use-the-default-os-browser"></a>기본 OS 브라우저를 사용하는 방법

MSAL.NET은 `http://localhost:port`에서 수신 대기하고 사용자가 인증을 완료할 때 AAD에서 전송하는 코드를 가로채야 합니다(자세한 내용은 [인증 코드](v2-oauth2-auth-code-flow.md) 참조).

시스템 브라우저를 사용하도록 설정하려면 다음과 같이 합니다.

1. 앱을 등록하는 동안 `http://localhost`를 리디렉션 URI로 구성합니다(현재 B2C에서 지원되지 않음).
2. PublicClientApplication을 생성하는 경우 다음 리디렉션 URI를 지정합니다.

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> `http://localhost`를 구성하는 경우 내부적으로 MSAL.NET은 임의의 열린 포트를 찾아 사용합니다.

### <a name="linux-and-mac"></a>Linux 및 Mac

MSAL.NET은 Linux에서 xdg-open 도구를 사용하여 기본 OS 브라우저를 엽니다. 문제를 해결하려면 터미널에서 이 도구를 실행합니다(예: `xdg-open "https://www.bing.com"`). Mac에서 `open <url>`을 호출하면 브라우저가 열립니다.

### <a name="customizing-the-experience"></a>환경 사용자 지정

> [!NOTE]
> 사용자 지정은 MSAL.NET 4.1.0 이상에서 사용할 수 있습니다.

MSAL.NET은 토큰을 받거나 오류가 발생한 경우 HTTP 메시지를 사용하여 응답할 수 있습니다. HTML 메시지를 표시하거나 선택한 URL로 리디렉션할 수 있습니다.

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>특정 브라우저 열기(실험적)

MSAL.NET이 브라우저를 여는 방식을 사용자 지정할 수 있습니다. 예를 들어 기본 브라우저를 사용하는 대신 특정 브라우저를 강제로 열도록 할 수 있습니다.

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP는 시스템 웹 보기를 사용하지 않음

그러나 데스크톱 애플리케이션의 경우 시스템 웹 보기를 시작하면 사용자가 다른 탭이 이미 열려 있는 브라우저를 볼 때 수준 이하의 사용자 환경이 될 수 있습니다. 그리고 인증이 발생하면 사용자에게 이 창을 닫도록 요청하는 페이지가 표시됩니다. 사용자가 주의하지 않으면 전체 프로세스(인증과 관련이 없는 다른 탭 포함)를 닫을 수 있습니다. 데스크톱의 시스템 브라우저를 활용하는 경우에도 로컬 포트를 열어 수신 대기해야 하며, 이 경우 애플리케이션에 대한 고급 권한이 필요할 수 있습니다. 개발자, 사용자 또는 관리자는 이 요구 사항을 꺼릴 수 있습니다.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>iOS 및 Android에서 포함된 웹 보기 사용

Xamarin.iOS 및 Xamarin.Android 앱에서 포함된 웹 보기를 사용하도록 설정할 수도 있습니다. MSAL.NET 2.0.0(미리 보기)부터 MSAL.NET은 **포함된** 웹 보기 옵션 사용도 지원합니다. ADAL.NET의 경우 포함된 웹 보기가 유일하게 지원되는 옵션입니다.

Xamarin을 대상으로 하는 MSAL.NET을 사용하는 개발자는 포함된 웹 보기 또는 시스템 브라우저 중 하나를 사용하도록 선택할 수 있습니다. 대상으로 지정할 사용자 환경 및 보안 문제에 따라 선택하면 됩니다.

현재 MSAL.NET은 Android 및 iOS 브로커를 아직 지원하지 않습니다. 따라서 SSO(Single Sign-On)를 제공해야 하는 경우 시스템 브라우저가 더 나은 옵션일 수 있습니다. 포함된 웹 브라우저를 사용하여 브로커를 지원하는 작업은 MSAL.NET 백로그에 있습니다.

### <a name="differences-between-embedded-webview-and-system-browser"></a>포함된 웹 보기와 시스템 브라우저의 차이점
MSAL.NET에서 포함된 웹 보기와 시스템 브라우저 간에는 몇 가지 시각적 차이점이 있습니다.

**포함된 웹 보기를 사용하여 MSAL.NET으로 대화형 로그인:**

![포함됨](media/msal-net-web-browsers/embedded-webview.png)

**시스템 브라우저를 사용하여 MSAL.NET으로 대화형 로그인:**

![시스템 브라우저](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>개발자 옵션

MSAL.NET을 사용하는 개발자는 STS의 대화형 대화 상자를 표시하는 몇 가지 옵션이 있습니다.

- **시스템 브라우저.** 시스템 브라우저는 기본적으로 라이브러리에 설정되어 있습니다. Android를 사용하는 경우 인증을 지원하는 브라우저에 대한 특정 정보는 [시스템 브라우저](msal-net-system-browser-android-considerations.md)를 참조하세요. Android에서 시스템 브라우저를 사용하는 경우 디바이스에 Chrome 사용자 지정 탭을 지원하는 브라우저가 있는 것이 좋습니다.  이와 같은 브라우저가 없는 경우 인증이 실패할 수 있습니다.
- **포함된 웹 보기.** MSAL.NET에서 포함된 웹 보기만 사용하도록 하기 위해 `AcquireTokenInteractively` 매개 변수 작성기에 `WithUseEmbeddedWebView()` 메서드가 포함됩니다.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Xamarin.iOS에서 포함된 웹 브라우저 또는 시스템 브라우저 중에서 선택

iOS 앱의 `AppDelegate.cs`에서 `ParentWindow`를 `null`로 초기화할 수 있습니다. 이렇게 하면 iOS에서 사용되지 않습니다.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Xamarin.Android에서 포함된 웹 브라우저 또는 시스템 브라우저 중에서 선택

Android 앱의 `MainActivity.cs`에서 다음과 같이 부모 작업을 설정하여 인증 결과가 반환되도록 할 수 있습니다.

```csharp
 App.ParentWindow = this;
```

그런 다음, `MainPage.xaml.cs`에서 다음을 사용합니다.

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin.Android에서 사용자 지정 탭이 있는지 검색

시스템 웹 브라우저를 사용하여 브라우저에서 실행 중인 앱에 SSO를 사용하도록 설정하고 싶지만 Android 디바이스용 사용자 환경에 사용자 지정 탭을 지원하는 브라우저가 없을까 걱정이 되는 경우 선택적으로 `IPublicClientApplication`에서 `IsSystemWebViewAvailable()` 메서드를 호출할 수 있습니다. 이 메서드는 PackageManager에서 사용자 지정 탭을 검색하면 `true`를 반환하고, 디바이스에서 이 탭이 검색되지 않으면 `false`를 반환합니다.

이 메서드에서 반환된 값 및 사용자 요구 사항에 따라 다음 결정을 내릴 수 있습니다.

- 사용자에게 사용자 지정 오류 메시지를 반환할 수 있습니다. 다음은 그 예입니다.  “인증을 계속하려면 Chrome을 설치하세요.”
- 또는 포함된 웹 보기 옵션으로 대체하고 UI를 포함된 웹 보기로 시작할 수 있습니다.

아래 코드에서는 포함된 웹 보기 옵션을 보여 줍니다.

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core는 포함된 브라우저를 사용한 대화형 인증을 지원하지 않습니다.

.NET Core의 경우 대화형으로 토큰을 획득하는 것은 포함된 웹 보기가 아닌 시스템 웹 브라우저를 통해서만 가능합니다. 실제로 .NET Core는 아직 UI를 제공하지 않습니다.
시스템 웹 브라우저를 사용하여 검색 환경을 사용자 지정하려는 경우 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 인터페이스를 구현하고 사용자 고유의 브라우저도 제공할 수 있습니다.
