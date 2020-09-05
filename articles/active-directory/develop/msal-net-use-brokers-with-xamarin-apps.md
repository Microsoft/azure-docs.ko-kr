---
title: Android & Xamarin iOS에서 broker 사용 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator 및 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용할 수 있는 Xamarin iOS 응용 프로그램을 설정 하는 방법에 대해 알아봅니다. 또한 .NET 용 Azure AD 인증 Library (ADAL.NET)에서 MSAL.NET (Microsoft Authentication Library for .NET)로 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 7fa13a328a55b0e9eaa546e70bf0711f4f011cf1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068539"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin 응용 프로그램에서 Microsoft Authenticator 또는 Intune 회사 포털 사용

Android 및 iOS에서 Microsoft Authenticator와 같은 broker와 Android 관련 Microsoft Intune 회사 포털 사용 하도록 설정 합니다.

- **Sso (Single sign-on)**: 사용자가 각 응용 프로그램에 로그인 할 필요가 없습니다.
- **장치 식별**: broker는 장치 인증서에 액세스 합니다. 이 인증서는 작업 공간에 조인 될 때 장치에 생성 됩니다.
- **응용 프로그램 id 확인**: 응용 프로그램에서 broker를 호출할 때 해당 리디렉션 URL을 전달 합니다. Broker에서 URL을 확인 합니다.

이러한 기능 중 하나를 사용 하도록 설정 하려면 `WithBroker()` 메서드를 호출할 때 매개 변수를 사용 `PublicClientApplicationBuilder.CreateApplication` 합니다. `.WithBroker()`매개 변수는 기본적으로 true로 설정 됩니다.

MSAL.NET (Microsoft Authentication Library for .NET)에서 조정 된 인증 설정은 플랫폼에 따라 다릅니다.

* [iOS 애플리케이션](#brokered-authentication-for-ios)
* [Android 애플리케이션](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>IOS에 대 한 조정 된 인증

다음 단계를 사용 하 여 Xamarin.ios 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정 합니다. IOS 13을 대상으로 하는 경우 [Apple의 주요 API 변경](./msal-net-xamarin-ios-considerations.md)에 대해 읽어 보세요.

### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용

의 개별 인스턴스에 대해 broker 지원을 사용 하도록 설정 해야 합니다 `PublicClientApplication` . 지원 기능은 기본적으로 사용 하지 않도록 설정 되어 있습니다. `PublicClientApplication`를 통해 만들 때 `PublicClientApplicationBuilder` `WithBroker()` 다음 예제와 같이 매개 변수를 사용 합니다. `WithBroker()`매개 변수는 기본적으로 true로 설정 됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2 단계: 키 집합 액세스 사용

키 집합 액세스를 사용 하도록 설정 하려면 응용 프로그램에 대 한 키 집합 액세스 그룹이 있어야 합니다. API를 사용 하 여 `WithIosKeychainSecurityGroup()` 응용 프로그램을 만들 때 키 집합 액세스 그룹을 설정할 수 있습니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

자세한 내용은 키 [집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조 하세요.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3 단계: 콜백을 처리 하도록 AppDelegate 업데이트

MSAL.NET가 broker를 호출 하면 broker는 클래스의 메서드를 통해 응용 프로그램을 다시 호출 합니다 `OpenUrl` `AppDelegate` . MSAL이 broker의 응답을 대기 하므로 응용 프로그램은 MSAL.NET를 다시 호출 해야 합니다. 이 협력을 사용 하도록 설정 하려면 *AppDelegate.cs* 파일을 업데이트 하 여 다음 메서드를 재정의 합니다.

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

이 메서드는 응용 프로그램이 시작 될 때마다 호출 됩니다. Broker에서 응답을 처리 하 고 MSAL.NET 시작 된 인증 프로세스를 완료 하는 기회를 사용 합니다.

### <a name="step-4-set-uiviewcontroller"></a>4 단계: UIViewController () 설정

*AppDelegate.cs* 파일에서 개체 창을 설정 해야 합니다. 일반적으로 Xamarin iOS에 대 한 개체 창을 설정 하지 않아도 되지만 broker에서 응답을 보내고 받으려면 개체 창이 필요 합니다.

개체 창을 설정 하려면 다음을 수행 합니다.

1. *AppDelegate.cs* 파일에서를 새으로 설정 `App.RootViewController` `UIViewController()` 합니다. 이렇게 할당 하면 broker에 대 한 호출에이 포함 됩니다 `UIViewController` . 이 설정이 잘못 할당 되 면 다음과 같은 오류가 발생할 수 있습니다.

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 호출에서를 사용 하 여 `AcquireTokenInteractive` `.WithParentActivityOrWindow(App.RootViewController)` 사용할 개체 창에 대 한 참조를 전달 합니다.

    *App.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    *AppDelegate.cs*:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    호출에서 `AcquireToken` 다음을 수행 합니다.

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>5 단계: URL 구성표 등록

MSAL.NET는 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱에 반환 합니다. 라운드트립을 완료 하려면 *info.plist* 파일에 앱에 대 한 URL 체계를 등록 합니다.

이름에는 `CFBundleURLSchemes` 접두사가 포함 되어야 합니다 `msauth.` . 접두사 뒤에를 붙입니다 `CFBundleURLName` .

URL 체계에서는 `BundleId` 앱을 고유 하 게 식별 `$"msauth.(BundleId)"` 합니다. 따라서 `BundleId` 가 인 경우 `com.yourcompany.xforms` URL 체계는 `msauth.com.yourcompany.xforms` 입니다.

> [!NOTE]
> 이 URL 구성표는 broker에서 응답을 받을 때 앱을 고유 하 게 식별 하는 리디렉션 URI의 일부가 됩니다.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6 단계: LSApplicationQueriesSchemes 섹션에 broker 식별자 추가

MSAL은 `–canOpenURL:` 장치에 broker가 설치 되어 있는지 여부를 확인 하기 위해를 사용 합니다. IOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 스키마를 잠갔습니다.

`msauthv2` `LSApplicationQueriesSchemes` 다음 예제와 같이 *info.plist* 파일의 섹션에를 추가 합니다.

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>7 단계: 앱 등록에 리디렉션 URI 추가

Broker를 사용 하는 경우 리디렉션 URI에는 추가 요구 사항이 있습니다. 리디렉션 URI의 형식은 다음과 _같아야 합니다_ .

```csharp
$"msauth.{BundleId}://auth"
```

예를 들면 다음과 같습니다.

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

리디렉션 URI는 `CFBundleURLSchemes` *info.plist* 파일에 포함 된 이름과 일치 합니다.

[Azure Portal](https://portal.azure.com)의 앱 등록에 리디렉션 URI를 추가 합니다. 올바른 형식의 리디렉션 URI를 생성 하려면 Azure Portal에서 **앱 등록** 를 사용 하 여 번들 ID에서 조정 된 리디렉션 uri를 생성 합니다.

**리디렉션 URI를 생성 하려면:**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**  >  **앱 등록** 를 선택 하 여 등록 된 앱 >
1. **인증**선택  >  **플랫폼**  >  **iOS/macos** 추가
1. 번들 ID를 입력 하 고 **구성**을 선택 합니다.

    코드에 포함 되도록 **리디렉션 uri** 텍스트 상자에 표시 되는 생성 된 리디렉션 uri를 복사 합니다.

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Azure Portal에서 리디렉션 URI를 생성 한 iOS 플랫폼 설정":::
1. **완료** 를 선택 하 여 리디렉션 URI 생성을 완료 합니다.

## <a name="brokered-authentication-for-android"></a>Android에 대 한 조정 된 인증

### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용

Broker 지원은 별로 사용 하도록 설정 됩니다 `PublicClientApplication` . 기본적으로 사용하지 않도록 설정되어 있습니다. `WithBroker()`를 통해를 만들 때 매개 변수를 사용 합니다 (기본적으로 true로 설정) `IPublicClientApplication` `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2 단계: 콜백을 처리 하도록 AppDelegate 업데이트

MSAL.NET가 broker를 호출할 때 broker는 메서드를 사용 하 여 응용 프로그램을 다시 호출 합니다 `OnActivityResult()` . MSAL은 broker의 응답을 기다리기 때문에 응용 프로그램에서 결과를 MSAL.NET로 라우팅해야 합니다.

다음과 같이 `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` 메서드를 재정의 하 여 결과를 메서드로 라우팅합니다 `OnActivityResult()` .

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

이 메서드는 broker 응용 프로그램이 시작 될 때마다 호출 되며, broker에서 응답을 처리 하 고 MSAL.NET에서 시작한 인증 프로세스를 완료할 수 있는 기회로 사용 됩니다.

### <a name="step-3-set-an-activity"></a>3 단계: 활동 설정

조정 된 인증을 사용 하도록 설정 하려면 MSAL에서 broker와의 응답을 보내고 받을 수 있도록 작업을 설정 합니다. 이렇게 하려면 작업 (일반적으로 `MainActivity` )을 부모 개체에 제공 합니다 `WithParentActivityOrWindow(object parent)` .

예를 들어,에 대 한 호출에서 `AcquireTokenInteractive()` 다음을 수행 합니다.

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>4 단계: 앱 등록에 리디렉션 URI 추가

MSAL은 Url을 사용 하 여 broker를 호출한 다음 앱으로 돌아갑니다. 이러한 라운드트립을 완료 하려면 [Azure Portal](https://portal.azure.com)을 사용 하 여 앱에 대 한 **리디렉션 URI** 를 등록 해야 합니다.

응용 프로그램에 대 한 리디렉션 URI의 형식은 APK에 서명 하는 데 사용 되는 인증서에 따라 다릅니다. 예를 들면 다음과 같습니다.

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI의 마지막 부분인는 `hgbUYHVBYUTvuvT&Y6tr554365466=` APK가 서명 된 서명의 Base64 인코딩 버전입니다. Visual Studio에서 앱을 개발 하는 동안 특정 인증서를 사용 하 여 APK에 서명 하지 않고 코드를 디버깅 하는 경우 Visual Studio는 디버깅을 위해 APK에 서명 합니다. 이러한 방식으로 Visual Studio에서 APK에 서명 하면 빌드된 컴퓨터에 대해 고유한 서명을 제공 합니다. 따라서 다른 컴퓨터에서 앱을 빌드할 때마다 MSAL으로 인증 하기 위해 응용 프로그램 코드에서 리디렉션 URI를 업데이트 하 고 Azure Portal의 응용 프로그램 등록을 업데이트 해야 합니다.

디버깅 하는 동안 제공 된 리디렉션 URI가 잘못 되었음을 나타내는 MSAL 예외 (또는 로그 메시지)가 발생할 수 있습니다. **예외 또는 로그 메시지는** 디버깅 중인 현재 컴퓨터에서 사용 해야 하는 리디렉션 URI도 나타냅니다. 제공 된 리디렉션 uri를 사용 하 여 코드에서 리디렉션 URI를 업데이트 하 고 제공 된 리디렉션 URI를 Azure Portal의 앱 등록에 추가 하기만 하면 앱을 계속 개발할 수 있습니다.

코드를 종료할 준비가 되 면 코드에서 리디렉션 URI를 업데이트 하 고 Azure Portal의 응용 프로그램 등록을 업데이트 하 여 APK에 서명 하는 데 사용 하는 인증서의 서명을 사용 합니다.

실제로이는 개발 팀의 각 멤버에 대해 리디렉션 URI를 추가 하는 것을 고려해 야 *하며, 프로덕션* 서명 된 버전의 apk에 대 한 리디렉션 uri를 추가 하는 것이 좋습니다.

MSAL에서 수행 하는 방식과 비슷하게 서명을 직접 계산할 수 있습니다.

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

다음 명령을 사용 하 여 **keytool** 을 사용 하 여 패키지에 대 한 서명을 가져오는 옵션도 있습니다.

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>5 단계 (선택 사항): 시스템 브라우저로 대체

MSAL이 broker를 사용 하도록 구성 되었지만 broker가 설치 되어 있지 않은 경우 MSAL은 웹 보기 (브라우저)를 사용 하 여 대체 합니다. MSAL은 장치에서 기본 시스템 브라우저를 사용 하 여 인증을 시도 합니다 .이는 리디렉션 URI가 broker에 대해 구성 되 고 시스템 브라우저는이를 사용 하 여 MSAL으로 다시 이동 하는 방법을 인식 하지 못하기 때문입니다. 오류를 방지 하려면 4 단계에서 사용한 broker 리디렉션 URI를 사용 하 여 *의도 필터* 를 구성 하면 됩니다.

응용 프로그램의 매니페스트를 수정 하 여 의도 필터를 추가 합니다.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

예를 들어의 리디렉션 URI가 있는 경우 `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` 매니페스트는 다음 XML 조각과 같이 표시 됩니다.

`/`값의 시그니처 앞에 슬래시 () `android:path` 가 **있어야**합니다.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

또는 리디렉션 URI를 사용 하지 않는 포함 된 브라우저로 대체 하도록 MSAL을 구성할 수 있습니다.

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android 조정 된 인증에 대 한 문제 해결 팁

다음은 Android에서 조정 된 인증을 구현할 때 발생 하는 문제를 방지 하기 위한 몇 가지 팁입니다.

- **리디렉션 uri** - [Azure Portal](https://portal.azure.com/)의 응용 프로그램 등록에 리디렉션 uri를 추가 합니다. 누락 되거나 잘못 된 리디렉션 URI는 개발자가 발생 하는 일반적인 문제입니다.
- **Broker 버전** -필요한 최소 버전의 broker 앱을 설치 합니다. 이러한 두 앱 중 하나를 Android에서 조정 된 인증에 사용할 수 있습니다.
  - [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (버전 5.0.4689.0 이상)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (버전 6.2001.0140 이상).
- **Broker 우선 순위** -msal은 여러 broker가 설치 된 경우 장치에 *설치 된 첫 번째 Broker* 와 통신 합니다.

    예: 먼저 Microsoft Authenticator를 설치 하 고 Intune 회사 포털을 설치 하는 경우 조정 된 인증은 Microsoft Authenticator *에서만* 수행 됩니다.
- **로그** -조정 된 인증에 문제가 발생 하는 경우 broker의 로그를 보면 원인을 진단 하는 데 도움이 될 수 있습니다.
  - Microsoft Authenticator 로그 보기:

    1. 앱의 오른쪽 위 모퉁이에 있는 메뉴 단추를 선택 합니다.
    1. **도움말**  >  **로그 보내기**  >  **보기 로그**를 선택 합니다.
    1. **모두 복사** 를 선택 하 여 브로커 로그를 장치의 클립보드에 복사 합니다.

    이러한 로그를 사용 하 여 디버그 하는 가장 좋은 방법은 자신에 게 전자 메일을 보내고 개발 컴퓨터에서 보는 것입니다. 장치 자체 대신 컴퓨터의 로그를 구문 분석 하는 것이 더 쉬울 수 있습니다. Android에서 테스트 편집기를 사용 하 여 로그를 텍스트 파일로 저장 한 다음 USB 케이블을 사용 하 여 파일을 컴퓨터에 복사할 수도 있습니다.

  - Intune 회사 포털 로그 보기:

    1. 앱의 왼쪽 위 모서리에 있는 메뉴 단추를 선택 합니다.
    1. **설정**  >  **진단 데이터** 선택
    1. **로그 복사** 를 선택 하 여 broker 로그를 장치의 SD 카드에 복사 합니다.
    1. USB 케이블을 사용 하 여 컴퓨터에 장치를 연결 하 여 개발 컴퓨터의 로그를 확인 합니다.

    로그가 있으면 상관 관계 ID를 통해 인증 시도를 검색할 수 있습니다. 상관 관계 ID는 모든 인증 요청에 연결 됩니다. Microsoft id 플랫폼 인증 끝점에서 반환 되는 오류를 찾으려면를 검색 `AADSTS` 합니다.

## <a name="next-steps"></a>다음 단계

[MSAL.NET와 함께 유니버설 Windows 플랫폼를 사용 하기 위한 고려 사항](msal-net-uwp-considerations.md)에 대해 알아봅니다.
