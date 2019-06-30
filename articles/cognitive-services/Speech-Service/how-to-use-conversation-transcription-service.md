---
title: Speech SDK-음성 서비스를 사용하여 다중 참가자 대화 기록
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 대화 기록을 사용하는 방법에 알아봅니다. C++, C#, 및 Java에서 사용할 수 있습니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604420"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Speech SDK를 사용하여 다중 참가자 대화 기록

Speech SDK **ConversationTranscriber** API는 `PullStream` 또는`PushStream`을 사용하여 음성 서비스에 오디오를 스트리밍하여 참가자를 추가, 제거 및 식별하는 기능으로 회의/대화를 기록할 수 있습니다.

## <a name="limitations"></a>제한 사항

* 대화 기록기는 Windows, Linux 및 Android에서 C++, C#, 및 Java에 대해 지원됩니다.
* ROOBO DevKit는 화자 식별을 위해 효율적으로 활용할 수 있는 순환 다중 마이크 배열을 제공하는 대화 기록 만들기에 대해 지원되는 하드웨어 환경입니다. 자세한 내용은 [음성 장치 SDK](speech-devices-sdk.md)를 참조하세요.
* 대화 기록에 대한 Speech SDK 지원은 16 비트 16kHz PCM 오디오의 8개 채널로 오디오 풀 및 푸시 모드의 사용으로 제한됩니다.

* 대화 기록은 현재 다음 지역에서 "en-US" 및 "zh-CN" 언어로 제공됩니다: centralus 및 eastasia.


## <a name="prerequisites"></a>필수 조건

* [Speech SDK를 사용하여 음성-텍스트를 사용하는 방법에 대해 알아봅니다.](quickstart-csharp-dotnet-windows.md)
* [음성 평가판 구독을 가져옵니다.](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK 버전 1.5.1 이상 버전이 필요 합니다.

## <a name="create-voice-signatures-for-participants"></a>참가자에 대한 음성 서명 생성

첫 번째 단계는 대화 참가자에 대한 음성 서명을 만드는 것입니다. 음성 서명을 만드는 것은 효율적인 화자 식별에 필요합니다.

### <a name="requirements-for-input-wave-file"></a>입력 wave 파일에 대한 요구 사항

* 음성 서명을 만들기 위한 입력 오디오 wave 파일은 16비트 샘플, 16kHz 샘플 속도 및 단일 채널(Mono) 형식이어야 합니다.
* 각 오디오 샘플에 대해 권장되는 길이는 30초에서 2분 사이입니다.

다음 예제에서는 C#으로 [REST API](https://aka.ms/cts/signaturegenservice)를 사용하여 음성 서명을 생성하기 위한 두 가지 방법을 보여줍니다.

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>대화 기록

여러 참가자와의 대화를 기록하기 위해서, `PullAudioInputStream` 또는 `PushAudioInputStream`를 사용하여 대화 세션과 오디어 스트림을 위해 생성된 `AudioConfig`와 연관된 `ConversationTranscriber` 개체를 생성합니다.

`MyConversationTranscriber` 이라는 ConversationTranscriber 클래스가 있다고 가정합니다. 코드는 다음과 비슷할 수 있습니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
