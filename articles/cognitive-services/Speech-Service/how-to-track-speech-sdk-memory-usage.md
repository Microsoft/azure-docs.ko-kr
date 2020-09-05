---
title: 음성 SDK 메모리 사용을 추적 하는 방법-Speech service
titleSuffix: Azure Cognitive Services
description: 음성 서비스 SDK는 음성 변환과 텍스트 음성 변환 및 음성 번역을 위한 다양 한 프로그래밍 언어를 지원 합니다. 이 문서에서는 SDK에 기본 제공 되는 메모리 관리 도구에 대해 설명 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934143"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>음성 SDK 메모리 사용을 추적 하는 방법

Speech SDK는 일련의 상호 운용성 계층을 통해 여러 프로그래밍 언어로 프로젝션 되는 네이티브 코드 베이스를 기반으로 합니다. 각 언어별 프로젝션에는 개체 수명 주기를 관리 하는 idiomatically 올바른 기능이 있습니다. 또한 Speech SDK에는 개체 로깅 및 개체 제한을 사용 하 여 리소스 사용을 추적 하는 메모리 관리 도구가 포함 되어 있습니다. 

## <a name="how-to-read-object-logs"></a>개체 로그를 읽는 방법

[SPEECH SDK 로깅을 사용 하는](how-to-use-logging.md)경우 추적 태그를 내보내면 기록 개체 관찰이 가능 합니다. 이러한 태그는 다음과 같습니다. 

* `TrackHandle` 또는 `StopTracking` 
* 개체 유형
* 현재 추적 되 고 있는 개체의 형식 및 현재 수를 추적 하는 개체의 수입니다.

샘플 로그는 다음과 같습니다. 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>경고 임계값 설정

경고 임계값을 만드는 옵션을 사용할 수 있으며,이 임계값을 초과 하는 경우 (로깅이 사용 되는 경우) 경고 메시지가 기록 됩니다. 경고 메시지에는 해당 수와 함께 존재 하는 모든 개체의 덤프가 포함 됩니다. 이 정보를 사용 하 여 문제를 보다 잘 이해할 수 있습니다. 

경고 임계값을 사용 하려면 개체에 대해 경고 임계값을 지정 해야 합니다 `SpeechConfig` . 새 인식기를 만들 때이 개체를 확인 합니다. 다음 예에서는 라는 인스턴스를 만들었다고 가정해 보겠습니다 `SpeechConfig` `config` .

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 1만입니다.

## <a name="set-an-error-threshold"></a>오류 임계값 설정 

Speech SDK를 사용 하 여 지정 된 시간에 허용 되는 최대 개체 수를 설정할 수 있습니다. 이 설정을 사용 하는 경우 최대 수에 도달 하면 새 인식기 개체를 만들려고 시도 하지 않습니다. 기존 개체는 계속 작동 합니다.

샘플 오류는 다음과 같습니다.

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

오류 임계값을 사용 하려면 개체에 지정 해야 합니다 `SpeechConfig` . 새 인식기를 만들 때이 개체를 확인 합니다. 다음 예에서는 라는 인스턴스를 만들었다고 가정해 보겠습니다 `SpeechConfig` `config` .

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 데이터 형식의 플랫폼별 최 댓 값입니다 `size_t` . 일반적인 인식은 7 개에서 10 개 사이의 내부 개체를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK에 대해 자세히 알아보기](speech-sdk.md)