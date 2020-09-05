---
title: 에지 디바이스에서 동작을 감지하고 비디오 녹화 - Azure
description: 이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하고, 동작이 있는지 감지하고, 해당하는 경우 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여 줍니다.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e456bb5b8d6d6658158af0c58f05ab38fe4693b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682341"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>빠른 시작: 에지 디바이스에서 동작을 감지하고 비디오 녹화
 
이 빠른 시작에서는 Live Video Analytics on IoT Edge를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 동작이 있는지 감지하는 방법, 동작이 있다면 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여줍니다. 이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림도 사용합니다. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>전제 조건

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>개요

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>검토 - 모듈의 상태 확인

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>검토 - 이벤트 모니터링 준비

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>결과 해석 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>MP4 클립 재생

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 시도하려면 여기서 만든 리소스를 그대로 유지합니다. 그렇지 않으면 Azure Portal에서 리소스 그룹으로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* 라이브 비디오 피드에 AI를 적용하는 [사용자 고유의 모델로 Live Video Analytics 실행](use-your-model-quickstart.md) 빠른 시작을 실행합니다.
* 고급 사용자에 대한 추가 문제를 검토합니다.

    * RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
    * Azure에서 Linux VM을 사용하는 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
