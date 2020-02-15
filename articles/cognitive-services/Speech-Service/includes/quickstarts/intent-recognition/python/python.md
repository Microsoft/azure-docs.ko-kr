---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9bc514c32d97958d8ab154dc2c5533747f380e86
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900429"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에:

* 첫 번째 Python 프로젝트인 경우 이 가이드를 사용하여 <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md" target="_blank">빈 샘플 프로젝트를 만듭니다<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">개발 환경용 Speech SDK를 설치합니다<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>의도 인식을 위한 LUIS 앱 만들기

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>프로젝트 열기

1. 선호하는 IDE를 엽니다.
2. 새 프로젝트를 만들고 `quickstart.py`라는 파일을 만든 다음, 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`IntentRecognizer` 개체를 초기화하기 전에 LUIS 예측 리소스의 키와 위치를 사용하는 구성을 만들어야 합니다.

이 코드를 `quickstart.py`에 삽입합니다. 다음 값을 업데이트해야 합니다.

* `"YourLanguageUnderstandingSubscriptionKey"`를 LUIS 예측 키로 바꿉니다. 
* `"YourLanguageUnderstandingServiceRegion"`을 LUIS 위치로 바꿉니다. 

>[!TIP]
> 이러한 값을 찾는 데 도움이 필요한 경우 [의도 인식을 위한 LUIS 앱 만들기](#create-a-luis-app-for-intent-recognition)를 참조하세요.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

이 샘플은 LUIS 키 및 지역을 사용하여 `SpeechConfig` 개체를 생성합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)를 참조하세요.

Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer 초기화

이제 `IntentRecognizer`를 만들어 보겠습니다. 음성 구성 바로 아래에 이 코드를 삽입합니다.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel 및 의도 추가

`LanguageUnderstandingModel`을 의도 인식기와 연결하고 인식되길 원하는 의도를 추가해야 합니다. 홈 자동화를 위해 미리 빌드된 도메인에서 의도를 사용할 것입니다.

이 코드를 `IntentRecognizer` 아래에 삽입합니다. `"YourLanguageUnderstandingAppId"`를 LUIS 앱 ID로 바꾸어야 합니다. 

>[!TIP]
> 이 값을 찾는 데 도움이 필요한 경우 [의도 인식을 위한 LUIS 앱 만들기](#create-a-luis-app-for-intent-recognition)를 참조하세요.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `recognize_once()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.

이 코드를 모델 아래에 삽입합니다.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

Speech Service에서 인식 결과가 반환되면 이에 따라 작업을 수행합니다. 작업을 간단하게 유지하고 결과를 콘솔에 출력합니다.

`recognize_once()`에 대한 호출 아래에 다음 코드를 추가합니다. [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.

> [!NOTE]
> 이 버전에 몇 가지 주석을 추가했습니다.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

콘솔 또는 IDE에서 샘플을 실행합니다.

```
python quickstart.py
```

마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
