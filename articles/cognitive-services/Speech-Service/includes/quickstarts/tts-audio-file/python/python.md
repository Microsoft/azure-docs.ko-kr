---
title: '빠른 시작: 오디오 파일에 음성 합성, Python - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 738ce3d52464897df42be49828ca838b9f77d441
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156157"
---
## <a name="prerequisites"></a>사전 요구 사항

* Speech Service에 대한 Azure 구독 키입니다. [무료로 가져올 수 있습니다](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3.5 이상](https://www.python.org/downloads/)
* Python Speech SDK 패키지는 다음과 같은 운영 체제용으로 제공됩니다.
    * Windows: x64 및 x86
    * Mac: macOS X 버전 10.12 이상.
    * Linux: x64 기반의 Ubuntu 16.04, Ubuntu 18.04, Debian 9.
* Linux에서 필요한 패키지를 설치하려면 다음 명령을 실행합니다.

  * Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

이 명령은 Speech SDK를 위해 [PyPI](https://pypi.org/)의 Python 패키지를 설치합니다.

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>지원 및 업데이트

Speech SDK Python 패키지에 대한 업데이트는 PyPI를 통해 배포되며, [릴리스 정보](~/articles/cognitive-services/Speech-Service/releasenotes.md)에서 발표됩니다.
새 버전을 사용할 수 있는 경우 `pip install --upgrade azure-cognitiveservices-speech` 명령을 사용하여 업데이트할 수 있습니다.
`azure.cognitiveservices.speech.__version__` 변수를 검사하여 현재 설치된 버전을 확인하세요.

문제가 있거나 기능이 누락된 경우 [지원 및 도움말 옵션](~/articles/cognitive-services/Speech-Service/support.md)을 참조하세요.

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Speech SDK를 사용하는 Python 애플리케이션 만들기

### <a name="run-the-sample"></a>샘플 실행

이 빠른 시작에서 원본 파일 `quickstart.py`로 [샘플 코드](#sample-code)를 복사하고 IDE 또는 콘솔에서 실행할 수 있습니다.

```sh
python quickstart.py
```

또는 [Speech SDK 샘플 리포지토리](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)에서 [Jupyter](https://jupyter.org) Notebook으로 이 빠른 시작 자습서를 다운로드하고 노트북으로 실행할 수 있습니다.

### <a name="sample-code"></a>예제 코드

````Python

import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
# from here: https://aka.ms/speech/sdkregion).
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Speech SDK 설치 및 사용

1. 컴퓨터에 [Python](https://www.python.org/downloads/)의 64비트 버전(3.5 이상)을 다운로드하고 설치합니다.
1. [Visual Studio Code](https://code.visualstudio.com/Download)를 다운로드하여 설치합니다.
1. Visual Studio Code를 열고 Python 확장을 설치합니다. 메뉴에서 **파일** > **기본 설정** > **확장**을 선택합니다. **Python**을 검색합니다.

   ![Python 확장 설치](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. 프로젝트를 저장할 폴더를 만듭니다. 예를 들어 Windows 탐색기를 사용합니다.
1. Visual Studio Code에서 **파일** 아이콘을 선택합니다. 그런 다음, 사용자가 만든 폴더를 엽니다.

   ![폴더 열기](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 새 파일 아이콘을 선택하여 새 Python 원본 파일 `speechsdk.py`를 만듭니다.

   ![파일 만들기](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 새로 생성된 파일에 [Python 코드](#sample-code)를 복사, 붙여넣기 및 저장합니다.
1. Speech Service 구독 정보를 삽입합니다.
1. Python 인터프리터가 이미 선택된 경우 창 아래쪽의 상태 표시줄 왼쪽에 표시됩니다.
   그렇지 않을 경우 사용 가능한 Python 인터프리터 목록을 불러오세요. 명령 팔레트를 열고(Ctrl+Shift+P) **Python: 인터프리터 선택**을 입력합니다. 적절한 항목을 선택합니다.
1. Visual Studio Code 내에서 Speech SDK Python 패키지를 설치할 수 있습니다. 선택한 Python 인터프리터에 아직 설치되지 않은 경우 이렇게 합니다.
   Speech SDK 패키지를 설치하려면 터미널을 엽니다. 명령 팔레트를 다시 불러오고(Ctrl+Shift+P) **터미널: 새 통합 터미널 만들기**를 입력하여 터미널을 엽니다.
   열리는 터미널에서 `python -m pip install azure-cognitiveservices-speech` 명령 또는 시스템에 대한 적절한 명령을 입력합니다.
1. 샘플 코드를 실행하려면 편집기 내의 임의 위치를 마우스 오른쪽 단추로 클릭합니다. **터미널에서 Python 파일 실행**을 선택합니다.
   텍스트가 음성으로 변환되고 지정된 오디오 데이터에 저장됩니다.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

이러한 지침에 따른 문제가 있는 경우 더 광범위한 [Visual Studio Code Python 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
