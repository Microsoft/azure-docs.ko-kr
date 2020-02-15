---
title: MSAL(iOS/macOS)에서 리디렉션 URI 사용 | Azure
titleSuffix: Microsoft identity platform
description: ObjectiveC용 Microsoft 인증 라이브러리(iOS 및 macOS용 MSAL)와 ObjectiveC용 Azure AD 인증 라이브러리(ADAL.ObjC)의 차이점과 이들 사이의 마이그레이션 방법에 대해 알아 봅니다.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085632"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>iOS 및 macOS용 Microsoft 인증 라이브러리를 통해 리디렉션 URI 사용

사용자 인증 시 Azure AD(Azure Active Directory)는 Azure AD 애플리케이션에 등록된 리디렉션 URI를 사용하여 앱에 토큰을 보냅니다.

MSAL(Microsoft 인증 라이브러리)을 사용하려면 리디렉션 URI를 특정 형식으로 Azure AD 앱에 등록해야 합니다. 이를 지정하지 않으면 MSAL은 기본 리디렉션 URI를 사용합니다. 형식은 `msauth.[Your_Bundle_Id]://auth`입니다.

기본 리디렉션 URI 형식은 조정된 인증 및 시스템 웹 보기를 비롯한 대부분의 앱 및 시나리오에서 작동합니다. 가능하면 기본 형식을 사용하십시오.

단, 고급 시나리오의 경우 아래에 설명된 대로 리디렉션 URI를 변경해야 할 수도 있습니다.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>다른 리디렉션 URI가 필요한 시나리오

### <a name="cross-app-single-sign-on-sso"></a>앱 간의 SSO(Single Sign-On)

Microsoft Identity 플랫폼이 여러 앱 간에 토큰을 공유하려면 각 앱의 클라이언트 ID 또는 애플리케이션 ID가 동일해야 합니다. 이것은 앱을 포털에 등록할 때 제공되는 고유 식별자입니다(Apple에서 앱별로 등록하는 애플리케이션 번들 ID는 아님).

리디렉션 URI는 iOS 앱마다 달라야 합니다. 그래야 애플리케이션 ID를 공유하는 다른 앱을 Microsoft ID 서비스가 고유하게 식별할 수 있습니다. 각 애플리케이션은 Azure Portal에 여러 리디렉션 URI를 등록할 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 다음은 그 예입니다.

Azure Portal에 다음과 같은 애플리케이션 등록이 있는 경우,

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1은 리디렉션 `msauth.com.contoso.app1://auth`를, App2는 `msauth.com.contoso.app2://auth`를 App3은 `msauth.com.contoso.app1://auth`를 사용합니다.

### <a name="migrating-from-adal-to-msal"></a>ADAL에서 MSAL로 마이그레이션

ADAL(Azure AD 인증 라이브러리)을 사용하는 코드를 MSAL로 마이그레이션하는 경우, 앱에 리디렉션 URI가 이미 구성되어 있을 수 있습니다. ADAL 앱이 조정된 시나리오를 지원하도록 구성되어 있고 리디렉션 URI가 MSAL 리디렉션 URI 형식 요구 사항을 충족하는 한 동일한 리디렉션 URI를 계속 사용할 수 있습니다.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL 리디렉션 URI 형식 요구 사항

* MSAL 리디렉션 URI는 `<scheme>://host` 형식이어야 합니다.

    여기서 `<scheme>`은 앱을 식별하는 고유 문자열입니다. 주로 애플리케이션의 번들 식별자를 기반으로 고유성을 보장합니다. 예를 들어 앱의 번들 ID가 `com.contoso.myapp`이면 리디렉션 URI는 `msauth.com.contoso.myapp://auth` 형식입니다.

    ADAL에서 마이그레이션하는 경우 리디렉션 URI는 `<scheme>://[Your_Bundle_Id]` 형식이고, 여기서 `scheme`은 고유 문자열입니다. 이 형식은 MSAL을 사용하는 경우 계속 작동합니다.

* `<scheme>`은 앱의 Info.plist에 `CFBundleURLTypes > CFBundleURLSchemes` 아래에 등록되어 있어야 합니다.  이 예제에서 Info.plist가 소스 코드로 열렸습니다.

    ```xml
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
    

MSAL은 리디렉션 URI가 올바르게 등록되었는지 확인하고 그렇지 않으면 오류를 반환합니다.
    
* 유니버설 링크를 리디렉션 URI로 사용하려면 `<scheme>`이 `https`여야 하고 `CFBundleURLSchemes`에서 선언할 필요가 없습니다. 대신 [개발자용 유니버설 링크](https://developer.apple.com/ios/universal-links/)에서 Apple의 지침에 따라 앱과 도메인을 구성하고, 유니버설 링크를 통해 애플리케이션이 열릴 때 `MSALPublicClientApplication`의 `handleMSALResponse:sourceApplication:` 메서드를 호출합니다.

## <a name="use-a-custom-redirect-uri"></a>사용자 지정 리디렉션 URI 사용

사용자 지정 리디렉션 URI를 사용하려면 `redirectUri` 매개 변수를 `MSALPublicClientApplicationConfig`에 전달하고 개체를 초기화할 때 해당 개체를 `MSALPublicClientApplication`에 전달합니다. 리디렉션 URI가 유효하지 않으면 이니셜라이저는 `nil`을 반환하고 추가 정보를 사용하여 `redirectURIError`를 설정합니다.  다음은 그 예입니다.

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>열린 URL 이벤트 처리

애플리케이션은 URL 스키마나 유니버설 링크를 통해 응답을 받으면 MSAL을 호출해야 합니다. 애플리케이션이 열리면 `MSALPublicClientApplication`의 `handleMSALResponse:sourceApplication:` 메서드를 호출합니다. 다음은 사용자 지정 스키마의 예입니다.

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
