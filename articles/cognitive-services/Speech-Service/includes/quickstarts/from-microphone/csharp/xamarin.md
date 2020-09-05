---
title: '빠른 시작: 마이크에서 음성 인식, C#(Xamarin) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK를 사용하여 UWP(유니버설 Windows 플랫폼), Android 및 iOS용 플랫폼 간 C# Xamarin 애플리케이션을 만듭니다. 디바이스 또는 시뮬레이터의 마이크에서 실시간으로 음성을 텍스트로 변환합니다. 이 애플리케이션은 Speech SDK NuGet 패키지와 Microsoft Visual Studio 2019로 빌드되었습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48a80d7367943c2c86fd3822a5c236b0cdf11846
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926443"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에:

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

이미 이 작업을 수행한 경우 매우 유용합니다. 계속 진행하겠습니다.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>일반적인 helloworld 프로젝트용 샘플 코드 추가

일반적인 helloworld 프로젝트에는 플랫폼 간 애플리케이션에 대한 플랫폼 독립적 구현이 포함되어 있습니다. 이제 애플리케이션의 사용자 인터페이스를 정의하는 XAML 코드를 추가하고, C# 코드 숨김을 구현 뒤에 추가합니다.

1. **솔루션 탐색기**의 일반 helloworld 프로젝트 아래에서 `MainPage.xaml`을 엽니다.

1. 디자이너의 XAML 보기에서 다음 XAML 코드 조각을 `<StackLayout>`과 `</StackLayout>` 사이의 **Grid** 태그에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml` 아래에 그룹화되어 있습니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 원본 파일의 `OnRecognitionButtonClicked` 처리기에서 `YourSubscriptionKey` 문자열을 찾아 구독 키로 바꿉니다.


1. `OnRecognitionButtonClicked` 처리기에서 `YourServiceregion` 문자열을 찾아 구독과 연결된 [지역](https://aka.ms/speech/sdkregion)의 **영역 식별자**로 바꿉니다. 

1. 다음으로, 다른 플랫폼 프로젝트(예: UWP, Android 및 iOS)에서 마이크 권한을 쿼리하는 데 사용되는 [Xamarin 서비스](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)를 만들어야 합니다. 이렇게 하려면 *Services*라는 새 폴더를 hellowworld 프로젝트 아래에 추가하고, 그 아래에 새 C# 소스 파일을 만듭니다. 마우스 오른쪽 단추로 *Services* 폴더를 클릭하고, **추가** > **새 항목** > **코드 파일**을 차례로 선택할 수 있습니다. 파일 이름을 `IMicrophoneService.cs`로 바꾸고, 다음 코드 조각의 모든 코드를 해당 파일에 넣습니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>`helloworld.Android` 프로젝트용 샘플 코드 추가

이제 애플리케이션의 Android 관련 부분을 정의하는 C# 코드를 추가합니다.

1. **솔루션 탐색기**의 helloworld.Android 프로젝트 아래에서 `MainActivity.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 다음으로, 새 *Services* 폴더를 helloworld.Android 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 Android 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 그 아래에 만듭니다. 파일 이름을 `MicrophoneService.cs`로 바꿉니다. 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 그런 다음, *Properties* 폴더 아래에서 `AndroidManifest.xml`을 엽니다. 마이크에 대한 다음 uses-permission 설정을 `<manifest>`와 `</manifest>` 사이에 추가합니다.

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>`helloworld.iOS` 프로젝트용 샘플 코드 추가

이제 애플리케이션의 iOS 관련 부분을 정의하는 C# 코드를 추가합니다. 또한 helloworld.iOS 프로젝트에 대한 Apple 디바이스 특정 구성을 만듭니다.

1. **솔루션 탐색기**의 helloworld.iOS 프로젝트 아래에서 `AppDelegate.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 다음으로, 새 *Services* 폴더를 helloworld.iO 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 iOS 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 그 아래에 만듭니다. 파일 이름을 `MicrophoneService.cs`로 바꿉니다. 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. 텍스트 편집기의 helloworld.iOS 프로젝트 아래에서 `Info.plist`를 엽니다. 다음 키 값 쌍을 dict 섹션 아래에 추가합니다.

   <key>NSMicrophoneUsageDescription</key>
   <string>이 샘플 앱에는 마이크 액세스가 필요합니다.</string>

   > [!NOTE]
   > iPhone 디바이스용으로 빌드하는 경우 `Bundle Identifier`가 디바이스의 프로비저닝 프로필 앱 ID와 일치하는지 확인하세요. 그렇지 않으면 빌드가 실패합니다. iPhoneSimulator를 사용하면 그대로 둘 수 있습니다.

1. Windows PC에서 빌드하는 경우 **도구** > **iOS** > **Mac에 페어링**을 통해 빌드할 Mac 디바이스에 대한 연결을 설정합니다. Visual Studio에서 제공하는 지침 마법사에 따라 Mac 디바이스에 대한 연결을 설정합니다.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>`helloworld.UWP` 프로젝트용 샘플 코드 추가

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>helloworld.UWP 프로젝트의 샘플 코드 추가

이제 애플리케이션의 UWP 관련 부분을 정의하는 C# 코드를 추가합니다.

1. **솔루션 탐색기**의 helloworld.UWP 프로젝트 아래에서 `MainPage.xaml.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 다음으로, 새 *Services* 폴더를 helloworld.UWP 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 UWP 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 그 아래에 만듭니다. 파일 이름을 `MicrophoneService.cs`로 바꿉니다. 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 다음으로, Visual Studio의 helloworld.UWP 프로젝트 아래에서 `Package.appxmanifest` 파일을 두 번 클릭합니다. **기능** 아래에서 **마이크**가 선택되어 있는지 확인하고, 파일을 저장합니다.

1. 다음으로, Visual Studio 내의 `helloworld.UWP` 프로젝트 아래에서 `Package.appxmanifest` 파일을 두 번 클릭하고 **기능** > **마이크**를 차례로 선택한 다음, 파일을 저장합니다.
   > 참고: "Hellowworld.UWP_TemporaryKey.pfx 인증서 파일이 없습니다."라는 경고가 표시되는 경우 자세한 내용은 [음성 텍스트 변환](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) 샘플을 확인하세요.

1. 메뉴 모음에서 **파일** > **모두 저장**을 차례로 선택하여 변경 내용을 저장합니다.

## <a name="build-and-run-the-uwp-application"></a>UWP 애플리케이션 빌드 및 실행

1. helloworld.UWP를 시작 프로젝트로 설정합니다. 마우스 오른쪽 단추로 helloworld.UWP 프로젝트를 클릭하고, **빌드**를 선택하여 애플리케이션을 빌드합니다.

1. **디버그** > **디버깅 시작**을 차례로 선택하거나 **F5** 키를 선택하여 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 음성 인식 애플리케이션 - 빠른 시작](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. **마이크 사용**을 선택합니다. 액세스 권한 요청이 표시되면 **예**를 선택합니다.

   ![마이크 액세스 권한 요청](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. **음성 인식 시작**을 선택하고, 디바이스의 마이크에 영어 문구 또는 문장을 말합니다. 음성은 음성 서비스로 전송되어 텍스트로 변환되고 창에 표시됩니다.

   ![음성 인식 사용자 인터페이스](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Android 및 iOS 애플리케이션 빌드 및 실행

디바이스 또는 시뮬레이터에서 Android 및 iOS 애플리케이션을 빌드하고 실행하는 것은 UWP와 비슷한 방식으로 수행됩니다. 이 문서의 "필수 조건" 섹션에서 요구한 대로 모든 SDK가 제대로 설치되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
