---
title: 미디어 그래프 확장이란? - Azure
description: IoT Edge의 Live Video Analytics를 사용하면 그래프 확장 노드를 통해 미디어 그래프 처리 기능을 확장할 수 있습니다.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2e1ca730a6736776425cd70b323147b58e8eacbf
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716081"
---
# <a name="media-graph-extension"></a>미디어 그래프 확장

IoT Edge의 Live Video Analytics를 사용하면 그래프 확장 노드를 통해 미디어 그래프 처리 기능을 확장할 수 있습니다. 분석 확장 플러그 인은 기존의 이미지 처리 기술 또는 컴퓨터 비전 AI 모델을 사용할 수 있습니다. 그래프 확장은 확장 프로세서 노드를 미디어 그래프에 포함하여 사용할 수 있습니다. 확장 프로세서 노드는 비디오 프레임을 구성된 엔드포인트에 릴레이하고, 확장에 대한 인터페이스로 작동합니다. 로컬 또는 원격 엔드포인트에 연결할 수 있으며, 필요한 경우 인증 및 TLS 암호화를 통해 보안을 유지할 수 있습니다. 또한 그래프 확장 프로세서 노드를 사용하면 사용자 지정 확장에 전송하기 전에 비디오 프레임을 선택적으로 크기 조정하고 인코딩할 수 있습니다.

Live Video Analytics는 다음 두 가지 종류의 미디어 그래프 확장 프로세서를 지원합니다.

* [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor)
* [gRPC 확장 프로세서](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서는 HTTP 프로토콜을 사용하는 확장성 시나리오를 가능하게 합니다. 여기서 성능 및/또는 최적 리소스 사용률은 주요 문제가 아닙니다. HTTP REST 엔드포인트를 통해 자신의 AI를 미디어 그래프에 공개할 수 있습니다. 

HTTP 확장 프로세서 노드를 사용하는 경우는 다음과 같습니다.

* 기존 HTTP 유추 시스템과의 상호 운용성을 더 효율적으로 향상시키려고 합니다.
* 저성능 데이터 전송이 허용됩니다.
* Live Video Analytics에 대한 간단한 요청-응답 인터페이스를 사용하려고 합니다.

## <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

gRPC 확장 프로세서는 고성능 기준에 맞는 gRPC 기반 구조적 프로토콜을 사용하는 확장성 시나리오를 가능하게 합니다. 성능 및/또는 최적 리소스 사용률이 중요하게 고려되는 시나리오에 적합합니다. gRPC 확장 프로세서를 사용하면 정형 데이터 정의의 모든 이점을 활용할 수 있습니다. gRPC는 다음을 사용하여 높은 콘텐츠 전송 성능을 제공합니다.

* [기본 공유 메모리](https://en.wikipedia.org/wiki/Shared_memory) 또는 
* 콘텐츠를 gRPC 메시지의 본문에 직접 포함 

gRPC 확장 프로세서는 유추 메시지 교환과 함께 미디어 속성을 보내는 데 사용할 수 있습니다.
따라서 gRPC 확장 프로세서 노드를 사용하는 경우는 다음과 같습니다.

* 구조화된 계약(예: 요청 및 응답에 대한 구조화된 메시지)을 사용하려고 합니다.
* 프로토콜 버퍼([protobuf](https://developers.google.com/protocol-buffers))를 통신에 대한 기본 메시지 교환 형식으로 사용하려고 합니다.
* 들어오는 요청을 구문 분석하고 올바른 구현 함수를 호출하는 사용자 지정 요청 처리기가 필요한 기존 요청-응답 모델 대신, 단일 스트림 세션에서 gRPC 서버와 통신하려고 합니다. 
* Live Video Analytics와 모듈 간에 짧은 대기 시간 및 높은 처리량의 통신이 필요합니다.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Live Video Analytics에서 유추 모델 사용

미디어 그래프 확장을 사용하면 사용자 고유의 Docker 컨테이너에 있는 ONNX, TensorFlow, PyTorch 등과 같은 사용 가능한 유추 런타임에서 원하는 유추 모델을 실행할 수 있습니다. 최상의 성능을 위해 유추 사용자 지정 확장을 Live Video Analytics 에지 모듈과 함께 배포해야 합니다. 그런 다음, 그래프 토폴로지에 포함된 HTTP 확장 프로세서 또는 gRPC 확장 프로세서를 통해 이 확장을 호출합니다. 또한 필요에 따라 [동작 탐지기 프로세서](media-graph-concept.md#motion-detection-processor) 및 [프레임 속도 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 업스트림을 미디어 확장 프로세서에 추가하여 사용자 지정 확장에 대한 호출 빈도를 제한할 수 있습니다.

아래 다이어그램에서는 개략적인 데이터 흐름을 보여 줍니다.
 
![데이터 흐름](./media/media-graph-extension/data-flow.png)

## <a name="samples"></a>샘플

Live Video Analytics를 위한 [Jupyter Notebook](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) 샘플 중 일부를 체크 아웃합니다. 이러한 Notebook에서는 다음 항목에 대한 단계별 지침을 제공합니다.

* 확장 서비스의 Docker 컨테이너 이미지를 만드는 방법
* Live Video Analytics 컨테이너와 함께 확장 서비스를 컨테이너로 배포하는 방법
* 확장 클라이언트에서 Live Video Analytics 미디어 그래프를 사용하고 이를 확장 gRPC 엔드포인트로 가리키는 방법
