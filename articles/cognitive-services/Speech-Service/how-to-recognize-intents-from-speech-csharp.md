---
title: '음성 SDK C를 사용 하 여 음성에서 의도를 인식 하는 방법 #'
titleSuffix: Azure Cognitive Services
description: '이 가이드에서는 c # 용 Speech SDK를 사용 하 여 음성에서 의도를 인식 하는 방법에 대해 알아봅니다.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 1138a970bf7c52182f13d0fd14d0178a2d0cfeba
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918794"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>C 용 Speech SDK를 사용 하 여 음성에서 의도를 인식 하는 방법 #

Cognitive Services [Speech SDK](speech-sdk.md)는 [LUIS(Language Understanding) 서비스](https://www.luis.ai/home)와 통합되어 **의도를 인식**합니다. 의도란 항공권 예약, 날씨 확인, 호출 등 사용자가 수행하려는 것을 말합니다. 사용자는 편한 용어를 사용할 수 있습니다. 기계 학습을 사용하면 LUIS는 사용자 요청을 개발자가 정의한 의도에 매핑합니다.

> [!NOTE]
> LUIS 애플리케이션은 인식할 의도와 엔터티를 정의합니다. 음성 서비스를 사용하는 C# 애플리케이션과는 다릅니다. 이 문서에서 "앱"은 LUIS 앱을 의미하고, "애플리케이션"은 C# 코드를 의미합니다.

이 가이드에서는 Speech SDK를 사용 하 여 장치의 마이크를 통해 사용자 길이 발언의 의도를 파생 하는 c # 콘솔 응용 프로그램을 개발 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
>
> - Speech SDK NuGet 패키지를 참조하는 Visual Studio 프로젝트 만들기
> - 음성 구성을 만들고 의도 인식기 가져오기
> - LUIS 앱에 사용할 모델을 가져오고 필요한 의도 추가
> - 음성 인식에 사용할 언어 지정
> - 파일에서 음성 인식
> - 비동기, 이벤트 중심 연속 인식 사용

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 시작 하기 전에 다음 항목이 있어야 합니다.

- LUIS 계정 [LUIS 포털](https://www.luis.ai/home)을 통해 무료로 얻을 수 있습니다.
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (모든 버전)

## <a name="luis-and-speech"></a>LUIS 및 음성

LUIS는 음성 서비스와 통합되어 음성에서 의도를 인식합니다. 음성 서비스 구독은 필요 없고 LUIS만 있으면 됩니다.

LUIS는 다음과 같은 세 가지 종류의 키를 사용합니다.

| 키 유형  | 목적                                               |
| --------- | ----------------------------------------------------- |
| 작성 | 프로그래밍 방식으로 LUIS 앱을 만들고 수정할 수 있음 |
| Starter   | 텍스트만 사용하여 LUIS 애플리케이션을 테스트할 수 있습니다.   |
| 엔드포인트  | 특정 LUIS 앱에 대한 액세스 권한 부여            |

이 가이드에서는 끝점 키 형식이 필요 합니다. 이 가이드에서는 미리 작성 된 [홈 자동화 앱 사용](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) 빠른 시작을 수행 하 여 만들 수 있는 HOME automation LUIS app 예제를 사용 합니다. LUIS 앱을 직접 만든 경우 그 앱을 사용해도 됩니다.

LUIS 앱을 만들 때 LUIS에서 텍스트 쿼리를 사용하여 앱을 테스트할 수 있도록 시작 키가 자동으로 생성됩니다. 이 키는 음성 서비스 통합을 사용 하지 않으며이 가이드에서는 작동 하지 않습니다. Azure 대시보드에서 LUIS 리소스를 만들고 LUIS 앱에 할당합니다. 이 가이드의 무료 구독 계층을 사용할 수 있습니다.

Azure 대시보드에서 LUIS 리소스를 만든 후에는 [LUIS 포털](https://www.luis.ai/home)에 로그인하고, **내 앱** 페이지에서 애플리케이션을 선택한 다음, 앱의 **관리** 페이지로 전환합니다. 마지막으로, 사이드바에서 **키 및 엔드포인트**를 선택합니다.

![LUIS 포털 키 및 엔드포인트 설정](media/sdk/luis-keys-endpoints-page.png)

**키 및 엔드포인트 설정** 페이지에서

1. **리소스 및 키** 섹션이 나올 때까지 아래로 스크롤한 후 **리소스 할당**을 선택합니다.
1. **앱에 키 할당** 대화 상자에서 다음과 같이 변경합니다.

   - **테넌트**에서 **Microsoft**를 선택합니다.
   - **구독 이름**에서 사용하려는 LUIS 리소스가 포함된 Azure 구독을 선택합니다.
   - **키**에서 앱에 사용하려는 LUIS 리소스를 선택합니다.

   잠시 후 페이지 아래쪽의 표에 새 구독이 나타납니다.

1. 키 옆에 있는 아이콘을 선택하여 클립보드에 복사합니다. 두 키 중 하나를 사용할 수 있습니다.

![LUIS 앱 구독 키](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio에서 음성 프로젝트 만들기

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>코드 추가

다음으로, 프로젝트에 코드를 추가합니다.

1. **솔루션 탐색기**에서 **Program.cs** 파일을 엽니다.

1. 파일 시작 부분의 `using` 문 블록을 다음 선언으로 바꿉니다.

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. 제공 된 `Main()` 메서드를 다음 비동기 동등으로 바꿉니다.

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. 다음과 같이 빈 비동기 메서드 `RecognizeIntentAsync()`를 만듭니다.

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. 이 새 메서드 본문에서 이 코드를 추가합니다.

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. 이 메서드의 자리 표시자를 다음과 같이 LUIS 구독 키, 지역 및 앱 ID로 바꿉니다.

   | 자리표시자 | 다음 항목으로 교체 |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | LUIS 엔드포인트 키 다시, “시작 키”가 아닌 Azure 대시보드에서 이 항목을 가져와야 합니다. [LUIS 포털](https://www.luis.ai/home)의 **관리** 아래에 있는 앱의 **키 및 엔드포인트** 페이지에서 찾을 수 있습니다. |
   | `YourLanguageUnderstandingServiceRegion` | LUIS 구독이 있는 지역의 짧은 식별자(예: 미국 서부를 의미하는 `westus`). [지역](regions.md)을 참조하세요. |
   | `YourLanguageUnderstandingAppId` | LUIS 앱 ID [LUIS 포털](https://www.luis.ai/home)의 앱 **설정** 페이지에서 찾을 수 있습니다. |

이러한 변경 내용을 적용 하면 응용 프로그램을 빌드 (**제어 + Shift + B**) 하 고 (**F5**) 응용 프로그램을 실행할 수 있습니다. 메시지가 표시되면 PC의 마이크에 대고 “Turn off the lights(조명 끄기)”라고 말합니다. 애플리케이션에서 콘솔 창에 결과를 표시합니다.

다음 섹션에는 코드 설명이 포함되어 있습니다.

## <a name="create-an-intent-recognizer"></a>의도 인식기 만들기

먼저 LUIS 엔드포인트 키 및 지역에서 음성 구성을 만들어야 합니다. 음성 구성은 Speech SDK의 다양한 기능에 대한 인식기를 만드는 데 사용할 수 있습니다. 음성 구성은 사용할 구독을 지정하는 여러 가지 방법을 제공하는데, 여기서는 구독 키와 지역을 사용하는 `FromSubscription`으로 하겠습니다.

> [!NOTE]
> 음성 서비스 구독이 아닌 LUIS 구독의 핵심 및 지역을 사용 합니다.

다음으로, `new IntentRecognizer(config)`를 사용하여 의도 인식기를 만듭니다. 어떤 구독을 사용해야 하는지 구성에서 이미 알고 있으므로 인식기를 만들 때 구독 키와 엔드포인트를 다시 지정할 필요가 없습니다.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS 모델을 가져오고 의도 추가

이제 `LanguageUnderstandingModel.FromAppId()`를 사용하여 LUIS 앱에서 모델을 가져오고, 인식기의 `AddIntent()` 메서드를 통해 인식하려는 LUIS 의도를 추가합니다. 이러한 두 단계는 사용자가 요청에서 사용할 가능성이 높은 단어를 지정하여 음성 인식의 정확도를 높입니다. 앱의 모든 의도를 애플리케이션에서 인식해야 하는 것이 아니라면 반드시 모든 앱의 의도를 추가할 필요는 없습니다.

의도를 추가하려면 LUIS 모델(이미 만들어져 있고 이름이 `model`인), 의도 이름 및 의도 ID라는 세 가지 인수가 필요합니다. ID와 이름의 차이는 다음과 같습니다.

| `AddIntent()`&nbsp;argument | 목적 |
| --------------------------- | ------- |
| `intentName` | LUIS 앱에서 정의된 의도의 이름입니다. 이 값은 LUIS 의도 이름과 정확히 일치해야 합니다. |
| `intentID` | Speech SDK가 인식한 의도에 할당되는 ID입니다. 이 값은 개발자가 원하는 대로 할 수 있으며, LUIS 앱에서 정의된 의도 이름과 일치하지 않아도 됩니다. 예를 들어 여러 의도가 동일한 코드를 통해 처리되는 경우 동일한 ID를 사용해도 됩니다. |

홈 자동화 LUIS 앱에는 두 가지 의도가 있는데, 하나는 디바이스를 켜는 용도로, 다른 하나는 디바이스를 끄는 용도로 사용됩니다. 아래는 이러한 의도를 인식기에 추가하는 코드 줄입니다. `RecognizeIntentAsync()` 메서드의 세 `AddIntent` 줄을 이 코드로 바꾸세요.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

개별 의도를 추가하는 대신 `AddAllIntents` 메서드를 사용하여 모델의 모든 의도를 인식기에 추가할 수도 있습니다.

## <a name="start-recognition"></a>인식 시작

인식기를 만들고 의도를 추가했으니, 이제 인식을 시작할 수 있습니다. Speech SDK는 1단계 인식과 연속 인식을 모두 지원합니다.

| 인식 모드 | 호출 방법 | 결과 |
| ---------------- | --------------- | ------ |
| 1단계 | `RecognizeOnceAsync()` | 한 번의 발언 후에 의도를 인식하고, 인식된 의도가 있으면 반환합니다. |
| 계속 | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | 여러 발언를 인식합니다. 결과를 사용할 수 있는 경우 이벤트(예: `IntermediateResultReceived`)를 내보냅니다. |

응용 프로그램은 단일 샷 모드를 사용 하므로 `RecognizeOnceAsync()` 인식을 시작 하기 위해 호출 합니다. 결과는 인식된 의도에 대한 정보를 포함하는 `IntentRecognitionResult` 개체입니다. LUIS JSON 응답은 다음 식을 사용하여 추출됩니다.

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

응용 프로그램이 JSON 결과를 구문 분석 하지 않습니다. JSON 텍스트만 콘솔 창에 표시됩니다.

![단일 LUIS 인식 결과](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>인식 언어 지정

기본적으로 LUIS는 미국 영어(`en-us`)로 의도를 인식합니다. 음성 구성의 `SpeechRecognitionLanguage` 속성에 로캘 코드를 할당하여 다른 언어로 의도를 인식할 수 있습니다. 예를 들어, `config.SpeechRecognitionLanguage = "de-de";` 독일어에서 의도를 인식할 수 있도록 인식기를 만들기 전에 응용 프로그램에를 추가 합니다. 자세한 내용은 [LUIS 언어 지원](../LUIS/luis-language-support.md#languages-supported)을 참조 하세요.

## <a name="continuous-recognition-from-a-file"></a>파일에서 연속 인식

다음 코드는 Speech SDK를 사용하는 의도 인식의 두 가지 추가 기능을 보여줍니다. 앞에서 언급한 첫 번째 기능은 연속 인식으로, 결과가 있으면 인식기가 이벤트를 내보냅니다. 이후 개발자가 제공하는 이벤트 처리기로 이러한 이벤트를 처리할 수 있습니다. 연속 인식에서는 `RecognizeOnceAsync()` 대신 인식기의 `StartContinuousRecognitionAsync()` 메서드를 호출하여 인식을 시작합니다.

다른 기능은 WAV 파일에서 처리할 음성이 포함된 오디오를 읽는 기능입니다. 구현에는 의도 인식기를 만들 때 사용할 수 있는 오디오 구성을 만드는 과정이 포함됩니다. 파일은 샘플링 속도가 16kHz인 단일 채널(모노)이어야 합니다.

이러한 기능을 사용해 보려면 `RecognizeIntentAsync()` 메서드의 본문을 삭제하거나 주석으로 처리하고 그 자리에 다음 코드를 추가합니다.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

이전처럼 LUIS 엔드포인트 키, 지역 및 앱 ID를 포함하고 홈 자동화 의도를 추가하도록 코드를 수정합니다. `whatstheweatherlike.wav`를 레코딩된 오디오 파일 이름으로 변경합니다. 그런 다음, 오디오 파일을 빌드 디렉터리에 복사하고 애플리케이션을 실행합니다.

예를 들어, 레코딩된 오디오 파일에서 “Turn off the lights(조명 끄기)”라고 말하고, 일시 중지한 다음, “Turn on the lights(조명 켜기)”라고 말하면 다음과 유사한 콘솔 출력이 표시될 수 있습니다.

![오디오 파일 LUIS 인식 결과](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 문서에 사용된 코드는 **samples/csharp/sharedcontent/console** 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 마이크에서 음성 인식](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
