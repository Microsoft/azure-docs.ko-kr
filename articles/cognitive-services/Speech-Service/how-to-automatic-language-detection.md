---
title: 음성 텍스트에 자동 언어 검색을 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 음성 텍스트에 대 한 자동 언어 검색을 지원 합니다. 이 기능을 사용 하는 경우 제공 된 오디오가 제공 된 언어 목록과 비교 되며 가장 가능성이 높은 항목이 결정 됩니다. 그러면 반환 된 값을 사용 하 여 음성 텍스트에 사용 되는 언어 모델을 선택할 수 있습니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 1fc7e7da62aa92812a124f920f5c59ebf8e50fcc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934619"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>음성 텍스트에 대 한 자동 언어 검색

자동 언어 검색은 제공 된 언어 목록과 비교할 때 음성 SDK에 전달 되는 오디오에 대해 가장 가능성이 높은 일치 항목을 확인 하는 데 사용 됩니다. 자동 언어 검색에서 반환 되는 값은 음성 텍스트에 대 한 언어 모델을 선택 하 여 더 정확한 기록을 제공 하는 데 사용 됩니다. 사용할 수 있는 언어를 확인 하려면 [언어 지원](language-support.md)을 참조 하세요.

이 문서에서는를 사용 하 여 `AutoDetectSourceLanguageConfig` 개체를 생성 하 `SpeechRecognizer` 고 검색 된 언어를 검색 하는 방법을 배웁니다.

> [!IMPORTANT]
> 이 기능은 c #, c + +, Java, Python, JavaScript 및 객관적인-C를 사용 하는 음성 SDK에만 사용할 수 있습니다.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>음성 SDK를 사용 하 여 자동 언어 검색

자동 언어 검색에는 현재 검색 당 4 개 언어의 서비스 쪽 제한이 있습니다. 개체를 생성할 때 이러한 제한을 염두에 두십시오 `AudoDetectSourceLanguageConfig` . 아래 샘플에서는를 만든 `AutoDetectSourceLanguageConfig` 다음이를 사용 하 여를 생성 `SpeechRecognizer` 합니다.

> [!TIP]
> 음성 텍스트를 수행할 때 사용할 사용자 지정 모델을 지정할 수도 있습니다. 자세한 내용은 [자동 언어 검색에 사용자 지정 모델 사용](#use-a-custom-model-for-automatic-language-detection)을 참조 하세요.

다음 코드 조각은 앱에서 자동 언어 검색을 사용 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig, autoDetectConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>자동 언어 검색에 사용자 지정 모델 사용

음성 서비스 모델을 사용 하는 언어 검색 외에도, 향상 된 인식을 위한 사용자 지정 모델을 지정할 수 있습니다. 사용자 지정 모델을 제공 하지 않으면 서비스에서 기본 언어 모델을 사용 합니다.

아래 코드 조각은 음성 서비스 호출에서 사용자 지정 모델을 지정 하는 방법을 보여 줍니다. 검색 된 언어가 이면 `en-US` 기본 모델이 사용 됩니다. 검색 된 언어가 이면 `fr-FR` 사용자 지정 모델의 끝점이 사용 됩니다.

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end

## <a name="next-steps"></a>다음 단계

::: zone pivot="programming-language-csharp"
* 자동 언어 검색에 대 한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) 를 참조 하세요.
::: zone-end

::: zone pivot="programming-language-cpp"
* 자동 언어 검색에 대 한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) 를 참조 하세요.
::: zone-end

::: zone pivot="programming-language-java"
* 자동 언어 검색에 대 한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) 를 참조 하세요.
::: zone-end

::: zone pivot="programming-language-python"
* 자동 언어 검색에 대 한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) 를 참조 하세요.
::: zone-end

::: zone pivot="programming-language-objectivec"
* 자동 언어 검색에 대 한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) 를 참조 하세요.
::: zone-end

* [Speech SDK 참조 설명서](speech-sdk.md)