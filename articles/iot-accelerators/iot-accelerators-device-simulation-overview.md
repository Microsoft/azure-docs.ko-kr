---
title: 디바이스 시뮬레이션 개요 - Azure | Microsoft Docs
description: 디바이스 시뮬레이션 솔루션 가속기 및 해당 기능에 대해 설명합니다.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448493"
---
# <a name="device-simulation-solution-accelerator-overview"></a>디바이스 시뮬레이션 솔루션 가속기 개요

클라우드 기반 IoT 솔루션에서 디바이스는 온도, 위치 및 상태와 같은 원격 분석을 보내기 위해 클라우드 엔드포인트에 연결됩니다. 솔루션은 이 원격 분석을 사용하여 사용자가 작업을 수행하거나 인사이트를 도출할 수 있도록 합니다.

IoT 솔루션을 개발할 때 실험 및 테스트는 해당 프로세스의 핵심 부분입니다. 시뮬레이션은 이 프로세스에서 중요한 도구입니다. 디바이스 시뮬레이션을 사용하여 다음을 수행할 수 있습니다.

* 프로토타입을 빠르게 시작 및 실행하고 시뮬레이트된 디바이스의 동작을 즉석에서 조정하면서 반복합니다. 이 프로세스를 사용하면 고비용 하드웨어에 투자하기 전에 아이디어를 입증할 수 있습니다. 웹 UI를 통해 사용자 지정 디바이스를 만들어 수초 내에 프로토타입 디바이스를 생성할 수 있습니다.
* 실제 디바이스 동작을 시뮬레이트하여 디바이스에서 솔루션까지 솔루션이 예상대로 작동하는지 확인합니다. JavaScript를 사용하여 복잡한 디바이스 동작을 스크립팅함으로써 실제와 같은 시뮬레이트된 원격 분석을 생성할 수 있습니다.
* 표준 부하, 최대 부하, 한계 초과 부하 상태를 시뮬레이트하여 솔루션의 규모 테스트를 실시합니다. 규모 테스트도 솔루션 실행에 필요한 Azure 리소스의 적절한 크기를 파악하는 데 도움이 됩니다.

![샘플 드론 시뮬레이션](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

디바이스 시뮬레이션을 사용하면 실제 디바이스를 시뮬레이션하는 디바이스 모델을 정의할 수 있습니다. 이 모델에는 메시지 형식, 쌍 속성 및 메서드가 포함 됩니다. 또한 JavaScript를 사용하여 복잡한 디바이스 동작을 시뮬레이트할 수도 있습니다.

IoT Hub에 연결된 디바이스 한 대에서 수천 대에 대한 시뮬레이션을 실행할 수 있습니다. 테스트하는 데 도움이 되도록, 경우에 따라 독립 실행형 환경에 대한 디바이스 시뮬레이션 함께 IoT Hub를 배포할 수 있습니다.

디바이스 시뮬레이션은 무료입니다. 그러나 디바이스 시뮬레이션은 클라우드의 Azure 구독에 배포되며 Azure 리소스를 사용합니다. 디바이스 시뮬레이션이 요구 사항을 충족하지 않을 경우 [GitHub의 소스 코드](https://github.com/Azure/device-simulation-dotnet)를 복사한 후 수정할 수 있습니다.

## <a name="sample-simulations"></a>샘플 시뮬레이션

디바이스 시뮬레이션을 배포하면 몇 가지 샘플 시뮬레이션 및 샘플 디바이스가 제공됩니다. 이러한 샘플을 사용하여 디바이스 시뮬레이션 사용 방법을 배울 수 있습니다. 시작하려면 [트럭 10대를 시뮬레이트하는 샘플 시뮬레이션](quickstart-device-simulation-deploy.md)을 실행합니다. [제공된 많은 샘플 디바이스 중 하나를 사용하여 사용자 고유의 시뮬레이션을 만들](iot-accelerators-device-simulation-create-simulation.md) 수도 있습니다.

![시뮬레이션 구성](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>사용자 지정 시뮬레이트된 디바이스

디바이스 시뮬레이션을 사용하여 시뮬레이션에서 사용할 [사용자 지정 디바이스 모델을 만들](iot-accelerators-device-simulation-create-custom-device.md) 수 있습니다. 예를 들어, 온도 및 습도 원격 분석을 전송하는 새 냉장고 디바이스 모델을 정의할 수 있습니다. 사용자 지정 시뮬레이트된 디바이스는 증분 및 증감되는 임의 원격 분석 값을 갖는 간단한 디바이스 동작에 적합합니다.

![디바이스 모델 만들기](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>고급 시뮬레이트된 디바이스

디바이스에서 보내는 원격 분석 값을 보다 강력하게 제어해야 할 경우 고급 디바이스 모델을 사용할 수 있습니다. 고급 디바이스 모델을 사용하면 JavaScript 지원을 통해 전송된 원격 분석 값을 조작할 수 있습니다. 예를 들어, 화창한 더운 날에 주차된 차의 내부 온도를 시뮬레이트할 수 있습니다. 외부 온도가 높아지면 내부 온도도 기하급수적으로 높아집니다.

고급 디바이스 모델을 사용하면 JSON 디바이스 정의 파일 및 해당 JavaScript 파일로 구성되는 [사용자 고유의 디바이스 모델을 만들고 업로드](iot-accelerators-device-simulation-advanced-device.md)할 수 있습니다.

고급 디바이스 모델을 사용하여 다음을 수행할 수 있습니다.

* 원격 분석 유형과 함께 디바이스에서 전송되는 메시지 형식을 지정합니다.
* 사용자 지정 스크립팅을 사용하여 시간에 따른 디바이스의 상태를 유지 관리하는 원격 분석 값을 생성합니다.
* 사용자 지정 스크립팅을 사용하여 시뮬레이트된 디바이스가 메서드에 대응하는 방식을 지정합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 시뮬레이션 솔루션 가속기 및 해당 기능에 대해 알아보았습니다. 솔루션 가속기를 사용하려면 빠른 시작:

> [!div class="nextstepaction"]
> [Azure에서 IoT 디바이스 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md)을 계속 진행합니다.
