---
title: 시뮬레이션된 Raspberry Pi-클라우드(Node.js) - Azure IoT Hub에 Raspberry Pi 웹 시뮬레이터 연결 | Microsoft Docs
description: Raspberry Pi가 Azure 클라우드에 데이터를 보내도록 Raspberry Pi 웹 시뮬레이터를 Azure IoT Hub에 연결합니다.
author: wesmc7777
manager: philmea
keywords: raspberry pi 시뮬레이터, azure iot raspberry pi, raspberry pi iot hub, raspberry pi에서 클라우드로 데이터 전송, raspberry pi-클라우드
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 42c2c0d1a015baf4b846c86ed22e8383e21028b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442354"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Azure IoT Hub에 Raspberry Pi 온라인 시뮬레이터 연결(Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 Raspberry Pi 온라인 시뮬레이터 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](about-iot-hub.md)를 사용하여 Pi 시뮬레이터를 클라우드에 원활하게 연결하는 방법을 알아봅니다.

물리적 디바이스가 있는 경우 시작하려면 [Azure IoT Hub에 Raspberry Pi 연결](iot-hub-raspberry-pi-kit-node-get-started.md)을 방문하세요.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>수행할 작업

* Raspberry Pi 온라인 시뮬레이터의 기본 사항을 알아봅니다.

* IoT Hub를 만듭니다.

* IoT Hub에 Pi용 디바이스를 등록합니다.

* Pi에서 샘플 애플리케이션을 실행하여 IoT Hub로 시뮬레이션된 센서 데이터를 보냅니다.

앞에서 만든 IoT Hub에 시뮬레이션된 Raspberry Pi를 연결합니다. 그런 다음 시뮬레이터에서 샘플 애플리케이션을 실행하여 센서 데이터를 생성합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 디바이스 연결 문자열을 가져오는 방법. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

* Raspberry Pi 온라인 시뮬레이터를 사용하는 방법

* IoT Hub로 센서 데이터를 보내는 방법.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi 웹 시뮬레이터 개요

단추를 클릭하여 Raspberry Pi 온라인 시뮬레이터를 시작합니다.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi 시뮬레이터 시작</a>

웹 시뮬레이터에는 세 가지 영역이 있습니다.

1. 어셈블리 영역 - 기본 회로에서는 Pi가 BME280 센서 및 LED에 연결됩니다. 미리 보기 버전에서는 이 영역이 잠겨 있으므로 지금은 사용자 지정을 수행할 수 없습니다.

2. 코딩 영역 - Raspberry Pi를 사용하여 코딩할 수 있는 온라인 코드 편집기입니다. 기본 샘플 애플리케이션을 사용하여 BME280 센서에서 센서 데이터를 손쉽게 수집한 후 Azure IoT Hub로 보낼 수 있습니다. 애플리케이션은 실제 Pi 장치와 완벽하게 호환됩니다. 

3. 통합 콘솔 창 - 코드의 출력을 표시합니다. 이 창의 맨 위에는 세 개의 단추가 있습니다.

   * **실행** - 코딩 영역에서 애플리케이션을 실행합니다.

   * **다시 설정** - 기본 샘플 애플리케이션으로 코딩 영역을 다시 설정합니다.

   * **접기/확장** - 오른쪽에 있는 단추를 사용하여 콘솔 창 접기/확장을 수행할 수 있습니다.

> [!NOTE]
> Raspberry Pi 웹 시뮬레이터는 현재 미리 보기 버전으로 제공됩니다. [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator)에서 사용자 의견을 듣고 싶습니다. 소스 코드는 [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator)에 공개되어 있습니다.

![Pi 온라인 시뮬레이터 개요](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT 허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Pi 웹 시뮬레이터에서 샘플 애플리케이션 실행

1. 코딩 영역에서 기본 샘플 애플리케이션으로 작업 중인지 확인합니다. 15행의 자리 표시자를 Azure IoT Hub 디바이스 연결 문자열로 대체합니다.
1. 
   ![장치 연결 문자열로 대체 합니다.](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. 선택 **실행** 종류나 `npm start` 응용 프로그램을 실행 합니다.

IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다. ![출력 - Raspberry Pi에서 IoT Hub로 전송된 센서 데이터](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>허브에서 수신한 메시지 읽기

시뮬레이트된 장치에서 IoT hub에서 수신한 메시지를 모니터링 하는 한 가지 방법은 Visual Studio Code 용 Azure IoT 도구를 사용 하는 것입니다. 자세한 내용은 참조 하세요 [for Visual Studio Code 장치 및 IoT Hub 간에 메시지를 받고 보내는 데 사용 하 여 Azure IoT 도구](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)합니다.

장치에서 보낸 데이터를 처리 하는 방법을 더 보려면, 다음 섹션을 진행 합니다.

## <a name="next-steps"></a>다음 단계

샘플 애플리케이션을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
