---
title: 웹 Api를 호출 하는 모바일 앱 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다 (앱 코드 구성).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132497"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>웹 Api를 호출 하는 모바일 앱 구성

응용 프로그램을 만든 후에 앱 등록 매개 변수를 사용 하 여 코드를 구성 하는 방법을 알아봅니다. 모바일 응용 프로그램은 생성 프레임 워크에 맞추기와 관련 된 복잡성을 제공 합니다.

## <a name="find-msal-support-for-mobile-apps"></a>모바일 앱에 대 한 MSAL 지원 찾기

다음 MSAL (Microsoft 인증 라이브러리) 유형은 모바일 앱을 지원 합니다.

MSAL | 설명
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 이식 가능한 응용 프로그램을 개발 하는 데 사용 됩니다. MSAL.NET는 모바일 응용 프로그램을 빌드하기 위한 다음과 같은 플랫폼을 지원 합니다. 유니버설 Windows 플랫폼 (UWP), Xamarin.ios 및 Xamarin.ios.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 목적-C 또는 Swift를 사용 하 여 네이티브 iOS 응용 프로그램을 개발 하는 데 사용 됩니다.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Android 용 Java에서 네이티브 Android 응용 프로그램을 개발 하는 데 사용 됩니다.

## <a name="instantiate-the-application"></a>응용 프로그램 인스턴스화

### <a name="android"></a>Android

모바일 응용 프로그램은 `PublicClientApplication` 클래스를 사용 합니다. 인스턴스화하는 방법은 다음과 같습니다.

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS의 모바일 응용 프로그램은 `MSALPublicClientApplication` 클래스를 인스턴스화해야 합니다. 클래스를 인스턴스화하려면 다음 코드를 사용 합니다. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[추가 MSALPublicClientApplicationConfig 속성](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) 은 기본 기관을 재정의 하거나, 리디렉션 URI를 지정 하거나, msal 토큰 캐싱의 동작을 변경할 수 있습니다. 

### <a name="xamarin-or-uwp"></a>Xamarin 또는 UWP

이 섹션에서는 Xamarin.ios, Xamarin Android 및 UWP 앱 용 응용 프로그램을 인스턴스화하는 방법에 대해 설명 합니다.

#### <a name="instantiate-the-application"></a>응용 프로그램 인스턴스화

Xamarin 또는 UWP에서 응용 프로그램을 인스턴스화하는 가장 간단한 방법은 다음 코드를 사용 하는 것입니다. 이 코드에서 `ClientId`은 등록 된 앱의 GUID입니다.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

추가 `With<Parameter>` 메서드는 UI 부모를 설정 하 고, 기본 기관을 재정의 하 고, 원격 분석에 대 한 클라이언트 이름 및 버전을 지정 하 고, 리디렉션 URI를 지정 하 고, 사용할 HTTP 팩터리를 지정 합니다. 예를 들어, HTTP 팩터리를 사용 하 여 프록시를 처리 하 고 원격 분석 및 로깅을 지정할 수 있습니다. 

다음 섹션에서는 응용 프로그램을 인스턴스화하는 방법에 대 한 자세한 정보를 제공 합니다.

##### <a name="specify-the-parent-ui-window-or-activity"></a>부모 UI, 창 또는 작업 지정

Android에서는 대화형 인증을 수행 하기 전에 부모 활동을 전달 해야 합니다. IOS에서 broker를 사용 하는 경우 `ViewController`를 전달 해야 합니다. UWP에서와 동일한 방식으로 부모 창에 전달 하는 것이 좋습니다. 토큰을 획득 하는 경우에 전달 합니다. 그러나 앱을 만들 때 콜백을 `UIParent`반환 하는 대리자로 지정할 수도 있습니다.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android에서는 [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)를 사용 하는 것이 좋습니다. 결과 `PublicClientApplication` builder 코드는 다음 예제와 같습니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>더 많은 앱 빌드 매개 변수 찾기

`PublicClientApplicationBuilder`에서 사용할 수 있는 모든 방법의 목록은 [메서드 목록을](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)참조 하세요.

`PublicClientApplicationOptions`에서 제공 하는 모든 옵션에 대 한 설명은 [참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)를 참조 하세요.

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS에 대 한 작업

Xamarin iOS에서 MSAL.NET를 사용 하는 경우 다음 작업을 수행 합니다.

* [`AppDelegate`에서 `OpenUrl` 함수를 재정의 하 고 구현 합니다.](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [키 집합 그룹 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [토큰 캐시 공유 사용](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [키 집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

자세한 내용은 [Xamarin iOS 고려 사항](msal-net-xamarin-ios-considerations.md)을 참조 하세요.

## <a name="tasks-for-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에 대 한 작업

IOS 및 macOS 용 MSAL을 사용 하는 경우 다음 작업이 필요 합니다.

* [`openURL` 콜백 구현](#brokered-authentication-for-msal-for-ios-and-macos)
* [키 집합 액세스 그룹 사용](howto-v2-keychain-objc.md)
* [브라우저 및 웹 보기 사용자 지정](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin Android 용 작업

Xamarin.ios를 사용 하는 경우 다음 작업을 수행 합니다.

- [인증 흐름의 대화형 부분이 종료 된 후 제어를 MSAL으로 되돌립니다.](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Android 매니페스트 업데이트](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [포함 된 웹 보기 사용 (선택 사항)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [필요에 따라 문제 해결](msal-net-xamarin-android-considerations.md#troubleshoot)

자세한 내용은 [Xamarin Android 고려 사항](msal-net-xamarin-android-considerations.md)을 참조 하세요.

Android의 브라우저에 대 한 고려 사항은 [MSAL.NET의 xamarin.ios 관련 고려 사항](msal-net-system-browser-android-considerations.md)을 참조 하세요.

#### <a name="tasks-for-uwp"></a>UWP에 대 한 작업

UWP에서는 회사 네트워크를 사용할 수 있습니다. 다음 섹션에서는 회사 시나리오에서 완료 해야 하는 작업에 대해 설명 합니다.

자세한 내용은 [MSAL.NET의 UWP 관련 고려 사항](msal-net-uwp-considerations.md)을 참조 하세요.

## <a name="configure-the-application-to-use-the-broker"></a>Broker를 사용 하도록 응용 프로그램 구성

Android 및 iOS에서 broker를 사용 하도록 설정 합니다.

- **Sso (Single sign-on)** : Azure Active Directory (Azure AD)에 등록 된 장치에 대해 sso를 사용할 수 있습니다. SSO를 사용 하는 경우 사용자는 각 응용 프로그램에 로그인 할 필요가 없습니다.
- **장치 식별**:이 설정은 Azure AD 장치와 관련 된 조건부 액세스 정책을 사용 하도록 설정 합니다. 인증 프로세스는 장치가 작업 공간에 조인 될 때 생성 된 장치 인증서를 사용 합니다.
- **응용 프로그램 id 확인**: 응용 프로그램에서 broker를 호출할 때 해당 리디렉션 URL을 전달 합니다. 그런 다음 broker에서 확인 합니다.

### <a name="enable-the-broker-on-xamarin"></a>Xamarin에서 broker 사용

Xamarin에서 broker를 사용 하도록 설정 하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용 합니다. 기본적으로 `.WithBroker()`는 true로 설정 됩니다. 

Xamarin.ios에 대해 조정 된 인증을 사용 하도록 설정 하려면이 문서에서 [xamarin.ios 섹션](#enable-brokered-authentication-for-xamarin-ios) 의 단계를 따릅니다.

### <a name="enable-the-broker-for-msal-for-android"></a>Android에 대 한 MSAL에 broker를 사용 하도록 설정

Android에서 broker를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [android에서](brokered-auth.md)조정 된 인증을 참조 하세요. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에 대해 broker를 사용 하도록 설정

조정 된 인증은 iOS 및 macOS 용 MSAL의 Azure AD 시나리오에 대해 기본적으로 사용 하도록 설정 됩니다. 

다음 섹션에서는 Xamarin.ios 용 MSAL 또는 iOS 및 macOS 용 MSAL에 대 한 조정 된 인증 지원을 위해 응용 프로그램을 구성 하는 지침을 제공 합니다. 두 지침 집합에서 일부 단계가 다릅니다.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Xamarin iOS에 대해 조정 된 인증 사용

이 섹션의 단계에 따라 Xamarin.ios 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정 합니다.

#### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용

Broker 지원은 기본적으로 사용 하지 않도록 설정 되어 있습니다. 개별 `PublicClientApplication` 클래스에 대해 사용 하도록 설정 합니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication` 클래스를 만들 때 `WithBroker()` 매개 변수를 사용 합니다. `WithBroker()` 매개 변수는 기본적으로 true로 설정 됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2 단계: 콜백을 처리 하도록 AppDelegate 업데이트

MSAL.NET가 broker를 호출할 때 broker는 응용 프로그램을 다시 호출 합니다. `AppDelegate.OpenUrl` 메서드를 사용 하 여 다시 호출 합니다. MSAL이 broker의 응답을 대기 하므로 응용 프로그램은 MSAL.NET를 호출 해야 합니다. 다음 코드에 나와 있는 것 처럼 메서드를 재정의 하도록 `AppDelegate.cs` 파일을 업데이트 하 여이 동작을 설정 합니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

이 메서드는 응용 프로그램이 시작 될 때마다 호출 됩니다. Broker에서 응답을 처리 하 고 MSAL.NET 시작 된 인증 프로세스를 완료할 수 있습니다.

#### <a name="step-3-set-a-uiviewcontroller"></a>3 단계: UIViewController () 설정

Xamarin iOS의 경우 일반적으로 개체 창을 설정 하지 않아도 됩니다. 그러나이 경우 broker에서 응답을 보내고 받을 수 있도록 설정 해야 합니다. 개체 창을 설정 하려면 `AppDelegate.cs`에서 `ViewController`를 설정 합니다.

개체 창을 설정 하려면 다음 단계를 수행 합니다.

1. `AppDelegate.cs`에서 `App.RootViewController`를 새 `UIViewController()`설정 합니다. 이 설정은 broker에 대 한 호출에 `UIViewController`포함 되도록 합니다. 올바르게 설정 되지 않은 경우 다음 오류가 발생할 수 있습니다.

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. `AcquireTokenInteractive` 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`를 사용 합니다. 사용할 개체 창에 대 한 참조를 전달 합니다. 예를 들면 다음과 같습니다.

    `App.cs`의 경우:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    `AppDelegate.cs`의 경우:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    `AcquireToken`에서 다음을 호출 합니다.
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>4 단계: URL 구성표 등록

MSAL.NET는 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱으로 다시 반환 합니다. 라운드트립을 완료 하려면 `Info.plist` 파일에 앱의 URL 체계를 등록 합니다. 

앱의 URL 체계를 등록 하려면 다음 단계를 수행 합니다.

1. `msauth`로 `CFBundleURLSchemes` 접두사입니다. 
1. 끝에 `CFBundleURLName`를 추가 합니다. 이 패턴을 따릅니다. 

   `$"msauth.(BundleId)"`

   여기서 `BundleId`는 장치를 고유 하 게 식별 합니다. 예를 들어 `BundleId` `yourcompany.xforms`경우 URL 체계가 `msauth.com.yourcompany.xforms`됩니다.
    
   > [!NOTE]
   > 이 URL 체계는 broker의 응답을 받을 때 앱을 고유 하 게 식별 하는 리디렉션 URI의 일부가 됩니다.
    
   ```XML
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5 단계: LSApplicationQueriesSchemes 섹션에 추가

MSAL은 `–canOpenURL:`를 사용 하 여 broker가 장치에 설치 되어 있는지 확인 합니다. IOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 스키마를 잠갔습니다.

다음 코드 예제와 같이 `Info.plist` 파일의 `LSApplicationQueriesSchemes` 섹션에 `msauthv2`를 추가 합니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에 대 한 조정 된 인증

조정 된 인증은 Azure AD 시나리오에 대해 기본적으로 사용 하도록 설정 됩니다.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1 단계: 콜백을 처리 하도록 AppDelegate 업데이트

IOS 및 macOS 용 MSAL이 broker를 호출 하면 broker는 `openURL` 메서드를 사용 하 여 응용 프로그램을 다시 호출 합니다. MSAL은 broker의 응답을 대기 하기 때문에 응용 프로그램을 협력 하 여 MSAL을 호출 해야 합니다. 다음 코드 예제에 나와 있는 것 처럼 메서드를 재정의 하도록 `AppDelegate.m` 파일을 업데이트 하 여이 기능을 설정 합니다.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> IOS 13 이상에서 `UISceneDelegate`를 사용한 경우 대신 `UISceneDelegate`의 `scene:openURLContexts:`에 MSAL 콜백을 추가 합니다. MSAL `handleMSALResponse:sourceApplication:`는 각 URL에 대해 한 번만 호출 해야 합니다.
>
> 자세한 내용은 [Apple 설명서](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)를 참조 하세요.

#### <a name="step-2-register-a-url-scheme"></a>2 단계: URL 구성표 등록

IOS 및 macOS 용 MSAL은 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱에 반환 합니다. 왕복을 완료 하려면 `Info.plist` 파일에 앱에 대 한 URL 체계를 등록 합니다.

앱에 대 한 구성표를 등록 하려면: 

1. `msauth`를 사용 하 여 사용자 지정 URL 체계에 접두사를 지정 합니다. 

1. 사용자의 구성표 끝에 번들 식별자를 추가 합니다. 이 패턴을 따릅니다. 

   `$"msauth.(BundleId)"`

   여기서 `BundleId`는 장치를 고유 하 게 식별 합니다. 예를 들어 `BundleId` `yourcompany.xforms`경우 URL 체계가 `msauth.com.yourcompany.xforms`됩니다.
  
   > [!NOTE]
   > 이 URL 체계는 broker의 응답을 받을 때 앱을 고유 하 게 식별 하는 리디렉션 URI의 일부가 됩니다. [Azure Portal](https://portal.azure.com)의 응용 프로그램에 대해 `msauth.(BundleId)://auth` 형식의 리디렉션 URI가 등록 되어 있는지 확인 합니다.
  
   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3 단계: LSApplicationQueriesSchemes 추가

설치 된 경우 Microsoft Authenticator 앱에 대 한 호출을 허용 하도록 `LSApplicationQueriesSchemes`를 추가 합니다.

> [!NOTE]
> `msauthv3` 구성표는 Xcode 11 이상을 사용 하 여 앱을 컴파일할 때 필요 합니다. 

`LSApplicationQueriesSchemes`를 추가 하는 방법의 예는 다음과 같습니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin Android에 대 한 조정 된 인증

MSAL.NET는 Android 용 브로커를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [토큰 획득](scenario-mobile-acquire-token.md)
