---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170172"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>스피커로 음성 합성

이제 Speech CLI를 실행하여 텍스트에서 음성을 합성할 준비가 되었습니다. 명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경합니다. 그런 후, 다음 명령을 실행합니다.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Speech CLI는 컴퓨터 스피커를 통해 영어로 자연어를 생성합니다.

## <a name="synthesize-speech-to-a-file"></a>음성을 파일로 합성

다음 명령을 실행하여 스피커의 출력을 `.wav` 파일로 변경합니다.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI는 `greetings.wav` 오디오 파일에 영어로 자연어를 생성합니다.
Windows에서는 `start greetings.wav`를 입력하여 오디오 파일을 재생할 수 있습니다.