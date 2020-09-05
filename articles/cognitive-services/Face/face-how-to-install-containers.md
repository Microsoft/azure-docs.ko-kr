---
title: 컨테이너 설치 및 실행-얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는이 연습 자습서에서 Face 용 컨테이너를 다운로드, 설치 및 실행 하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 766af570c959ff2c49256058dfbfffdd1021295f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548466"
---
# <a name="install-and-run-face-containers-preview"></a>Face 컨테이너 설치 및 실행 (미리 보기)

> [!IMPORTANT]
> Face 컨테이너 사용자 제한에 도달했습니다. 현재 Face 컨테이너에 대해 새로운 애플리케이션을 수락하지 않습니다.

Azure Cognitive Services Face는 이미지에서 인간 얼굴을 검색 하는 Docker 용 표준화 된 Linux 컨테이너를 제공 합니다. 또한 noses, 눈동자, 성별, 연령 및 기타 기계 예측 얼굴 기능과 같은 얼굴 랜드마크 포함 하는 특성도 식별 합니다. 검색 외에도 Face는 신뢰도 점수를 사용 하 여 동일한 이미지 또는 다른 이미지의 두 얼굴이 동일한 지 확인할 수 있습니다. 또한 face는 데이터베이스에 대해 얼굴을 비교 하 여 비슷하거나 동일한 얼굴이 이미 있는지 여부를 확인할 수 있습니다. 또한 공유 시각적 특성을 사용 하 여 유사한 얼굴을 그룹으로 구성할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Face 서비스 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| Docker 엔진이 [호스트 컴퓨터](#the-host-computer)에 설치 되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서는 Linux 컨테이너를 지원 하도록 Docker도 구성 해야 합니다.<br><br>|
|Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념을 기본적으로 이해 해야 합니다. 또한 기본 명령에 대 한 지식이 필요 `docker` 합니다.| 
|얼굴 리소스 |컨테이너를 사용 하려면 다음이 있어야 합니다.<br><br>Azure **Face** 리소스와 연결 된 API 키 및 끝점 URI입니다. 두 값은 모두 리소스의 **개요** 및 **키** 페이지에서 사용할 수 있습니다. 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공 된 끝점입니다.

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Face 서비스 컨테이너에 대해 할당할 최소 및 권장 CPU 코어 및 메모리를 설명 합니다.

| 컨테이너 | 최소 | 권장 | 초당 트랜잭션 수<br>(최소, 최대)|
|-----------|---------|-------------|--|
|Face | 1 코어, 2gb 메모리 | 1 코어, 4gb 메모리 |10, 20|

* 각 코어는 2.6 GHz 이상 이어야 합니다.
* 초당 트랜잭션 수 (TPS).

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>docker pull를 사용 하 여 컨테이너 이미지 가져오기

Face 서비스의 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>컨테이너 사용

컨테이너를 [호스트 컴퓨터](#the-host-computer)에 설치한 후 다음 프로세스를 사용 하 여 컨테이너 작업을 수행 합니다.

1. 필요한 청구 설정으로 [컨테이너를 실행](#run-the-container-with-docker-run) 합니다. `docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다. 

## <a name="run-the-container-with-docker-run"></a>Docker run을 사용 하 여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 face 컨테이너를 실행 합니다.
* CPU 코어 1 개 및 4gb 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> `Eula`컨테이너를 `Billing` `ApiKey` 실행 하려면, 및 옵션을 지정 해야 합니다. 그렇지 않으면 컨테이너가 시작 되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./face-resource-container-config.md#mount-settings) 를 사용 하 여 컨테이너를 실행 하 고 로깅을 사용 하도록 설정 하면 컨테이너는 컨테이너를 시작 하거나 실행 하는 동안 발생 하는 문제를 해결 하는 데 도움이 되는 로그 파일을 생성 합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Face 서비스 컨테이너는 Azure 계정에서 얼굴 리소스를 사용 하 여 Azure로 청구 정보를 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./face-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Face 서비스 컨테이너를 다운로드, 설치 및 실행 하는 방법에 대 한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 컨테이너 이미지는 Azure Container Registry에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 SDK 중 하나를 사용 하 여 Face 서비스 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정 해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 계량을 위해 Azure에 연결 하지 않고 실행할 수 있는 권한이 없습니다. 고객은 컨테이너를 사용 하도록 설정 하 여 계량 서비스와 요금 청구 정보를 항상 전달 해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정은 [컨테이너 구성](face-resource-container-config.md)을 참조 하세요.
* 얼굴을 감지 하 고 식별 하는 방법에 대해 자세히 알아보려면 [얼굴 개요](Overview.md)를 참조 하세요.
* 컨테이너에서 지 원하는 메서드에 대 한 자세한 내용은 [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조 하세요.
* 더 많은 Cognitive Services 컨테이너를 사용 하려면 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)를 참조 하세요.
