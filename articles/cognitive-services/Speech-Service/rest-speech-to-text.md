---
title: 음성-텍스트 API 참조 (REST)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성-텍스트 REST API를 사용 하는 방법을 알아봅니다. 이 문서에서는 권한 부여 옵션, 쿼리 옵션, 요청을 구성하고 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57895315"
---
# <a name="speech-to-text-rest-api"></a>Speech-to-Text REST API

[음성 SDK](speech-sdk.md) 대안으로, 음성 서비스는 REST API를 사용하여 음성-텍스트 변환이 가능합니다. 액세스 가능한 각 엔드포인트는 지역과 연결됩니다. 사용하려는 엔드포인트에 대한 구독 키가 애플리케이션에 필요합니다.

음성-텍스트 REST API를 사용 하기 전에 다음을 이해 합니다.
* REST API를 사용하는 요청은 10초 동안 기록된 오디오만 포함할 수 있습니다.
* Speech-to-Text REST API는 최종 결과만 반환합니다. 부분 결과는 제공되지 않습니다.

애플리케이션을 위해 더 긴 오디오를 보내야 하는 경우 [음성 SDK](speech-sdk.md) 또는 [일괄 처리 기록](batch-transcription.md)을 사용하는 것이 좋습니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

이러한 지역은 REST API를 사용한 음성 텍스트 변환 기록에 대해 지원됩니다. 사용자 구독 지역과 일치하는 엔드포인트를 선택해야 합니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>쿼리 매개 변수

이 매개 변수는 REST 요청의 쿼리 문자열에 포함할 수 있습니다.

| 매개 변수 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `language` | 인식되는 음성 언어를 식별합니다. [지원되는 언어](language-support.md#speech-to-text)를 참조하세요. | 필수 |
| `format` | 결과 형식을 지정합니다. 허용되는 값은 `simple` 및 `detailed`입니다. simple 결과에는 `RecognitionStatus`, `DisplayText`, `Offset` 및 `Duration`이 포함됩니다. detailed 응답에는 신뢰도 값 및 4가지 다른 표현과 함께 여러 결과가 포함됩니다. 기본 설정은 `simple`입니다. | 옵션 |
| `profanity` | 인식 결과에서 욕설의 처리 방법을 지정합니다. 허용되는 값은 욕설을 별표로 바꾸는 `masked`, 결과에서 모든 욕설을 제거하는 `removed` 또는 욕설을 결과에 포함하는 `raw`입니다. 기본 설정은 `masked`입니다. | 옵션 |

## <a name="request-headers"></a>요청 헤더

이 표에는 음성 텍스트 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나와 있습니다.

|헤더| 설명 | 필수/선택 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 음성 서비스 구독 키입니다. | 이 헤더 또는 `Authorization`가 필요합니다. |
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 이 헤더 또는 `Ocp-Apim-Subscription-Key`가 필요합니다. |
| `Content-type` | 제공된 오디오 데이터의 형식과 코덱을 설명합니다. 허용되는 값은 `audio/wav; codecs=audio/pcm; samplerate=16000` 및 `audio/ogg; codecs=opus`입니다. | 필수 |
| `Transfer-Encoding` | 단일 파일이 아닌 청크 분할된 오디오 데이터가 전송되고 있음을 지정합니다. 오디오 데이터를 청크 분할하는 경우에만 이 헤더를 사용합니다. | 옵션 |
| `Expect` | 청크 분할된 전송을 사용하는 경우 `Expect: 100-continue`를 전송합니다. 음성 서비스는 초기 요청을 확인 하 고 추가 데이터를 기다립니다.| 청크 분할된 오디오 데이터를 전송하는 경우에 필요합니다. |
| `Accept` | 제공하는 경우 `application/json`이어야 합니다. Speech Services는 json에서 결과 제공합니다. 값을 지정하지 않을 경우 일부 웹 요청 프레임워크는 호환되지 않는 기본값을 지정하므로, 항상 `Accept`를 포함하는 것이 좋습니다. | 선택 사항이지만 권장됩니다. |

## <a name="audio-formats"></a>오디오 형식

오디오는 HTTP `POST` 요청 본문에서 전송됩니다. 오디오는 이 테이블의 형식 중 하나여야 합니다.

| 형식 | Codec | Bitrate | 샘플링 주기 |
|--------|-------|---------|-------------|
| WAV | PCM | 16비트 | 16kHz, mono |
| OGG | OPUS | 16비트 | 16kHz, mono |

>[!NOTE]
>위의 형식은 REST API 및 음성 서비스에서 WebSocket을 통해 지원 됩니다. [음성 SDK](speech-sdk.md)는 현재 PCM 코덱을 사용하는 WAV 형식만 지원합니다.

## <a name="sample-request"></a>샘플 요청

일반적인 HTTP 요청입니다. 아래 샘플에는 호스트 이름 및 필수 헤더가 포함되어 있습니다. 서비스는 이 샘플에 포함되지 않은 오디오 데이터도 예상한다는 것에 유의해야 합니다. 앞에서 언급한 대로 청크 분할은 권장되지만 필수는 아닙니다.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | 설명 | 가능한 원인 |
|------------------|-------------|-----------------|
| 100 | 계속 | 초기 요청이 수락되었습니다. 나머지 데이터의 전송을 계속합니다. (청크 분할 전송으로 사용함) |
| 200 | 확인 | 요청이 성공했습니다. 응답 본문이 JSON 개체입니다. |
| 400 | 잘못된 요청 | 언어 코드가 제공되지 않았거나 지원되지 않는 언어입니다. 오디오 파일이 잘못되었습니다. |
| 401 | 권한 없음 | 구독 키 또는 권한 부여 토큰이 지정된 지역에서 올바르지 않거나 엔드포인트가 올바르지 않습니다. |
| 403 | 사용할 수 없음 | 구독 키 또는 권한 부여 토큰이 없습니다. |

## <a name="chunked-transfer"></a>청크 분할 전송

청크 분할 전송(`Transfer-Encoding: chunked`)은 오디오 파일을 전송하는 동안 음성 서비스가 처리를 시작하는 것을 허용하므로 인식 대기 시간을 줄이는 데 도움이 됩니다. REST API는 부분 또는 중간 결과를 제공하지 않습니다. 이 옵션은 응답성을 향상하기 위한 용도로만 사용됩니다.

이 코드 샘플은 오디오를 청크로 보내는 방법을 보여 줍니다. 오직 첫 번째 청크만 오디오 파일의 헤더를 포함해야 합니다. `request`는 적절한 REST 엔드포인트에 연결된 HTTPWebRequest 개체입니다. `audioFile`은 디스크에서 오디오 파일의 경로입니다.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>응답 매개 변수

결과는 JSON으로 제공됩니다. `simple` 형식에는 이러한 최상위 수준 필드가 포함됩니다.

| 매개 변수 | 설명  |
|-----------|--------------|
|`RecognitionStatus`|상태(예: 인식 성공에 `Success`)입니다. 다음 표를 참조하세요.|
|`DisplayText`|대문자로 표시, 문장 부호, 역 텍스트 정규화(”two hundred”를 “200”, 또는 “doctor smith”를 “Dr. Smith"로, 발화된 텍스트를 더 짧은 형태로 변환 ) 및 욕설 마스킹 후에 인식된 텍스트입니다. 성공 시만 표시합니다.|
|`Offset`|인식된 음성이 오디오 스트림에서 시작하는 시간(100나노초 단위)입니다.|
|`Duration`|오디오 스트림에서 인식된 음성의 기간(100나노초 단위)입니다.|

`RecognitionStatus` 필드에는 다음 값이 포함될 수 있습니다.

| 상태 | 설명 |
|--------|-------------|
| `Success` | 성공적으로 인식했고 `DisplayText` 필드가 있습니다. |
| `NoMatch` | 오디오 스트림에서 음성이 감지되었지만 대상 언어의 단어가 일치하지 않습니다. 일반적으로 인식 언어는 사용자가 말하는 것과 다른 언어를 의미합니다. |
| `InitialSilenceTimeout` | 오디오 스트림의 시작 부분에는 묵음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `BabbleTimeout` | 오디오 스트림의 시작 부분에는 소음만 있으며, 서비스의 음성 대기 시간이 초과되었습니다. |
| `Error` | 인식 서비스에서 내부 오류가 발생하여 계속할 수 없습니다. 가능한 경우 다시 시도하세요. |

> [!NOTE]
> 오디오가 욕설로만 구성되어 있고 `profanity` 쿼리 매개 변수가 `remove`로 설정되어 있는 경우 서비스는 음성 결과를 변환하지 않습니다.

`detailed` 형식은 동일한 인식 결과에 대한 대체 해석 목록인 `NBest`와 함께 `simple` 형식과 동일한 데이터를 포함합니다. 이러한 결과는 가장 가능성이 높은 항목부터 가장 낮은 항목 순으로 순위가 지정되어 있으며, 첫 번째 항목은 기본 인식 결과와 동일합니다.  `detailed` 형식을 사용하는 경우 `DisplayText`는 `NBest` 목록의 각 결과에 대한 `Display`로 제공됩니다.

`NBest` 목록의 각 개체에는 다음이 포함됩니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| `Confidence` | 0\.0(신뢰도 없음)에서 1.0(완전 신뢰도)까지 항목의 신뢰도 점수입니다. |
| `Lexical` | 인식된 텍스트의 어휘 형태, 즉 인식된 실제 단위입니다. |
| `ITN` | 전화 번호, 숫자, 축약어("doctor smith"가 "dr smith")가 포함된 인식된 텍스트의 역 텍스트 정규화된("기본형") 형태와 적용된 기타 변형입니다. |
| `MaskedITN` | 요청된 경우 욕설 마스킹이 적용된 ITN 형태입니다. |
| `Display` | 문장 부호 및 대문자로 표시가 추가된 인식된 텍스트의 표시 형태입니다. 이 매개 변수는 형식이 `simple`로 설정된 경우에 제공되는 `DisplayText`와 동일합니다. |

## <a name="sample-responses"></a>샘플 응답

`simple` 인식에 대한 일반적인 응답입니다.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 인식에 대한 일반적인 응답입니다.

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
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
