---
title: 음성 컨테이너 설치-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 컨테이너를 설치하고 실행합니다. 음성 텍스트 변환은 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 기록합니다. 텍스트 음성 변환은 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: 80b7d5ca67751cf7ece775331cc13cfbac10395b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182397"
---
# <a name="install-and-run-speech-service-containers-preview"></a>음성 서비스 컨테이너 설치 및 실행 (미리 보기)

컨테이너를 사용하면 사용자 환경에서 음성 서비스 API의 일부를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 음성 컨테이너를 다운로드, 설치 및 실행하는 방법에 대해 알아봅니다.

음성 컨테이너는 고객이 강력한 클라우드 기능 및 에지 지역성 모두에 최적화된 음성 애플리케이션 아키텍처를 구축할 수 있도록 합니다. 사용할 수 있는 4개의 서로 다른 컨테이너가 있습니다. 두 표준 컨테이너는 **음성에서 텍스트** 이며 **텍스트를 음성으로 변환**합니다. 두 사용자 지정 컨테이너는 **Custom Speech 텍스트** 및 **사용자 지정 텍스트 음성 변환**입니다. 음성 컨테이너는 클라우드 기반 Azure Speech Services와 동일한 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 을 갖습니다.

> [!IMPORTANT]
> 모든 음성 컨테이너는 현재 [공용 "제어 된" 미리 보기](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)의 일부로 제공 됩니다. 음성 컨테이너가 GA (일반 공급)로 진행 될 때 알림이 생성 됩니다.

| 함수 | 기능 | 가장 늦은 날짜 |
|--|--|--|
| 음성 텍스트 변환 | 중간 결과를 사용 하 여 감정 및 speech 연속 실시간 음성 또는 배치 오디오 녹음을 분석 합니다.  | 2.4.0 |
| Custom Speech 텍스트 | [Custom Speech 포털](https://speech.microsoft.com/customspeech)에서 사용자 지정 모델을 사용 하 여 연속 실시간 음성 또는 배치 오디오 녹음을 중간 결과가 포함 된 텍스트로 speech. | 2.4.0 |
| 텍스트 음성 변환 | 일반 텍스트 입력 또는 SSML (음성 합성 마크업 언어)을 사용 하 여 텍스트를 자연 스런 음성으로 변환 합니다. | 1.6.0 |
| 사용자 지정 텍스트 음성 변환 | [사용자 지정 음성 포털](https://aka.ms/custom-voice-portal)에서 사용자 지정 모델을 사용 하 여 텍스트를 일반 텍스트 입력 또는 SSML (음성 합성 마크업 언어)을 사용 하 여 자연 스러운 음성으로 변환 합니다. | 1.6.0 |

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

음성 컨테이너를 사용 하기 전에 다음 필수 구성 요소:

| 필수 | 용도 |
|--|--|
| Docker 엔진 | [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br> |
| Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다. |
| 음성 리소스 | 이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결 된 API 키와 끝점 URI를 가져오는 Azure _Speech_ 리소스입니다. 두 값은 모두 Azure Portal의 **음성** 개요 및 키 페이지에서 사용할 수 있습니다. 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공 된 끝점입니다. |

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

[요청 양식을](https://aka.ms/cognitivegate) 작성 하 고 전송 하 여 컨테이너에 대 한 액세스를 요청 합니다. 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트**는 Docker 컨테이너를 실행하는 컴퓨터입니다. 호스트는 AVX2 ( [고급 벡터 확장](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) )를 *지원 해야* 합니다. 다음 명령을 사용 하 여 Linux 호스트에서 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2을 지원 하려면 호스트 컴퓨터가 *필요* 합니다. 컨테이너가 AVX2을 지원 하지 않으면 제대로 작동 *하지* 않습니다.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 음성 컨테이너에 대 한 최소 및 권장 리소스 할당을 설명 합니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 음성 텍스트 변환 | 2 코어, 2gb 메모리 | 4 코어, 4gb 메모리 |

# <a name="custom-speech-to-text"></a>[Custom Speech 텍스트](#tab/cstt)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Custom Speech 텍스트 | 2 코어, 2gb 메모리 | 4 코어, 4gb 메모리 |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 텍스트 음성 변환 | 1 코어, 2gb 메모리 | 2 코어, 3gb 메모리 |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 사용자 지정 텍스트 음성 변환 | 1 코어, 2gb 메모리 | 2 코어, 3gb 메모리 |

***

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!NOTE]
> 최소 및 권장 사항은 호스트 컴퓨터 리소스가 *아니라* Docker 한도를 기반으로 합니다. 예를 들어, 음성-텍스트 컨테이너는 큰 언어 모델의 맵 부분을 기억하며 추가로 4 ~ 6GB의 메모리에 전체 파일을 두는 것을 *권장* 합니다. 또한 모델은 메모리로 페이징되므로 두 컨테이너의 첫 번째 실행은 더 오래 걸릴 수 있습니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

다음 Container Registry에서 음성을 위한 컨테이너 이미지를 사용할 수 있습니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 음성 텍스트 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech 텍스트](#tab/cstt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Custom Speech 텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>음성 컨테이너에 대 한 Docker 풀

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>음성-텍스트 컨테이너를 위한 Docker 풀

[Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest`태그는 로캘을 끌어옵니다 `en-US` . 추가 로캘은 [음성 텍스트 로캘을](#speech-to-text-locales)참조 하세요.

#### <a name="speech-to-text-locales"></a>음성 텍스트 로캘

을 제외한 모든 태그는 `latest` 다음과 같은 형식 이며 대/소문자를 구분 합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
2.4.0-amd64-en-us-preview
```

**음성-텍스트** 컨테이너의 지원 되는 모든 로캘에 대해 [음성 텍스트 이미지 태그](../containers/container-image-tags.md#speech-to-text)를 참조 하세요.

# <a name="custom-speech-to-text"></a>[Custom Speech 텍스트](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Custom Speech-텍스트 컨테이너의 Docker 풀

[Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` 사용자 지정 음성 컨테이너의 및는 컨테이너에 의해 수집 된 사용자 지정 모델에 의해 결정 됩니다.

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>텍스트 음성 변환 용 Docker 풀 컨테이너

[Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 태그는 `en-US` 로캘 및 `ariarus` 음성을 끌어오기 합니다. 추가 로캘에 대해서는 [텍스트 음성 변환 로캘을](#text-to-speech-locales)참조 하세요.

#### <a name="text-to-speech-locales"></a>텍스트 음성 변환

을 제외한 모든 태그는 `latest` 다음과 같은 형식 이며 대/소문자를 구분 합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
1.6.0-amd64-en-us-ariarus-preview
```

지원 되는 모든 로캘과 **텍스트 음성 변환** 컨테이너의 해당 음성에 대해 [텍스트 음성 변환 이미지 태그](../containers/container-image-tags.md#text-to-speech)를 참조 하세요.

> [!IMPORTANT]
> *표준 텍스트 음성 변환* HTTP POST를 생성 하는 경우 [SSML (Speech 합성 Markup Language)](speech-synthesis-markup.md) 메시지에는 `voice` 특성이 있는 요소가 필요 합니다 `name` . 값은 해당 컨테이너 로캘 및 음성 ( ["짧은 이름"](language-support.md#standard-voices)이 라고도 함)입니다. 예를 들어 태그에는 `latest` 의 음성 이름이 `en-US-AriaRUS` 있습니다.

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>사용자 지정 텍스트 음성 변환 컨테이너에 대 한 Docker pull

[Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` 사용자 지정 음성 컨테이너의 및는 컨테이너에 의해 수집 된 사용자 지정 모델에 의해 결정 됩니다.

***

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](speech-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다.

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{Endpoint_URI}` 및 `{API_Key}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. 명령의 추가 [예](speech-container-configuration.md#example-docker-run-commands) `docker run` 도 사용할 수 있습니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

*음성 텍스트* 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *음성 텍스트* 컨테이너를 실행 합니다.
* 4 CPU 코어와 4gb 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>음성 텍스트 출력에서 감정 분석 

음성-텍스트 컨테이너의 v 2.2.0에서 시작 하 여 출력에서 [감정 분석 V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 를 호출할 수 있습니다. 감정 분석을 호출 하려면 텍스트 분석 API 리소스 끝점이 필요 합니다. 예: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

클라우드의 텍스트 분석 끝점에 액세스 하는 경우 키가 필요 합니다. Text Analytics를 로컬로 실행 하는 경우에는이 기능을 제공 하지 않아도 됩니다.

키와 끝점은 다음 예제와 같이 음성 컨테이너에 인수로 전달 됩니다.

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

이 명령은 다음을 수행합니다.

* 위의 명령과 동일한 단계를 수행 합니다.
* 감정 분석 요청을 보내기 위한 텍스트 분석 API 끝점 및 키를 저장 합니다. 


# <a name="custom-speech-to-text"></a>[Custom Speech 텍스트](#tab/cstt)

*Custom Speech-텍스트* 컨테이너는 사용자 지정 음성 모델을 기반으로 합니다. 사용자 지정 모델은 [사용자 지정 음성 포털](https://speech.microsoft.com/customspeech)을 사용 하 여 [학습](how-to-custom-speech-train-model.md) 해야 합니다.

> [!IMPORTANT]
> 다음 모델 버전 중 하나에서 Custom Speech 모델을 학습 해야 합니다.
> * **20181201 (v 3.3 통합)**
> * **20190520 (v 4.14 통합)**
> * **20190701 (v 4.17 통합)**<br>
> ![컨테이너 모델 Custom Speech 학습](media/custom-speech/custom-speech-train-model-container-scoped.png)

컨테이너를 실행 하려면 사용자 지정 음성 **모델 ID** 가 필요 합니다. 사용자 지정 음성 포털의 **학습** 페이지에서 찾을 수 있습니다. 사용자 지정 음성 포털에서 **학습** 페이지로 이동 하 여 모델을 선택 합니다.
<br>

![사용자 지정 음성 학습 페이지](media/custom-speech/custom-speech-model-training.png)

명령의 매개 변수에 대 한 인수로 사용할 **모델 ID** 를 가져옵니다 `ModelId` `docker run` .
<br>

![사용자 지정 음성 모델 세부 정보](media/custom-speech/custom-speech-model-details.png)

다음 표는 다양 한 `docker run` 매개 변수 및 해당 설명을 나타냅니다.

| 매개 변수 | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker에서 사용자 지정 모델을 유지 하는 데 사용 하는 호스트 컴퓨터 [볼륨 탑재](https://docs.docker.com/storage/volumes/)입니다. 예를 들어 *C 드라이브가* 호스트 컴퓨터에 있는 *c:\customspeech* 가 여기에 해당 합니다. |
| `{MODEL_ID}` | 사용자 지정 음성 포털의 **학습** 페이지에서 CUSTOM SPEECH **모델 ID** 입니다. |
| `{ENDPOINT_URI}` | 이 끝점은 계량 및 요금 청구에 필요 합니다. 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. |
| `{API_KEY}` | API 키가 필요 합니다. 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. |

*Custom Speech-텍스트* 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *Custom Speech 텍스트* 컨테이너를 실행 합니다.
* 4 CPU 코어와 4gb 메모리를 할당 합니다.
* 볼륨 입력 탑재에서 *Custom Speech 텍스트* 모델을 로드 합니다 (예: *c:\customspeech*).
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 지정 된 `ModelId` (볼륨 탑재에서 찾을 수 없는 경우) 모델을 다운로드 합니다.
* 사용자 지정 모델이 이전에 다운로드 된 경우는 `ModelId` 무시 됩니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

*텍스트 음성 변환* 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *텍스트 음성 변환* 컨테이너를 실행 합니다.
* 1 개의 CPU 코어와 2gb의 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

*사용자 지정 텍스트 음성 변환* 컨테이너는 사용자 지정 음성 모델을 기반으로 합니다. 사용자 지정 모델은 [사용자 지정 음성 포털](https://aka.ms/custom-voice-portal)을 사용 하 여 [학습](how-to-custom-voice-create-voice.md) 해야 합니다. 컨테이너를 실행 하려면 사용자 지정 음성 **모델 ID** 가 필요 합니다. 사용자 지정 음성 포털의 **학습** 페이지에서 찾을 수 있습니다. 사용자 지정 음성 포털에서 **학습** 페이지로 이동 하 여 모델을 선택 합니다.
<br>

![사용자 지정 음성 학습 페이지](media/custom-voice/custom-voice-model-training.png)

Docker run 명령의 매개 변수에 대 한 인수로 사용할 **모델 ID** 를 가져옵니다 `ModelId` .
<br>

![사용자 지정 음성 모델 세부 정보](media/custom-voice/custom-voice-model-details.png)

다음 표는 다양 한 `docker run` 매개 변수 및 해당 설명을 나타냅니다.

| 매개 변수 | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker에서 사용자 지정 모델을 유지 하는 데 사용 하는 호스트 컴퓨터 [볼륨 탑재](https://docs.docker.com/storage/volumes/)입니다. 예를 들어 *C 드라이브가* 호스트 컴퓨터에 있는 *c:\customspeech* 가 여기에 해당 합니다. |
| `{MODEL_ID}` | 사용자 지정 음성 포털의 **학습** 페이지에서 CUSTOM SPEECH **모델 ID** 입니다. |
| `{ENDPOINT_URI}` | 이 끝점은 계량 및 요금 청구에 필요 합니다. 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. |
| `{API_KEY}` | API 키가 필요 합니다. 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. |

*사용자 지정 텍스트 음성 변환* 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *사용자 지정 텍스트 음성 변환* 컨테이너를 실행 합니다.
* 1 개의 CPU 코어와 2gb의 메모리를 할당 합니다.
* 볼륨 입력 탑재에서 *사용자 지정 텍스트 음성 변환* 모델을 로드 합니다 (예: *c:\customvoice*).
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 지정 된 `ModelId` (볼륨 탑재에서 찾을 수 없는 경우) 모델을 다운로드 합니다.
* 사용자 지정 모델이 이전에 다운로드 된 경우는 `ModelId` 무시 됩니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

***

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

> [!NOTE]
> 여러 컨테이너를 실행 하는 경우 고유한 포트 번호를 사용 합니다.

| 컨테이너 | SDK 호스트 URL | 프로토콜 |
|--|--|--|
| 음성 텍스트 및 Custom Speech 텍스트 | `ws://localhost:5000` | WS |
| 텍스트 음성 변환 및 사용자 지정 텍스트 음성 변환 | `http://localhost:5000` | HTTP |

WSS 및 HTTPS 프로토콜을 사용 하는 방법에 대 한 자세한 내용은 [컨테이너 보안](../cognitive-services-container-support.md#azure-cognitive-services-container-security)을 참조 하세요.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>감정 분석

[컨테이너에](#analyze-sentiment-on-the-speech-to-text-output)텍스트 분석 API 자격 증명을 제공한 경우 speech SDK를 사용 하 여 감정 분석을 통해 음성 인식 요청을 보낼 수 있습니다. *단순* 또는 *자세한* 형식을 사용 하도록 API 응답을 구성할 수 있습니다.
> [!NOTE]
> 음성 서비스의 v2.0 Python SDK에는 감정 분석에 대해 식별 된 문제가 있습니다. 음성 서비스 Python SDK에서 감정 분석을 사용 하는 경우에는 v 1.12. x 또는 이전 버전을 사용 하세요.

# <a name="simple-format"></a>[단순 형식](#tab/simple-format)

간단한 형식을 사용 하도록 음성 클라이언트를 구성 하려면 `"Sentiment"` 에 값을 추가 `Simple.Extensions` 합니다. 특정 Text Analytics 모델 버전을 선택 하려면 `'latest'` 속성 구성에서를 대체 합니다 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` 는 응답의 루트 계층에 감정 결과를 반환 합니다.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[자세한 형식](#tab/detailed-format)

자세한 형식을 사용 하도록 음성 클라이언트를 구성 하려면 `"Sentiment"` `Detailed.Extensions` , 또는 둘 다의 값으로를 추가 `Detailed.Options` 합니다. 특정 Text Analytics 모델 버전을 선택 하려면 `'latest'` 속성 구성에서를 대체 합니다 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` 응답의 루트 계층에 감정 결과를 제공 합니다. `Detailed.Options` 응답의 계층에 결과를 제공 합니다 `NBest` . 개별적으로 또는 함께 사용할 수 있습니다.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

감정 분석을 완전히 사용 하지 않도록 설정 하려면 `false` 에 값을 추가 `sentimentanalysis.enabled` 합니다.

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>텍스트 음성 변환 또는 사용자 지정 텍스트 음성 변환

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

컨테이너를 시작 하거나 실행 하면 문제가 발생할 수 있습니다. 출력 [탑재](speech-container-configuration.md#mount-settings) 를 사용 하 고 로깅을 사용 하도록 설정 합니다. 이렇게 하면 컨테이너에서 문제 해결에 도움이 되는 로그 파일을 생성할 수 있습니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

음성 컨테이너는 Azure 계정의 *음성* 리소스를 사용하여 청구 정보를 Azure로 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](speech-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 음성 컨테이너 다운로드, 설치 및 실행에 대한 개념 및 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Speech는 다양 한 기능을 캡슐화 하는 Docker 용 Linux 컨테이너 4 개를 제공 합니다.
  * *음성 텍스트 변환*
  * *Custom Speech 텍스트*
  * *텍스트 음성 변환*
  * *사용자 지정 텍스트 음성 변환*
* 컨테이너 이미지는 Azure의 컨테이너 레지스트리에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API (텍스트 음성 변환 전용) 또는 SDK (음성 텍스트 또는 텍스트 음성 변환)를 사용 하 든 관계 없이 컨테이너의 호스트 URI를 지정 합니다. 
* 컨테이너를 인스턴스화할 때 청구 정보를 제공 해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정에 대 한 [컨테이너 구성](speech-container-configuration.md) 검토
* [Kubernetes 및 투구에서 음성 서비스 컨테이너를 사용](speech-container-howto-on-premises.md) 하는 방법을 알아봅니다.
* 더 많은 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
