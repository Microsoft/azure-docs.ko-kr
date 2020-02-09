---
title: 웹 브라우저 사용 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)에서 Xamarin Android를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084604"
---
# <a name="using-web-browsers-msalnet"></a>웹 브라우저 사용 (MSAL.NET)

대화형 인증에는 웹 브라우저가 필요 합니다. 기본적으로 MSAL.NET 및 Xamarin.ios에서 [시스템 웹 브라우저](#system-web-browser-on-xamarinios-xamarinandroid) 를 지원 합니다. 그러나 [xamarin.ios](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 및 [xamarin Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 앱에서 요구 사항 (UX, SSO (Single Sign-On에 필요), 보안)에 따라 [포함 된 웹 브라우저를 사용 하도록 설정할 수도 있습니다](#enable-embedded-webviews-on-ios-and-android) . 뿐만 아니라 Android에서 chrome 사용자 지정 탭을 지 원하는 Chrome 또는 브라우저를 지 원하는 브라우저의 유무에 따라 사용할 웹 브라우저를 [동적으로 선택할](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 수도 있습니다. MSAL.NET는 .NET Core 데스크톱 응용 프로그램에서 시스템 브라우저만 지원 합니다.

## <a name="web-browsers-in-msalnet"></a>MSAL.NET의 웹 브라우저

### <a name="interaction-happens-in-a-web-browser"></a>웹 브라우저에서 상호 작용이 발생 합니다.

토큰을 대화형으로 가져올 때 대화 상자의 콘텐츠는 라이브러리에서 제공 하지 않고 STS (보안 토큰 서비스)에 의해 제공 되지 않는다는 것을 이해 하는 것이 중요 합니다. 인증 엔드포인트은 웹 브라우저나 웹 컨트롤에서 렌더링 되는 상호 작용을 제어 하는 HTML 및 JavaScript를 다시 보냅니다. STS가 HTML 상호 작용을 처리할 수 있도록 하면 다음과 같은 많은 이점이 있습니다.

- 암호 (입력 한 경우)는 응용 프로그램 또는 인증 라이브러리에 의해 저장 되지 않습니다.
- 다른 id 공급자에 대 한 리디렉션을 사용 하도록 설정 합니다. 예를 들어 회사 학교 계정이 나 MSAL을 사용 하는 개인 계정으로 로그인 하거나 Azure AD B2C 있는 소셜 계정을 사용 하 여 로그인 합니다.
- 를 사용 하면 STS는 사용자가 인증 단계 중에 MFA (multi-factor authentication)를 수행 하 여 (예: Windows Hello pin을 입력 하거나 휴대폰의 인증 앱에서 호출 되는 경우) 조건부 액세스를 제어할 수 있습니다. 필요한 multi-factor authentication이 아직 설정 되지 않은 경우 사용자는 동일한 대화 상자에서 just-in-time을 설정할 수 있습니다.  사용자는 휴대폰 번호를 입력 하 고 인증 응용 프로그램을 설치 하 고 QR 태그를 스캔 하 여 계정을 추가 합니다. 이 서버를 기반으로 하는 상호 작용은 훌륭한 환경입니다.
- 사용자가 암호가 만료 된 경우이 대화 상자에서 암호를 변경할 수 있습니다 (이전 암호 및 새 암호에 대 한 추가 필드 제공).
- 테넌트의 브랜딩 또는 Azure AD 테넌트 관리자/응용 프로그램 소유자가 제어 하는 응용 프로그램 (이미지)을 사용 하도록 설정 합니다.
- 를 사용 하면 사용자가 인증 후에도 응용 프로그램에서 이름에 있는 리소스/범위에 액세스할 수 있습니다.

### <a name="embedded-vs-system-web-ui"></a>포함 된 vs 시스템 웹 UI

MSAL.NET는 다중 프레임 워크 라이브러리 이며 UI 컨트롤에서 브라우저를 호스트 하는 프레임 워크 관련 코드가 있습니다. 예를 들어, .Net 클래식 it에서는 WinForms를 사용 하 고, Xamarin it에서는 네이티브 모바일 컨트롤을 사용 합니다. 이 컨트롤을 웹 UI `embedded` 이라고 합니다. 또는 MSAL.NET가 시스템 OS 브라우저를 시작할 수도 있습니다.

일반적으로 플랫폼 기본값을 사용 하는 것이 좋으며,이는 일반적으로 시스템 브라우저입니다. 시스템 브라우저는 이전에 로그인 한 사용자를 기억 하는 것이 더 좋습니다. 이 동작을 변경 해야 하는 경우 `WithUseEmbeddedWebView(bool)` 사용

### <a name="at-a-glance"></a>개요

| 프레임워크        | 포함된 | 시스템 | 기본값 |
| ------------- |-------------| -----| ----- |
| .NET 클래식     | yes | 예 ^ | 포함된 |
| .NET Core     | 예 | 예 ^ | 시스템 |
| .NET Standard | 예 | 예 ^ | 시스템 |
| UWP | yes | 예 | 포함된 |
| Xamarin.Android | yes | yes  | 시스템 |
| Xamarin.iOS | yes | yes  | 시스템 |
| Xamarin.ios| yes | 예 | 포함된 |

^ "http://localhost" 리디렉션 URI가 필요 합니다.

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.ios, Xamarin Android의 시스템 웹 브라우저

기본적으로 MSAL.NET는 Xamarin.ios, Xamarin Android 및 .NET Core에서 시스템 웹 브라우저를 지원 합니다. UI를 제공 하는 모든 플랫폼 (즉, .NET Core 아님)의 경우 웹 브라우저 컨트롤을 포함 하는 라이브러리에서 대화 상자를 제공 합니다. 또한 MSAL.NET는 .NET 데스크톱에 포함 된 웹 뷰와 UWP 플랫폼용으로 WAB를 사용 합니다. 그러나 Xamarin iOS 및 Xamarin Android 응용 프로그램용 **시스템 웹 브라우저** 는 기본적으로 활용 됩니다. IOS에서는 운영 체제의 버전 (iOS12, iOS11 및 이전 버전)에 따라 사용할 웹 보기도 선택 합니다.

시스템 브라우저를 사용 하면 broker (회사 포털/인증자) 없이 다른 응용 프로그램 및 웹 응용 프로그램과 SSO 상태를 공유할 때 상당한 이점이 있습니다. 이러한 플랫폼에서는 시스템 웹 브라우저가 전체 화면을 차지 하 고 사용자 환경을 개선 하기 때문에, 기본적으로 시스템 브라우저가 Xamarin iOS 및 Xamarin Android 플랫폼용 MSAL.NET에 사용 되었습니다. 시스템 웹 보기는 대화 상자와 구별 되지 않습니다. 그러나 iOS에서 사용자는 브라우저에서 응용 프로그램을 다시 호출 하도록 동의 해야 할 수 있습니다 .이는 성가신 일 수 있습니다.

## <a name="system-browser-experience-on-net-core"></a>.NET Core의 시스템 브라우저 환경

.NET Core에서 MSAL.NET는 시스템 브라우저를 별도의 프로세스로 시작 합니다. MSAL.NET는이 브라우저를 제어할 수 없지만 사용자가 인증을 완료 하면 MSAL.NET에서 Uri를 가로챌 수 있는 방식으로 웹 페이지가 리디렉션됩니다.

을 지정 하 여이 브라우저를 사용 하도록 .NET 클래식 용으로 작성 된 앱을 구성할 수도 있습니다.

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 사용자가 탐색 하는 경우 나 브라우저를 닫는 경우를 감지할 수 없습니다. 이 기술을 사용 하는 앱은 제한 시간 (`CancellationToken`을 통해)을 정의 하는 것이 좋습니다. 사용자에 게 암호를 변경 하거나 multi-factor authentication을 수행 하 라는 메시지가 표시 되는 경우를 고려 하 여 최소한 몇 분 이상의 시간 제한을 사용 하는 것이 좋습니다.

### <a name="how-to-use-the-default-os-browser"></a>기본 OS 브라우저를 사용 하는 방법

MSAL.NET는 `http://localhost:port`에서 수신 대기 하 고 사용자가 인증을 수행할 때 AAD에서 전송 하는 코드를 가로채 야 합니다 (자세한 내용은 [권한 부여 코드](v2-oauth2-auth-code-flow.md) 참조).

시스템 브라우저를 사용 하도록 설정 하려면:

1. 앱을 등록 하는 동안 `http://localhost`를 리디렉션 uri로 구성 합니다 (현재 B2C에서 지원 되지 않음).
2. PublicClientApplication을 생성 하는 경우 다음 리디렉션 uri를 지정 합니다.

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> `http://localhost`를 구성 하는 경우 내부적으로 MSAL.NET는 임의의 열린 포트를 찾아 사용 합니다.

### <a name="linux-and-mac"></a>Linux 및 MAC

Linux에서 MSAL.NET는 xdg-열기 도구를 사용 하 여 기본 OS 브라우저를 엽니다. 문제를 해결 하려면 터미널에서 도구를 실행 합니다 (예: `xdg-open "https://www.bing.com"`  
Mac에서는를 호출 하 여 브라우저를 엽니다 `open <url>`

### <a name="customizing-the-experience"></a>환경 사용자 지정

> [!NOTE]
> 사용자 지정은 MSAL.NET 4.1.0 이상에서 사용할 수 있습니다.

MSAL.NET는 토큰을 받거나 오류가 발생 한 경우 HTTP 메시지를 사용 하 여 응답할 수 있습니다. HTML 메시지를 표시 하거나 선택한 url로 리디렉션할 수 있습니다.

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

### <a name="opening-a-specific-browser-experimental"></a>특정 브라우저 열기 (실험적)

MSAL.NET 브라우저를 여는 방식을 사용자 지정할 수 있습니다. 예를 들어 어떤 브라우저가 기본값 인지를 사용 하는 대신 특정 브라우저를 강제로 열도록 할 수 있습니다.

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP는 시스템 웹 보기를 사용 하지 않습니다.

그러나 데스크톱 응용 프로그램의 경우에는 사용자에 게 다른 탭이 이미 열려 있을 수 있는 브라우저가 표시 되기 때문에 시스템 웹 보기를 시작 하면 subpar 환경으로 이어집니다. 그리고 인증이 발생 하면 사용자에 게이 창을 닫도록 요청 하는 페이지가 나타납니다. 사용자가 주의 하지 않으면 전체 프로세스를 닫을 수 있습니다 (인증과 관련이 없는 다른 탭 포함). 바탕 화면에서 시스템 브라우저를 활용 하는 경우에는 로컬 포트를 열고 수신 대기 해야 하며,이 경우 응용 프로그램에 대 한 고급 권한이 필요할 수 있습니다. 개발자, 사용자 또는 관리자는이 요구 사항에 대해 꺼려할 수 있습니다.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>IOS 및 Android에서 embedded 웹 보기 사용

Xamarin.ios 및 Xamarin Android 앱에서 포함 된 웹 보기를 사용 하도록 설정할 수도 있습니다. MSAL.NET 2.0.0-preview부터 MSAL.NET는 **포함** 된 웹 보기 옵션 사용도 지원 합니다. ADAL.NET의 경우 포함 된 웹 보기는 유일 하 게 지원 되는 옵션입니다.

Xamarin을 대상으로 하는 MSAL.NET를 사용 하는 개발자는 embedded 웹 보기 또는 시스템 브라우저 중 하나를 사용 하도록 선택할 수 있습니다. 대상으로 지정할 사용자 환경 및 보안 문제에 따라 선택 하는 것이 좋습니다.

현재 MSAL.NET는 Android 및 iOS broker를 아직 지원 하지 않습니다. 따라서 SSO (Single Sign-On)를 제공 해야 하는 경우에도 시스템 브라우저가 더 나은 옵션 일 수 있습니다. 포함 된 웹 브라우저에서 broker를 지 원하는 것은 MSAL.NET 백로그에 있습니다.

### <a name="differences-between-embedded-webview-and-system-browser"></a>포함 된 웹 보기와 시스템 브라우저의 차이점
MSAL.NET의 포함 된 웹 보기와 시스템 브라우저 간에는 몇 가지 시각적 차이점이 있습니다.

**포함 된 웹 보기를 사용 하 여 MSAL.NET로 대화형 로그인 합니다.**

![포함](media/msal-net-web-browsers/embedded-webview.png)

**시스템 브라우저를 사용 하 여 MSAL.NET로 대화형 로그인:**

![시스템 브라우저](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>개발자 옵션

MSAL.NET를 사용 하는 개발자는 STS의 대화형 대화 상자를 표시 하는 몇 가지 옵션이 있습니다.

- **시스템 브라우저.** 시스템 브라우저는 기본적으로 라이브러리에 설정 되어 있습니다. Android를 사용 하는 경우 인증을 위해 지원 되는 브라우저에 대 한 특정 정보는 [시스템 브라우저](msal-net-system-browser-android-considerations.md) 를 참조 하세요. Android에서 시스템 브라우저를 사용 하는 경우 장치에 Chrome 사용자 지정 탭을 지 원하는 브라우저를 사용 하는 것이 좋습니다.  그렇지 않으면 인증이 실패할 수 있습니다.
- **포함 된 웹 보기.** MSAL.NET에 포함 된 웹 보기만 사용 하려면 `AcquireTokenInteractively` 매개 변수 작성기에 `WithUseEmbeddedWebView()` 메서드가 포함 되어 있습니다.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Xamarin.ios의 포함 된 웹 브라우저 또는 시스템 브라우저 중에서 선택

IOS 앱의 `AppDelegate.cs`에서 `ParentWindow`를 초기화 하 여 `null`수 있습니다. IOS에서 사용 되지 않습니다.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Xamarin Android에서 포함 된 웹 브라우저 또는 시스템 브라우저 중에서 선택

Android 앱의 `MainActivity.cs`에서 부모 작업을 설정 하 여 인증 결과가 반환 되도록 할 수 있습니다.

```csharp
 App.ParentWindow = this;
```

그런 다음 `MainPage.xaml.cs`에서 다음을 수행 합니다.

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin Android에서 사용자 지정 탭의 존재 여부 감지

시스템 웹 브라우저를 사용 하 여 브라우저에서 실행 중인 앱에 SSO를 사용 하도록 설정 하 고 사용자 지정 탭을 지 원하는 브라우저가 없는 Android 장치에 대 한 사용자 환경에 대해 걱정 하는 경우 `IPublicClientApplication`에서 `IsSystemWebViewAvailable()` 메서드를 호출 하 여 결정할 수 있습니다. 이 메서드는 PackageManager에서 사용자 지정 탭을 검색 `false` 하 고 장치가 검색 되지 않는 경우에는 `true`를 반환 합니다.

이 메서드에서 반환 된 값과 요구 사항에 따라 결정을 내릴 수 있습니다.

- 사용자에 게 사용자 지정 오류 메시지를 반환할 수 있습니다. 예: "인증을 계속 하려면 Chrome을 설치 하세요."-또는-
- 포함 된 웹 보기 옵션으로 대체 하 고 UI를 포함 된 웹 보기로 시작할 수 있습니다.

아래 코드에서는 포함 된 웹 보기 옵션을 보여 줍니다.

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core는 포함 된 브라우저를 사용 하 여 대화형 인증을 지원 하지 않습니다.

.NET Core의 경우 대화형으로 토큰을 획득 하는 것은 포함 된 웹 보기가 아닌 시스템 웹 브라우저를 통해서만 사용할 수 있습니다. 실제로 .NET Core는 UI를 아직 제공 하지 않습니다.
시스템 웹 브라우저를 사용 하 여 검색 환경을 사용자 지정 하려는 경우 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 인터페이스를 구현 하 고 사용자 고유의 브라우저를 제공할 수 있습니다.
