---
title: Azure IoT Hub(Python)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: Python용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110426"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub(Python)를 사용하여 클라우드-디바이스 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 iot hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-python.md) 에서는 iot hub를 만들고, 장치 id를 프로 비전 하 고, 장치-클라우드 메시지를 보내는 시뮬레이션 된 장치 앱을 코딩 하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서 [는 장치에서 IoT hub로 원격 분석 전송](quickstart-send-telemetry-python.md)을 기반으로 합니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서 IoT Hub 장치에 전송 된 메시지에 대 한 배달 승인 (*피드백*)을 요청 합니다.

클라우드-장치 메시지에 대 한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)에서 찾을 수 있습니다.

이 자습서의 끝 부분에서 다음의 두 Python 콘솔 앱을 실행합니다.

* **SimulatedDevice.py**- [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-python.md)에서 만든 앱의 수정 된 버전으로, iot hub에 연결 하 고 클라우드-장치 메시지를 수신 합니다.

* **SendCloudToDeviceMessage.py**-IoT Hub를 통해 시뮬레이션 된 장치 앱에 클라우드-장치 메시지를 보낸 다음 배달 승인을 수신 합니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 디바이스를 시뮬레이션하고 IoT Hub에서 클라우드-디바이스 메시지를 수신하는 Python 콘솔 앱을 만듭니다.

1. 텍스트 편집기를 사용하여 **SimulatedDevice.py** 파일을 만듭니다.

2. `import`SimulatedDevice.py**파일의 시작 부분에 다음** 문 및 변수를 추가합니다.

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. 다음 코드를 **SimulatedDevice.py** 파일에 추가합니다. "{DeviceConnectionString}" 자리 표시자 값을 [장치에서 IoT hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-python.md) 에서 만든 장치에 대 한 장치 연결 문자열로 바꿉니다.

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 다음 함수를 추가하여 수신한 메시지를 콘솔에 인쇄합니다.

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. 다음 코드를 추가하여 클라이언트를 초기화하고 클라우드-디바이스 메시지를 수신하기 위해 대기합니다.

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. 다음 main 함수를 추가합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. **SimulatedDevice.py** 파일을 저장하고 닫습니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

이 문서에서는 [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-python.md)에서 만든 iot hub를 통해 클라우드-장치 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-장치 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub은이 사용 권한을 부여 하는 **서비스** 라는 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이션된 디바이스 앱으로 보내는 Python 콘솔 응용 프로그램을 만듭니다. [장치에서 IoT hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-python.md) 에서 추가한 장치의 장치 ID가 필요 합니다. 이전에 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 iot hub 연결 문자열도 필요 합니다.

1. 텍스트 편집기를 사용하여 **SendCloudToDeviceMessage.py** 파일을 만듭니다.

2. `import`SendCloudToDeviceMessage.py**파일 앞에 다음** 문 및 변수를 추가합니다.

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. **SendCloudToDeviceMessage.py** 파일에 다음 코드를 추가합니다. "{Iot hub 연결 문자열}" 및 "{장치 id}" 자리 표시자 값을 앞에서 적어둔 IoT hub 연결 문자열 및 장치 ID로 바꿉니다.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. 다음 함수를 추가하여 피드백 메시지를 콘솔에 출력합니다.

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. 다음 코드를 추가하여 디바이스에 메시지를 보내고 디바이스가 클라우드-디바이스 메시지를 승인할 때 피드백 메시지를 처리합니다.

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. 다음 main 함수를 추가합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. **SendCloudToDeviceMessage.py** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 명령 프롬프트를 열고 **Python용 Azure IoT Hub 디바이스 SDK**를 설치합니다.

    ```shell
    pip install azure-iothub-device-client
    ```

2. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-디바이스 메시지를 수신합니다.

    ```shell
    python SimulatedDevice.py
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 새 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다.

    ```shell
    pip install azure-iothub-service-client
    ```

4. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-디바이스 메시지를 보내고 메시지 피드백을 대기합니다.

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![앱을 실행하여 클라우드-디바이스 명령 보내기](./media/iot-hub-python-python-c2d/send-command.png)

5. 디바이스에서 수신한 메시지를 기록해 둡니다.

    ![수신된 메시지](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://azure.microsoft.com/documentation/suites/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
