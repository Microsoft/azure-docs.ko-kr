---
title: 컨테이너 설치 및 실행 방법 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 단계별 자습서에서 Computer Vision용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 70cbb21430253dc9683cd3803f2a09ef8bb858cb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545644"
---
# <a name="install-and-run-read-containers-preview"></a>컨테이너 읽기 (미리 보기)를 설치 하 고 실행 합니다.

컨테이너를 사용하면 사용자 환경에서 Computer Vision API를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하는 방법에 대해 알아봅니다.

단일 Docker 컨테이너 *읽기*는 Computer Vision 사용할 수 있습니다. *읽기* 컨테이너를 사용 하면 다른 표면 및 배경 (예: 수신, 포스터 및 비즈니스 카드)을 사용 하 여 다양 한 개체의 이미지에서 *인쇄 된 텍스트* 를 검색 하 고 추출할 수 있습니다. 또한 *읽기* 컨테이너는 이미지에서 *필기 텍스트* 를 검색 하 고 PDF, TIFF 및 다중 페이지 파일 지원을 제공 합니다. 자세한 내용은 [Read](concept-recognizing-text.md#read-api) API 설명서를 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Computer Vision 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Computer Vision** 리소스 및 연결 된 API 키 끝점 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공 된 끝점입니다.|

## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

[요청 양식을](https://aka.ms/cognitivegate) 작성 하 고 전송 하 여 컨테이너에 대 한 액세스를 요청 합니다. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트** 컴퓨터는 docker 컨테이너를 실행 하는 컴퓨터입니다. 호스트는 AVX2 ( [고급 벡터 확장](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) )를 *지원 해야* 합니다. 다음 명령을 사용 하 여 Linux 호스트에서 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2을 지원 하려면 호스트 컴퓨터가 *필요* 합니다. 컨테이너가 AVX2을 지원 하지 않으면 제대로 작동 *하지* 않습니다.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

읽을 컨테이너 이미지를 사용할 수 있습니다.

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|-----------|------------|
| 읽기 | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 컨테이너 이미지를 다운로드 합니다.

### <a name="docker-pull-for-the-read-container"></a>읽기 컨테이너의 Docker pull

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](computer-vision-resource-container-config.md)를 사용할 수 있습니다. 
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다. 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 읽기 컨테이너를 실행 합니다.
* 8 CPU 코어와 16gb (GB)의 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

`docker run` 명령의 자세한 [예제](./computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

### <a name="asynchronous-read"></a>비동기 읽기

및 작업을 함께 사용 하 여 `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` Computer Vision 서비스에서 해당 REST 작업을 사용 하는 방법과 유사한 방식으로 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 `operationId` HTTP GET 요청에 대 한 식별자로 사용 되는를 반환 합니다.

Swagger UI에서을 선택 하 여 `asyncBatchAnalyze` 브라우저에서 확장 합니다. **그런 다음**  >  **파일 선택**을 선택 합니다. 이 예제에서는 다음 이미지를 사용 합니다.

![탭 및 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행 되 면 **HTTP 202** 상태 코드를 반환 합니다. 응답의 일부로 `operation-location` 요청에 대 한 결과 끝점을 포함 하는 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

는 `operation-location` 정규화 된 URL 이며 HTTP GET을 통해 액세스 됩니다. 이전 이미지에서 URL을 실행 하 여 받은 JSON 응답은 `operation-location` 다음과 같습니다.

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>동기 읽기

작업을 사용 하 여 `POST /vision/v2.0/read/core/Analyze` 이미지를 동기적으로 읽을 수 있습니다. 전체적으로 이미지를 읽으면 API는 JSON 응답을 반환 합니다. 단, 오류가 발생 하는 경우는 예외입니다. 오류가 발생 하면 다음 JSON이 반환 됩니다.

```json
{
    status: "Failed"
}
```

JSON 응답 개체에는 비동기 버전과 동일한 개체 그래프가 있습니다. JavaScript 사용자 이며 형식 안전성을 원하는 경우 다음 형식을 사용 하 여 JSON 응답을 개체로 캐스팅할 수 있습니다 `AnalyzeResult` .

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

예제 사용 사례는 <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">여기 <span class="docon docon-navigate-external x-hidden-focus"></span> 에서 TypeScript 샌드박스</a> 를 참조 하 고 **실행** 을 선택 하 여 사용 편의성을 시각화 합니다.

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./computer-vision-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Cognitive Services 컨테이너는 Azure 계정의 해당 리소스를 사용 하 여 Azure로 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Computer Vision는 Docker에 대 한 Linux 컨테이너를 제공 하며 읽기를 캡슐화 합니다.
* 컨테이너 이미지는 Azure의 "컨테이너 미리 보기" 컨테이너 레지스트리에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 SDK를 사용 하 여 읽기 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Computer Vision 개요](Home.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.
* [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
