---
title: 음성 텍스트에 대 한 소스 언어를 지정 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용 하면 음성을 텍스트로 변환할 때 원본 언어를 지정할 수 있습니다. 이 문서에서는 FromConfig 및 SourceLanguageConfig 메서드를 사용 하 여 음성 서비스에서 소스 언어를 인식 하 고 사용자 지정 모델 대상을 제공 하는 방법을 설명 합니다.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e4f4dd3c1e23855a8a1a69dac72c232779206f1d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121712"
---
# <a name="specify-source-language-for-speech-to-text"></a>음성 텍스트에 대 한 소스 언어 지정

이 문서에서는 음성 인식을 위해 음성 SDK에 전달 되는 오디오 입력의 원본 언어를 지정 하는 방법에 대해 알아봅니다. 또한 사용자 지정 음성 모델을 지정 하는 예제 코드를 제공 하 여 인식 기능을 향상 시킬 수 있습니다.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>에서 원본 언어를 지정 하는 방법C#

이 예제에서 소스 언어는 `SpeechRecognizer` 구문을 사용 하 여 명시적으로 매개 변수로 제공 됩니다.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어를 제공 합니다. 그런 다음 `sourceLanguageConfig` `SpeechRecognizer` 생성에 매개 변수로 전달 됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어 및 사용자 지정 엔드포인트을 제공 합니다. 그런 다음 `sourceLanguageConfig` `SpeechRecognizer` 생성에 매개 변수로 전달 됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` 및 `EndpointId` set 메서드는의 C#`SpeechConfig` 클래스에서 더 이상 사용 되지 않습니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다. `SpeechRecognizer`을 생성할 때 사용 하면 안 됩니다.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>에서 원본 언어를 지정 하는 방법C++

이 예제에서 소스 언어는 `FromConfig` 메서드를 사용 하 여 명시적으로 매개 변수로 제공 됩니다.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어를 제공 합니다. 그런 다음 `recognizer`를 만들 때 `FromConfig`에 대 한 매개 변수로 `sourceLanguageConfig` 전달 됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어 및 사용자 지정 엔드포인트을 제공 합니다. `sourceLanguageConfig`는 `recognizer`를 만들 때 `FromConfig`에 매개 변수로 전달 됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 및 `SetEndpointId`는 및 Java의 C++ `SpeechConfig` 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다. `SpeechRecognizer`을 생성할 때 사용 하면 안 됩니다.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java에서 원본 언어를 지정 하는 방법

이 예제에서는 새 `SpeechRecognizer`를 만들 때 소스 언어가 명시적으로 제공 됩니다.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어를 제공 합니다. 그런 다음 `sourceLanguageConfig` 새 `SpeechRecognizer`을 만들 때 매개 변수로 전달 됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 `SourceLanguageConfig`를 사용 하 여 소스 언어 및 사용자 지정 엔드포인트을 제공 합니다. 그런 다음 `sourceLanguageConfig` 새 `SpeechRecognizer`을 만들 때 매개 변수로 전달 됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 및 `setEndpointId`는 및 Java의 C++ `SpeechConfig` 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다. `SpeechRecognizer`을 생성할 때 사용 하면 안 됩니다.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python에서 원본 언어를 지정 하는 방법

첫 번째 단계는 `speech_config`를 만드는 것입니다.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

다음으로 `speech_recognition_language`에서 오디오의 원본 언어를 지정 합니다.

```Python
speech_config.speech_recognition_language="de-DE"
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 `endpoint_id`를 사용 하 여 엔드포인트을 지정할 수 있습니다.

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Javascript에서 소스 언어를 지정 하는 방법

첫 번째 단계는 `SpeechConfig`를 만드는 것입니다.

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

다음으로 `speechRecognitionLanguage`에서 오디오의 원본 언어를 지정 합니다.

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 `endpointId`를 사용 하 여 엔드포인트을 지정할 수 있습니다.

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>목표에서 원본 언어를 지정 하는 방법-C

첫 번째 단계는 `speechConfig`를 만드는 것입니다.

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

다음으로 `speechRecognitionLanguage`에서 오디오의 원본 언어를 지정 합니다.

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 `endpointId`를 사용 하 여 엔드포인트을 지정할 수 있습니다.

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>참고 항목

* 음성 텍스트에 대해 지원 되는 언어 및 로캘 목록은 [언어 지원](language-support.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
