---
title: 일괄 처리 기록을 사용하는 방법 - Speech Services
titlesuffix: Azure Cognitive Services
description: 일괄 처리 기록은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 기록하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 기록을 수신할 수 있습니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 2148d1bd79a858bec37e6c574c2a6b6e2009fe46
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190408"
---
# <a name="why-use-batch-transcription"></a>일괄 처리 기록을 사용하는 이유

일괄 처리 기록은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 기록하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 기록을 수신할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

### <a name="subscription-key"></a>구독 키

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](get-started.md)에 따라 [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다. 기준 모델에서 기록을 가져오려는 경우 키 만들기만 수행하면 됩니다.

>[!NOTE]
> Batch Transcription을 사용하려면 Speech Services를 위한 표준 구독(S0)이 필요합니다. 체험 구독 키(F0)는 작동하지 않습니다. 자세한 내용은 [가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

### <a name="custom-models"></a>사용자 지정 모델

어쿠스틱 또는 언어 모델을 사용자 지정하려는 경우, [어쿠스틱 모델 사용자 지정](how-to-customize-acoustic-models.md) 및 [언어 모델 사용자 지정](how-to-customize-language-model.md)의 단계를 수행합니다. 생성된 모델을 일괄 처리 기록에 사용하려면 모델 ID가 필요합니다. 이 ID는 엔드포인트 세부 정보 보기에 표시되는 엔드포인트 ID가 아니라 모델의 세부 정보를 선택할 때 검색할 수 있는 모델 ID입니다.

## <a name="the-batch-transcription-api"></a>Batch Transcription API

Batch Transcription API는 추가 기능과 함께 비동기 음성 텍스트 변환 기록을 제공합니다. 다음 용도로 사용되는 메서드를 노출하는 REST API입니다.

1. 일괄 처리 요청 만들기
1. 상태 쿼리
1. 기록 다운로드

> [!NOTE]
> Batch Transcription API는 일반적으로 수천 시간의 오디오를 누적하는 호출 센터에 적합합니다. 이렇게 하면 대량의 오디오 녹음을 쉽게 전사할 수 있습니다.

### <a name="supported-formats"></a>지원되는 형식

Batch Transcription API에서 지원하는 형식은 다음과 같습니다.

| 형식 | Codec | Bitrate | 샘플링 주기 |
|--------|-------|---------|-------------|
| WAV | PCM | 16비트 | 8 또는 16kHz, 모노, 스테레오 |
| MP3 | PCM | 16비트 | 8 또는 16kHz, 모노, 스테레오 |
| OGG | OPUS | 16비트 | 8 또는 16kHz, 모노, 스테레오 |

스테레오 오디오 스트림의 경우 Batch Transcription API는 기록하는 동안 왼쪽 및 오른쪽 채널을 분할합니다. 결과를 포함하는 2개의 JSON 파일이 각각 단일 채널에서 만들어집니다. 말하기 기준 타임스탬프를 사용하여 개발자는 순서가 지정된 최종 기록을 만들 수 있습니다. 이 샘플 요청에는 욕설 필터링, 문장 부호 및 단어 수준 타임스탬프에 대한 속성이 포함되어 있습니다. 

### <a name="configuration"></a>구성

구성 매개 변수는 JSON으로 제공됩니다.

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Batch Transcription API는 기록, 해당 상태 및 관련 결과를 요청하기 위해 REST 서비스를 사용합니다. 모든 언어에서 API를 사용할 수 있습니다. 다음 섹션에서는 API 사용 방법을 설명합니다.

### <a name="configuration-properties"></a>구성 속성

| 매개 변수 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용되는 값은 욕설 필터링을 비활성화하는 `none`, 욕설을 별표로 바꾸는 `masked`, 결과에서 모든 욕설을 제거하는 `removed`, 또는 “profanity” 태그를 추가하는 `tags`입니다. 기본 설정은 `masked`입니다. | 옵션 |
| `PunctuationMode` | 인식 결과에서 문장 부호의 처리 방법을 지정합니다. 허용되는 값은 문장 부호를 비활성화하는 `none`, 명시적인 문장 부호를 의미하는 `dictated`, 디코더가 문장 부호를 처리하도록 하는 `automatic`, 지정된 문장 부호 또는 자동을 의미하는 `dictatedandautomatic`입니다. | 옵션 |
 | `AddWordLevelTimestamps` | 단어 수준 타임스탬프를 출력에 추가할지 여부를 지정합니다. 허용되는 값은 단어 수준 타임스탬프를 사용하는 `true`와 사용하지 않는 `false`(기본값)입니다. | 옵션 |
 | `AddSentiment` | 발언에 감정을 추가할 것인지 지정합니다. 허용되는 값인 `true`는 발언당 감정을 사용 설정하고 `false`(기본값)는 사용하지 않도록 설정합니다. | 옵션 |

### <a name="storage"></a>Storage

일괄 처리 기록은 오디오를 읽고 기록을 저장소에 쓰기 위해 [Azure Blob 저장소](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)를 지원합니다.

## <a name="webhooks"></a>Webhook 

기록 상태에 대한 폴링은 가장 효율적이거나 최상의 사용자 환경을 제공하지 못할 수 있습니다. 상태를 조사하기 위해, 장기 실행 기록 작업이 완료되었을 때 클라이언트에 알리도록 콜백을 등록할 수 있습니다.

자세한 내용은 참조 하세요. [웹 후크](webhooks.md)합니다.

## <a name="sentiment"></a>감정

감정은 일괄 처리 기록 API의 새로운 기능이자 호출 센터 도메인의 중요한 기능입니다. 고객은 다음 요청에 `AddSentiment` 매개 변수를 사용할 수 있습니다. 

1.  고객 만족도에 대한 통찰력 얻기
2.  에이전트(호출을 수행하는 팀)의 성능에 대한 통찰력 확보
3.  부정 방향으로 호출될 때의 정확한 시점을 파악
4.  부정 호출이 긍정으로 바뀔 경우를 파악
5.  제품이나 서비스에 대해 고객이 좋아하는 점과 싫어하는 점을 식별

감정은 오디어 세그먼트가 발언의 시작(오프셋)과 바이트 스트림 끝의 침묵 감지 사이의 시간 경과로 정의되는 오디오 세그먼트당 점수가 매겨집니다. 해당 세그먼트 내의 전체 텍스트가 감정 계산에 사용됩니다. 전체 호출 또는 각 채널의 전체 음성에 대한 종합 감정의 값은 계산하지 않습니다. 이를 추가로 적용하는 것은 도메인 소유자의 몫입니다.

감정은 어휘 폼에 적용됩니다.

JSON 출력 예제는 아래와 같습니다.

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
현재 베타 상태인 감정 모델을 사용 하는 기능입니다.

## <a name="sample-code"></a>샘플 코드

전체 샘플은 `samples/batch` 하위 디렉터리 내부의 [GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples)에서 확인할 수 있습니다.

사용자 지정 어쿠스틱 또는 언어 모델을 사용하려는 경우 사용자 구독 정보, 서비스 지역, 기록할 오디오 파일을 가리키는 SAS URI 및 모델 ID를 사용하여 샘플 코드를 사용자 지정해야 합니다. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

샘플 코드는 클라이언트를 설정하고 기록 요청을 제출합니다. 그런 다음, 상태 정보를 폴링하고 기록 진행 상황에 대한 세부 정보를 출력합니다.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

이전 호출에 대한 자세한 내용은 [Swagger 문서](https://westus.cris.ai/swagger/ui/index)를 참조하세요. 여기에 표시된 전체 샘플을 보려면 [GitHub](https://aka.ms/csspeech/samples)의 `samples/batch` 하위 디렉터리로 이동합니다.

오디오를 게시하고 기록 상태를 수신하려면 비동기 설정을 기록해 둡니다. 만든 클라이언트는 .NET HTTP 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드 및 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 기록 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 사용자 지정 모델을 지정하지 않습니다. 이 서비스는 파일을 기록하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 음향 및 언어 모델에 대해 모델 ID와 동일한 메서드를 전달할 수 있습니다.

> [!NOTE]
> 기준 기록의 경우 기준 모델의 ID를 선언할 필요가 없습니다. 언어 모델 ID(어쿠스틱 모델 ID 아님)만 지정하면 일치하는 어쿠스틱 모델이 자동으로 선택됩니다. 어쿠스틱 모델 ID만 지정하면 일치하는 언어 모델이 자동으로 선택됩니다.

## <a name="download-the-sample"></a>샘플 다운로드

[GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples)의 `samples/batch` 디렉터리에서 샘플을 확인할 수 있습니다.

> [!NOTE]
> 일괄 처리 기록 작업은 최상의 노력을 기준으로 예약되며, 작업이 언제 실행 상태로 변경될지에 대한 시간 예상은 없습니다. 실행 상태가 되면 실제 기록이 실시간 오디오보다 빠르게 처리됩니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
