---
title: 텍스트 음성 변환, Python - Speech Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Python 및 텍스트 음성 변환 REST API를 사용 하 여 텍스트를 음성으로 변환 하는 방법을 알아봅니다. 이 가이드에 포함된 샘플 텍스트는 SSML(Speech Synthesis Markup Language)로 구조화되어 있습니다. 이를 통해 음성 응답의 음성 및 언어를 선택할 수 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 63e6a2a47265eae08a653f3eadaf6bad86dd0635
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119721"
---
# <a name="convert-text-to-speech-using-python"></a>Python을 사용하여 텍스트 음성 변환

이 문서에서는 Python 및 텍스트 음성 변환 REST API를 사용 하 여 텍스트를 음성으로 변환 하는 방법을 알아봅니다. 이 가이드의 요청 본문은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 구조화되어 있으므로 응답의 음성 및 언어를 선택할 수 있습니다.

이 문서에는 음성 서비스 리소스를 사용 하는 [Azure Cognitive Services 계정이](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 필요 합니다. 계정이 없는 경우 [평가판](get-started.md)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Python 2.7.x 또는 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) 또는 즐겨 사용하는 텍스트 편집기
* Speech Service에 대한 Azure 구독 키

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `tts.py`라는 파일의 프로젝트에 복사합니다.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `pip install requests`를 실행합니다.

이러한 모듈은 음성 응답을 모듈은 타임스탬프가 있는 파일에 쓰고, HTTP 요청을 구성하고, Text-to-Speech API를 호출하는 데 사용됩니다.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>구독 키를 설정하고 TTS 프롬프트를 만듭니다.

다음 몇 개 섹션에서는 권한 부여를 처리하고, Text-to-Speech API를 호출하고, 응답의 유효성을 검사하는 메서드를 만듭니다. 먼저 이 샘플이 Python 2.7.x 및 3.x에서 작동하는지 확인하는 일부 코드를 추가하겠습니다.

```python
try:
    input = raw_input
except NameError:
    pass
```

그런 다음, 클래스를 만들겠습니다. 여기에 토큰 교환 및 Text-to-Speech API 호출을 위한 메서드를 넣습니다.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key`는 Azure Portal의 고유 키입니다. `tts`는 사용자에게 음성으로 변환될 텍스트를 입력하도록 요청합니다. 이 입력은 문자열 리터럴이므로 문자를 이스케이프할 필요가 없습니다. 마지막으로 `timestr`은 파일 이름을 지정하는 데 사용할 현재 시간을 가져옵니다.

## <a name="get-an-access-token"></a>액세스 토큰 가져오기

Text-to-Speech REST API에는 인증을 위한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오려면 교환이 필요합니다. 이 샘플에서는 `issueToken` 엔드포인트를 사용하여 액세스 토큰의 Speech Service 구독 키를 교환합니다.

이 샘플에서는 Speech Service 구독이 미국 서부 지역에 있다고 가정합니다. 다른 지역을 사용하는 경우 `fetch_token_url` 값을 업데이트합니다. 전체 목록은 [지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)을 참조하세요.

이 코드를 `TextToSpeech` 클래스에 복사합니다.

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> 인증에 대한 자세한 내용은 [액세스 토큰으로 인증](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)을 참조하세요.

## <a name="make-a-request-and-save-the-response"></a>요청을 작성하고 응답을 저장합니다.

여기서 요청을 작성하고 음성 응답을 저장하게 됩니다. 먼저 `base_url` 및 `path`를 설정해야 합니다. 이 샘플에서는 사용자가 미국 서부 엔드포인트를 사용하고 있다고 가정합니다. 리소스가 다른 지역에 등록된 경우에는 `base_url`을 업데이트해야 합니다. 자세한 내용은 [Speech Service 지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)을 참조하세요.

그런 다음, 요청에 필요한 헤더를 추가해야 합니다. Azure Portal에 있는 리소스 이름을 사용하여 `User-Agent`를 업데이트하고 `X-Microsoft-OutputFormat`을 선호하는 오디오 출력으로 설정해야 합니다. 출력 형식의 전체 목록은 [오디오 출력](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)을 참조하세요.

그런 다음, SSML(Speech Synthesis Markup Language)을 사용하여 요청 본문을 구성합니다. 이 샘플에서는 구조체를 정의하고 이전에 만든 `tts` 입력을 사용합니다.

>[!NOTE]
> 이 샘플에서는 `Guy24KRUS` 음성 글꼴을 사용합니다. Microsoft 제공 음성/언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.
> 브랜드의 고유하고 인식 가능한 음성을 만들려면 [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)를 참조하세요.

마지막으로 서비스에 대한 요청을 만듭니다. 요청이 성공하고 200 상태 코드가 반환되면 음성 응답이 타임스탬프가 있는 파일에 기록됩니다.

이 코드를 `TextToSpeech` 클래스에 복사합니다.

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>모든 요소 결합

이제 거의 완료되었습니다. 마지막 단계는 클래스를 인스턴스화하고 함수를 호출하는 것입니다.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 텍스트 음성 변환 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
python tts.py
```

프롬프트가 표시되면 텍스트를 음성으로 변환하려는 항목을 입력합니다. 성공하면 음성 파일이 프로젝트 폴더에 있습니다. 선호하는 미디어 플레이어를 사용하여 재생합니다.

## <a name="clean-up-resources"></a>리소스 정리

샘플 앱의 소스 코드에서 구독 키와 같은 기밀 정보를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Python 샘플 살펴보기](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>참고 항목

* [텍스트를 음성으로 변환 API 참조](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Python 및 Speech SDK를 사용하여 텍스트 음성 변환](quickstarts/speech-to-text-from-microphone.md)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
* [사용자 지정 음성을 만들기 위한 음성 샘플 녹음](record-custom-voice-samples.md)
