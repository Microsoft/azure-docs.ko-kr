---
title: '빠른 시작: 마이크에서 음성 인식, C#(UWP) - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: e3d169ac42e03f0c57ea1c23247fd0daa1cf51d0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926442"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 수행합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

이미 이 작업을 수행한 경우 매우 유용합니다. 계속 진행하겠습니다.

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

첫 번째 단계로 Visual Studio에서 프로젝트를 열어야 합니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

1. **솔루션 탐색기**에서 `MainPage.xaml` 파일을 엽니다.

2. 디자이너의 XAML 뷰에서 다음 XAML 코드 조각을 **Grid** 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml`로 그룹화되어 있습니다.

4. 코드를 다음 기본 코드로 바꿉니다.

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`SpeechRecognizer` 개체를 초기화하기 전에 구독 키와 구독 지역을 사용하는 구성을 만들어야 합니다. `SpeechRecognitionFromMicrophone_ButtonClicked()` 메서드에 이 코드를 삽입합니다.

> [!NOTE]
> 이 샘플에서는 `FromSubscription()` 메서드를 사용하여 `SpeechConfig`를 빌드합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]를 참조하세요.

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer 초기화

이제 `SpeechRecognizer`를 만들어 보겠습니다. 이 개체는 관리되지 않는 리소스의 적절한 릴리스를 보장하기 위해 using 문 내에 생성됩니다. `SpeechRecognitionFromMicrophone_ButtonClicked()` 메서드(음성 구성 바로 아래)에 이 코드를 삽입합니다.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>구문 인식

`SpeechRecognizer` 개체에서 `RecognizeOnceAsync()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

Speech Service에서 인식 결과가 반환되면 이에 따라 작업을 수행합니다. 작업을 간단하게 유지하고 결과를 상태 패널에 인쇄합니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

이제 애플리케이션을 빌드하고 테스트할 준비가 되었습니다.

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 음성 인식 애플리케이션 - 빠른 시작](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. **마이크 사용**을 선택하고, 액세스 권한 요청이 팝업되면 **예**를 선택합니다.

   ![마이크 액세스 권한 요청](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **마이크 입력을 사용하여 음성 인식**을 클릭하고 디바이스의 마이크에 짧은 영어 구나 문장을 말합니다. 음성은 음성 서비스로 전송되어 텍스트로 변환되고 창에 표시됩니다.

   ![음성 인식 사용자 인터페이스](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

