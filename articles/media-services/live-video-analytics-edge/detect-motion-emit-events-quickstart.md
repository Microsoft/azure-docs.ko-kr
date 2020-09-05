---
title: 동작 감지 및 이벤트 내보내기 - Azure
description: 이 빠른 시작에서는 프로그래밍 방식으로 직접 메서드를 호출하여 Live Video Analytics on IoT Edge를 사용하여 동작을 감지하고 이벤트를 내보내는 방법을 보여 줍니다.
ms.topic: quickstart
ms.date: 08/10/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 45595e8e6aa53b41d6d8b0d9d11457468dc47013
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691037"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>빠른 시작: 동작 감지 및 이벤트 내보내기

이 빠른 시작에서는 Live Video Analytics on IoT Edge를 시작하는 단계를 안내합니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다. 설치 단계가 완료되면 해당 스트림에서 동작을 감지하고 보고하는 미디어 그래프를 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 다음 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여 줍니다.

![동작 감지를 기반으로 하는 Live Video Analytics](./media/analyze-live-video/motion-detection.png) 

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
