---
title: 사용자 고유의 HTTP 모델을 사용하여 라이브 비디오 분석 - Azure
description: 이 빠른 시작에서는 컴퓨터 비전을 적용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석합니다.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 5fd7f1a7db323e3aa7971c1ced6fc588d6e60e37
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684184"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>빠른 시작: 사용자 고유의 HTTP 모델을 사용하여 라이브 비디오 분석

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여 줍니다. 컴퓨터 비전 모델을 적용하여 개체를 감지하는 방법을 볼 수 있습니다. 라이브 비디오 피드의 프레임 하위 집합은 유추 서비스로 보냅니다. 결과는 IoT Edge Hub로 보냅니다. 

이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다. C#으로 작성된 샘플 코드 및 [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 기반으로 합니다. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>개요

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>미디어 그래프를 만들고 배포

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>결과 해석

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 사용하려면 여기서 만든 리소스를 그대로 유지합니다. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* [YoloV3 모델의 보안 버전](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md)을 사용해 보고 이를 IoT Edge 디바이스에 배포합니다. 

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
