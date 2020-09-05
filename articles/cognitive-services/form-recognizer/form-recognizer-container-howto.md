---
title: 양식 인식기의 컨테이너를 설치 하 고 실행 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services 양식 인식기 컨테이너를 사용 하 여 양식 및 테이블 데이터를 구문 분석 하는 방법을 설명 합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e4e85de2fad5c08f296d8089f48fa8614f7f1739
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925184"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>양식 인식기 컨테이너 설치 및 실행 (미리 보기)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure 양식 인식기는 기계 학습 기술을 적용 하 여 양식에서 키-값 쌍 및 테이블을 식별 하 고 추출 합니다. 값과 테이블 항목을 키-값 쌍에 연결 하 고 원본 파일의 관계를 포함 하는 구조화 된 데이터를 출력 합니다. 

복잡성을 줄이고 사용자 지정 양식 인식기 모델을 워크플로 자동화 프로세스나 기타 응용 프로그램에 쉽게 통합 하기 위해 간단한 REST API를 사용 하 여 모델을 호출할 수 있습니다. 5 개의 양식 문서 (또는 빈 양식과 두 개의 채워진 양식)만 필요 하므로 특정 콘텐츠에 대 한 결과를 빠르고 정확 하 게 볼 수 있습니다. 높은 수동 작업 또는 광범위 한 데이터 과학에 대 한 지식이 필요 하지 않습니다. 데이터 레이블 지정 또는 데이터 주석이 필요 하지 않습니다.

| 기능 | 기능 |
|----------|----------|
| Form Recognizer | <li>PDF, PNG 및 JPG 파일을 처리 합니다.<li>최소 5 가지 형식의 동일한 레이아웃을 사용 하 여 사용자 지정 모델 학습 <li>키-값 쌍 및 테이블 정보를 추출 합니다. <li>Azure Cognitive Services Computer Vision API 텍스트 인식 기능을 사용 하 여 양식 내 이미지에서 인쇄 된 텍스트를 검색 하 고 추출 합니다.<li>주석이 나 레이블 지정이 필요 하지 않습니다. |

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

양식 인식기 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

| 필수 | 목적 |
|----------|---------|
| Docker 엔진 | [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br> |
| Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념을 기본적으로 이해 하 고 기본 명령에 대 한 지식이 있어야 합니다 `docker` . |
| Azure CLI | 호스트에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 를 설치 합니다. |
| Computer Vision API 리소스 | 스캔 한 문서와 이미지를 처리 하려면 Computer Vision 리소스가 필요 합니다. 텍스트 인식 기능에 Azure 리소스 (REST API 또는 SDK) 또는 인식 *서비스 인식-텍스트* [컨테이너](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)중 하나로 액세스할 수 있습니다. 일반 청구 요금이 적용됩니다. <br><br>Computer Vision 리소스 (Azure 클라우드 또는 Cognitive Services 컨테이너)에 대 한 API 키와 끝점을 모두 전달 합니다. 이 API 키와 끝점을 **{COMPUTER_VISION_API_KEY}** 및 **{COMPUTER_VISION_ENDPOINT_URI}** 로 사용 합니다.<br><br> 인식 *서비스-인식-텍스트* 컨테이너를 사용 하는 경우 다음을 확인 합니다.<br><br>양식 인식기 컨테이너의 Computer Vision 키는 인식 `docker run` *서비스 인식-텍스트* 컨테이너의 Computer Vision 명령에 지정 된 키입니다.<br>청구 끝점은 컨테이너의 끝점 (예: `http://localhost:5000` )입니다. 동일한 호스트에서 Computer Vision 컨테이너와 폼 인식기 컨테이너를 함께 사용 하는 경우 기본 포트인 *5000*를 사용 하 여 시작할 수 없습니다. |
| Form Recognizer 리소스 | 이러한 컨테이너를 사용 하려면 다음이 있어야 합니다.<br><br>연결 된 API 키와 끝점 URI를 가져오는 Azure **양식 인식기** 리소스입니다. 두 값은 모두 Azure Portal **폼 인식기** 개요 및 키 페이지에서 사용할 수 있으며 두 값 모두 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{FORM_RECOGNIZER_API_KEY}**: 키 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: 개요 페이지에 제공 된 끝점입니다. |

> [!NOTE]
> Computer Vision 리소스 이름은 하이픈 `-` 또는 기타 특수 문자가 없는 단일 단어 여야 합니다. 이 제한은 양식 인식기와 텍스트 인식 컨테이너 호환성을 보장 하기 위해 마련 되었습니다.

## <a name="gathering-required-parameters"></a>필수 매개 변수 수집

필요한 모든 Cognitive Services의 컨테이너에 대 한 세 가지 기본 매개 변수가 필요 합니다. 최종 사용자 사용권 계약 (EULA)은 값과 함께 제공 되어야 합니다 `accept` . 또한 끝점 URL과 API 키가 모두 필요 합니다.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>끝점 URI `{COMPUTER_VISION_ENDPOINT_URI}` 및 `{FORM_RECOGNIZER_ENDPOINT_URI}`

**끝점** URI 값은 해당 하는 인지 서비스 리소스의 Azure Portal *개요* 페이지에서 사용할 수 있습니다. *개요* 페이지로 이동 하 고 끝점을 마우스로 가리키면 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘이 표시 됩니다. 필요에 따라 복사 하 여 사용 합니다.

![나중에 사용할 끝점 uri 수집](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>키 `{COMPUTER_VISION_API_KEY}` 및 `{FORM_RECOGNIZER_API_KEY}`

이 키는 컨테이너를 시작 하는 데 사용 되며 해당 하는 인지 서비스 리소스의 Azure Portal 키 페이지에서 사용할 수 있습니다. *키* 페이지로 이동 하 여 아이콘을 클릭 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 합니다.

![나중에 사용 하기 위해 두 키 중 하나를 가져옵니다.](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> 이러한 구독 키는 인식 서비스 API에 액세스 하는 데 사용 됩니다. 키를 공유 하지 마세요. 예를 들어 Azure Key Vault를 사용 하 여 안전 하 게 저장 합니다. 또한 이러한 키를 정기적으로 다시 생성 하는 것이 좋습니다. API 호출을 수행 하는 데는 키가 하나만 필요 합니다. 첫 번째 키를 다시 생성 하는 경우 두 번째 키를 사용 하 여 서비스에 계속 액세스할 수 있습니다.

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

각 폼 인식기 컨테이너에 대해 할당할 최소 및 권장 CPU 코어와 메모리는 다음 표에 설명 되어 있습니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Form Recognizer | 2개 코어, 4GB 메모리 | 4개 코어, 8GB 메모리 |
| 텍스트 인식 | 1 코어, 8gb 메모리 | 2 코어, 8gb 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!Note]
> 최소 및 권장 값은 호스트 컴퓨터 리소스가 *아니라* Docker 제한을 기반으로 합니다.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>docker pull 명령을 사용 하 여 컨테이너 이미지 가져오기

**양식 인식기** 와 **텍스트 인식** 제공 모두에 대 한 컨테이너 이미지는 다음 컨테이너 레지스트리에서 사용할 수 있습니다.

| 컨테이너 | 정규화 된 이미지 이름 |
|-----------|------------|
| Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 텍스트 인식 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

두 컨테이너가 모두 필요 합니다. **인식기 텍스트** 컨테이너는 [이 문서 외부에 자세히 설명](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) 되어 있습니다.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Form Recognizer 컨테이너에 대한 docker pull

#### <a name="form-recognizer"></a>Form Recognizer

양식 인식기 컨테이너를 가져오려면 다음 명령을 사용 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>텍스트 인식 컨테이너용 docker pull

#### <a name="recognize-text"></a>텍스트 인식

텍스트 인식 컨테이너를 가져오려면 다음 명령을 사용 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너를 [호스트 컴퓨터](#the-host-computer)에 설치한 후 다음 프로세스를 사용 하 여 컨테이너 작업을 수행 합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-by-using-the-docker-run-command)합니다. `docker run` 명령의 자세한 [예제](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다.

## <a name="run-the-container-by-using-the-docker-run-command"></a>Docker run 명령을 사용 하 여 컨테이너를 실행 합니다.

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. , 및 값을 가져오는 방법에 대 한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters) 을 참조 하세요 `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` `{FORM_RECOGNIZER_API_KEY}` .

`docker run`명령의 [예](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.

### <a name="form-recognizer"></a>Form Recognizer

> [!NOTE]
> `--mount`이러한 예제에서 사용 되는 디렉터리는 Windows 디렉터리 경로입니다. Linux 또는 macOS를 사용 하는 경우 사용자 환경에 대 한 매개 변수를 변경 합니다. 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 폼 인식기 컨테이너를 실행 합니다.
* 2 개의 CPU 코어와 8gb의 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.
* 컨테이너에/input 및/output 볼륨을 탑재 합니다.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>별도의 docker run 명령을 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스트 되는 폼 인식기와 텍스트 인식기 조합의 경우 다음 두 가지 Docker CLI 명령 예제를 사용 합니다.

포트 5000에서 첫 번째 컨테이너를 실행합니다. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

포트 5001에서 두 번째 컨테이너를 실행합니다.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
이후의 각 컨테이너는 다른 포트에 있어야 합니다. 

### <a name="run-separate-containers-with-docker-compose"></a>Docker Compose를 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스트 되는 폼 인식기와 텍스트 인식기 조합에 대해서는 다음 예제 Docker Compose YAML 파일을 참조 하세요. `{COMPUTER_VISION_API_KEY}` Text Recognizer는 `formrecognizer` 및 `ocr` 컨테이너 모두에서 동일해야 합니다. `{COMPUTER_VISION_ENDPOINT_URI}` `ocr` `formrecognizer` 컨테이너는 이름 및 포트를 사용 하기 때문에는 컨테이너 에서만 사용 됩니다 `ocr` . 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> `Eula`컨테이너를 `Billing` 실행 하려면, 및과 `ApiKey` `FormRecognizer:ComputerVisionApiKey` 및 옵션을 `FormRecognizer:ComputerVisionEndpointUri` 지정 해야 합니다. 그렇지 않으면 컨테이너가 시작 되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

|컨테이너|엔드포인트|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 [폼 인식기 서비스 SDK 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)를 통해 액세스 하는 websocket 기반 쿼리 끝점 api를 제공 합니다.

기본적으로 Form Recognizer SDK는 온라인 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다. 아래 예제를 참조하세요.

#### <a name="for-c"></a>C#의 경우

다음 Azure 클라우드 초기화 호출을

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
컨테이너 끝점을 사용 하는이 호출의 경우:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python의 경우

다음 Azure 클라우드 초기화 호출을

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

컨테이너 끝점을 사용 하는이 호출의 경우:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 REST 끝점 Api를 제공 합니다 .이 Api는 [폼 인식기 api](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) 페이지에서 찾을 수 있습니다.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](form-recognizer-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

양식 인식기 컨테이너는 Azure 계정의 _양식 인식기_ 리소스를 사용 하 여 azure에 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](form-recognizer-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Form Recognizer 컨테이너의 다운로드, 설치 및 실행에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Form Recognizer는 하나의 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 Azure의 프라이빗 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 REST SDK를 사용 하 여 폼 인식기 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정 해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정에 대 한 [컨테이너 구성](form-recognizer-container-configuration.md) 을 검토 합니다.
* 더 많은 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)를 사용 합니다.
