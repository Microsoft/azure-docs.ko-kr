---
title: Azure IoT Hub 디바이스 관리 시작(Python) | Microsoft Docs
description: IoT Hub 디바이스 관리를 사용하여 원격 디바이스 재부팅을 시작하는 방법입니다. Python용 Azure IoT SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 디바이스 앱 및 직접 메서드를 호출하는 서비스 앱을 구현합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 04fc1da04d9da715acfed8ca9d26e9c325afb403
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441800"
---
# <a name="get-started-with-device-management-python"></a>디바이스 관리 시작(Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 디바이스 ID를 만듭니다.

* 디바이스를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 디바이스 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.

* IoT Hub를 통해 시뮬레이션된 디바이스 앱에서 재부팅 직접 메서드를 호출하는 Python 콘솔 앱을 만듭니다.

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **dmpatterns_getstarted_device.py** - 이전에 만든 디바이스 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

* **dmpatterns_getstarted_service.py** - 시뮬레이션된 디바이스 앱에 직접 메서드를 호출하고 응답을 표시하고 업데이트된 reported 속성을 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x](https://www.python.org/downloads/)합니다. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [*pip* Python 패키지 관리 시스템을 설치 또는 업그레이드](https://pip.pypa.io/en/stable/installing/)해야 할 수 있습니다.

* 설치를 [iot hub 장치 클라이언트 azure](https://pypi.org/project/azure-iothub-device-client/) 패키지 명령을 사용 하 여       `pip install azure-iothub-device-client`

* 설치 합니다 [iothub 서비스 클라이언트 azure](https://pypi.org/project/azure-iothub-service-client/) 패키지 명령을 사용 하 여       `pip install azure-iothub-service-client`

* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 [Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/confirmation.aspx?id=48145)가 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT 허브에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Python 콘솔 앱 만들기

* 디바이스 다시 부팅 시뮬레이션

* reported 속성을 사용하여 디바이스 및 해당 디바이스가 마지막으로 재부팅한 시간을 확인하는 디바이스 쌍 쿼리를 사용하도록 설정

1. 텍스트 편집기를 사용하여 **dmpatterns_getstarted_device.py** 파일을 만듭니다.

2. 다음 `import` 문을 **dmpatterns_getstarted_device.py** 파일의 시작 부분에 추가합니다.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. **CONNECTION_STRING** 변수 및 클라이언트 초기화를 포함한 변수를 추가합니다.  연결 문자열을 디바이스 연결 문자열로 바꿉니다.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. 디바이스에서 직접 메서드를 구현하도록 다음 함수 콜백을 추가합니다.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. 직접 메서드 수신기를 시작하고 기다립니다.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

6. **dmpatterns_getstarted_device.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 디바이스에서 원격 재부팅 트리거

이 섹션에서는 디바이스에서 직접 메서드를 사용하여 원격 다시 부팅을 시작하는 Python 콘솔 앱을 만듭니다. 앱은 디바이스 쌍 쿼리를 사용하여 해당 디바이스에 대한 마지막 다시 시작 시간을 검색합니다.

1. 텍스트 편집기를 사용하여 **dmpatterns_getstarted_service.py** 파일을 만듭니다.

2. 다음 `import` 문을 **dmpatterns_getstarted_service.py** 파일의 시작 부분에 추가합니다.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. 다음 변수 선언을 추가합니다. _IoTHubConnectionString_ 및 _deviceId_에 대한 자리 표시자 값만 바꿉니다.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. 디바이스 메서드를 호출하여 대상 디바이스를 다시 부팅한 다음, 디바이스 쌍을 쿼리하고 마지막 다시 부팅 시간을 가져오도록 다음 함수를 추가합니다.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

5. **dmpatterns_getstarted_service.py** 파일을 저장하고 닫습니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 다시 부팅 직접 메서드에 대한 수신 대기를 시작합니다.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. 다른 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 부팅을 트리거하고 디바이스 쌍을 쿼리하여 마지막 다시 부팅 시간을 찾습니다.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. 콘솔에서 직접 메서드에 대한 디바이스 응답을 확인합니다.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]