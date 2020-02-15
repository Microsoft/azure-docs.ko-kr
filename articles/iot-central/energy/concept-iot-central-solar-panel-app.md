---
title: Azure IoT Central의 아키텍처 개념 - 에너지 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 아키텍처와 관련된 주요 개념을 소개합니다.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018009"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - 태양 전지판 앱 아키텍처




이 문서에서는 태양 전지판 모니터링 앱 템플릿 아키텍처의 개요를 제공합니다. 아래 다이어그램은 IoT Central 플랫폼을 사용하여 Azure에서 일반적으로 사용되는 태양 전지판 앱 아키텍처를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![스마트 측정기 아키텍처](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

이 아키텍처는 다음과 같은 구성 요소로 구성됩니다. 일부 애플리케이션에는 여기에 나열된 모든 구성 요소가 필요하지 않을 수 있습니다.

## <a name="solar-panels-and-connectivity"></a>태양 전지판 및 연결 

태양 전지판은 재생 에너지의 중요한 소스 중 하나입니다. 태양 전지판 유형 및 설정에 따라 게이트웨이이나 다른 중간 디바이스 및 독점 시스템을 사용하여 연결할 수 있습니다. 직접 연결할 수 없는 디바이스를 연결하기 위해 IoT Central 디바이스 브리지를 빌드해야 할 수도 있습니다. IoT Central 디바이스 브리지는 오픈 소스 솔루션이며 [여기](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)에서 전체 세부 정보를 확인할 수 있습니다. 



## <a name="iot-central-platform"></a>IoT Central 플랫폼
Azure IoT Central은 IoT 솔루션의 빌드를 간소화하고 IoT 관리, 운영 및 개발 부담과 비용을 줄여주는 플랫폼입니다. IoT Central을 사용하면 대규모로 손쉽게 IoT(사물 인터넷) 자산을 연결, 모니터링 및 관리할 수 있습니다. 태양 전지판을 IoT Central에 연결하면 앱 템플릿에서 디바이스 모델, 명령, 대시보드 등의 기본 제공 기능을 사용합니다. 또한 앱 템플릿은 근 실시간 측정기 데이터 모니터링, 분석, 규칙 및 시각화와 같은 웜 경로 시나리오에 IoT Central 스토리지를 사용합니다.


## <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central을 사용하여 빌드하는 확장성 옵션
IoT Central 플랫폼은 두 가지 확장성 옵션인 CDE(연속 데이터 내보내기) 및 API를 제공합니다. 고객 및 파트너는 특정 요구 사항에 맞게 솔루션을 사용자 지정하기 위해 이러한 옵션 중에서 선택할 수 있습니다. 예를 들어 파트너 중 하나가 ADLS(Azure Data Lake Storage)를 사용하여 CDE를 구성했습니다. 장기 데이터 보존 및 기타 콜드 경로 스토리지 시나리오(일괄 처리, 감사 및 보고 목적)에 ADLS를 사용하고 있습니다. 

## <a name="next-steps"></a>다음 단계

* 지금까지 이 아키텍처에 대해 알아보았으므로 [무료로 태양 전지판을 만들어 봅니다](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring).
* [Azure IoT Central 개요](https://docs.microsoft.com/azure/iot-central/)를 참조하여 Azure IoT Central에 대해 자세히 알아보기