---
title: 컨테이너 설치 및 실행-Text Analytics
titleSuffix: Azure Cognitive Services
description: 이 단계별 자습서에서 Text Analytics용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 6e05dc2136211bcd15a9f0583358b05ccbf96f5a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383155"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics 컨테이너 설치 및 실행

컨테이너를 사용 하면 사용자 환경에서 텍스트 분석 Api를 실행할 수 있으며 특정 보안 및 데이터 관리 요구 사항에 적합 합니다. Text Analytics 컨테이너는 원시 텍스트에 대 한 고급 자연어 처리를 제공 하며, 감정 분석, 핵심 구 추출 및 언어 감지 라는 세 가지 주요 함수를 포함 합니다. 현재 컨테이너에 엔터티 연결이 지원되지 않습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>전제 조건

Text Analytics 컨테이너를 실행 하려면 호스트 컴퓨터와 컨테이너 환경이 있어야 합니다.

## <a name="preparation"></a>준비

Text Analytics 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Text Analytics 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결 된 API 키와 엔드포인트 URI를 가져오는 Azure [Text Analytics 리소스](../../cognitive-services-apis-create-account.md) 입니다. 두 값 모두 Azure Portal의 Text Analytics 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공 된 엔드포인트입니다.|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Text Analytics 컨테이너에 할당할 CPU 코어(2.6GHz 이상) 수 및 메모리(GB) 양에 대한 최소 및 권장 크기에 대해 설명합니다.

# <a name="key-phrase-extractiontabkeyphrase"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[언어 감지](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

Text Analytics에 대 한 컨테이너 이미지는 Microsoft Container Registry에서 사용할 수 있습니다.

# <a name="key-phrase-extractiontabkeyphrase"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[언어 감지](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Text Analytics 컨테이너에 대 한 Docker 풀

# <a name="key-phrase-extractiontabkeyphrase"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[언어 감지](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](../text-analytics-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대 한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters) 을 참조 하세요.

`docker run` 명령의 [예](../text-analytics-resource-container-config.md#example-docker-run-commands) 를 사용할 수 있습니다.

# <a name="key-phrase-extractiontabkeyphrase"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[언어 감지](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트, `http://localhost:5000`을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](../text-analytics-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>청구

Text Analytics 컨테이너는 Azure 계정의 _Text Analytics_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](../text-analytics-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Text Analytics 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Text Analytics는 Docker에 대 한 세 가지 Linux 컨테이너를 제공 하 여 다양 한 기능을 캡슐화 합니다.
   * *핵심 구 추출*
   * *언어 감지*
   * *감정 분석*
* 컨테이너 이미지는 Azure의 MCR(Microsoft Container Registry)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Text Analytics 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](../text-analytics-resource-container-config.md)에서 구성 설정을 검토합니다.
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)를 참조하여 기능과 관련된 문제를 해결합니다.
