---
title: Microsoft ID 플랫폼 iOS 및 macOS 빠른 시작 | Azure
description: iOS 및 macOS 애플리케이션에서 사용자를 로그인하고 Microsoft Graph를 쿼리하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 39062396e0076af5901f2fc7d76f5c989e2ccc3a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115257"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>빠른 시작: iOS 또는 macOS 앱에서 사용자 로그인 및 Microsoft Graph API 호출

이 빠른 시작에는 네이티브 iOS 또는 macOS 애플리케이션이 Microsoft ID 플랫폼을 사용하여 개인, 회사 및 학교 계정에 로그인하고, 액세스 토큰을 가져오고, Microsoft Graph API를 호출할 수 있는 방법을 보여주는 코드 샘플이 포함되어 있습니다.

이 빠른 시작은 iOS 및 macOS 앱 모두에 적용됩니다. 일부 단계는 iOS 앱에만 필요합니다. 이러한 단계를 iOS 전용이라고 합니다.

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **필수 구성 요소**
> * XCode 10 이상
> * iOS 10 이상
> * macOS 10.12 이상

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하는 옵션은 두 가지가 있습니다.
> * [기본] [옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [수동] [옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 앱을 등록하려면
> 1. 새 [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) 창으로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
> 1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://aka.ms/MobileAppReg) 페이지로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
>      - **이름** 섹션에서 앱에 로그인하거나 동의할 때 앱 사용자에게 표시할 의미 있는 애플리케이션 이름을 입력합니다.
>      - 이 페이지의 다른 구성은 건너뜁니다.
>      - `Register`를 선택합니다.
> 1. **관리** 섹션에서 `Authentication` > `Add Platform` > `iOS`를 선택합니다.
>      - 애플리케이션에 대한 ***번들 식별자***를 입력합니다. 번들 식별자는 애플리케이션을 고유하게 식별하는 고유 문자열(예: `com.<yourname>.identitysample.MSALMacOS`)입니다. 사용하는 값을 기록해 둡니다.
>      - iOS 구성은 macOS 애플리케이션에도 적용할 수 있습니다.
> 1. 이 빠른 시작 후반에 사용할 수 있도록 `Configure`를 선택하고 ***MSAL 구성*** 세부 정보를 저장합니다.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1단계: 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 인증 broker와 호환되는 리디렉션 URI를 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-ios/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.
> 
> #### <a name="step-2-download-the-sample-project"></a>2단계: 샘플 프로젝트 다운로드
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [iOS용 코드 샘플 다운로드]()
> 
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [macOS용 코드 샘플 다운로드]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>2단계: 샘플 프로젝트 다운로드
> 
> - [iOS용 코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [macOS용 코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>3단계: 종속성 설치

터미널 창에서 다운로드한 코드 샘플이 있는 폴더로 이동하고 `pod install`을 실행하여 최신 MSAL 라이브러리를 설치합니다.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>4단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱 속성 값을 사용하여 프로젝트를 구성했고 실행할 준비가 되었습니다.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>4단계: 프로젝트 구성
> 위의 옵션 1을 선택한 경우 이러한 단계를 건너뛸 수 있습니다.
> 1. Zip 파일을 추출하고 XCode에서 프로젝트를 엽니다.
> 1. **ViewController.swift**를 편집하고, 'let kClientID'로 시작하는 줄을 다음 코드 조각으로 바꿉니다. `kClientID` 값은 이 빠른 시작의 앞부분에서 포털에 앱을 등록할 때 저장한 clientID로 업데이트해야 합니다.
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. [Azure AD 국가별 클라우드](/graph/deployments#app-registration-and-token-service-root-endpoints)에 대한 앱을 빌드하는 경우 'let kGraphEndpoint' 및 'let kAuthority'로 시작하는 줄을 올바른 엔드포인트로 바꿉니다. 글로벌 액세스의 경우 기본값을 사용합니다.
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. 다른 엔드포인트는 [여기에](/graph/deployments#app-registration-and-token-service-root-endpoints) 설명되어 있습니다. 예를 들어 Azure AD 독일에서 빠른 시작을 실행하려면 다음을 사용합니다.
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. 프로젝트 설정을 엽니다. **ID** 섹션에서 포털에 입력한 **번들 식별자**를 입력합니다.
> 1. 마우스 오른쪽 단추로 **Info.plist**를 클릭하고, **파일 열기 형식** > **소스 코드**를 차례로 선택합니다.
> 1. dict 루트 노드 아래에서 `Enter_the_bundle_Id_Here`를 포털에서 사용한 ***번들 ID***로 바꿉니다.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. 앱을 빌드하고 실행합니다!

## <a name="more-information"></a>추가 정보

이 빠른 시작에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="get-msal"></a>MSAL 가져오기

MSAL([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))은 사용자를 로그인하고 Microsoft ID 플랫폼에서 보호되는 API 액세스에 사용되는 토큰을 요청하는 데 사용되는 라이브러리입니다. 다음 프로세스를 사용하여 애플리케이션에 MSAL을 추가할 수 있습니다.

```
$ vi Podfile

```
다음을 이 podfile에 추가합니다(프로젝트의 대상 포함).

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

CocoaPods 설치 명령을 실행합니다.

```pod install```

### <a name="initialize-msal"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```swift
import MSAL
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |위치: | Description |
> |---------|---------|
> | `clientId` | *portal.azure.com*에 등록된 애플리케이션의 애플리케이션 ID |
> | `authority` | Microsoft ID 플랫폼 엔드포인트. 대부분의 경우 *https<span/>://login.microsoftonline.com/common*입니다. |
> | `redirectUri` | 애플리케이션의 리디렉션 URI입니다. 'nil'을 전달하여 기본값을 사용하거나 사용자 지정 리디렉션 URI를 사용할 수 있습니다. |

### <a name="for-ios-only-additional-app-requirements"></a>iOS 전용, 추가 앱 요구 사항

앱의 `AppDelegate`에도 다음이 있어야 합니다. 이렇게 하면 인증을 수행할 때 MSAL SDK에서 인증 broker 앱의 토큰 응답을 처리할 수 있습니다.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> iOS 13 이상에서 `UIApplicationDelegate` 대신 `UISceneDelegate`를 채택하는 경우 이 코드를 `scene:openURLContexts:` 콜백에 대신 배치합니다([Apple 설명서](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc) 참조).
> 이전 iOS와의 호환성을 위해 UISceneDelegate 및 UIApplicationDelegate를 모두 지원하는 경우 MSAL 콜백을 두 곳 모두에 배치해야 합니다.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

마지막으로 앱의 ***Info.plist***에 `CFBundleURLTypes`와 함께 `LSApplicationQueriesSchemes` 항목이 있어야 합니다. 샘플에는 이 항목이 포함되어 있습니다.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>사용자 로그인 및 토큰 요청

MSAL에는 토큰 가져오기에 사용되는 두 가지 메서드인 `acquireToken` 및 `acquireTokenSilent`가 있습니다.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: 대화형으로 토큰 가져오기

상황에 따라 사용자가 Microsoft ID 플랫폼과 상호 작용해야 합니다. 이러한 경우 최종 사용자는 해당 계정을 선택하거나, 해당 자격 증명을 입력하거나, 앱의 권한에 동의해야 할 수 있습니다. 예를 들면 다음과 같습니다.

* 처음으로 사용자가 애플리케이션에 로그인한 경우
* 사용자가 자신의 암호를 다시 설정하는 경우 해당 자격 증명을 입력해야 합니다.
* 애플리케이션에서 처음으로 리소스에 대한 액세스를 요청하는 경우
* MFA 또는 기타 조건부 액세스 정책이 필요한 경우

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |위치:| Description |
> |---------|---------|
> | `scopes` | 요청된 범위(즉 Microsoft Graph의 경우 `[ "user.read" ]`, 사용자 지정 웹 API(`api://<Application ID>/access_as_user`)의 경우 `[ "<Application ID URL>/scope" ]`)가 포함됩니다. |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: 자동으로 액세스 토큰 가져오기

사용자가 토큰을 요청할 때마다 앱에서 사용자가 로그인하도록 요구하지 않아야 합니다. 사용자가 이미 로그인한 경우 다음 메서드를 사용하면 앱에서 토큰을 자동으로 요청할 수 있습니다.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |위치: | Description |
> |---------|---------|
> | `scopes` | 요청된 범위(즉 Microsoft Graph의 경우 `[ "user.read" ]`, 사용자 지정 웹 API(`api://<Application ID>/access_as_user`)의 경우 `[ "<Application ID URL>/scope" ]`)가 포함됩니다. |
> | `account` | 토큰을 요청하는 계정입니다. 이 빠른 시작은 단일 계정 애플리케이션에 대한 것입니다. 다중 계정 앱을 빌드하려면 `accountsFromDeviceForParameters:completionBlock:`를 사용하여 토큰 요청에 사용할 계정을 식별하고 올바른 `accountIdentifier`를 전달하는 논리를 정의해야 합니다. |

## <a name="next-steps"></a>다음 단계

이 빠른 시작에 대한 전체 설명을 포함하여 애플리케이션 빌드에 대한 완전한 단계별 가이드로서 iOS 및 macOS용 자습서를 사용해 보세요.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>이 빠른 시작에서 사용되는 애플리케이션을 만드는 방법 알아보기

> [!div class="nextstepaction"]
> [iOS 및 macOS용 Graph API 자습서 호출](./tutorial-v2-ios.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]