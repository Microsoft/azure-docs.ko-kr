---
title: Xamarin Android 코드 구성 및 문제 해결 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Xamarin Android를 사용할 때 고려해 야 할 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 256bee26ec6aef464d9ee838ffca4d8c1ecbbb4e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047789"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>MSAL.NET를 사용 하는 Xamarin Android의 구성 요구 사항 및 문제 해결 팁

Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Xamarin Android를 사용 하는 경우 코드에서 몇 가지 구성 변경을 수행 해야 합니다. 다음 섹션에서는 가장 일반적인 몇 가지 문제를 방지 하기 위해 필요한 수정 사항에 대해 설명 하 고 [문제 해결](#troubleshooting) 섹션을 설명 합니다.

## <a name="set-the-parent-activity"></a>부모 작업 설정

Xamarin Android에서 상호 작용 후 토큰이 반환 되도록 부모 활동을 설정 합니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL.NET 4.2 이상에서는 [Publicclientapplication][PublicClientApplication]수준에서이 기능을 설정할 수도 있습니다. 이렇게 하려면 콜백을 사용 합니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[Currentactivityplugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)를 사용 하는 경우 [`PublicClientApplication`][PublicClientApplication] 작성기 코드는 다음 코드 조각과 유사 하 게 표시 됩니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>컨트롤이 MSAL로 반환 되는지 확인 합니다.

인증 흐름의 대화형 부분이 종료 되 면를 재정의 하 여 MSAL으로 제어를 반환 [`Activity`][Activity] 합니다.[`OnActivityResult()`][OnActivityResult] 메서드를 재정의합니다.

재정의에서 MSAL을 호출 합니다. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` 인증 흐름의 대화형 부분 끝에서 MSAL에 대 한 제어를 반환 하는 메서드입니다.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Android 매니페스트 업데이트

*AndroidManifest.xml* 파일에는 다음 값이 포함 되어야 합니다.

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
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

Azure Portal에서 등록 한 패키지 이름을 값으로 대체 합니다 `android:host=` . Azure Portal에서 등록 한 키 해시를 값으로 대체 합니다 `android:path=` . 서명 *해시는 URL 인코딩되지 않아야 합니다* . 선행 슬래시 ( `/` )가 서명 해시의 시작 부분에 표시 되는지 확인 합니다.

또는 *AndroidManifest.xml*를 수동으로 편집 하 [는 대신 코드에서 작업을 만듭니다](/xamarin/android/platform/android-manifest#the-basics) . 코드에서 활동을 만들려면 먼저 특성 및 특성을 포함 하는 클래스를 만듭니다 `Activity` `IntentFilter` .

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.ios 4.3. x 매니페스트

Xamarin.ios 4.3. x는 `package` `com.companyname.{appName}` *AndroidManifest.xml*에서 특성을로 설정 하는 코드를 생성 합니다. As를 사용 하는 경우 `DataScheme` `msal{client_id}` 네임 스페이스의 값과 일치 하도록 값을 변경 하는 것이 좋습니다 `MainActivity.cs` .

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

## <a name="troubleshooting"></a>문제 해결

### <a name="general-tips"></a>일반 팁

- 기존 MSAL.NET NuGet 패키지를 [최신 버전의 MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)로 업데이트 합니다.
- Xamarin.ios가 최신 버전에 있는지 확인 합니다.
- Xamarin.ios가 최신 버전에 있는지 확인 합니다.
- 모든 Xamarin. 지원 패키지가 최신 버전을 대상으로 하는지 확인 합니다.
- 응용 프로그램을 정리 하거나 다시 빌드합니다.
- Visual Studio에서 최대 병렬 프로젝트 빌드 수를 **1**로 설정 해 봅니다. 이렇게 하려면 **옵션**  >  **프로젝트 및 솔루션**을 선택 하  >  **고**  >  **최대 병렬 프로젝트 수**빌드를 실행 합니다.
- 명령줄에서 빌드하는 경우 명령에서를 사용 하는 경우 `/m` 명령에서이 요소를 제거 합니다.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>오류: AuthenticationContinuationHelper 이름이 현재 컨텍스트에 없습니다.

오류가 현재 컨텍스트에 존재 하지 않는 것으로 표시 되는 경우 `AuthenticationContinuationHelper` Visual Studio는 *Android .csproj \* * 파일을 잘못 업데이트 했을 수 있습니다. 경우에 따라 요소의 파일 경로에 `<HintPath>` 대신이 잘못 포함 `netstandard13` `monoandroid90` 됩니다.

이 예제에는 올바른 파일 경로가 포함 되어 있습니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft id 플랫폼을 사용 하는 Xamarin mobile 응용 프로그램](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)샘플을 참조 하세요. 다음 표에서는 추가 정보 파일의 관련 정보를 요약 하 여 보여 줍니다.

| 예제 | 플랫폼 | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.ios, Android, UWP | MSAL을 사용 하 여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증 하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 앱은 Microsoft Graph 액세스 하 고 결과 토큰을 표시 하는 방법을 보여 줍니다. <br>![인증 흐름 다이어그램](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
