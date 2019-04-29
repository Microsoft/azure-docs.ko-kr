---
title: Azure IoT Hub(Python)를 사용한 클라우드-장치 메시지 | Microsoft Docs
description: Python용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441406"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub(Python)를 사용하여 클라우드-장치 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>소개
Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. 합니다 [IoT Hub 시작](quickstart-send-telemetry-python.md) 빠른 시작에서는 IoT hub를 만들고 그 안에 장치 id를 프로 비전, 장치-클라우드 메시지를 보내는 시뮬레이션 된 장치 앱을 코딩 하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [IoT Hub 시작하기](quickstart-send-telemetry-python.md)를 토대로 작성되었습니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서, IoT Hub에서 디바이스로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

클라우드-장치 메시지에 대 한 자세한 정보를 찾을 수 있습니다 합니다 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)합니다.

이 자습서의 끝 부분에서 다음의 두 Python 콘솔 앱을 실행합니다.

* **SimulatedDevice.py** - [IoT Hub 시작](quickstart-send-telemetry-python.md)에서 만든 앱의 수정된 버전으로서 IoT Hub에 연결하고 클라우드-장치 메시지를 수신합니다.

* **SendCloudToDeviceMessage.py** - IoT Hub를 통해 시뮬레이션된 디바이스 앱에 클라우드-디바이스 메시지를 보낸 다음 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 많은 디바이스 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 디바이스 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://www.azure.com/develop/iot)를 참조하세요.
>

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x](https://www.python.org/downloads/)합니다. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [*pip* Python 패키지 관리 시스템을 설치 또는 업그레이드](https://pip.pypa.io/en/stable/installing/)해야 할 수 있습니다.

* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 [Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/confirmation.aspx?id=48145)가 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

> [!NOTE]
> `azure-iothub-service-client` 및 `azure-iothub-device-client`에 대한 *pip* 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS를 참조 하십시오에서 Linux 및 Mac OS에 해당 섹션에는 [Python 용 개발 환경 준비](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) 게시 합니다.
>

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 디바이스를 시뮬레이션하고 IoT Hub에서 클라우드-디바이스 메시지를 수신하는 Python 콘솔 앱을 만듭니다.

1. 텍스트 편집기를 사용하여 **SimulatedDevice.py** 파일을 만듭니다.

2. **SimulatedDevice.py** 파일 앞에 다음 `import` 문 및 변수를 추가합니다.

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. 다음 코드를 **SimulatedDevice.py** 파일에 추가합니다. 만든 장치에 대 한 장치 연결 문자열을 사용 하 여 "{deviceConnectionString}" 자리 표시자 값을 대체 합니다 [IoT Hub 시작](quickstart-send-telemetry-python.md) 빠른 시작:

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 다음 함수를 추가하여 수신한 메시지를 콘솔에 인쇄합니다.

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. 다음 코드를 추가하여 클라이언트를 초기화하고 클라우드-디바이스 메시지를 수신하기 위해 대기합니다.

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. 다음 main 함수를 추가합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. **SimulatedDevice.py** 파일을 저장하고 닫습니다.

## <a name="send-a-cloud-to-device-message"></a>클라우드-장치 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이션된 디바이스 앱으로 보내는 Python 콘솔 응용 프로그램을 만듭니다. 추가한 장치의 장치 ID가 필요 합니다 [IoT Hub 시작](quickstart-send-telemetry-python.md) 빠른 시작 합니다. [Azure Portal](https://portal.azure.com)에서 찾을 수 있는 허브에 대한 IoT Hub 연결 문자열도 필요합니다.

1. 텍스트 편집기를 사용하여 **SendCloudToDeviceMessage.py** 파일을 만듭니다.

2. **SendCloudToDeviceMessage.py** 파일 앞에 다음 `import` 문 및 변수를 추가합니다.

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

3. **SendCloudToDeviceMessage.py** 파일에 다음 코드를 추가합니다. 만든 허브에 대 한 IoT Hub 연결 문자열을 사용 하 여 "{IoTHubConnectionString}" 자리 표시자 값을 대체 합니다 [IoT Hub 시작](quickstart-send-telemetry-python.md) 빠른 시작 합니다. "{DeviceId}" 자리 표시자에서 추가한 장치의 장치 ID를 대체 합니다 [IoT Hub 시작](quickstart-send-telemetry-python.md) 빠른 시작:

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

    ```
    pip install azure-iothub-device-client
    ```

2. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-장치 메시지를 수신합니다.

    ```shell
    python SimulatedDevice.py
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 새 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK**를 설치합니다.

    ```
    pip install azure-iothub-service-client
    ```

4. 명령 프롬프트에서 다음 명령을 실행하여 클라우드-장치 메시지를 보내고 메시지 피드백을 대기합니다.

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![앱을 실행하여 클라우드-장치 명령 보내기](./media/iot-hub-python-python-c2d/send-command.png)

5. 디바이스에서 수신한 메시지를 기록해 둡니다.

    ![수신된 메시지](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-장치 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 종단 간 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://azure.microsoft.com/documentation/suites/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
