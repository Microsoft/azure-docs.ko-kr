---
title: Azure IoT Edge용 모듈 개발 | Microsoft Docs
description: 런타임 및 IoT Hub과 통신할 수 있는 Azure IoT Edge용 사용자 지정 모듈을 개발합니다.
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 02/25/2019
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ea2d1dbcf991ce858f07905bc1bb41809f9bcdd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322451"
---
# <a name="develop-your-own-iot-edge-modules"></a>사용자 고유의 IoT Edge 모듈 개발

Azure IoT Edge 모듈은 다른 Azure 서비스와 연결하고 더 큰 클라우드 데이터 파이프라인에 속할 수 있습니다. 이 문서에서는 IoT Edge 런타임 및 IoT Hub, 나머지 Azure 클라우드와 통신하기 위한 모듈을 개발하는 방법을 설명합니다. 

## <a name="iot-edge-runtime-environment"></a>IoT Edge 런타임 환경
IoT Edge 런타임은 여러 IoT Edge 모듈의 기능을 통합하고 이를 IoT Edge 디바이스에 배포하기 위한 인프라를 제공합니다. 상위 수준에서 모든 프로그램을 IoT Edge 모듈로 패키지할 수 있습니다. 그러나 IoT Edge 통신 및 관리 기능을 최대한 활용하기 위해 모듈에서 실행되는 프로그램이 IoT Edge 런타임에 통합된 로컬 IoT Edge 허브에 연결할 수 있습니다.

## <a name="using-the-iot-edge-hub"></a>IoT Edge 허브 사용
IoT Edge 허브는 IoT Hub에 프록시 및 로컬 통신이라는 두 가지 주요 기능을 제공합니다.

### <a name="iot-hub-primitives"></a>IoT Hub 기본 형식
IoT Hub는 다음과 같은 점에서 디바이스와 비슷하게 모듈 인스턴스를 표시합니다.

* [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 및 해당 디바이스의 다른 모듈 쌍과 별개이며 격리된 모듈 쌍이 있습니다.
* [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messaging.md)를 보낼 수 있습니다.
* 특별히 해당 ID에서 대상으로 지정된 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 받을 수 있습니다.

현재 모듈은 클라우드-장치 메시지를 받거나 파일 업로드 기능을 사용할 수 없습니다.

모듈을 작성할 때 [Azure IoT 장치 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 IoT Edge 허브에 연결하고, 장치 애플리케이션과 함께 IoT Hub를 사용할 때와 동일한 방식으로 위의 기능을 사용할 수 있습니다. 유일한 차이점은 애플리케이션 백 엔드에서 장치 ID 대신 모듈 ID를 참조해야 한다는 것입니다.

### <a name="device-to-cloud-messages"></a>디바이스-클라우드 메시지
IoT Edge 허브는 디바이스-클라우드 메시지의 복잡한 처리를 사용할 수 있게 하기 위해 모듈 간 메시지 및 모듈과 IoT Hub 간 메시지의 선언적 라우팅을 제공합니다. 선언적 라우팅을 사용하면 모듈은 다른 모듈에서 보낸 메시지를 가로채고, 처리하고, 복잡한 파이프라인에 전파할 수 있습니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로 설정](module-composition.md)을 참조하세요.

IoT Edge 모듈은 일반적인 IoT Hub 디바이스 애플리케이션과 반대로 처리를 위해 로컬 IoT Edge 허브에서 프록시하는 디바이스-클라우드 메시지를 받을 수 있습니다.

IoT Edge 허브는 [배포 매니페스트](module-composition.md)에서 설명하는 선언적 경로에 따라 메시지를 모듈에 전파합니다. IoT Edge 모듈을 개발할 경우 메시지 처리기를 설정하여 이러한 메시지를 받을 수 있습니다.

IoT Edge는 경로 만들기를 간소화하기 위해 모듈 *입력* 및 *출력* 엔드포인트의 개념을 추가합니다. 모듈은 입력을 지정하지 않고 라우팅되는 모든 디바이스-클라우드 메시지를 받을 수 있으며, 출력을 지정하지 않고 디바이스-클라우드 메시지를 보낼 수 있습니다. 그러나 명시적 입력 및 출력을 사용하면 라우팅 규칙을 더 쉽게 이해할 수 있습니다. 

마지막으로 Edge 허브에서 처리하는 디바이스-클라우드 메시지는 다음 시스템 속성으로 스탬프 처리됩니다.

| 자산 | 설명 |
| -------- | ----------- |
| $connectionDeviceId | 메시지를 보낸 클라이언트의 디바이스 ID입니다. |
| $connectionModuleId | 메시지를 보낸 모듈의 모듈 ID입니다. |
| $inputName | 이 메시지를 받은 입력입니다. 비어 있을 수 있습니다. |
| $outputName | 메시지를 보내는 데 사용된 출력입니다. 비어 있을 수 있습니다. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>모듈에서 IoT Edge 허브에 연결
모듈에서 로컬 IoT Edge 허브로의 연결은 두 단계로 이루어집니다. 
1. 애플리케이션에서 ModuleClient 인스턴스를 만듭니다.
2. 애플리케이션이 해당 디바이스의 IoT Edge 허브에서 제공한 인증서를 허용하는지 확인합니다.

DeviceClient 인스턴스가 IoT Hub에 IoT 디바이스를 연결하는 방법과 비슷하게 디바이스에서 실행 중인 IoT Edge 허브에 모듈을 연결하는 ModuleClient 인스턴스를 만듭니다. ModuleClient 클래스 및 해당 통신 메서드에 대한 자세한 내용은 원하는 SDK 언어에 대한 API 참조를 참조하세요. (예: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C 및 Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) 또는 [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).)


## <a name="next-steps"></a>다음 단계

[IoT Edge에 대 한 개발 및 테스트 환경 준비](development-environment.md)

[개발에 Visual Studio 2017을 사용 하 여 C# IoT Edge 용 모듈](how-to-visual-studio-develop-csharp-module.md)

[Visual Studio 코드를 사용 하 여 IoT Edge 용 모듈 개발](how-to-vs-code-develop-module.md)

