---
title: 일괄 처리 기록을 사용 하는 방법-Speech service
titleSuffix: Azure Cognitive Services
description: Batch Transcription은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 전사하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 전사를 수신할 수 있습니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: dc473c814cdd69204cddd976bc77f19b5db567b1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200081"
---
# <a name="how-to-use-batch-transcription"></a>일괄 처리 기록을 사용 하는 방법

일괄 처리는 저장소에서 많은 양의 오디오를 찍으면 되므로 간편 하는 데 적합 합니다. 전용 REST API를 사용 하 여 SAS (공유 액세스 서명) URI를 사용 하는 오디오 파일을 가리키고 기록 결과를 비동기적으로 받을 수 있습니다.

API는 비동기 음성-텍스트 기록 및 기타 기능을 제공 합니다. REST API를 사용 하 여 다음에 메서드를 노출할 수 있습니다.

- 일괄 처리 요청 만들기
- 상태 쿼리
- 기록 결과 다운로드
- 서비스에서 기록 정보 삭제

자세한 API는 [ 제목 아래에 ](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)Swagger 문서`Custom Speech transcriptions`로 제공됩니다.

일괄 처리 작업은 최상의 노력으로 예약 됩니다. 현재 작업을 실행 중 상태로 변경 하는 시기에 대 한 예측은 없습니다. 정상적인 시스템 로드에서는 몇 분 내에 발생 해야 합니다. 실행 상태에 있으면 실제 기록을 오디오 실시간 보다 빠르게 처리 합니다.

사용 하기 쉬운 API 옆에 사용자 지정 끝점을 배포할 필요가 없으며 관찰할 동시성 요구 사항이 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="subscription-key"></a>구독 키

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](https://portal.azure.com)에 따라 [Azure Portal](get-started.md)에서 구독 키를 만듭니다.

>[!NOTE]
> 일괄 처리 기록을 사용 하려면 Speech (표준 구독) 서비스를 사용 해야 합니다. 체험 구독 키(F0)는 작동하지 않습니다. 자세한 내용은 [가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조 하세요.

### <a name="custom-models"></a>사용자 지정 모델

음향 또는 언어 모델을 사용자 지정 하려는 경우 [음향 모델 사용자 지정](how-to-customize-acoustic-models.md) 및 [디자인 사용자 지정 언어 모델](how-to-customize-language-model.md)의 단계를 따르세요. 일괄 처리에 생성 된 모델을 사용 하려면 모델 Id가 필요 합니다. 모델의 세부 정보를 검사할 때 모델 ID를 검색할 수 있습니다. 배치 기록 서비스에는 배포 된 사용자 지정 끝점이 필요 하지 않습니다.

## <a name="the-batch-transcription-api"></a>Batch Transcription API

### <a name="supported-formats"></a>지원되는 형식

Batch Transcription API에서 지원하는 형식은 다음과 같습니다.

| 형식 | Codec | Bitrate | 샘플링 주기 |
|--------|-------|---------|-------------|
| WAV | PCM | 16비트 | 8Khz 또는 16khz, mono 또는 스테레오 |
| MP3 | PCM | 16비트 | 8Khz 또는 16khz, mono 또는 스테레오 |
| OGG | OPUS | 16비트 | 8Khz 또는 16khz, mono 또는 스테레오 |

스테레오 오디오 스트림의 경우 기록 중 왼쪽 채널과 오른쪽 채널이 분할 됩니다. 각 채널에 대해 JSON 결과 파일을 만들고 있습니다. Utterance 당 생성 된 타임 스탬프를 통해 개발자는 주문 된 최종 성적 증명서를 만들 수 있습니다.

### <a name="configuration"></a>구성

구성 매개 변수는 JSON으로 제공됩니다.

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>구성 속성

이러한 선택적 속성을 사용 하 여 기록을 구성 합니다.

| 매개 변수 | Description |
|-----------|-------------|
| `ProfanityFilterMode` | 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용되는 값은 욕설 필터링을 비활성화하는 `None`, 욕설을 별표로 바꾸는 `Masked`, 결과에서 모든 욕설을 제거하는 `Removed`, 또는 “profanity” 태그를 추가하는 `Tags`입니다. 기본 설정은 `Masked`입니다. |
| `PunctuationMode` | 인식 결과에서 문장 부호의 처리 방법을 지정합니다. 허용되는 값은 문장 부호를 비활성화하는 `None`, 명시적인 문장 부호를 의미하는 `Dictated`, 디코더가 문장 부호를 처리하도록 하는 `Automatic`, 지정된 문장 부호 또는 자동을 의미하는 `DictatedAndAutomatic`입니다. |
| `AddWordLevelTimestamps` | 단어 수준 타임스탬프를 출력에 추가할지 여부를 지정합니다. 허용되는 값은 단어 수준 타임스탬프를 사용하는 `true`와 사용하지 않는 `false`(기본값)입니다. |
| `AddSentiment` | 발언에 감정을 추가할 것인지 지정합니다. 허용 되는 값은 감정 utterance 및 `false` (기본값)를 사용 하지 않도록 설정 하는 `true`입니다. |
| `AddDiarization` | 두 개의 음성을 포함 하는 mono 채널이 될 것으로 예상 되는 입력에 대해 diarization 분석을 수행 하도록 지정 합니다. 허용 되는 값은 diarization 및 `false` (기본값)을 사용 하지 않도록 설정 하는 `true`입니다. 또한 `AddWordLevelTimestamps`를 true로 설정 해야 합니다.|
|`TranscriptionResultsContainerUrl`|Azure의 쓰기 가능한 컨테이너에 [서비스 SAS](../../storage/common/storage-sas-overview.md) 를 사용 하는 선택적 URL입니다. 결과는이 컨테이너에 저장 됩니다.

### <a name="storage"></a>스토리지

일괄 처리 기록은 오디오를 읽고 저장소에 대 한 비디오를 작성 하기 위해 [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 를 지원 합니다.

## <a name="the-batch-transcription-result"></a>일괄 처리 기록 결과

Mono 입력 오디오의 경우 하나의 기록 결과 파일을 만듭니다. 스테레오 입력 오디오의 경우 두 개의 기록 결과 파일이 생성 됩니다. 각에는 다음 구조가 있습니다.

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

결과에는 다음과 같은 형식이 포함 됩니다.

|Form|콘텐츠|
|-|-|
|`Lexical`|실제 단어를 인식 합니다.
|`ITN`|인식 되는 텍스트의 반전 된 텍스트 형식입니다. 약어 ("의사 smith"), 전화 번호 및 기타 변환이 적용 됩니다.
|`MaskedITN`|사용 금지 마스킹을 적용 한 ITN 폼입니다.
|`Display`|인식 되는 텍스트의 표시 형식입니다. 여기에는 문장 부호와 대/소문자가 추가 됩니다.

## <a name="speaker-separation-diarization"></a>스피커 분리 (Diarization)

Diarization는 스피커를 오디오 조각으로 분리 하는 프로세스입니다. Batch 파이프라인은 diarization를 지원 하 고 mono 채널 녹화에서 두 개의 스피커를 인식할 수 있습니다. 스테레오 녹화에서는이 기능을 사용할 수 없습니다.

모든 기록 출력에는 `SpeakerId`포함 됩니다. Diarization를 사용 하지 않으면 JSON 출력에 `"SpeakerId": null` 표시 됩니다. Diarization의 경우 두 가지 음성을 지원 하므로 스피커가 `"1"` 또는 `"2"`로 식별 됩니다.

Diarization를 요청 하려면 아래와 같이 HTTP 요청에 관련 매개 변수를 추가 하기만 하면 됩니다.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

위의 요청에서 매개 변수가 표시 되므로 단어 수준 타임 스탬프도 ' 켜 짐 ' 이어야 합니다.

## <a name="sentiment-analysis"></a>정서 분석

감정 기능은 오디오에 표현 된 감정를 추정 합니다. 감정 `Negative`, `Neutral`및 `Positive` 감정에 대해 0에서 1 사이의 값으로 표현 됩니다. 예를 들어, 콜 센터 시나리오에서 감정 분석을 사용할 수 있습니다.

- 고객 만족도에 대 한 통찰력 얻기
- 에이전트(호출을 수행하는 팀)의 성능에 대한 통찰력 확보
- 호출이 음수 방향으로 전환 되는 정확한 시점 찾기
- 음수 호출을 긍정 방향으로 전환할 때의 기능
- 제품이나 서비스에 대해 고객이 좋아하는 점과 싫어하는 점을 식별

감정는 어휘 형태를 기준으로 오디오 세그먼트로 점수가 매겨집니다. 해당 오디오 세그먼트 내의 전체 텍스트는 감정을 계산 하는 데 사용 됩니다. 전체 기록을 위해 집계 감정 계산 되 고 있지 않습니다.

JSON 출력 샘플은 다음과 같습니다.

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>모범 사례

기록 서비스는 많은 수의 제출 된 많은 서비스를 처리할 수 있습니다. 이러한 [메서드](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)를 통해 `GET`를 통해 진행 상태를 쿼리할 수 있습니다. `take` 매개 변수 (몇 백)를 지정 하 여 반환 되는 정보를 적절 한 크기로 유지 합니다. 결과를 검색 한 후 서비스에서 정기적으로 [삭제](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) 합니다. 이렇게 하면 기록 관리 통화의 빠른 회신이 보장 됩니다.

## <a name="sample-code"></a>예제 코드

전체 샘플은 `samples/batch` 하위 디렉터리 내의 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) 사용할 수 있습니다.

사용자 지정 어쿠스틱 또는 언어 모델을 사용하려는 경우 사용자 구독 정보, 서비스 지역, 전사할 오디오 파일을 가리키는 SAS URI 및 모델 ID를 사용하여 샘플 코드를 사용자 지정해야 합니다.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

샘플 코드에서는 클라이언트를 설정 하 고 기록 요청을 제출 합니다. 그런 다음, 상태 정보를 폴링하고 전사 진행 상황에 대한 세부 정보를 출력합니다.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

이전 호출에 대한 자세한 내용은 [Swagger 문서](https://westus.cris.ai/swagger/ui/index)를 참조하세요. 여기에 표시된 전체 샘플을 보려면 [GitHub](https://aka.ms/csspeech/samples)의 `samples/batch` 하위 디렉터리로 이동합니다.

오디오를 게시하고 전사 상태를 수신하려면 비동기 설정을 기록해 둡니다. 만든 클라이언트는 .NET HTTP 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드 및 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 사용자 지정 모델을 지정하지 않습니다. 이 서비스는 파일을 전사하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 음향 및 언어 모델에 대해 모델 ID와 동일한 메서드를 전달할 수 있습니다.

> [!NOTE]
> 기준 전사의 경우 기준 모델의 ID를 선언할 필요가 없습니다. 언어 모델 ID(어쿠스틱 모델 ID 아님)만 지정하면 일치하는 어쿠스틱 모델이 자동으로 선택됩니다. 어쿠스틱 모델 ID만 지정하면 일치하는 언어 모델이 자동으로 선택됩니다.

## <a name="download-the-sample"></a>샘플 다운로드

`samples/batch`GitHub 샘플 리포지토리[의 ](https://aka.ms/csspeech/samples) 디렉터리에서 샘플을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
