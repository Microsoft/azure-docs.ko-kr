---
title: 텍스트 음성 변환 컨테이너 엔드포인트 쿼리
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "73491118"
---
컨테이너는 [REST 기반 엔드포인트 api](../rest-text-to-speech.md)를 제공 합니다. 플랫폼, 프레임 워크 및 언어 변형을 사용할 수 있는 [샘플 소스 코드 프로젝트](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) 는 여러 가지가 있습니다.

*표준 텍스트 음성 변환* 컨테이너를 사용 하 여 다운로드 한 이미지 태그의 로캘과 음성을 사용 해야 합니다. 예를 들어 `latest` 태그를 다운로드 한 경우 기본 로캘은 `en-US` 및 `JessaRUS` 음성입니다. 그러면 `{VOICE_NAME}` 인수가 [`en-US-JessaRUS`](../language-support.md#standard-voices)됩니다. 아래 예제 SSML를 참조 하세요.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

그러나 *사용자 지정 텍스트 음성 변환* 의 경우 [사용자 지정 음성 포털](https://aka.ms/custom-voice-portal)에서 **음성/모델** 을 가져와야 합니다. 사용자 지정 모델 이름은 음성 이름과 동의어입니다. **학습** 페이지로 이동 하 여 `{VOICE_NAME}` 인수로 사용할 **음성/모델** 을 복사 합니다.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="사용자 지정 음성 모델-음성 이름":::

아래 예제 SSML를 참조 하세요.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

몇 가지 헤더와 데이터 페이로드를 제공 하는 HTTP POST 요청을 생성 해 보겠습니다. `{VOICE_NAME}` 자리 표시자를 사용자 고유의 값으로 바꿉니다.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

이 명령은 다음을 수행합니다.

* `speech/synthesize/cognitiveservices/v1` 엔드포인트에 대 한 HTTP POST 요청을 생성 합니다.
* `audio/*` `Accept` 헤더를 지정 합니다.
* `application/ssml+xml`의 `Content-Type` 헤더를 지정 합니다. 자세한 내용은 [요청 본문](../rest-text-to-speech.md#request-body)을 참조 하세요.
* `riff-16khz-16bit-mono-pcm`의 `X-Microsoft-OutputFormat` 헤더를 지정 합니다. 자세한 옵션은 [오디오 출력](../rest-text-to-speech.md#audio-outputs)을 참조 하세요.
* `{VOICE_NAME}` 지정 된, 엔드포인트에 대 한 [SSML (음성 합성 마크업 언어)](../speech-synthesis-markup.md) 요청을 보냅니다.