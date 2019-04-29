---
title: Azure IoT Central 디바이스 브리지 빌드 | Microsoft Docs
description: 다른 IoT 클라우드(Sigfox, Particle, The Things Network 등)를 사용자의 IoT Central 앱에 연결하는 IoT Central 디바이스 브리지를 빌드합니다.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 246c9ad8ab3083c1b847c1c25230a7193a8192e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886875"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>다른 IoT 클라우드를 IoT Central에 연결하는 IoT Central 디바이스 브리지 빌드

*이 항목의 내용은 관리자에게 적용됩니다.*

IoT Central 디바이스 브리지는 Sigfox, Particle, The Things Network 및 다른 클라우드를 사용자의 IoT Central 앱에 연결하는 오픈 소스 솔루션입니다. Sigfox의 저전력 장거리 네트워크에 연결된 자산 추적 디바이스를 사용하거나 Particle Device Cloud에서 대기질 모니터링 디바이스를 사용하거나 TTN에서 토양 수분 모니터링 디바이스를 사용하는 경우 IoT Central 디바이스 브리지를 사용하여 직접 IoT Central의 기능을 활용할 수 있습니다. 디바이스 브리지는 디바이스가 보내는 데이터를 IoT Central 앱을 통해 다른 클라우드로 전달하여 다른 IoT 클라우드를 IoT Central과 연결합니다. IoT Central 앱에서 해당 데이터에 대해 규칙을 빌드하고 분석을 실행하며, Microsoft Flow 및 Azure Logic Apps에서 워크플로를 만들고, 해당 데이터를 내보내는 등의 작업을 수행할 수 있습니다. GitHub에서 [IoT Central 디바이스 브리지](https://aka.ms/iotcentralgithubdevicebridge) 가져오기

## <a name="what-is-it-and-how-does-it-work"></a>정의 및 작동 방식
IoT Central 디바이스 브리지는 GitHub의 오픈 소스 솔루션입니다. Azure 구독에 여러 Azure 리소스를 사용하여 사용자 지정 Azure Resource Manager 템플릿을 배포하는 “Azure에 배포” 단추를 사용하도록 준비가 완료된 것입니다. 리소스에는 다음이 포함됩니다.
-   Azure Function 앱
-   Azure Storage 계정
-   소비 계획
-   Azure Key Vault

함수 앱이 장치 연결의 중요 한 부분입니다. 간단한 웹후크 통합을 통해 다른 IoT 플랫폼 또는 사용자 지정 플랫폼에서 HTTP POST 요청을 받습니다. Sigfox, Particle 및 TTN 클라우드에 연결하는 방법을 보여주는 예제가 나와 있습니다. 플랫폼에서 함수 앱에 HTTP POST 요청을 보낼 수 있는 경우 사용자 지정 IoT 클라우드에 연결하도록 이 솔루션을 손쉽게 확장할 수 있습니다.
함수 앱은 IoT Central에서 허용되는 형식으로 데이터를 변환하고 DPS API를 통해 전달합니다.

![Azure Functions 스크린샷](media/howto-build-iotc-device-bridge/azfunctions.png)

IoT Central 앱에서 디바이스를 전달된 메시지에 있는 디바이스 ID로 인식하는 경우 해당 디바이스에 대해 새 측정값이 표시됩니다. 디바이스 ID가 IoT Central 앱에 표시되지 않으면 함수 앱에서 해당 디바이스 ID를 사용하여 새 디바이스를 등록하려고 시도하고 IoT Central 앱에는 “연결되지 않은 디바이스”로 표시됩니다. 

## <a name="how-do-i-set-it-up"></a>설정하려면 어떻게 해야 하나요?
GitHub 리포지토리의 README 파일에 지침이 자세히 나열되어 있습니다. 

## <a name="pricing"></a>가격
Azure 리소스를 Azure 구독에서 호스트합니다. [README 파일](https://aka.ms/iotcentralgithubdevicebridge)에서 가격 책정에 대한 자세한 정보를 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
IoT Central 디바이스 브리지를 빌드하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [디바이스 관리](howto-manage-devices.md)
