---
title: 게이트웨이를 사용하여 modbus 프로토콜 변환 - Azure IoT Edge | Microsoft Docs
description: Modbus TCP를 사용하는 디바이스에서 IoT Edge 게이트웨이 디바이스를 만들어 Azure IoT Hub와 통신할 수 있도록 합니다.
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248046"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>IoT Edge 디바이스 게이트웨이를 통해 Modbus TCP 디바이스 연결

Modbus TCP 또는 RTU 프로토콜을 사용하는 IoT 디바이스를 Azure IoT Hub에 연결하려면 IoT Edge 디바이스를 게이트웨이로 사용합니다. 게이트웨이 디바이스는 Modbus 디바이스에서 데이터를 읽은 다음 지원되는 프로토콜을 사용하여 해당 데이터를 클라우드에 전달합니다.

![Modbus 장치 IoT Edge 게이트웨이 통해 IoT Hub에 연결합니다.](./media/deploy-modbus-gateway/diagram.png)

이 문서에서는 Modbus 모듈에 대한 사용자 고유의 컨테이너 이미지를 만드는 방법(또는 미리 빌드된 샘플을 사용할 수 있음) 및 게이트웨이로 작동할 IoT Edge 디바이스에 이 이미지를 배포하는 방법에 대해 설명합니다.

이 문서에서는 Modbus TCP 프로토콜을 사용한다고 가정합니다. Modbus RTU를 지원하도록 모듈을 구성하는 방법에 대한 자세한 내용은 GitHub의 [Azure IoT Edge Modbus 모듈](https://github.com/Azure/iot-edge-modbus) 프로젝트를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure IoT Edge 디바이스 참조 하나를 설정 하는 방법에 대 한 연습은 [Windows에 Azure IoT Edge 배포](quickstart.md) 하거나 [Linux](quickstart-linux.md)합니다.
* IoT Edge 디바이스에 대한 기본 키 연결 문자열입니다.
* Modbus TCP를 지원하는 물리적 또는 시뮬레이션된 Modbus 디바이스

## <a name="prepare-a-modbus-container"></a>Modbus 컨테이너 준비

Modbus 게이트웨이 기능을 테스트하려는 경우 사용할 수 있는 샘플 모듈은 Microsoft에 있습니다. Azure Marketplace에서 모듈에 액세스할 수 있습니다 [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), 또는 이미지 URI 사용 하 여 **mcr.microsoft.com/azureiotedge/modbus:1.0**합니다.

사용자 고유의 모듈을 만들고 환경에 맞게 사용자 지정하려는 경우 GitHub에 오픈 소스 [Azure IoT Edge Modbus 모듈](https://github.com/Azure/iot-edge-modbus) 프로젝트가 있습니다. 해당 프로젝트의 지침에 따라 사용자 고유의 컨테이너 이미지를 만듭니다. 사용자 고유의 컨테이너 이미지를 만들 경우 참조 [개발 C# Visual Studio의 모듈](how-to-visual-studio-develop-csharp-module.md) 하거나 [Visual Studio Code에서 모듈을 개발할](how-to-vs-code-develop-module.md)합니다. 이러한 문서를 새로운 모듈을 만들고 컨테이너 이미지를 레지스트리에 게시 지침을 제공 합니다.

## <a name="try-the-solution"></a>솔루션을 시도

이 섹션에서는 IoT Edge 장치에 Microsoft의 샘플 Modbus 모듈을 배포 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하여 IoT Edge 디바이스를 클릭합니다.

3. **모듈 설정**을 선택합니다.

4. Modbus 모듈을 추가합니다.

   1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.

   2. **이름** 필드에 "modbus"를 입력합니다.

   3. **이미지** 필드에 샘플 컨테이너의 이미지 URI(`mcr.microsoft.com/azureiotedge/modbus:1.0`)를 입력합니다.

   4. **사용** 상자를 선택하여 모듈 쌍의 desired 속성을 업데이트합니다.

   5. 텍스트 상자에 다음 JSON을 복사합니다. **SlaveConnection**의 값을 Modbus 디바이스의 IPv4 주소로 변경합니다.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. **저장**을 선택합니다.

5. **모듈 추가** 단계로 돌아가서 **다음**을 선택합니다.

7. **경로 지정** 단계에서 텍스트 상자에 다음 JSON을 복사합니다. 이 경로는 Modbus 모듈에서 수집된 모든 메시지를 IoT Hub로 보냅니다. 이 경로 'modbusOutput '은 Modbus 모듈 데이터를 출력 하는 데 사용 하는 끝점 및 ' upstream '은 IoT Hub에 메시지를 보내도록 IoT Edge hub에 알리는 특수 대상입니다.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. **다음**을 선택합니다.

9. **배포 검토** 단계에서 **제출**을 선택합니다.

10. 디바이스 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다. IoT Edge 런타임과 함께 실행되는 새 **modbus** 모듈이 표시됩니다.

## <a name="view-data"></a>데이터 보기
Modbus 모듈을 통해 들어오는 데이터를 봅니다.
```cmd/sh
iotedge logs modbus
```

또한 [Visual Studio Code용 Azure IoT Hub Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)(이전 이름 Azure IoT Toolkit 확장)을 사용하여 디바이스에서 보내는 원격 분석을 볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- IoT Edge 장치가 게이트웨이로 작동 하는 방법에 대 한 자세한 내용은 참조 하세요 [투명 한 게이트웨이로 작동 하는 IoT Edge 장치 만들기](./how-to-create-transparent-gateway.md)합니다.
- IoT Edge 모듈이 작동 하는 방법에 대 한 자세한 내용은 참조 하십시오 [이해 Azure IoT Edge 모듈](iot-edge-modules.md)합니다.
