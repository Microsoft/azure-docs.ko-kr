---
title: Azure MQTT 클라이언트 라이브러리를 사용하여 MQTT 서버에 메시지 보내기 | Microsoft Docs
description: DevKit를 클라이언트로 사용하여 MQTT 서버에 메시지 보내기
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369349"
---
# <a name="send-messages-to-an-mqtt-server"></a>MQTT 서버에 메시지 보내기

IoT(사물 인터넷) 시스템은 종종 간헐적이거나, 품질이 낮거나, 속도가 느린 인터넷 연결을 처리합니다. MQTT는 이러한 과제를 고려하여 개발된 M2M(컴퓨터 간) 연결 프로토콜입니다. 

여기에 사용되는 MQTT 클라이언트 라이브러리는 여러 전송 수단을 통해 MQTT를 사용하기 위한 API를 제공하는 [Eclipse Paho](https://www.eclipse.org/paho/) 프로젝트의 일부입니다.

## <a name="what-you-learn"></a>학습 내용

이 프로젝트에서는 다음에 대해 알아봅니다.
- MQTT 클라이언트 라이브러리를 사용하여 MQTT broker에 메시지를 보내는 방법
- MXChip Iot DevKit를 MQTT 클라이언트로 구성하는 방법.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 완료하여:

* DevKit을 Wi-Fi에 연결
* 개발 환경 준비

## <a name="open-the-project-folder"></a>프로젝트 폴더 열기

1. DevKit가 이미 컴퓨터에 연결되어 있으면 해당 연결을 끊습니다.

2. VS Code를 시작합니다.

3. DevKit를 컴퓨터에 연결합니다.

## <a name="open-the-mqttclient-sample"></a>MQTTClient 샘플 열기

왼쪽의 **ARDUINO 예제** 섹션을 펼치고, **MXCHIP AZ3166 예제 > MQTT**로 차례로 이동하고, **MQTTClient**를 선택합니다. 프로젝트 폴더가 있는 새 VS Code 창이 열립니다.

> [!NOTE]
> 명령 팔레트에서 예제를 열 수도 있습니다. `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음, **Arduino: 예제**를 찾아서 선택합니다.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>DevKit에 Arduino 스케치 빌드 및 업로드

`Ctrl+P`(macOS: `Cmd+P`)를 입력하여 `task device-upload`를 실행합니다. 업로드가 완료되면 DevKit가 다시 시작되고 스케치가 실행됩니다.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> "오류: AZ3166: 알 수 없는 패키지" 오류 메시지가 표시될 수 있습니다. 보드 패키지 인덱스가 올바르게 새로 고쳐지지 않으면 이 오류가 발생합니다. 이 오류를 해결하려면 [IoT DevKit FAQ의 개발 섹션](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)을 참조하세요.

## <a name="test-the-project"></a>프로젝트 테스트

VS Code에서 다음 절차에 따라 직렬 모니터를 열고 설정합니다.

1. 상태 표시줄에서 `COM[X]` 단어를 클릭하여 오른쪽의 COM 포트를 `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)로 설정합니다.

2. 상태 표시줄의 전원 플러그 아이콘을 클릭하여 ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg) 직렬 모니터를 엽니다.
  
3. 상태 표시줄에서 전송 속도를 나타내는 숫자를 클릭하고 `115200`으로 설정합니다. ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

직렬 모니터는 샘플 스케치에서 보낸 메시지를 모두 표시합니다. 이 스케치는 DevKit를 Wi-Fi에 연결합니다. Wi-Fi 연결이 성공적으로 완료되면 스케치에서 MQTT broker에 메시지를 전송합니다. 그런 다음, 샘플에서 QoS 0과 QoS 1을 각각 사용하여 두 개의 "iot.eclipse.org" 메시지를 반복적으로 보냅니다.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 채널을 사용하여 문의하세요.

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>참고 항목

* [클라우드의 Azure IoT Hub에 IoT DevKit AZ3166 연결](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [흔들기, 흔들어서 트윗 찾기](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>다음 단계

이제 MXChip Iot DevKit를 MQTT 클라이언트로 구성하고, MQTT 클라이언트 라이브러리를 사용하여 메시지를 MQTT broker에 보내는 방법을 알아보았으므로 여기서 제안되는 단계는 다음과 같습니다.

* [Azure IoT 원격 모니터링 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)
* [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 장치 연결](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
