---
title: 긴 오디오 API (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 긴 형식의 텍스트를 음성으로 통합 하기 위해 디자인 되는 방법에 대해 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167762"
---
# <a name="long-audio-api-preview"></a>긴 오디오 API (미리 보기)

긴 오디오 API는 긴 형식 텍스트를 음성으로 통합 (예: 오디오 서적, 뉴스 기사 및 문서) 하는 데 적합 합니다. 이 API는 합성 오디오를 실시간으로 반환 하지 않습니다. 대신 응답을 폴링하고 서비스에서 제공 되는 출력을 사용 하는 것이 예상 됩니다. 음성 SDK에서 사용 하는 텍스트-음성 API와 달리 긴 오디오 API는 10 분 이상 합성 오디오를 만들 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에 이상적입니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스에서 반환 되는 합성 된 음성은 가장 적합 한 신경망을 사용 합니다.
* 합성 음성 끝점은 실시간 일괄 처리 모드가 아닌 음성으로 배포할 필요가 없습니다.

> [!NOTE]
> 이제 긴 오디오 API는 [공용 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) 및 [사용자 지정 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)을 모두 지원 합니다.

## <a name="workflow"></a>워크플로

일반적으로 긴 오디오 API를 사용 하는 경우 합성 될 텍스트 파일을 제출 하 고, 상태를 폴링하고, 상태가 성공 인 경우 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공 합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비 하는 경우 다음을 확인 합니다.

* 일반 텍스트 (.txt) 또는 SSML 텍스트 (.txt) 중 하나입니다.
* 는 [BOM (바이트 순서 표시)을 사용 하 여 u t f-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 로 인코딩됩니다.
* 는 zip이 아니라 단일 파일입니다.
* 일반 텍스트의 경우 400 자 보다 크거나, SSML 텍스트에 대 한 400 [청구 가능 문자](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) 를 포함 하며, 1만 단락 보다 작음
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** -View [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 에 따라 구분 됩니다.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주 됩니다. [Ssml 부분은](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) 다른 단락으로 구분 해야 합니다.
> [!NOTE]
> 중국어 (중국), 중국어 (홍콩 특별 행정구), 중국어 (대만), 일본어 및 한국어의 경우 한 단어는 두 문자로 계산 됩니다. 

## <a name="python-example"></a>Python 예제

이 섹션에는 긴 오디오 API의 기본 사용법을 보여 주는 Python 예제가 포함 되어 있습니다. 즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음이 코드 조각을 이라는 파일에 복사 `voice_synthesis_client.py` 합니다.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

이러한 라이브러리는 인수를 구문 분석 하 고, HTTP 요청을 생성 하 고, 텍스트 음성 변환 긴 오디오 REST API 호출 하는 데 사용 됩니다.

### <a name="get-a-list-of-supported-voices"></a>지원 되는 음성 목록 가져오기

이 코드를 사용 하 여 특정 지역/끝점에 대 한 전체 음성 목록을 가져올 수 있습니다. 코드를 다음에 추가 합니다 `voice_synthesis_client.py` .

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

명령을 사용 하 여 스크립트를 실행 하 `python voice_synthesis_client.py --voices -key <your_key> -region <region>` 고 다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* 를 `<region>` 음성 리소스가 생성 된 지역 (예: `eastus` 또는)으로 바꿉니다 `westus` . 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.

다음과 같은 출력이 표시 됩니다.

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

**Publicvoice** 매개 변수가 **True**인 경우 음성은 공용 신경망입니다. 그렇지 않으면 사용자 지정 신경망입니다.

### <a name="convert-text-to-speech"></a>텍스트를 음성으로 변환

일반 텍스트 또는 SSML 텍스트에서 입력 텍스트 파일을 준비한 후에 다음 코드를 추가 합니다 `voice_synthesis_client.py` .

> [!NOTE]
> ' concatenateResult '은 선택적 매개 변수입니다. 이 매개 변수를 설정 하지 않으면 오디오 출력이 단락 단위로 생성 됩니다. 매개 변수를 설정 하 여 오디오 된 os를 1 개의 출력으로 연결할 수도 있습니다. 기본적으로 오디오 출력은 riff-16khz-16 비트로 설정 됩니다. 지원 되는 오디오 출력에 대 한 자세한 내용은 [오디오 출력 형식](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)을 참조 하세요.

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

명령을 사용 하 여 스크립트를 실행 하 `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` 고 다음 값을 바꿉니다.

* `<your_key>`를 Speech Service 구독 키로 바꿉니다. 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* 를 `<region>` 음성 리소스가 생성 된 지역 (예: `eastus` 또는)으로 바꿉니다 `westus` . 이 정보는 [Azure Portal](https://aka.ms/azureportal)의 리소스에 대 한 **개요** 탭에서 확인할 수 있습니다.
* `<input>`텍스트 음성 변환에 대해 준비한 텍스트 파일의 경로로 대체 합니다.
* `<locale>`원하는 출력 로캘로 대체 합니다. 자세한 내용은 [언어 지원](language-support.md#neural-voices)을 참조 하세요.
* `<voice_guid>`원하는 출력 음성으로 대체 합니다. 끝점에 대 한 이전 호출에서 반환 된 음성 중 하나를 사용 `/voicesynthesis/voices` 합니다.

다음과 같은 출력이 표시 됩니다.

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

결과에는 서비스에 의해 생성 되는 입력 텍스트와 오디오 출력 파일이 포함 됩니다. 이러한 파일은 zip에서 다운로드할 수 있습니다.

> [!NOTE]
> 입력 파일이 2 개 이상 있는 경우 여러 요청을 제출 해야 합니다. 유의 해야 하는 몇 가지 제한 사항이 있습니다. 
> * 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 **5 개의** 요청을 서버에 제출할 수 있습니다. 이 제한이 초과 되 면 클라이언트는 429 오류 코드 (너무 많은 요청)를 가져옵니다. 초당 요청 크기를 줄이십시오.
> * 서버를 실행 하 고 각 Azure 구독 계정에 대해 최대 **120** 요청을 큐에 대기 시킬 수 있습니다. 이 제한이 초과 되 면 서버는 429 오류 코드 (너무 많은 요청)를 반환 합니다. 잠시 후 요청이 완료 될 때까지 새 요청을 제출 하지 마세요.

### <a name="remove-previous-requests"></a>이전 요청 제거

서비스는 각 Azure 구독 계정에 대해 최대 **2만** 개의 요청을 유지 합니다. 요청 금액이이 제한을 초과 하는 경우 새 요청을 만들기 전에 이전 요청을 제거 하세요. 기존 요청을 제거 하지 않으면 오류 알림이 수신 됩니다.

다음 코드를 `voice_synthesis_client.py`에 추가합니다.

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

을 실행 하 여 만든 `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` 합성 요청 목록을 가져옵니다. 다음과 같은 출력이 표시 됩니다.

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

요청을 삭제 하려면를 실행 하 `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` 고를 `<synthesis_id>` 이전 요청에서 반환 된 요청 ID 값으로 바꿉니다.

> [!NOTE]
> 상태가 ' 실행 중 '/' 대기 중 ' 인 요청은 제거 하거나 삭제할 수 없습니다.

완성 된는 `voice_synthesis_client.py` [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)에서 사용할 수 있습니다.

## <a name="http-status-codes"></a>HTTP 상태 코드

다음 표에서는 REST API의 HTTP 응답 코드 및 메시지에 대해 자세히 설명 합니다.

| API | HTTP 상태 코드 | Description | 해결 방법 |
|-----|------------------|-------------|----------|
| 생성 | 400 | 음성 합성은이 지역에서 사용할 수 없습니다. | 음성 구독 키를 지원 되는 지역으로 변경 합니다. |
|        | 400 | 이 영역에 대 한 **표준** 음성 구독만 유효 합니다. | 음성 구독 키를 "표준" 가격 책정 계층으로 변경 합니다. |
|        | 400 | Azure 계정에 대 한 2만 요청 제한을 초과 합니다. 새 요청을 제출 하기 전에 일부 요청을 제거 하세요. | 서버는 각 Azure 계정에 대해 최대 2만 개의 요청을 유지 합니다. 새 요청을 제출 하기 전에 일부 요청을 삭제 합니다. |
|        | 400 | 이 모델은 음성 합성에서 사용할 수 없습니다. {modelID}. | {ModelID}의 상태가 올바른지 확인 하세요. |
|        | 400 | 요청 지역이 {modelID} 모델의 지역과 일치 하지 않습니다. | {ModelID}의 지역이 요청 지역과 일치 하는지 확인 합니다. |
|        | 400 | 음성 합성은 바이트 순서 표식을 사용 하 여 UTF-8 인코딩의 텍스트 파일만 지원 합니다. | 입력 파일이 바이트 순서 표식을 사용 하 여 UTF-8 인코딩으로 되어 있는지 확인 합니다. |
|        | 400 | 음성 합성 요청에서는 유효한 SSML 입력만 허용 됩니다. | 입력 SSML 식이 올바른지 확인 합니다. |
|        | 400 | 입력 파일에 음성 이름 {voiceName}이 (가) 없습니다. | 입력 SSML 음성 이름이 모델 ID와 맞지 않습니다. |
|        | 400 | 입력 파일의 단락 수는 1만 미만 이어야 합니다. | 파일의 단락 수가 1만 미만 인지 확인 합니다. |
|        | 400 | 입력 파일은 400 자이 하 여야 합니다. | 입력 파일이 400 자를 초과 하는지 확인 합니다. |
|        | 404 | 음성 합성 정의에 선언 된 모델을 찾을 수 없습니다. {modelID}. | {ModelID}가 올바른지 확인 하세요. |
|        | 429 | 활성 음성 합성 제한을 초과 합니다. 일부 요청이 완료 될 때까지 기다려 주세요. | 서버를 실행 하 고 각 Azure 계정에 대해 최대 120 개의 요청을 큐에 대기 시킬 수 있습니다. 잠시 기다렸다가 일부 요청이 완료 될 때까지 새 요청을 제출 하지 마세요. |
| 모두       | 429 | 요청이 너무 많습니다. | 클라이언트는 각 Azure 계정에 대해 초당 최대 5 개의 요청을 서버에 제출할 수 있습니다. 초당 요청 크기를 줄이십시오. |
| DELETE    | 400 | 음성 합성 작업을 계속 사용 하 고 있습니다. | **완료** 또는 **실패**한 요청만 삭제할 수 있습니다. |
| GetByID   | 404 | 지정 된 엔터티를 찾을 수 없습니다. | 합성 ID가 올바른지 확인 합니다. |

## <a name="regions-and-endpoints"></a>지역 및 엔드포인트

긴 오디오 API는 고유한 끝점을 사용 하 여 여러 지역에서 사용할 수 있습니다.

| Azure 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| 캐나다 중부 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 미국 동부 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 인도 중부 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 미국 중남부 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 동남아시아 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 영국 남부 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 서유럽 | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 미국 서부 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>오디오 출력 형식

유연한 오디오 출력 형식을 지원 합니다. 단락 당 오디오 출력을 생성 하거나 ' concatenateResult ' 매개 변수를 설정 하 여 오디오 출력을 단일 출력에 연결할 수 있습니다. 긴 오디오 API에서 지원 되는 오디오 출력 형식은 다음과 같습니다.

> [!NOTE]
> 기본 오디오 형식은 riff-16khz-16 비트입니다.

* riff-8khz-16 비트
* riff-16khz-16 비트
* riff-24khz-16 비트
* riff-48khz-16 비트
* 오디오-16khz-32kbitrate 전송률-mono-mp3
* 오디오-16khz-64kbitrate 전송률-mono-mp3
* 오디오-16khz-128kbitrate 전송률-mono-mp3
* 오디오-24khz-48kbitrate 전송률-mono-mp3
* 오디오-24khz-96kbitrate 전송률-mono-mp3
* 오디오-24khz-160kbitrate 전송률-mono-mp3

## <a name="sample-code"></a>예제 코드
긴 오디오 API에 대 한 샘플 코드는 GitHub에서 사용할 수 있습니다.

* [샘플 코드: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
