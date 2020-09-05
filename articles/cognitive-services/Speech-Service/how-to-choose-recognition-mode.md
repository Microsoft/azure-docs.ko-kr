---
title: 음성 SDK를 사용 하 여 음성 인식 모드 선택
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용 하는 경우 가장 적합 한 인식 모드를 선택 하는 방법을 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 29dcd3ddc32ac92f7543e5d7648680e36f50dcfb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934602"
---
# <a name="choose-a-speech-recognition-mode"></a>음성 인식 모드 선택

음성-텍스트 인식 작업을 고려할 때 speech [SDK](speech-sdk.md) 는 음성 처리를 위한 여러 모드를 제공 합니다. 개념적으로 *인식 모드*라고도 합니다. 이 문서에서는 다양 한 인식 모드를 비교 합니다.

## <a name="recognize-once"></a>한 번 인식

각 utterance 한 번에 하나씩 처리 하려면 "한 번 인식" 함수를 사용 합니다. 이 메서드는 다음 일시 중지 될 때까지 검색 된 음성이 시작 될 때부터 입력 으로부터 인식 된 utterance를 검색 합니다. 일반적으로 일시 중지는 문장이 나 사고의 줄의 끝을 표시 합니다.

인식 된 하나의 utterance 끝에서 서비스는 해당 요청에서 오디오 처리를 중지 합니다. 최대 인식 제한은 20 초의 문장 기간입니다.

::: zone pivot="programming-language-csharp"

함수 사용에 대 한 자세한 내용은 `RecognizeOnceAsync` [.NET Speech SDK 문서](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)를 참조 하세요.

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

함수 사용에 대 한 자세한 내용은 `RecognizeOnceAsync` [c + + Speech SDK 문서](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)를 참조 하세요.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

함수 사용에 대 한 자세한 내용은 `recognizeOnceAsync` [JAVA Speech SDK 문서](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)를 참조 하세요.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

함수 사용에 대 한 자세한 내용은 `recognize_once` [PYTHON Speech SDK 문서](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)를 참조 하세요.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

함수 사용에 대 한 자세한 내용은 `recognizeOnceAsync` [JavaScript 용 Speech SDK 문서](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)를 참조 하세요.

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

추가 언어는 [SPEECH SDK 참조 문서](speech-to-text.md#speech-sdk-reference-docs)를 참조 하세요.

::: zone-end

## <a name="continuous"></a>계속

장기 실행 인식이 필요한 경우 연속 인식을 위해 start 및 해당 stop 함수를 사용 합니다. Start 함수는 stop 함수를 호출할 때까지 또는 대기 시간이 너무 많이 경과 될 때까지 모든 길이 발언를 시작 하 고 계속 처리 합니다. 연속 모드를 사용 하는 경우 발생 시 발생 하는 다양 한 이벤트에 등록 해야 합니다. 예를 들어, "인식 된" 이벤트는 음성 인식이 발생할 때 발생 합니다. 인식을 처리 하려면 이벤트 처리기가 있어야 합니다.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

추가 언어는 [SPEECH SDK 참조 문서](speech-to-text.md#speech-sdk-reference-docs)를 참조 하세요.

::: zone-end

## <a name="dictation"></a>받아쓰기

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

::: zone pivot="programming-language-csharp"

함수 사용에 대 한 자세한 내용은 `EnableDictation` [.NET Speech SDK 문서](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)를 참조 하세요.

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

함수 사용에 대 한 자세한 내용은 `EnableDictation` [c + + Speech SDK 문서](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)를 참조 하세요.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

함수 사용에 대 한 자세한 내용은 `enableDictation` [JAVA Speech SDK 문서](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)를 참조 하세요.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

함수 사용에 대 한 자세한 내용은 `enable_dictation` [PYTHON Speech SDK 문서](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)를 참조 하세요.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

함수 사용에 대 한 자세한 내용은 `enableDictation` [JavaScript 용 Speech SDK 문서](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--)를 참조 하세요.

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

추가 언어는 [SPEECH SDK 참조 문서](speech-to-text.md#speech-sdk-reference-docs)를 참조 하세요.

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 추가 음성 SDK 샘플 살펴보기](https://aka.ms/csspeech/samples)
