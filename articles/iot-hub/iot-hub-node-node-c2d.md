---
title: Azure IoT Hub(노드)를 사용한 클라우드-장치 메시지 | Microsoft Docs
description: Node.js용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: baefd05b562d688b662bf988c7b36a0e9cd154b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441851"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>IoT Hub(노드)를 사용하여 클라우드-장치 메시지 보내기
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>소개
Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [IoT Hub 시작](quickstart-send-telemetry-node.md) 자습서에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비전하고 디바이스-클라우드 메시지를 보내는 시뮬레이션된 디바이스 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [IoT Hub 시작하기](quickstart-send-telemetry-node.md)를 토대로 작성되었습니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.
* 디바이스에서 클라우드-디바이스 메시지를 받습니다.
* 솔루션 백 엔드에서, IoT Hub에서 디바이스로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

클라우드-장치 메시지에 대 한 자세한 정보를 찾을 수 있습니다 합니다 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)합니다.

이 자습서의 끝 부분에서는 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **SimulatedDevice** - [IoT Hub 시작](quickstart-send-telemetry-node.md)에서 만든 앱의 수정된 버전으로서 IoT Hub에 연결하고 클라우드-장치 메시지를 수신합니다.

* **SendCloudToDeviceMessage**, IoT Hub를 통해 시뮬레이션된 디바이스 앱에 클라우드-디바이스 메시지를 보낸 다음 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 많은 디바이스 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 디바이스 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.
>

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial)을 만들 수 있습니다.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 [IoT Hub 시작](quickstart-send-telemetry-node.md)에서 만든 시뮬레이션된 디바이스 앱을 수정하여 IoT Hub로부터 클라우드-디바이스 메시지를 수신합니다.

1. 텍스트 편집기를 사용하여 SimulatedDevice.js 파일을 엽니다.

2. IoT Hub에서 전송된 메시지를 처리하도록 **connectCallback** 함수를 수정합니다. 이 예제에서는 디바이스가 항상 **complete** 함수를 호출하여 메시지를 처리했음을 IoT Hub에 알립니다. **connectCallback** 함수의 새 버전은 다음 코드 조각과 같이 표시됩니다.
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > 전송으로 MQTT 또는 AMQP 대신 HTTPS를 사용하는 경우 **DeviceClient** 인스턴스는 IoT Hub의 메시지를 자주(25분 미만 간격으로) 확인합니다. MQTT, AMQP 및 HTTPS 지원과 IoT Hub 제한 간의 차이점에 대 한 자세한 내용은 참조는 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)합니다.
   >

## <a name="send-a-cloud-to-device-message"></a>클라우드-장치 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이트된 디바이스 앱으로 보내는 Node.js 콘솔 앱을 만듭니다. [IoT Hub 시작](quickstart-send-telemetry-node.md) 자습서에서 추가한 디바이스의 디바이스 ID가 필요합니다. [Azure Portal](https://portal.azure.com)에서 찾을 수 있는 허브에 대한 IoT Hub 연결 문자열도 필요합니다.

1. **sendcloudtodevicemessage**라는 빈 폴더를 만듭니다. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```shell
    npm init
    ```

2. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.
   
    ```shell
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **sendcloudtodevicemessage** 폴더에 **SendCloudToDeviceMessage.js** 파일을 만듭니다.

4. **SendCloudToDeviceMessage.js** 파일 앞에 다음 `require` 문을 추가합니다.
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. **SendCloudToDeviceMessage.js** 파일에 다음 코드를 추가합니다. “{iot hub connection string}” 자리 표시자 값을 [IoT Hub 시작](quickstart-send-telemetry-node.md) 자습서에서 만든 허브에 대한 IoT Hub 연결 문자열로 바꿉니다. “{device id}” 자리 표시자 값을 [IoT Hub 시작](quickstart-send-telemetry-node.md) 자습서에서 추가한 디바이스의 디바이스 ID로 바꿉니다.
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 다음 함수를 추가하여 작업 결과를 콘솔에 출력합니다.
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 다음 함수를 추가하여 배달 피드백 메시지를 콘솔에 출력합니다.
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 다음 코드를 추가하여 디바이스에 메시지를 보내고 디바이스가 클라우드-디바이스 메시지를 승인할 때 피드백 메시지를 처리합니다.
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **SendCloudToDeviceMessage.js** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석을 보내고 클라우드-장치 메시지를 수신합니다.
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 명령 프롬프트의 **sendcloudtodevicemessage** 폴더에서 다음 명령을 실행하여 클라우드-장치 메시지를 보내고 승인 피드백을 대기합니다.
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![앱을 실행하여 클라우드-장치 명령 보내기](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.
   >

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-장치 메시지를 보내고 받는 방법을 알아보았습니다. 

IoT Hub를 사용하는 완전한 종단 간 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://azure.microsoft.com/documentation/suites/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.