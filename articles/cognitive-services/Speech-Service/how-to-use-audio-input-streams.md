---
title: 음성 SDK 오디오 입력 스트림 개념
titleSuffix: Azure Cognitive Services
description: 음성 SDK의 오디오 입력 스트림 API 기능에 대한 개요입니다.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c8d3162e13c31204ed317edc653756b04ef8dd4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934126"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>음성 SDK 오디오 입력 스트림 API 정보

음성 SDK의 **오디오 입력 스트림** API는 마이크나 입력 파일 api를 사용 하는 대신 인식기로 오디오를 스트리밍하는 방법을 제공 합니다.

다음 단계는 오디오 입력 스트림을 사용할 때 필요합니다.

- 오디오 스트림의 형식을 식별합니다. 형식은 음성 SDK 및 Speech Service에서 지원되어야 합니다. 현재는 다음 구성만 지원됩니다.

  PCM 형식의 오디오 샘플, 채널 1 개, 샘플 당 16 비트, 8000 또는 16000 샘플 (초당 16000 또는 32000 바이트), 2 개의 블록 맞춤 (샘플에 대 한 안쪽 여백을 포함 하는 16 비트)

  오디오 형식을 만들기 위한 SDK의 해당 코드는 다음과 같습니다.

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- 코드가 이 사양에 따라 RAW 오디오 데이터를 제공할 수 있는지 확인합니다. 오디오 원본 데이터가 지원되는 형식과 일치하지 않는 경우, 오디오를 필요한 형식으로 트랜스코딩해야 합니다.

- `PullAudioInputStreamCallback`에서 파생된 고유한 오디오 입력 스트림 클래스를 만듭니다. `Read()` 및 `Close()` 멤버를 구현합니다. 정확한 함수 시그니처는 언어에 따라 다르지만 코드는 다음 코드 샘플과 유사합니다.

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- 오디오 형식 및 입력 스트림을 기반으로 오디오 구성을 만듭니다. 인식기를 만들 때 일반 음성 구성과 오디오 입력 구성을 둘 다 전달합니다. 예를 들면 다음과 같습니다.

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>다음 단계

- [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
- [C#에서 음성을 인식하는 방법 참조](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
