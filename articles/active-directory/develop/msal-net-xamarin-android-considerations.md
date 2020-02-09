---
title: Xamarin Android 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)에서 Xamarin Android를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fd6dd6781b808bc454a402a55aac9d07a6fc23b0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085824"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET를 사용 하 여 Xamarin Android 관련 고려 사항
이 문서에서는 MSAL.NET (Microsoft Authentication Library for .NET)에서 Xamarin Android를 사용할 때의 구체적인 고려 사항을 설명 합니다.

## <a name="set-the-parent-activity"></a>부모 작업 설정

Xamarin에서 상호 작용이 발생 한 후 토큰을 다시 사용할 수 있도록 부모 활동을 설정 해야 합니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
콜백을 통해 PublicClientApplication 수준 (MSAL 4.2 이상)에서이를 설정할 수도 있습니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[여기서](https://github.com/jamesmontemagno/CurrentActivityPlugin)는 CurrentActivityPlugin를 사용 하는 것이 좋습니다.  그런 다음 PublicClientApplication builder 코드는 다음과 같습니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>인증 흐름의 대화형 부분이 종료 되 면 컨트롤이 MSAL으로 다시 이동 하도록 보장
Android에서 `Activity`의 `OnActivityResult` 메서드를 재정의 하 고 AuthenticationContinuationHelper MSAL 클래스의 SetAuthenticationContinuationEventArgs 메서드를 호출 해야 합니다.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
그러면 인증 흐름의 대화형 부분이 종료 되 면 컨트롤이 MSAL로 돌아갑니다.

## <a name="update-the-android-manifest"></a>Android 매니페스트 업데이트
`AndroidManifest.xml`에는 다음 값이 포함 되어야 합니다.

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">< 의도-필터 > <action android:name="android.intent.action.VIEW" /> <category android:name="android.intent.category.DEFAULT" /> <category android:name="android.intent.category.BROWSABLE" /> <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" /> </intent-filter ></activity>
```
Substitute the package name you registered in the Azure portal for the `android:host=` value. Substitute the key hash you registered in the Azure portal for the `android:path=` value. The Signature Hash should **not** be URL encoded. Ensure that there is a leading `/` at the beginning of your Signature Hash.

Or, you can [create the activity in code](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) and not manually edit `AndroidManifest.xml`. For that, you must create a class that has the `Activity` and `IntentFilter` attribute. A class that represents the same values of the above xml would be:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X 매니페스트

XamarinForms 4.3. x에 의해 생성 된 코드는 `AndroidManifest.xml`에서 `com.companyname.{appName}` `package` 특성을 설정 합니다. `DataScheme`을 `msal{client_id}`사용 하는 경우 `MainActivity.cs` 네임 스페이스와 동일 하 게 값을 변경할 수 있습니다.

## <a name="use-the-embedded-web-view-optional"></a>포함 된 웹 보기 사용 (선택 사항)

기본적으로 MSAL.NET는 웹 응용 프로그램 및 다른 앱을 사용 하 여 SSO를 가져올 수 있는 시스템 웹 브라우저를 사용 합니다. 경우에 따라 포함 된 웹 보기를 사용 하도록 지정할 수 있습니다. 자세한 내용은 웹 브라우저 및 [Android 시스템 브라우저](msal-net-system-browser-android-considerations.md)를 [사용 하는 MSAL.NET](msal-net-web-browsers.md) 을 참조 하세요.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>문제 해결
새 Xamarin Forms 응용 프로그램을 만들고 MSAL.Net NuGet 패키지에 대 한 참조를 추가 하는 경우에만 작동 합니다.
그러나 기존 Xamarin.ios 응용 프로그램을 MSAL.NET preview 1.1.2 이상으로 업그레이드 하려는 경우 빌드 문제가 발생할 수 있습니다.

이러한 문제를 해결 하려면 다음을 수행 해야 합니다.
- 기존 MSAL.NET NuGet 패키지를 MSAL.NET preview 1.1.2 이상으로 업데이트 합니다.
- Xamarin.ios가 2.5.0.122203 버전으로 자동으로 업데이트 되는지 확인 합니다 (그렇지 않은 경우이 버전으로 업데이트).
- 25.4.0.2 버전이 자동으로 업데이트 되는지 확인 합니다 (그렇지 않은 경우이 버전으로 업데이트).
- 모든 Xamarin.ios. 지원 패키지는 버전 25.4.0.2를 대상으로 해야 합니다.
- 정리/다시 빌드
- Visual Studio에서 최대 병렬 프로젝트 빌드를 1로 설정 해 봅니다 (옵션-> 프로젝트 및 솔루션-> 빌드 및 실행 > 최대 병렬 프로젝트 수 빌드).
- 또는 명령줄에서 빌드하는 경우 사용 하는 경우 명령에서/m을 제거 해 보세요.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>오류: ' AuthenticationContinuationHelper ' 이름이 현재 컨텍스트에 없습니다.

Visual Studio에서 Android .csproj * 파일을 올바르게 업데이트 하지 않았기 때문일 수 있습니다. 경우에 따라 **\<HintPath >** filepath에 **monoandroid90**대신 netstandard13이 잘못 포함 됩니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

다음 샘플의 readme.md 파일에 있는 [Android 관련 고려 사항](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) 단락에서 자세한 내용 및 샘플을 제공 합니다.

| 샘플 | 플랫폼 | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | 간단한 Xamarin Forms 앱은 MSAL을 사용 하 여 AADD v2.0 엔드포인트을 통해 MSA 및 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph에 액세스 하는 방법을 보여주는 합니다. <br>![토폴로지](media/msal-net-xamarin-android-considerations/topology.png) |
