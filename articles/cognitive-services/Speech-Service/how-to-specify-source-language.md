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
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 3d761bbad4cb2cd2cdd1c34459f25c811bb41c7e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918692"
---
# <a name="specify-source-language-for-speech-to-text"></a>음성 텍스트에 대 한 소스 언어 지정

이 문서에서는 음성 인식을 위해 음성 SDK에 전달 되는 오디오 입력의 원본 언어를 지정 하는 방법에 대해 알아봅니다. 또한 사용자 지정 음성 모델을 지정 하는 예제 코드를 제공 하 여 인식 기능을 향상 시킬 수 있습니다.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>C에서 원본 언어를 지정 하는 방법 #

이 예제에서 소스 언어는 생성자를 사용 하 여 매개 변수로 명시적으로 제공 됩니다 `SpeechRecognizer` .

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어를 제공 합니다 `SourceLanguageConfig` . 그런 다음 `sourceLanguageConfig` 가 생성할 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어 및 사용자 지정 끝점을 제공 합니다 `SourceLanguageConfig` . 그런 다음 `sourceLanguageConfig` 가 생성할 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` 및 `EndpointId` set 메서드는 `SpeechConfig` c #의 클래스에서 더 이상 사용 되지 않습니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 .을 생성할 때 사용 하지 않아야 `SpeechRecognizer` 합니다.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>C + +에서 소스 언어를 지정 하는 방법

이 예제에서 소스 언어는 메서드를 사용 하 여 매개 변수로 명시적으로 제공 됩니다 `FromConfig` .

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어를 제공 합니다 `SourceLanguageConfig` . 그런 다음를 `sourceLanguageConfig` 만들 때가 매개 변수로 전달 됩니다 `FromConfig` `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어 및 사용자 지정 끝점을 제공 합니다 `SourceLanguageConfig` . 는를 `sourceLanguageConfig` 만들 때 매개 변수로 전달 됩니다 `FromConfig` `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 및 `SetEndpointId` 는 `SpeechConfig` c + + 및 Java의 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 .을 생성할 때 사용 하지 않아야 `SpeechRecognizer` 합니다.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java에서 원본 언어를 지정 하는 방법

이 예제에서 소스 언어는 새를 만들 때 명시적으로 제공 됩니다 `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어를 제공 합니다 `SourceLanguageConfig` . 그런 다음 `sourceLanguageConfig` 새을 만들 때가 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 소스 언어 및 사용자 지정 끝점을 제공 합니다 `SourceLanguageConfig` . 그런 다음 `sourceLanguageConfig` 새을 만들 때가 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 및 `setEndpointId` 는 `SpeechConfig` c + + 및 Java의 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 .을 생성할 때 사용 하지 않아야 `SpeechRecognizer` 합니다.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python에서 원본 언어를 지정 하는 방법

이 예제에서 소스 언어는 생성자를 사용 하 여 매개 변수로 명시적으로 제공 됩니다 `SpeechRecognizer` .

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

이 예제에서는를 사용 하 여 소스 언어를 제공 합니다 `SourceLanguageConfig` . 그런 다음 `SourceLanguageConfig` 가 생성할 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

이 예제에서는를 사용 하 여 소스 언어 및 사용자 지정 끝점을 제공 합니다 `SourceLanguageConfig` . 그런 다음 `SourceLanguageConfig` 가 생성할 매개 변수로 전달 됩니다 `SpeechRecognizer` .

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` 및 `endpoint_id` 속성은 Python의 클래스에서 더 이상 사용 되지 않습니다 `SpeechConfig` . 이러한 속성을 사용 하는 것은 권장 되지 않으며을 생성할 때 사용 하지 않아야 `SpeechRecognizer` 합니다.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Javascript에서 소스 언어를 지정 하는 방법

첫 번째 단계는 다음을 만드는 것입니다 `SpeechConfig` .

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

다음으로 오디오의 원본 언어를 지정 합니다 `speechRecognitionLanguage` .

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 다음을 사용 하 여 끝점을 지정할 수 있습니다 `endpointId` .

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>목표에서 원본 언어를 지정 하는 방법-C

이 예제에서 소스 언어는 생성자를 사용 하 여 매개 변수로 명시적으로 제공 됩니다 `SPXSpeechRecognizer` .

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

이 예제에서는를 사용 하 여 소스 언어를 제공 합니다 `SPXSourceLanguageConfiguration` . 그런 다음 `SPXSourceLanguageConfiguration` 가 생성할 매개 변수로 전달 됩니다 `SPXSpeechRecognizer` .

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

이 예제에서는를 사용 하 여 소스 언어 및 사용자 지정 끝점을 제공 합니다 `SPXSourceLanguageConfiguration` . 그런 다음 `SPXSourceLanguageConfiguration` 가 생성할 매개 변수로 전달 됩니다 `SPXSpeechRecognizer` .

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` 및 `endpointId` 속성은 `SPXSpeechConfiguration` 목표-C의 클래스에서 더 이상 사용 되지 않습니다. 이러한 속성을 사용 하는 것은 권장 되지 않으며을 생성할 때 사용 하지 않아야 `SPXSpeechRecognizer` 합니다.

::: zone-end

## <a name="see-also"></a>참조

* 음성 텍스트에 대해 지원 되는 언어 및 로캘 목록은 [언어 지원](language-support.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)