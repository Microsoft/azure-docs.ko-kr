---
title: Azure IoT Hub 디바이스 쌍 시작(Python) | Microsoft Docs
description: Azure IoT Hub 디바이스 쌍을 사용하여 태그를 추가한 다음, IoT Hub 쿼리를 사용하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 디바이스 앱 및 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: a6210c4672042801350e56ef6c8e8a2c02420a81
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110398"
---
# <a name="get-started-with-device-twins-python"></a>디바이스 쌍 시작(Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **AddTagsAndQuery.py**는 태그를 추가하고 디바이스 쌍을 쿼리하는 Python 백 엔드 앱입니다.

* **ReportConnectivity.py**는 앞에서 만든 디바이스 ID와 IoT Hub를 연결하고 연결 상태를 보고하는 디바이스를 시뮬레이트하는 Python 앱입니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 **{장치 ID}** 와 연결 된 장치 쌍에 위치 메타 데이터를 추가 하는 Python 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 디바이스 쌍을 쿼리하여 Redmond에 있는 디바이스를 선택한 다음 셀룰러 연결을 보고하는 디바이스를 선택합니다.

1. 작업 디렉터리에서 명령 프롬프트를 열고 **Python 용 Azure IoT Hub SERVICE SDK**를 설치 합니다.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Iothub-client의 pip 패키지는 현재 Windows OS에만 사용할 수 있습니다. Linux/Mac OS의 경우 [Python 용 개발 환경 준비](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) 게시물의 linux 및 Mac OS 관련 섹션을 참조 하세요.
   >

2. 텍스트 편집기를 사용하여 새 **AddTagsAndQuery.py** 파일을 만듭니다.

3. SDK 서비스에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. 다음 코드를 추가합니다. `[IoTHub Connection String]`를 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 iot hub 연결 문자열로 바꿉니다. `[Device Id]`를 [IoT hub에서 새 장치 등록](#register-a-new-device-in-the-iot-hub)에 등록 한 장치 ID로 바꿉니다.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. 다음 코드를 **AddTagsAndQuery.py** 파일에 추가합니다.

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    **레지스트리** 개체는 서비스의 디바이스 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 코드는 먼저 **레지스트리** 개체를 초기화한 다음, **deviceId**에 대한 디바이스 쌍을 업데이트하고, 마지막으로 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 위치한 디바이스의 디바이스 쌍만을 선택하고, 두 번째는 또한 셀룰러 네트워크를 통해서 연결된 디바이스만을 선택하도록 쿼리를 구체화합니다.

6. **AddTagsAndQuery.py**의 끝부분에 다음 코드를 추가하고 **iothub_service_sample_run** 함수를 구현합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. 다음으로 애플리케이션을 실행합니다.

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    **Redmond43**에 위치한 모든 디바이스를 요청하는 쿼리에 대한 결과로는 하나의 디바이스를 보고 셀룰러 네트워크를 사용하는 디바이스에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 디바이스도 볼 수 없어야 합니다.

    ![Redmond의 모든 장치를 표시 하는 첫 번째 쿼리](./media/iot-hub-python-twin-getstarted/service-1.png)

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 디바이스 앱을 만듭니다.

## <a name="create-the-device-app"></a>디바이스 앱 만들기

이 섹션에서는 **{장치 ID}** 로 허브에 연결 하는 Python 콘솔 앱을 만든 다음 셀룰러 네트워크를 사용 하 여 연결 된 정보를 포함 하도록 장치 쌍의 보고 된 속성을 업데이트 합니다.

1. 작업 디렉터리의 명령 프롬프트에서 **Python 용 Azure IoT Hub 장치 SDK**를 설치 합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 텍스트 편집기를 사용하여 새 **ReportConnectivity.py** 파일을 만듭니다.

3. 디바이스 SDK에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. 다음 코드를 추가합니다. `[IoTHub Device Connection String]` 자리 표시자 값을 [IoT hub에서 새 장치 등록](#register-a-new-device-in-the-iot-hub)에서 복사한 장치 연결 문자열로 바꿉니다.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 다음 코드를 **ReportConnectivity.py** 파일에 추가하여 디바이스 쌍 기능을 구현합니다.

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    **Client** 개체는 서비스의 디바이스 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드에서는 **클라이언트** 개체를 초기화한 다음 디바이스에 대한 디바이스 쌍을 검색하고, 연결 정보로 reported 속성을 업데이트합니다.

6. **ReportConnectivity.py**의 끝부분에 다음 코드를 추가하고 **iothub_client_sample_run** 함수를 구현합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 디바이스 앱 실행:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    디바이스 쌍이 업데이트되었다는 확인 메시지가 표시됩니다.

    ![업데이트 쌍](./media/iot-hub-python-twin-getstarted/device-1.png)

8. 디바이스가 연결 정보를 보고했으므로 두 쿼리 모두에 나타나야 합니다. 뒤로 돌아가서 쿼리를 다시 실행합니다.

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    이번에는 **{장치 ID}가** 두 쿼리 결과에 모두 나타나야 합니다.

    ![두 번째 쿼리](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 디바이스 ID를 만들었습니다. 백 엔드 앱에서 tags로 디바이스 메타데이터를 추가하고, 디바이스 쌍에서 디바이스 연결 정보를 보고하는 시뮬레이션된 디바이스 앱을 작성했습니다. 또한 레지스트리를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작](quickstart-send-telemetry-python.md) 자습서를 참조하여 디바이스에서 원격 분석을 보냅니다.

* [Desired 속성을 사용](tutorial-device-twins.md) 하 여 장치 구성 자습서에서 장치 쌍의 desired 속성을 사용 하 여 장치를 구성 합니다.

* [직접 메서드 사용](quickstart-control-device-python.md) 자습서를 사용 하 여 대화형으로 (예: 사용자 제어 앱에서 팬 켜기) 장치를 제어 합니다.
