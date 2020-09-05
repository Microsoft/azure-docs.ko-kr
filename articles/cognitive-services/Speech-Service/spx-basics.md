---
title: Speech CLI 기본 사항
titleSuffix: Azure Cognitive Services
description: Speech CLI 명령 도구를 사용하여 코드 없이 최소한의 설정으로 Speech Service를 작업하는 방법을 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854892"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Speech CLI의 기본 사항 알아보기

이 문서에서는 코드를 작성하지 않고 Speech Service를 사용하는 명령줄 도구인 Speech CLI의 기본 사용 패턴을 알아봅니다. 개발 환경을 만들거나 코드를 작성하지 않고도 Speech Service의 주요 기능을 신속하게 테스트하여 해당 사용 사례를 적절히 충족할 수 있는지 확인할 수 있습니다. 또한 Speech CLI는 프로덕션 환경에 즉시 사용할 수 있으며, `.bat` 또는 셸 스크립트를 사용하여 Speech Service에서 간단한 워크플로를 자동화할 수 있습니다.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>기본 사용법

이 섹션에서는 처음으로 테스트 또는 실험을 수행할 때 도움이 되는 몇 가지 기본 SPX 명령을 보여줍니다. 다음 명령을 실행하여 도구에 기본 제공되는 도움말을 확인하세요.

```shell
spx
```

명령 매개 변수 오른쪽에 나열된 도움말 항목을 **참조**하세요. 이러한 명령을 입력하여 하위 명령에 대한 자세한 도움말을 가져올 수 있습니다.

키워드를 기준으로 도움말 항목을 검색할 수 있습니다. 예를 들어 다음 명령을 입력하여 Speech CLI 사용 예제 목록을 확인합니다.

```shell
spx help find --topics "examples"
```

다음 명령을 입력하여 recognize 명령에 대한 옵션을 표시합니다.

```shell
spx help recognize
```

이제 Speech Service를 통해 다음 명령을 실행하여 기본 마이크를 사용하는 일부 음성 인식을 수행합니다.

```shell
spx recognize --microphone
```

명령을 입력하면 SPX는 현재 활성 입력 디바이스에서 오디오 수신 대기를 시작하고, `ENTER`를 누르면 중지됩니다. 그러면 기록된 음성이 인식되고 콘솔 출력의 텍스트로 변환됩니다. 텍스트 음성 변환 합성 역시 Speech CLI를 사용하여 쉽게 수행할 수 있습니다. 

다음 명령을 실행하면 입력한 텍스트가 입력으로 사용되고, 합성된 음성이 현재 활성 출력 디바이스로 출력됩니다.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

음성 인식 및 합성 외에도, Speech CLI를 사용하여 음성을 번역할 수 있습니다. 위의 음성 인식 명령과 마찬가지로, 다음 명령을 실행하여 기본 마이크의 오디오를 캡처한 후 대상 언어로 작성된 텍스트로 번역합니다.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

이 명령에서는 원본(**원래** 언어)과 대상(**번역되는** 언어)을 모두 지정합니다. `--microphone` 인수를 사용하면 현재 활성 입력 디바이스에서 오디오를 수신 대기하고, `ENTER`를 누르면 중지됩니다. 출력은 텍스트 파일에 대상 언어로 작성된 텍스트 번역입니다.

> [!NOTE]
> 지원되는 언어와 해당 로캘 코드 목록은 [언어 및 로캘 문서](language-support.md)를 참조하세요.

## <a name="batch-operations"></a>일괄 처리 작업

이전 섹션의 명령은 Speech Service의 작동 원리를 빠르게 알아보는 데 유용합니다. 그러나 사용자는 자신의 사용 사례를 충족할 수 있는지 평가할 때 현재 보유한 입력의 범위를 대상으로 일괄 처리 작업을 수행하여 서비스가 다양한 시나리오를 어떻게 처리하는지 확인해야 합니다. 이 섹션에서는 그 방법을 보여줍니다.

* 오디오 파일의 디렉터리에서 일괄 처리 음성 인식 실행
* `.tsv` 파일을 반복하고 일괄 처리 텍스트 음성 변환 합성 실행

## <a name="batch-speech-recognition"></a>일괄 처리 음성 인식

오디오 파일의 디렉터리가 있는 경우 간편하게 Speech CLI를 사용하여 일괄 처리 음성 인식을 신속하게 실행할 수 있습니다. 간단하게 다음 명령을 실행하고, `--files` 명령으로 해당 디렉터리를 가리키면 됩니다. 이 예제에서는 dir에 있는 모든 `.wav` 파일을 인식하도록 디렉터리에 `\*.wav`를 추가합니다. 또한 10개 병렬 스레드에서 인식을 실행하도록 `--threads` 인수를 지정합니다.

> [!NOTE]
> `--threads` 인수는 다음 섹션의 `spx synthesize` 명령에도 사용할 수 있으며, 사용 가능한 스레드는 CPU 및 현재 로드 비율에 따라 달라집니다.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

인식된 음성 출력은 `--output file` 인수를 사용하여 `speech_output.tsv`에 기록됩니다. 다음은 출력 파일 구조의 예입니다.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>일괄 처리 텍스트 음성 변환 합성

일괄 처리 텍스트 음성 변환을 실행하는 가장 쉬운 방법은 새 `.tsv`(탭으로 구분된 값) 파일을 만들고, Speech CLI에서 `--foreach` 명령을 활용하는 것입니다. 다음 `text_synthesis.tsv` 파일을 고려해 보세요.

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 그런 다음, 명령을 실행하여 `text_synthesis.tsv`를 가리키고, 각 `text` 필드에서 합성을 수행하고, 결과를 해당 `audio.output` 경로에 `.wav` 파일로 작성합니다. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

이 명령은 `.tsv` 파일의 **각** 레코드에 대해 `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav`를 실행하는 것과 동일한 효과가 있습니다. 다음 사항에 주의해야 합니다.

* 열 헤더 `audio.output` 및 `text`는 각각 명령줄 인수 `--audio output` 및 `--text`에 해당합니다. `--audio output`처럼 여러 부분으로 구성된 명령줄 인수는 파일에서 공백, 선행 대시, 문자열을 구분하는 마침표 없이 서식을 지정해야 합니다(예: `audio.output`). 그 외의 기존 명령줄 인수는 이 패턴을 사용하여 파일에 추가 열로 추가할 수 있습니다.
* 이 방법으로 파일의 형식을 지정하면 `--foreach`에 추가 인수를 전달할 필요가 없습니다.
* `.tsv`의 각 값을 **탭**으로 구분해야 합니다.

그러나 다음 예제와 같은 `.tsv` 파일이 있고 열 헤더가 명령줄 인수와 **일치하지 않는** 경우:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

`--foreach` 호출에서 다음 구문을 사용하여 이러한 필드 이름을 올바른 인수로 재정의할 수 있습니다. 이 호출은 위와 동일한 호출입니다.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>다음 단계

* SDK를 사용하여 [음성 인식](./quickstarts/speech-to-text-from-microphone.md) 또는 [음성 합성](./quickstarts/text-to-speech.md) 빠른 시작을 완료합니다.
