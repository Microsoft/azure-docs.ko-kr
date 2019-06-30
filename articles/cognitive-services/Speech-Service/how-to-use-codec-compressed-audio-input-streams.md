---
title: Speech SDK-음성 서비스를 사용하여 코덱 압축 오디오 스트리밍하기
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 압축 오디오를 Azure 음성 서비스로 스트리밍하는 방법을 알아봅니다. C++, C# 및 Linux용 Java에서 사용 가능합니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468315"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK를 통해 코덱 압축 오디오 입력 사용

Speech SDK의 **압축된 오디오 입력 스트림** API는 PullStream 또는 PushStream을 사용하여 압축 오디오를 음성 서비스로 스트림하는 방법을 제공합니다.

> [!IMPORTANT]
> 압축 오디오 스트리밍은 C++, C#, 및 Java linux(Ubuntu 16.04, Ubuntu 18.04 Debian 9)에서만 지원됩니다.

Wav/PCM에 대해서는 메인 라인 음성 설명서를 참조하십시오.  Wav/PCM을 제외하고, 다음과 같은 압축 코덱 입력 형식이 지원됩니다.

- MP3
- 저작/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하기 위한 필수 구성 요소

Linux용 Speech SDK를 사용하여 압축 오디오 입력을 사용하려면 다음과 같은 추가 종속성을 설치합니다.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

음성 서비스로 압축 오디어 형식으로 스트리밍하려면, `PullAudioInputStream` 또는 `PushAudioInputStream`를 생성합니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 생성하여 스트림의 압축 형식을 지정합니다.

`myPushStream`이라고 하는 입력 스트림 클래스가 있고 OPUS/OGG를 사용하고 있다고 가정하겠습니다. 코드는 다음과 비슷할 수 있습니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
