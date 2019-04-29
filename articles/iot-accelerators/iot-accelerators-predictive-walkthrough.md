---
title: 예측 유지 관리 솔루션 가속기 개요 - Azure | Microsoft Docs
description: Azure IoT 예측 유지 관리 솔루션 가속기에 대한 개요입니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3387996dc0e1953eaafee9c4c61eb8faa865b654
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447539"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>예측 유지 관리 솔루션 가속기 개요

예측 유지 관리 솔루션 가속기는 오류가 발생할 가능성이 있는 경우 지점을 예측하는 비즈니스 시나리오에 대한 종단 간 솔루션입니다. 유지 관리를 최적화하는 등의 작업에 솔루션 가속기를 사전에 사용할 수 있습니다. 솔루션은 IoT Hub 및 [Azure Machine Learning][lnk-machine-learning] 작업 영역과 같은 주요 Azure IoT 솔루션 가속기 서비스를 결합합니다. 이 작업 영역에는 공용 샘플 데이터 집합에 따라 항공기 엔진의 RUL(잔여 수명)을 예측하는 모델이 포함되어 있습니다. 솔루션은 IoT 비즈니스 시나리오를 시작점으로 구현하여 고유한 특정 비즈니스 요구 사항을 충족하는 솔루션을 계획하고 구현합니다.

예측 유지 관리 솔루션 가속기 [코드는 GitHub에서 확인할 수 있습니다](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램에서는 솔루션 가속기의 논리적 구성 요소를 간략히 보여 줍니다.

![논리 아키텍처][img-architecture]

파란색 항목은 솔루션 가속기를 배포한 지역에 프로비전되는 Azure 서비스입니다. 솔루션 가속기를 배포할 수 있는 지역 목록은 [프로비전 페이지][lnk-azureiotsolutions]에 표시됩니다.

녹색 항목은 시뮬레이션된 항공기 엔진입니다. 시뮬레이션된 디바이스에 대한 자세한 내용은 [시뮬레이션된 디바이스](#simulated-devices) 섹션에서 알아볼 수 있습니다.

회색 항목은 *디바이스 관리* 기능을 구현하는 구성 요소입니다. 예측 유지 관리 솔루션 가속기의 현재 릴리스에서는 이러한 리소스를 프로비전하지 않습니다. 디바이스 관리에 대한 자세한 내용은 [솔루션 가속기 원격 모니터링][lnk-remote-monitoring]을 참조합니다.

## <a name="azure-resources"></a>Azure 리소스

Azure 포털에서 선택한 솔루션 이름을 가진 리소스 그룹으로 이동하여 프로비전된 리소스를 볼 수 있습니다.

![가속기 리소스][img-resource-group]

솔루션 가속기를 프로비전할 때 Machine Learning 작업 영역에 대한 링크가 포함된 전자 메일을 수신합니다. [Microsoft Azure IoT 솔루션 가속기][lnk-azureiotsolutions] 페이지에서 Machine Learning 작업 영역으로 이동할 수도 있습니다. 솔루션이 **준비** 상태일 때 이 페이지에 타일이 제공됩니다.

![Machine Learning 모델][img-machine-learning]

## <a name="simulated-devices"></a>시뮬레이션된 디바이스

솔루션 가속기에서 시뮬레이션된 디바이스는 항공기 엔진입니다. 솔루션은 단일 비행기에 매핑하는 2개의 엔진으로 프로비전됩니다. 각 엔진은 다음과 같은 4가지 형식의 원격 분석을 내보냅니다. 센서9, 센서11, 센서14 및 센서15는 Machine Learning 모델이 엔진의 RUL을 계산하는 데 필요한 데이터를 제공합니다. 시뮬레이션된 각 디바이스는 IoT Hub에 다음과 같은 원격 분석 메시지를 보냅니다.

*계수 주기*. 주기는 2시간에서 10시간 사이의 기간으로 완료된 비행입니다. 비행 동안 원격 분석 데이터는 30분마다 캡처됩니다.

*원격 분석*. 엔진 특성을 기록하는 4가지 센서가 있습니다. 센서9, 센서11, 센서14 및 센서15는 일반적으로 레이블이 지정됩니다. 이러한 네 개의 센서는 RUL 모델에서 유용한 결과를 얻기 위해 충분한 원격 분석을 보냅니다. 솔루션 가속기에서 사용되는 모델은 실제 엔진 센서 데이터를 포함하는 공용 데이터 집합에서 만들어집니다. 원래 데이터 집합에서 모델을 만드는 방법에 대한 자세한 내용은 [Cortana Intelligence 갤러리 예측 유지 관리 템플릿][lnk-cortana-analytics]을 참조하세요.

또한 시뮬레이션된 디바이스는 솔루션의 IoT Hub에서 보낸 다음 명령을 처리할 수 있습니다.

| 명령 | 설명 |
| --- | --- |
| StartTelemetry |시뮬레이션의 상태를 제어합니다.<br/>원격 분석을 보내는 디바이스를 시작합니다. |
| StopTelemetry |시뮬레이션의 상태를 제어합니다.<br/>원격 분석을 보내는 디바이스를 중지합니다. |

IoT Hub는 디바이스 명령 승인을 제공합니다.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics 작업

**작업: 원격 분석**은 두 가지 문을 사용하여 들어오는 디바이스 원격 분석 스트림에서 작동합니다.

* 첫 번째 문은 디바이스에서 모든 원격 분석을 선택하고 Blob Storage에 이 데이터를 보냅니다. 여기에서 데이터가 웹앱에 시각화됩니다.
* 두 번째 문은 2분 슬라이딩 창을 통해 평균 센서 값을 계산하고 이벤트 허브를 통해 **이벤트 프로세서**로 이 데이터를 보냅니다.

## <a name="event-processor"></a>이벤트 프로세서
**이벤트 프로세서 호스트**는 Azure Web Job에서 실행됩니다. **이벤트 프로세서** 는 완료된 주기의 평균 센서 값을 사용합니다. 그런 다음, 엔진의 RUL을 계산하는 학습된 모델에 해당 값을 전달합니다. API는 솔루션의 일부인 Machine Learning 작업 영역의 모델에 대한 액세스를 제공합니다.

## <a name="machine-learning"></a>Machine Learning
Machine Learning 구성 요소는 실제 항공기 엔진에서 수집된 데이터에서 파생된 모델을 사용합니다. 솔루션 타일에서 Machine Learning 작업 영역으로 이동할 수는 [azureiotsolutions.com] [ lnk-azureiotsolutions] 페이지입니다. 솔루션이 **준비** 상태일 때 타일이 제공됩니다.

Machine Learning 모델은 IoT 솔루션 가속기 서비스를 통해 수집된 원격 분석을 사용하는 방법을 보여 주는 템플릿으로 제공됩니다. Microsoft는 공개적으로 사용 가능한 데이터<sup>\[1\]</sup>을 기반으로 하는 항공기 엔진의 [회귀 모델][lnk_regression_model] 및 해당 모델을 사용하는 방법에 대한 단계별 지침을 구축했습니다.

Azure IoT 예측 유지 관리 솔루션 가속기는 이 템플릿에서 만든 회귀 모델을 사용합니다. 모델은 Azure 구독에 배포되고 자동으로 생성된 API를 통해 제공됩니다. 이 솔루션은 4개(총 100개 중)의 엔진에 대한 테스트 데이터의 하위 집합과 4개(총 21개 중)의 센서 데이터 스트림을 포함합니다. 이 데이터는 학습된 모델을 통해 정확한 결과를 제공하는 데 충분합니다.

*\[1\] A. Saxena and K. Goebel(2008). "Turbofan 엔진 성능 저하 시뮬레이션 데이터 집합", NASA Ames Prognostics Data Repository(https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*)

## <a name="next-steps"></a>다음 단계
지금까지 예측 유지 관리 솔루션 가속기의 주요 구성 요소를 살펴보았으며 이를 사용자 지정할 수 있습니다.

IoT 솔루션 가속기의 몇 가지 다른 기능을 살펴볼 수도 있습니다.

* [IoT 솔루션 가속기에 대한 질문과 대답][lnk-faq]
* [처음부터 IoT 보안을 고려][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
