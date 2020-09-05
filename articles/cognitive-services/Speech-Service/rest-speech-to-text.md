---
title: 음성 텍스트 API 참조 (REST)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 REST API를 사용 하는 방법에 대해 알아봅니다. 이 문서에서는 권한 부여 옵션, 쿼리 옵션, 요청을 구성하고 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 874978288a38ff56ce220dd13cb6f3dfec902b2d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934585"
---
# <a name="speech-to-text-rest-api"></a>Speech-to-Text REST API

음성 [SDK](speech-sdk.md)대신 음성 서비스를 사용 하면 REST API를 사용 하 여 음성을 텍스트로 변환할 수 있습니다. 액세스 가능한 각 엔드포인트는 지역과 연결됩니다. 사용하려는 엔드포인트에 대한 구독 키가 애플리케이션에 필요합니다. REST API은 매우 제한적 이며, [음성 SDK](speech-sdk.md) 가 없는 경우에만 사용 해야 합니다.

음성 텍스트 REST API를 사용 하기 전에 다음을 이해 합니다.

* REST API를 사용 하 고 오디오를 직접 전송 하는 요청은 최대 60 초의 오디오만 포함할 수 있습니다.
* Speech-to-Text REST API는 최종 결과만 반환합니다. 부분 결과는 제공되지 않습니다.

응용 프로그램의 요구 사항이 긴 오디오를 보내는 경우에는 [음성 SDK](speech-sdk.md) 또는 [배치](batch-transcription.md)전송과 같은 파일 기반 REST API를 사용 하는 것이 좋습니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

REST API 끝점에는 다음과 같은 형식이 있습니다.

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`이 테이블에서 구독의 지역과 일치 하는 식별자로 대체 합니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP 오류가 발생하지 않도록 언어 매개 변수를 URL에 추가해야 합니다. 예를 들어 미국 서부 엔드포인트를 사용하는 미국 영어로 설정된 언어는 `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`입니다.

## <a name="query-parameters"></a>쿼리 매개 변수

이 매개 변수는 REST 요청의 쿼리 문자열에 포함할 수 있습니다.

| 매개 변수 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `language` | 인식되는 음성 언어를 식별합니다. [지원 되는 언어](language-support.md#speech-to-text)를 참조 하세요. | 필수 |
| `format` | 결과 형식을 지정합니다. 허용되는 값은 `simple` 및 `detailed`입니다. simple 결과에는 `RecognitionStatus`, `DisplayText`, `Offset` 및 `Duration`이 포함됩니다. 자세한 응답에는 표시 텍스트의 네 가지 표현이 포함 되어 있습니다. 기본 설정은 `simple`입니다. | 선택 사항 |
| `profanity` | 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용 되는 값은 사용 금지를 별표 ()로 대체 하는로, 결과의 `masked` `removed` 모든 비속어를 제거 하는 또는 `raw` 결과의 비속어를 포함 하는입니다. 기본 설정은 `masked`입니다. | 선택 사항 |
| `cid` | [Custom Speech 포털](how-to-custom-speech.md) 을 사용 하 여 사용자 지정 모델을 만드는 경우 **배포** 페이지에서 찾을 수 있는 **끝점 ID** 를 통해 사용자 지정 모델을 사용할 수 있습니다. 쿼리 문자열 매개 변수에 대 한 인수로 **끝점 ID** 를 사용 합니다 `cid` . | 선택 사항 |

## <a name="request-headers"></a>요청 헤더

이 표에는 음성 텍스트 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나와 있습니다.

|헤더| 설명 | 필수/선택 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Speech Service 구독 키입니다. | 이 헤더 또는 `Authorization`가 필요합니다. |
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 이 헤더 또는 `Ocp-Apim-Subscription-Key`가 필요합니다. |
| `Pronunciation-Assessment` | 인식 결과에서 발음 점수를 표시 하는 매개 변수를 지정 합니다 .이 매개 변수는 정확도, 능숙, 완전성 등의 표시기를 사용 하 여 음성 입력의 발음 품질을 평가 합니다. 이 매개 변수는 여러 개의 자세한 매개 변수를 포함 하는 base64 인코딩 json입니다. 이 헤더를 빌드하는 방법은 [발음 평가 매개 변수](#pronunciation-assessment-parameters) 를 참조 하세요. | 선택 사항 |
| `Content-type` | 제공된 오디오 데이터의 형식과 코덱을 설명합니다. 허용되는 값은 `audio/wav; codecs=audio/pcm; samplerate=16000` 및 `audio/ogg; codecs=opus`입니다. | 필수 |
| `Transfer-Encoding` | 단일 파일이 아닌 청크 분할된 오디오 데이터가 전송되고 있음을 지정합니다. 오디오 데이터를 청크 분할하는 경우에만 이 헤더를 사용합니다. | 선택 사항 |
| `Expect` | 청크 분할된 전송을 사용하는 경우 `Expect: 100-continue`를 전송합니다. Speech Service는 초기 요청을 인식하고 추가 데이터를 대기합니다.| 청크 분할된 오디오 데이터를 전송하는 경우에 필요합니다. |
| `Accept` | 제공하는 경우 `application/json`이어야 합니다. 음성 서비스는 JSON에서 결과를 제공 합니다. 일부 요청 프레임 워크는 호환 되지 않는 기본값을 제공 합니다. 항상를 포함 하는 것이 좋습니다 `Accept` . | 선택 사항이지만 권장됩니다. |

## <a name="audio-formats"></a>오디오 형식

오디오는 HTTP `POST` 요청 본문에서 전송됩니다. 오디오는 이 테이블의 형식 중 하나여야 합니다.

| 서식 | Codec | 비트 전송률 | 샘플링 주기  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16kHz, mono |
| OGG    | OPUS  | 256 kpbs | 16kHz, mono |

>[!NOTE]
>위의 형식은 음성 서비스의 REST API 및 WebSocket을 통해 지원 됩니다. [음성 SDK](speech-sdk.md) 는 현재 [다른 형식](how-to-use-codec-compressed-audio-input-streams.md)뿐만 아니라 PCM 코덱으로 WAV 형식을 지원 합니다.

## <a name="pronunciation-assessment-parameters"></a>발음 평가 매개 변수

다음 표에서는 발음 평가를 위한 필수 및 선택적 매개 변수를 보여 줍니다.

| 매개 변수 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| ReferenceText | 발음이 계산 될 텍스트입니다. | 필수 |
| GradingSystem | 점수 보정의 시점 시스템입니다. 허용되는 값은 `FivePoint` 및 `HundredMark`입니다. 기본 설정은 `FivePoint`입니다. | 선택 사항 |
| 세분성 | 평가 세분성입니다. 허용 되는 값은 전체 텍스트의 점수를 표시 하는 전체 텍스트, word 및 음소 수준에 대 한 점수를 표시 하는입니다 .이 값은 전체 텍스트 `Phoneme` `Word` `FullText` 수준 에서만 점수를 표시 합니다. 기본 설정은 `Phoneme`입니다. | 선택 사항 |
| 차원 | 출력 조건을 정의 합니다. 허용 되는 값은 정확도 점수를 표시 하는 이며, `Basic` `Comprehensive` 더 많은 차원에 대 한 점수를 표시 합니다 (예: 전체 텍스트 수준에서 점수와 완전성 점수, 단어 수준의 오류 유형 능숙). [응답 매개 변수](#response-parameters) 를 확인 하 여 다른 점수 차원 및 단어 오류 유형의 정의를 확인 합니다. 기본 설정은 `Basic`입니다. | 선택 사항 |
| EnableMiscue | Miscue 계산을 사용 합니다. 이 기능을 사용 하도록 설정 하면 단어를 참조 텍스트와 비교 하 여 비교에 따라 생략/삽입으로 표시 됩니다. 허용되는 값은 `False` 및 `True`입니다. 기본 설정은 `False`입니다. | 선택 사항 |
| ScenarioId | 사용자 지정 된 지점 시스템을 나타내는 GUID입니다. | 선택 사항 |

다음은 발음 평가 매개 변수를 포함 하는 JSON 예제입니다.

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

다음 샘플 코드에서는 발음 평가 매개 변수를 헤더에 빌드하는 방법을 보여 줍니다 `Pronunciation-Assessment` .

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

오디오 데이터를 게시 하는 동안 스트리밍 (청크 분할)을 업로드 하는 것이 좋습니다. 이렇게 하면 대기 시간이 크게 줄어들 수 있습니다. 스트리밍을 사용 하도록 설정 하는 방법에 대 한 [다른 프로그래밍 언어의 샘플 코드](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) 를 참조 하세요.

>[!NOTE]
>발음 평가 기능은 현재 `westus` , 및 지역 에서만 사용할 수 `eastasia` 있습니다 `centralindia` . 이 기능은 현재 언어 에서만 사용할 수 있습니다 `en-US` .

## <a name="sample-request"></a>샘플 요청

아래 샘플에는 호스트 이름 및 필수 헤더가 포함되어 있습니다. 서비스는 이 샘플에 포함되지 않은 오디오 데이터도 예상한다는 것에 유의해야 합니다. 앞에서 언급한 대로 청크 분할은 권장되지만 필수는 아닙니다.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

발음 평가를 사용 하려면 아래 헤더를 추가할 수 있습니다. 이 헤더를 빌드하는 방법은 [발음 평가 매개 변수](#pronunciation-assessment-parameters) 를 참조 하세요.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

## <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | Description | 가능한 원인 |
|------------------|-------------|-----------------|
| `100` | 계속 | 초기 요청이 수락되었습니다. 나머지 데이터의 전송을 계속합니다. (청크 분할 전송에 사용 됨) |
| `200` | 정상 | 요청이 성공했습니다. 응답 본문이 JSON 개체입니다. |
| `400` | 잘못된 요청 | 지원 되지 않는 언어 코드, 잘못 된 오디오 파일 등은 제공 되지 않습니다. |
| `401` | 권한 없음 | 구독 키 또는 권한 부여 토큰이 지정된 지역에서 올바르지 않거나 엔드포인트가 올바르지 않습니다. |
| `403` | 사용할 수 없음 | 구독 키 또는 권한 부여 토큰이 없습니다. |

## <a name="chunked-transfer"></a>청크 분할 전송

청크 분할 전송 ( `Transfer-Encoding: chunked` )을 통해 인식 대기 시간을 줄일 수 있습니다. 음성 서비스는 전송 되는 동안 오디오 파일의 처리를 시작할 수 있습니다. REST API는 부분 또는 중간 결과를 제공하지 않습니다.

이 코드 샘플은 오디오를 청크로 보내는 방법을 보여 줍니다. 오직 첫 번째 청크만 오디오 파일의 헤더를 포함해야 합니다. `request` 는 `HttpWebRequest` 적절 한 REST 끝점에 연결 된 개체입니다. `audioFile`은 디스크에서 오디오 파일의 경로입니다.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>응답 매개 변수

결과는 JSON으로 제공됩니다. `simple` 형식에는 이러한 최상위 수준 필드가 포함됩니다.

| 매개 변수 | Description  |
|-----------|--------------|
|`RecognitionStatus`|상태(예: 인식 성공에 `Success`)입니다. 다음 표를 참조하세요.|
|`DisplayText`|대/소문자, 문장 부호, 역 텍스트 정규화 (음성 텍스트를 "200"의 경우 200, "의사 smith"의 경우 "Dr. Smith"의 경우) 및 불경 마스킹을 통해 인식 되는 텍스트입니다. 성공 시만 표시합니다.|
|`Offset`|인식된 음성이 오디오 스트림에서 시작하는 시간(100나노초 단위)입니다.|
|`Duration`|오디오 스트림에서 인식된 음성의 기간(100나노초 단위)입니다.|

`RecognitionStatus` 필드에는 다음 값이 포함될 수 있습니다.

| 상태 | Description |
|--------|-------------|
| `Success` | 성공적으로 인식했고 `DisplayText` 필드가 있습니다. |
| `NoMatch` | 오디오 스트림에서 음성이 감지되었지만 대상 언어의 단어가 일치하지 않습니다. 일반적으로 인식 언어는 사용자가 말하는 것과 다른 언어를 의미합니다. |
| `InitialSilenceTimeout` | 오디오 스트림의 시작 부분에는 묵음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `BabbleTimeout` | 오디오 스트림의 시작 부분에는 소음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `Error` | 인식 서비스에서 내부 오류가 발생하여 계속할 수 없습니다. 가능한 경우 다시 시도하세요. |

> [!NOTE]
> 오디오가 욕설로만 구성되어 있고 `profanity` 쿼리 매개 변수가 `remove`로 설정되어 있는 경우 서비스는 음성 결과를 변환하지 않습니다.

형식에는 `detailed` 인식 된 결과의 추가 형식이 포함 되어 있습니다.
`detailed` 형식을 사용하는 경우 `DisplayText`는 `NBest` 목록의 각 결과에 대한 `Display`로 제공됩니다.

목록의 개체는 `NBest` 다음을 포함할 수 있습니다.

| 매개 변수 | Description |
|-----------|-------------|
| `Confidence` | 0.0(신뢰도 없음)에서 1.0(완전 신뢰도)까지 항목의 신뢰도 점수입니다. |
| `Lexical` | 인식된 텍스트의 어휘 형태, 즉 인식된 실제 단위입니다. |
| `ITN` | 전화 번호, 숫자, 축약어("doctor smith"가 "dr smith")가 포함된 인식된 텍스트의 역 텍스트 정규화된("기본형") 형태와 적용된 기타 변형입니다. |
| `MaskedITN` | 요청된 경우 욕설 마스킹이 적용된 ITN 형태입니다. |
| `Display` | 문장 부호 및 대문자로 표시가 추가된 인식된 텍스트의 표시 형태입니다. 이 매개 변수는 형식이 `simple`로 설정된 경우에 제공되는 `DisplayText`와 동일합니다. |
| `AccuracyScore` | 음성의 발음 정확도입니다. 정확도는 음소가 네이티브 스피커의 발음에 얼마나 근접 하 게 일치 하는지를 나타냅니다. 단어 및 전체 텍스트 수준 정확도 점수는 음소 수준 정확도 점수에서 집계 됩니다. |
| `FluencyScore` | 지정 된 음성의 능숙입니다. 능숙는 음성이 단어 사이에서 기본 스피커의 자동 나누기 사용과 일치 하는지 여부를 나타냅니다. |
| `CompletenessScore` | 음성의 완전성으로, 텍스트 입력을 참조 하는 단어를 발음 하는 비율을 계산 하 여 결정 됩니다. |
| `PronScore` | 지정 된 음성의 발음 품질을 나타내는 전체 점수입니다. 이는에서 `AccuracyScore` , 가중치를 사용 하 여 집계 됩니다 `FluencyScore` `CompletenessScore` . |
| `ErrorType` | 이 값은와 비교 하 여 단어를 생략 하거나, 삽입 하거나, 잘못 표시 하는지 여부를 나타냅니다 `ReferenceText` . 가능한 값은 `None` (이 단어에 오류가 없음을 의미 함) `Omission` , `Insertion` 및 `Mispronunciation` 입니다. |

## <a name="sample-responses"></a>샘플 응답

인식에 대 한 일반적인 응답 `simple` :

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

인식에 대 한 일반적인 응답 `detailed` :

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

음성 인식에 대 한 일반적인 답변은 다음과 같습니다.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계

- [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
