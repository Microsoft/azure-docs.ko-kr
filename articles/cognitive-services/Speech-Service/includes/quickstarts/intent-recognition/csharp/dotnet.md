---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 70dc07fcbfc740ce0ec6b7a841221e3b0af5f7d6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925977"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">개발 환경용 Speech SDK를 설치하고 빈 샘플 프로젝트를 만듭니다<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>의도 인식을 위한 LUIS 앱 만들기

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

그런 다음, Visual Studio에서 프로젝트를 엽니다.

1. Visual Studio 2019를 시작합니다.
2. 프로젝트를 로드하고 `Program.cs`를 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다. `RecognizeIntentAsync()`라는 비동기 메서드를 만듭니다.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`IntentRecognizer` 개체를 초기화하기 전에 LUIS 예측 리소스의 키와 위치를 사용하는 구성을 만들어야 합니다.

> [!IMPORTANT]
> 시작 키와 제작 키가 작동하지 않습니다. 이전에 만든 예측 키와 위치를 사용해야 합니다. 자세한 내용은 [의도 인식을 위한 LUIS 앱 만들기](#create-a-luis-app-for-intent-recognition)를 참조하세요.

`RecognizeIntentAsync()` 메서드에 이 코드를 삽입합니다. 다음 값을 업데이트해야 합니다.

* `"YourLanguageUnderstandingSubscriptionKey"`를 LUIS 예측 키로 바꿉니다.
* `"YourLanguageUnderstandingServiceRegion"`을 LUIS 위치로 바꿉니다. [지역](https://aka.ms/speech/sdkregion)의 **영역 식별자**를 사용합니다.

>[!TIP]
> 이러한 값을 찾는 데 도움이 필요한 경우 [의도 인식을 위한 LUIS 앱 만들기](#create-a-luis-app-for-intent-recognition)를 참조하세요.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

이 샘플에서는 `FromSubscription()` 메서드를 사용하여 `SpeechConfig`를 빌드합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)를 참조하세요.

Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer 초기화

이제 `IntentRecognizer`를 만들어 보겠습니다. 이 개체는 관리되지 않는 리소스의 적절한 릴리스를 보장하기 위해 using 문 내에 생성됩니다. `RecognizeIntentAsync()` 메서드(음성 구성 바로 아래)에 이 코드를 삽입합니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel 및 의도 추가

`LanguageUnderstandingModel`을 의도 인식기와 연결하고 인식하려는 의도를 추가해야 합니다. 홈 자동화를 위해 미리 빌드된 도메인에서 의도를 사용할 것입니다. 이 코드를 이전 섹션의 using 문에 삽입합니다. `"YourLanguageUnderstandingAppId"`를 LUIS 앱 ID로 바꾸어야 합니다.

>[!TIP]
> 이 값을 찾는 데 도움이 필요한 경우 [의도 인식을 위한 LUIS 앱 만들기](#create-a-luis-app-for-intent-recognition)를 참조하세요.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

이 예제에서는 `AddIntent()` 함수를 사용하여 개별적으로 의도를 추가합니다. 모델에서 모든 의도를 추가하려면 `AddAllIntents(model)`을(를) 사용하고 모델을 전달합니다. 

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `RecognizeOnceAsync()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.

using 문 내에서 이 코드를 모델 아래에 추가합니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

Speech Service에서 인식 결과가 반환되면 이에 따라 작업을 수행합니다. 작업을 간단하게 유지하고 결과를 콘솔에 출력합니다.

using 문의 `RecognizeOnceAsync()` 아래에 다음 코드를 추가합니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.

> [!NOTE]
> 이 버전에 몇 가지 주석을 추가했습니다.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

1. **코드 컴파일** - Visual Studio의 메뉴 모음에서 **빌드** > **빌드 솔루션**을 선택합니다.
2. **앱 시작** - 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 <kbd>F5</kbd> 키를 누릅니다.
3. **인식 시작** - 영어로 문구를 말하라는 메시지가 표시됩니다. 음성은 Speech Service로 전송되어 텍스트로 변환되고 콘솔에 렌더링됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
