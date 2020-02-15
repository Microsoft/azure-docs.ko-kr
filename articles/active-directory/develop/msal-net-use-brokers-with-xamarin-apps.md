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
ms.custom: aaddev
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132650"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin 응용 프로그램에서 Microsoft Authenticator 또는 Intune 회사 포털 사용

Android 및 iOS에서 Microsoft Authenticator와 같은 broker와 Android 관련 Microsoft Intune 회사 포털 사용 하도록 설정 합니다.

- **Sso (Single sign-on)** : 사용자가 각 응용 프로그램에 로그인 할 필요가 없습니다.
- **장치 식별**: broker는 장치 인증서에 액세스 합니다. 이 인증서는 작업 공간에 조인 될 때 장치에 생성 됩니다.
- **응용 프로그램 id 확인**: 응용 프로그램에서 broker를 호출할 때 해당 리디렉션 URL을 전달 합니다. Broker에서 URL을 확인 합니다.

이러한 기능 중 하나를 사용 하도록 설정 하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용 합니다. `.WithBroker()` 매개 변수는 기본적으로 true로 설정 됩니다. 

또한 다음 섹션의 지침을 사용 하 여 [iOS](#brokered-authentication-for-ios) 응용 프로그램 또는 [Android](#brokered-authentication-for-android) 응용 프로그램에 대 한 조정 된 인증을 설정 합니다.

## <a name="brokered-authentication-for-ios"></a>IOS에 대 한 조정 된 인증

다음 단계를 사용 하 여 Xamarin.ios 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정 합니다.

### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용
`PublicClientApplication`의 개별 인스턴스에 대해 broker 지원을 사용 하도록 설정 해야 합니다. 지원 기능은 기본적으로 사용 하지 않도록 설정 되어 있습니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication`을 만드는 경우 다음 예제와 같이 `WithBroker()` 매개 변수를 사용 합니다. `WithBroker()` 매개 변수는 기본적으로 true로 설정 됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2 단계: 키 집합 액세스 사용

키 집합 액세스를 사용 하도록 설정 하려면 응용 프로그램에 대 한 키 집합 액세스 그룹이 있어야 합니다. 응용 프로그램을 만들 때 `WithIosKeychainSecurityGroup()` API를 사용 하 여 키 집합 액세스 그룹을 설정할 수 있습니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

자세한 내용은 키 [집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조 하세요.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3 단계: 콜백을 처리 하도록 AppDelegate 업데이트
Microsoft Authentication Library for .NET (MSAL.NET)이 broker를 호출할 때 broker는 `AppDelegate` 클래스의 `OpenUrl` 메서드를 통해 응용 프로그램을 다시 호출 합니다. MSAL이 broker의 응답을 대기 하므로 응용 프로그램은 MSAL.NET를 다시 호출 해야 합니다. 이 협력을 사용 하도록 설정 하려면 `AppDelegate.cs` 파일을 업데이트 하 여 다음 메서드를 재정의 합니다.

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
`AppDelegate.cs` 파일에서 개체 창을 설정 해야 합니다. 일반적으로 Xamarin iOS의 경우 개체 창을 설정 하지 않아도 됩니다. 그러나 broker에서 응답을 보내고 받으려면 개체 창이 필요 합니다. 

개체 창을 설정 하려면 다음을 수행 합니다. 
1. `AppDelegate.cs` 파일에서 `App.RootViewController`를 새 `UIViewController()`설정 합니다. 이렇게 할당 하면 broker에 대 한 호출에 `UIViewController`포함 됩니다. 이 설정이 잘못 할당 되 면 다음과 같은 오류가 발생할 수 있습니다.

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive` 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`를 사용 하 여 사용할 개체 창에 대 한 참조를 전달 합니다.

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

### <a name="step-5-register-a-url-scheme"></a>5 단계: URL 구성표 등록
MSAL.NET는 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱에 반환 합니다. 왕복을 완료 하려면 `Info.plist` 파일에 앱에 대 한 URL 체계를 등록 합니다.

`CFBundleURLSchemes` 이름은 접두사로 `msauth.`를 포함 해야 합니다. `CFBundleURLName`접두사를 따릅니다. 

URL 체계에서 `BundleId`는 앱 `$"msauth.(BundleId)"`고유 하 게 식별 합니다. 따라서 `BundleId` `com.yourcompany.xforms`경우 URL 체계가 `msauth.com.yourcompany.xforms`됩니다.

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
MSAL은 `–canOpenURL:`를 사용 하 여 broker가 장치에 설치 되어 있는지 여부를 확인 합니다. IOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 스키마를 잠갔습니다. 

다음 예제와 같이 `Info.plist` 파일의 `LSApplicationQueriesSchemes` 섹션에 `msauthv2`를 추가 합니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7 단계: 응용 프로그램 포털에서 리디렉션 URI 등록
Broker를 사용 하는 경우 리디렉션 URI에는 추가 요구 사항이 있습니다. 리디렉션 URI의 형식은 다음과 _같아야 합니다_ .
```csharp
$"msauth.{BundleId}://auth"
```

예를 들면 다음과 같습니다. 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
리디렉션 URI가 `Info.plist` 파일에 포함 된 `CFBundleURLSchemes` 이름과 일치 하는지 확인 합니다.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8 단계: 리디렉션 URI가 앱에 등록 되었는지 확인

응용 프로그램에 대 한 유효한 리디렉션 URI로 리디렉션 URI를 [앱 등록 포털](https://portal.azure.com) 에 등록 해야 합니다. 

앱 등록 포털은 번들 ID에서 조정 된 회신 URI를 계산 하는 데 도움이 되는 새로운 환경을 제공 합니다. 

리디렉션 URI를 계산 하려면:

1. 앱 등록 포털에서 **인증** > 선택 하 **여 새로운 환경을 사용해 보세요**.

   ![새 앱 등록 환경 사용해 보기](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. **플랫폼 추가를**선택 합니다.

   ![플랫폼 추가](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 플랫폼 목록이 지원 되는 경우 **iOS**를 선택 합니다.

   ![IOS 구성](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 요청에 따라 번들 ID를 입력 하 고 **구성**을 선택 합니다.

   ![번들 ID 입력](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

단계를 완료 하면 리디렉션 URI가 계산 됩니다.

![리디렉션 URI 복사](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Android에 대 한 조정 된 인증

MSAL.NET는 Xamarin.ios 플랫폼만 지원 합니다. Xamarin Android 플랫폼용 broker를 아직 지원 하지 않습니다.

MSAL Android native library는 이미 조정 된 인증을 지원 합니다. 자세한 내용은 [Android에서](brokered-auth.md)조정 된 인증을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[MSAL.NET와 함께 유니버설 Windows 플랫폼를 사용 하기 위한 고려 사항](msal-net-uwp-considerations.md)에 대해 알아봅니다.
