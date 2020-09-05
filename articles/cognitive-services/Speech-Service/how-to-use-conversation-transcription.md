---
title: 실시간 대화 기록 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: '음성 SDK를 사용 하 여 실시간 대화 기록을 사용 하는 방법을 알아봅니다. C + +, c # 및 Java에서 사용할 수 있습니다.'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.custom: devx-track-csharp
ms.openlocfilehash: a425c75dfd57f0d5f9c98b73d8b351972b70703a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918658"
---
# <a name="real-time-conversation-transcription-preview"></a>실시간 대화 기록 (미리 보기)

Speech SDK의 **ConversationTranscriber** API를 사용 하면 또는를 사용 하 여 음성 서비스에 오디오를 스트리밍하 여 여러 참가자를 추가, 제거 및 식별 하는 기능을 통해 모임 및 기타 대화를 높여줄 수 있습니다 `PullStream` `PushStream` . 이 항목에서는 Speech SDK (버전 1.8.0 이상)에서 음성 텍스트를 사용 하는 방법을 알고 있어야 합니다. 자세한 내용은 [Speech Services 란?](overview.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항

- ConversationTranscriber API는 Windows, Linux 및 Android에서 c + +, c # 및 Java에 대해 지원 됩니다.
- 현재는 다음과 같은 지역의 "en-us" 및 "zh-cn" 언어에서 사용할 수 있습니다. _centralus_ 및 _eastasia_.
- 재생 참조 스트림이 있는 7-mic 원형 다중 마이크 배열이 필요 합니다. 마이크 배열은 [사양을](https://aka.ms/sdsdk-microphone)충족 해야 합니다.
- [음성 장치 SDK](speech-devices-sdk.md) 는 적절 한 장치 및 대화 기록을 보여 주는 샘플 앱을 제공 합니다.

## <a name="optional-sample-code-resources"></a>선택적 샘플 코드 리소스

음성 장치 SDK는 8 개의 채널을 사용 하 여 실시간 오디오 캡처를 위한 Java의 샘플 코드를 제공 합니다.

- [ROOBO 장치 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Azure Kinect Dev Kit 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>필수 구성 요소

음성 서비스 구독입니다. [무료 Azure 계정](https://azure.microsoft.com/free/cognitive-services/) (없는 경우)을 만들 수 있습니다.

## <a name="create-voice-signatures"></a>음성 서명 만들기

첫 번째 단계는 효율적인 스피커 식별을 위해 대화 참가자에 대 한 음성 서명을 만드는 것입니다.

### <a name="audio-input-requirements"></a>오디오 입력 요구 사항

- 음성 서명을 만들기 위한 입력 오디오 웨이브 파일은 16 비트 샘플, 16Khz 샘플 요금 및 단일 채널 (mono) 형식 이어야 합니다.
- 각 오디오 샘플의 권장 길이는 30 초에서 2 분 사이입니다.

### <a name="sample-code"></a>예제 코드

다음 예제에서는 c #에서 [REST API를 사용](https://aka.ms/cts/signaturegenservice) 하 여 음성 서명을 만드는 두 가지 방법을 보여 줍니다. "SpeakerVoice"에 대 한 실제 정보, ""에 대 한 웨이브 파일 이름, `{region}` 및 "YourServiceRegion" (_centralus_ 또는 _eastasia_)의 지역으로 대체 해야 합니다.

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>높여줄 대화

다음 샘플 코드는 세 개의 스피커로 대화를 실시간으로 높여줄 하는 방법을 보여 줍니다. 위에서 설명한 것 처럼 각 스피커에 대해 음성 서명을 이미 만들었다고 가정 합니다. SpeechConfig 개체를 만들 때 "YourServiceRegion" 및 ""에 대 한 실제 정보를 대체 합니다.

샘플 코드 하이라이트는 다음과 같습니다.

- 을 `Conversation` `SpeechConfig` 사용 하 여 생성 된 회의 식별자를 사용 하 여 개체에서 개체 만들기 `Guid.NewGuid()`
- 개체를 만들고 `ConversationTranscriber` 와 대화를 조인 `JoinConversationAsync()` 하 여 기록을 시작 합니다.
- 관심 있는 이벤트 등록
- 대화 개체를 사용 하 여 대화에 참가자 추가 또는 제거
- 오디오 스트리밍
- Speech SDK 버전 1.9.0 및 `int` `string` 값 형식은 모두 음성 서명 버전 필드에서 지원 됩니다.

기록 및 스피커 식별자가 등록 된 이벤트로 돌아옵니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
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

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비동기 대화 기록](how-to-async-conversation-transcription.md)
