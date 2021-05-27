---
title: Live Video Analytics에서 사용자 고유의 gRPC 모델을 사용하여 라이브 비디오 분석 - Azure
description: 이 빠른 시작에서는 Live Video Analytics를 통해 컴퓨터 비전을 적용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석합니다.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1079e374d34512e3054c4185302059ffd178c804
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371735"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model-with-live-video-analytics"></a>빠른 시작: Live Video Analytics에서 사용자 고유의 gRPC 모델을 사용하여 라이브 비디오 분석

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여 줍니다. 컴퓨터 비전 모델을 적용하여 개체를 감지하는 방법을 볼 수 있습니다. 라이브 비디오 피드의 프레임 하위 집합은 유추 서비스로 보냅니다. 결과는 IoT Edge Hub로 보냅니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>필수 조건

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>개요

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>미디어 그래프를 만들고 배포

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>결과 해석

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 사용하려면 여기서 만든 리소스를 그대로 유지합니다. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* gRPC 프로토콜을 사용하여 다른 미디어 그래프 토폴로지를 실행해 보세요.
* **샘플 LVA(Live Video Analytics) 확장 빌드 및 실행**
<br/>LVA(Live Video Analytics) 확장으로 [ONNX](http://onnx.ai/) 기반 YOLO 모델을 빌드하고 실행할 수 있는 Jupyter 샘플 Notebooks를 사용해 보세요.
    * [샘플 YOLOv3 모델](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [샘플 YOLOv4 모델](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)

