---
title: '빠른 시작: 오디오 파일에 음성 합성, C#(.NET) - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 17e5f35e83f5cb6e8115a9d344f93abf84e5b38a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156085"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

첫 번째 단계로 Visual Studio에서 프로젝트를 열어야 합니다.

1. Visual Studio 2019를 시작합니다.
2. 프로젝트를 로드하고 `Program.cs`를 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다. `SynthesisToAudioFileAsync()`라는 비동기 메서드를 만듭니다.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`SpeechSynthesizer` 개체를 초기화하기 전에 구독 키와 구독 지역을 사용하는 구성을 만들어야 합니다. `SynthesisToAudioFileAsync()` 메서드에 이 코드를 삽입합니다.

````C#
// Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
// from here: https://aka.ms/speech/sdkregion).
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>오디오 구성 만들기

이제 오디오 파일을 가리키는 ````AudioConfig```` 개체를 만들어야 합니다. 이 개체는 관리되지 않는 리소스의 적절한 릴리스를 보장하기 위해 using 문 내에 생성됩니다. `SynthesisToAudioFileAsync()` 메서드(음성 구성 바로 아래)에 이 코드를 삽입합니다.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer 초기화

이제 앞에서 만든 `SpeechConfig` 및 `AudioConfig` 개체를 사용하여 `SpeechSynthesizer` 개체를 만들어 보겠습니다. 이 개체는 관리되지 않는 리소스의 적절한 릴리스를 보장하기 위해 using 문 내에도 생성됩니다. ````AudioConfig```` 개체를 래핑하는 using 문 내의 `SynthesisToAudioFileAsync()` 메서드에 이 코드를 삽입합니다.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>SpeakTextAsync를 사용하여 텍스트 합성

`SpeechSynthesizer` 개체에서 `SpeakTextAsync()` 메서드를 호출합니다. 이 메서드는 텍스트를 오디오로 변환하는 Speech Service로 텍스트를 보냅니다. `config.VoiceName`을 명시적으로 지정하지 않으면 `SpeechSynthesizer`에서 기본 음성을 사용합니다.

using 문 내에 다음 코드를 추가합니다.
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>오류 확인

음성 서비스에서 합성 결과를 반환하면 텍스트가 제대로 합성되었는지 확인해야 합니다.

using 문의 `SpeakTextAsync()` 아래에 다음 코드를 추가합니다.
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 합성을 테스트할 준비가 되었습니다.

1. **코드 컴파일** - Visual Studio의 메뉴 모음에서 **빌드** > **빌드 솔루션**을 선택합니다.
2. **앱 시작** - 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.
3. **합성 시작** - 텍스트가 음성으로 변환되고 지정된 오디오 데이터에 저장됩니다.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
