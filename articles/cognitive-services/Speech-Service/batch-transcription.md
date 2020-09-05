---
title: 일괄 처리 기록을 사용 하는 방법-Speech service
titleSuffix: Azure Cognitive Services
description: Batch Transcription은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 전사하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 전사를 수신할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: 3b9a491f7546fbaa8722498b164bfa56353dfcfc
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050185"
---
# <a name="how-to-use-batch-transcription"></a>일괄 처리 기록을 사용 하는 방법

일괄 처리는 저장소에서 많은 양의 오디오를 높여줄 수 있도록 하는 REST API 작업 집합입니다. 일반적인 URI 또는 SAS (공유 액세스 서명) URI를 사용 하 여 오디오 파일을 가리키고 기록 결과를 비동기적으로 받을 수 있습니다. V 3.0 API를 사용 하 여 하나 이상의 오디오 파일을 높여줄 하거나 전체 저장소 컨테이너를 처리할 수 있습니다.

일괄 처리 기록 REST Api를 사용 하 여 다음 메서드를 호출할 수 있습니다.

|    일괄 처리 기록 작업                                             |    메서드    |    REST API 호출                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    새 기록을 만듭니다.                                              |    POST      |    speechtotext/v 3.0/            |
|    인증 된 구독의 기록 목록을 검색 합니다.    |    GET       |    speechtotext/v 3.0/            |
|    오프 라인 상태에서 지원 되는 로캘 목록을 가져옵니다.              |    GET       |    speechtotext/v 3.0//및 로캘    |
|    ID로 식별 되는 기록의 변경 가능한 세부 정보를 업데이트 합니다.    |    패치     |    speechtotext/v 3.0//{id}       |
|    지정 된 기록 작업을 삭제 합니다.                                 |    Delete    |    speechtotext/v 3.0//{id}       |
|    지정 된 ID로 식별 되는 기록을 가져옵니다.                        |    GET       |    speechtotext/v 3.0//{id}       |
|    지정 된 ID로 식별 되는 기록의 결과 파일을 가져옵니다.    |    GET       |    speechtotext/v 3.0//{id}/파일 |

[Swagger 문서로](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)제공 되는 자세한 API를 검토 하 고 테스트할 수 있습니다.

이 API에는 사용자 지정 끝점이 필요 하지 않으며 동시성 요구 사항이 없습니다.

일괄 처리 작업은 최상의 노력으로 예약 됩니다.
작업이 실행 중 상태로 변경 되는 경우를 예측할 수 없지만 일반적인 시스템 로드에서 몇 분 내에 발생 해야 합니다. 실행 중 상태가 되 면 기록을 오디오 런타임 재생 속도 보다 빠르게 수행 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](get-started.md)에 따라 [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다.

>[!NOTE]
> 일괄 처리 기록을 사용 하려면 Speech (표준 구독) 서비스를 사용 해야 합니다. 무료 구독 키 (F0)가 작동 하지 않습니다. 자세한 내용은 [가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조 하세요.

모델을 사용자 지정 하려는 경우 [음향 사용자 지정](how-to-customize-acoustic-models.md) 및 [언어 사용자 지정](how-to-customize-language-model.md)의 단계를 따릅니다. 일괄 처리에 생성 된 모델을 사용 하려면 모델 위치가 필요 합니다. 모델 (속성)의 세부 정보를 검사할 때 모델 위치를 검색할 수 있습니다 `self` . 배치 기록 서비스에는 배포 된 사용자 지정 끝점이 *필요 하지 않습니다* .

## <a name="batch-transcription-api"></a>Batch Transcription API

일괄 처리 기록 API는 다음과 같은 형식을 지원 합니다.

| 서식 | Codec | 샘플 당 비트 수 | 샘플링 주기             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16비트  | 8Khz 또는 16khz, mono 또는 스테레오 |
| MP3    | PCM   | 16비트  | 8Khz 또는 16khz, mono 또는 스테레오 |
| OGG    | OPUS  | 16비트  | 8Khz 또는 16khz, mono 또는 스테레오 |

스테레오 오디오 스트림의 경우 기록 중 왼쪽 채널과 오른쪽 채널이 분할 됩니다. 각 채널에 대해 JSON 결과 파일이 생성 되 고 있습니다.
정렬 된 최종 성적 증명서를 만들려면 utterance 생성 된 타임 스탬프를 사용 합니다.

### <a name="configuration"></a>Configuration

구성 매개 변수는 JSON (하나 이상의 개별 파일)으로 제공 됩니다.

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

구성 매개 변수는 JSON으로 제공 됩니다 (전체 저장소 컨테이너 처리).

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

다음 JSON은 일괄 처리 기록에 사용할 사용자 지정 학습 된 모델을 지정 합니다.

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>구성 속성

이러한 선택적 속성을 사용 하 여 기록을 구성 합니다.

:::row:::
   :::column span="1":::
      **매개 변수**
   :::column-end:::
   :::column span="2":::
      **설명**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      선택 사항이 며 기본값은 `Masked` 입니다. 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용 되는 값은 비속어 `None` 필터링을 사용 하지 않도록 설정 하 고, 사용 하지 않도록 설정 하 여 `Masked` `Removed` 결과에서 모든 비속어를 제거 하거나, `Tags` "비속어" 태그를 추가 하는 것입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      선택 사항이 며 기본값은 `DictatedAndAutomatic` 입니다. 인식 결과에서 문장 부호의 처리 방법을 지정합니다. 허용 되는 값은 문장 부호를 `None` 사용 하지 않도록 설정 하 여, `Dictated` `Automatic` 디코더가 문장 부호를 처리 하도록 하거나, 지정 된 `DictatedAndAutomatic` 자동 문장 부호를 사용 하도록 하는 것입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      `false`기본적으로 선택 사항입니다. 단어 수준 타임스탬프를 출력에 추가할지 여부를 지정합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      `false`기본적으로 선택 사항입니다. 입력에 대해 diarization 분석을 수행 하도록 지정 합니다 .이는 두 개의 음성을 포함 하는 mono 채널이 될 것으로 예상 됩니다. 참고: `wordLevelTimestampsEnabled` 를로 설정 해야 `true` 합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      선택 사항이 `0` 며 `1` 기본적으로 transcribed입니다. 처리할 채널 번호의 배열입니다. 여기에서 오디오 파일에서 사용 가능한 채널의 하위 집합을 처리 하도록 지정할 수 있습니다 (예: `0` ).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      선택 사항으로, 기본적으로 삭제 되지 않습니다. 기록을 완료 한 후 자동으로 삭제 하는 기간입니다. 는 `timeToLive` 대량 처리를 위해 대량 처리에서 유용 하 게 사용할 수 있도록 합니다 (예: `PT12H` 12 시간).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure에서 쓰기 가능한 컨테이너에 [서비스 임시 SAS](../../storage/common/storage-sas-overview.md) 를 사용 하는 선택적 URL입니다. 결과는이 컨테이너에 저장 됩니다. 저장 된 액세스 정책을 사용 하는 SAS는 지원 **되지 않습니다** . 지정 하지 않으면 microsoft에서 관리 하는 저장소 컨테이너에 결과를 저장 합니다. [삭제 기록을](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)호출 하 여 기록을 삭제 하면 결과 데이터도 삭제 됩니다.
:::row-end:::

### <a name="storage"></a>Storage

일괄 처리는 공개 된 인터넷 URI에서 오디오를 읽을 수 있으며, [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)에서 SAS URI를 사용 하 여 오디오를 읽거나 비디오를 작성할 수 있습니다.

## <a name="batch-transcription-result"></a>일괄 처리 기록 결과

각 오디오 입력에 대해 하나의 기록 결과 파일이 만들어집니다.
[Get-help 파일 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) 작업은이 기록에 대 한 결과 파일 목록을 반환 합니다. 특정 입력 파일에 대 한 기록 파일을 찾으려면 및를 사용 하 여 반환 된 모든 파일을 필터링 합니다 `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` .

각 기록 결과 파일의 형식은 다음과 같습니다.

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]    
    }
  ]
}
```

결과에는 다음 필드가 포함 됩니다.

:::row:::
   :::column span="1":::
      **필드**
   :::column-end:::
   :::column span="2":::
      **콘텐츠**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      실제 단어를 인식 합니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      인식 되는 텍스트의 반전 된 텍스트 형식입니다. 약어 ("의사 smith"), 전화 번호 및 기타 변환이 적용 됩니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      사용 금지 마스킹을 적용 한 ITN 폼입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      인식 되는 텍스트의 표시 형식입니다. 추가 된 문장 부호와 대문자 표시가 포함 됩니다.
:::row-end:::

## <a name="speaker-separation-diarization"></a>스피커 분리 (diarization)

Diarization는 스피커를 오디오 조각으로 분리 하는 프로세스입니다. Batch 파이프라인은 diarization를 지원 하 고 mono 채널 녹화에서 두 개의 스피커를 인식할 수 있습니다. 스테레오 녹화에서는이 기능을 사용할 수 없습니다.

Diarization enabled를 사용 하는 기록의 출력에는 `Speaker` 각 transcribed 구에 대 한 항목이 포함 됩니다. Diarization가 사용 되지 않는 경우 `Speaker` 속성은 JSON 출력에 표시 되지 않습니다. Diarization의 경우 두 개의 음성을 지원 하므로 스피커가 또는로 식별 됩니다 `1` `2` .

Diarization를 요청 하려면 `diarizationEnabled` `true` 아래에 HTTP 요청이 표시 되는 것 처럼 속성을로 설정 합니다.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

위의 요청에서 매개 변수가 나타내는 것 처럼 단어 수준 타임 스탬프를 사용 하도록 설정 해야 합니다.

## <a name="best-practices"></a>모범 사례

일괄 처리 기록 서비스는 많은 수의 제출 된 작업을 처리할 수 있습니다. [Get-help](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)의 상태를 쿼리할 수 있습니다.
결과를 검색 한 후 서비스에서 정기적으로 [삭제 기록을](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) 호출 합니다. 또는 `timeToLive` 결과가 최종 삭제 되도록 속성을 설정 합니다.

## <a name="sample-code"></a>예제 코드

전체 샘플은 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) 하위 디렉터리 내에서 사용할 수 있습니다 `samples/batch` .

사용자 지정 모델을 사용 하는 경우 구독 정보, 서비스 지역, 높여줄로 오디오 파일을 가리키는 URI 및 모델 위치를 사용 하 여 샘플 코드를 업데이트 합니다.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

샘플 코드는 클라이언트를 설정 하 고 기록 요청을 제출 합니다. 그런 다음 상태 정보를 폴링하고 기록 진행률에 대 한 세부 정보를 출력 합니다.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

이전 호출에 대한 자세한 내용은 [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)를 참조하세요. 여기에 표시된 전체 샘플을 보려면 [GitHub](https://aka.ms/csspeech/samples)의 `samples/batch` 하위 디렉터리로 이동합니다.

이 샘플에서는 비동기 설정을 사용 하 여 오디오를 게시 하 고 기록 상태를 수신 합니다.
`PostTranscriptions`메서드는 오디오 파일 정보를 보내고 메서드는 `GetTranscriptions` 상태를 수신 합니다.
`PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

이 샘플 코드는 사용자 지정 모델을 지정 하지 않습니다. 서비스는 파일을 찍으면 되므로 간편 하기 위한 기준 모델을 사용 합니다. 모델을 지정 하기 위해 사용자 지정 모델에 대 한 모델 참조와 동일한 메서드에 전달할 수 있습니다.

> [!NOTE]
> 초기 계획의 경우 기준 모델의 ID를 선언할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [음성 텍스트 v3 API 참조](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
