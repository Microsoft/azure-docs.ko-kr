---
title: Xamarin Android 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Xamarin Android를 사용할 때 고려해 야 할 사항에 대해 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132507"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>MSAL.NET와 함께 Xamarin Android 사용에 대 한 고려 사항
이 문서에서는 Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Xamarin Android를 사용할 때 고려해 야 할 사항을 설명 합니다.

## <a name="set-the-parent-activity"></a>부모 작업 설정

Xamarin Android에서 상호 작용 후 토큰이 반환 되도록 부모 활동을 설정 합니다. 다음은 코드 예제입니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL 4.2 이상에서는 `PublicClientApplication`수준에서이 기능을 설정할 수도 있습니다. 이렇게 하려면 콜백을 사용 합니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[Currentactivityplugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)를 사용 하는 경우 `PublicClientApplication` builder 코드는 다음 예제와 같습니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>컨트롤이 MSAL로 반환 되는지 확인 합니다. 
인증 흐름의 대화형 부분이 종료 되 면 컨트롤이 MSAL로 돌아갑니다. Android에서 `Activity`의 `OnActivityResult` 메서드를 재정의 합니다. 그런 다음 `AuthenticationContinuationHelper` MSAL 클래스의 `SetAuthenticationContinuationEventArgs` 메서드를 호출 합니다. 

예를 들면 다음과 같습니다.

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

이 줄은 인증 흐름의 대화형 부분 끝에서 컨트롤이 MSAL으로 반환 되도록 합니다.

## <a name="update-the-android-manifest"></a>Android 매니페스트 업데이트
*Androidmanifest .xml* 파일에는 다음 값이 포함 되어야 합니다.

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Azure Portal에서 등록 한 패키지 이름을 `android:host=` 값으로 대체 합니다. Azure Portal에서 등록 한 키 해시를 `android:path=` 값으로 대체 합니다. 서명 *해시는 URL 인코딩되지 않아야 합니다* . 선행 슬래시 (`/`)가 서명 해시의 시작 부분에 표시 되는지 확인 합니다.

또는 수동으로 *Androidmanifest*을 편집 하 [는 대신 코드에서 활동을 만듭니다](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) . 코드에서 활동을 만들려면 먼저 `Activity` 특성 및 `IntentFilter` 특성을 포함 하는 클래스를 만듭니다. 

XML 파일의 값을 나타내는 클래스의 예는 다음과 같습니다.

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.ios 4.3. X 매니페스트

Xamarin.ios 4.3. x는 `package` 특성을 *Androidmanifest*에서 `com.companyname.{appName}`로 설정 하는 코드를 생성 합니다. `DataScheme`를 `msal{client_id}`사용 하는 경우 `MainActivity.cs` 네임 스페이스의 값과 일치 하도록 값을 변경 하는 것이 좋습니다.

## <a name="use-the-embedded-web-view-optional"></a>포함 된 웹 보기 사용 (선택 사항)

기본적으로 MSAL.NET는 시스템 웹 브라우저를 사용 합니다. 이 브라우저를 사용 하면 웹 응용 프로그램 및 다른 앱을 사용 하 여 Single Sign-On (SSO)을 가져올 수 있습니다. 경우에 따라 시스템에서 포함 된 웹 보기를 사용 하는 것이 좋습니다. 

이 코드 예제에서는 포함 된 웹 보기를 설정 하는 방법을 보여 줍니다.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

자세한 내용은 [웹 브라우저를 사용 하 여 MSAL.NET](msal-net-web-browsers.md) 및 [Xamarin Android 시스템 브라우저 고려 사항](msal-net-system-browser-android-considerations.md)을 참조 하세요.


## <a name="troubleshoot"></a>문제 해결
새 Xamarin Forms 응용 프로그램을 만들고 MSAL.NET NuGet 패키지에 대 한 참조를 추가할 수 있습니다.
그러나 기존 Xamarin.ios 응용 프로그램을 MSAL.NET preview 1.1.2 이상으로 업그레이드 하는 경우 빌드 문제가 발생할 수 있습니다.

빌드 문제를 해결 하려면 다음을 수행 합니다.

- 기존 MSAL.NET NuGet 패키지를 MSAL.NET preview 1.1.2 이상으로 업데이트 합니다.
- Xamarin 양식이 2.5.0.122203 버전으로 자동으로 업데이트 되는지 확인 합니다. 필요한 경우 Xamarin.ios를이 버전으로 업데이트 합니다.
- 25.4.0.2 버전이 자동으로 업데이트 되는지 확인 합니다. 필요한 경우 25.4.0.2 버전으로 업데이트 합니다.
- 모든 Xamarin.ios 지원 패키지가 25.4.0.2 버전을 대상으로 하는지 확인 합니다.
- 응용 프로그램을 정리 하거나 다시 빌드합니다.
- Visual Studio에서 최대 병렬 프로젝트 빌드 수를 1로 설정 해 봅니다. 이렇게 하려면 **최대 병렬 프로젝트 수** > **빌드 및 실행** > **옵션** > **프로젝트 및 솔루션** 을 선택 합니다.
- 명령줄에서 빌드하는 경우 명령에서 `/m`사용 하는 경우 명령에서이 요소를 제거해 보세요.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>오류: AuthenticationContinuationHelper 이름이 현재 컨텍스트에 없습니다.

현재 컨텍스트에 `AuthenticationContinuationHelper` 없음을 나타내는 오류가 발생 하는 경우 Visual Studio는 Android .csproj * 파일을 잘못 업데이트 했을 수 있습니다. 경우에 따라 *\<HintPath >* 파일 경로에 *monoandroid90*대신 *netstandard13* 이 잘못 포함 됩니다.

이 예제에는 올바른 파일 경로가 포함 되어 있습니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft id 플랫폼을 사용 하는 Xamarin mobile 응용 프로그램](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)샘플을 참조 하세요. 다음 표에서는 추가 정보 파일의 관련 정보를 요약 하 여 보여 줍니다.

| 샘플 | 플랫폼 | 설명 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.ios, Android, UWP | MSAL을 사용 하 여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증 하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 앱은 Microsoft Graph 액세스 하 고 결과 토큰을 표시 하는 방법을 보여 줍니다. <br>![토폴로지](media/msal-net-xamarin-android-considerations/topology.png) |
