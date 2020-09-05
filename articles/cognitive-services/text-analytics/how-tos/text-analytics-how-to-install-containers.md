---
title: 컨테이너 설치 및 실행-Text Analytics
titleSuffix: Azure Cognitive Services
description: 이 단계별 자습서에서 Text Analytics용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 61303a52212c4cec88dee2ccd8a1c08fb971a9b8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545661"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics 컨테이너 설치 및 실행

> [!NOTE]
> * 감정 분석 v3의 컨테이너가 이제 일반 공급 됩니다. 핵심 구 추출 및 언어 검색 컨테이너는 제어되지 않은 공개 미리 보기로 제공됩니다.
> * 엔터티 링크 및 NER는 현재 컨테이너로 사용할 수 없습니다.
> * 현재 상태 컨테이너 사용에 대한 Text Analytics에 대해서는 요금이 청구되지 않습니다.

컨테이너를 사용하면 사용자 환경에서 Text Analytic API를 실행할 수 있으며 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. Text Analytics 컨테이너는 원시 텍스트에 대 한 고급 자연어 처리를 제공 하며, 감정 분석, 핵심 구 추출 및 언어 감지 라는 세 가지 주요 함수를 포함 합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

> [!IMPORTANT]
> 무료 계정은 매월 5000 개의 트랜잭션으로 제한 되며, **무료** 및 **표준** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">가격 책정 계층만 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 컨테이너에 대해 유효 합니다. 트랜잭션 요청 요금에 대 한 자세한 내용은 [데이터 제한](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

Text Analytics 컨테이너를 실행 하려면 호스트 컴퓨터와 컨테이너 환경이 있어야 합니다.

## <a name="preparation"></a>준비

Text Analytics 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Text Analytics 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결 된 API 키와 끝점 URI를 가져오는 Azure [Text Analytics 리소스](../../cognitive-services-apis-create-account.md) 입니다. 두 값 모두 Azure Portal의 Text Analytics 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공 된 끝점입니다.|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 Text Analytics 컨테이너의 최소 및 권장 사양을 설명 합니다. 최소 2gb의 메모리가 필요 하며 각 CPU 코어는 2.6 GHz 이상 이상 이어야 합니다. 노드당 허용 되는 트랜잭션 (TPS)도 나열 됩니다.

|  | 최소 호스트 사양 | 권장 호스트 사양 | 최소 TPS | 최대 TPS|
|---|---------|-------------|--|--|
| **언어 검색, 핵심 문구 추출**   | 1 개 코어, 2GB 메모리 | 1 코어, 4GB 메모리 |15 | 30|
| **감정 분석 v3**   | 1 개 코어, 2GB 메모리 | 4 코어, 8GB 메모리 |15 | 30|
| **상태-1 문서/요청에 대 한 Text Analytics**   |  4 코어, 10GB 메모리 | 6 코어, 12GB 메모리 |15 | 30|
| **상태-10 개 문서/요청에 대 한 Text Analytics**   |  6 코어, 16GB 메모리 | 8 코어, 20GB 메모리 |15 | 30|

CPU 코어 및 메모리는 `--cpus` 명령의 일부로 사용 되는 및 설정에 해당 `--memory` `docker run` 합니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Text Analytics에 대 한 컨테이너 이미지는 Microsoft Container Registry에서 사용할 수 있습니다.

# <a name="sentiment-analysis-v3"></a>[감정 분석 v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[핵심 구 추출 (미리 보기)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[언어 감지 (미리 보기)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[상태 (미리 보기)에 대 한 Text Analytics](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다.
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다.

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용 하 여 컨테이너를 실행 합니다. 컨테이너는 중지 될 때까지 계속 실행 됩니다.

> [!IMPORTANT]
> * 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
> * 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.
> * 감정 분석 v3 컨테이너는 이제 일반 공급 되며, 응답에서 [감정 레이블을](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) 반환 합니다. 키 구 추출 및 언어 검색 컨테이너는 API의 v2를 사용 하 고 미리 보기 상태입니다.

# <a name="sentiment-analysis-v3"></a>[감정 분석 v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[핵심 구 추출 (미리 보기)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[언어 감지 (미리 보기)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[상태 (미리 보기)에 대 한 Text Analytics](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](../text-analytics-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Text Analytics 컨테이너는 Azure 계정의 _Text Analytics_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](../text-analytics-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Text Analytics 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Text Analytics는 Docker에 대 한 세 가지 Linux 컨테이너를 제공 하 여 다양 한 기능을 캡슐화 합니다.
   * *감정 분석*
   * *핵심 구 추출 (미리 보기)* 
   * *언어 감지 (미리 보기)*
   * *상태 (미리 보기)에 대 한 Text Analytics*
* 컨테이너 이미지는 MCR (Microsoft Container Registry) 또는 preview 컨테이너 리포지토리에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Text Analytics 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터 (예: 분석 중인 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](../text-analytics-resource-container-config.md)에서 구성 설정을 검토합니다.
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)를 참조하여 기능과 관련된 문제를 해결합니다.
