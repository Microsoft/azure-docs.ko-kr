---
title: Azure Live Video Analytics를 사용하여 동작 감지 및 이벤트 내보내기
description: 이 빠른 시작에서는 프로그래밍 방식으로 직접 메서드를 호출하여 IoT Edge의 Azure Live Video Analytics를 통해 동작을 감지하고 이벤트를 내보내는 방법을 보여줍니다.
ms.topic: quickstart
ms.date: 08/10/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1119d6ab2295138b7c13ca19e36a48417e72b6cb
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379597"
---
# <a name="quickstart-detect-motion-and-emit-events-with-live-video-analytics"></a>빠른 시작: Live Video Analytics를 사용하여 동작 감지 및 이벤트 내보내기

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 시작하는 단계를 안내합니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다. 설치 단계가 완료되면 해당 스트림에서 동작을 감지하고 보고하는 미디어 그래프를 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 다음 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여 줍니다.

![동작 감지를 기반으로 하는 Live Video Analytics](./media/analyze-live-video/motion-detection.svg) 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-emit-events-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-emit-events-quickstart/python/review-sample-video.md)]
::: zone-end


## <a name="set-up-azure-resources"></a>Azure 리소스 설정

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/csharp/setup-azure-resources.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/python/setup-azure-resources.md)]
::: zone-end

## <a name="set-up-your-development-environment"></a>개발 환경 설정

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/csharp/setup-development-environment.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/python/setup-development-environment.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>샘플 파일 검사

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

::: zone pivot="programming-language-csharp"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/csharp/prepare-monitor-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/python/prepare-monitor-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>결과 해석

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 시도하려면 생성된 리소스를 유지해야 합니다. 그렇지 않으면 Azure Portal에서 리소스 그룹으로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

다른 빠른 시작을 실행합니다(예: 라이브 비디오 피드에서 개체 감지).        
