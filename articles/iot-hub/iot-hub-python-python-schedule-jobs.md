---
title: Azure IoT Hub(Python)를 사용하여 작업 예약 | Microsoft Docs
description: 여러 디바이스에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 디바이스 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442803"
---
# <a name="schedule-and-broadcast-jobs-python"></a>작업 예약 및 브로드캐스트(Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub는 백 엔드 앱에서 수백만 개의 디바이스를 예약 및 업데이트하는 작업을 만들고 추적할 수 있게 하는 완전히 관리되는 서비스입니다.  작업(job)은 다음과 같은 작업(action)에 사용될 수 있습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

개념적으로 작업(job)은 이러한 작업(action) 중 하나를 래핑하고 디바이스 쌍 쿼리로 정의된 디바이스 집합에 대해 실행 진행 상태를 추적합니다.  예를 들어 백 엔드 앱은 작업을 사용하여 디바이스 쌍 쿼리로 지정되고 향후에 예약된 10,000개 디바이스에서 다시 부팅 메서드를 호출할 수 있습니다.  그런 다음 애플리케이션은 해당하는 각 디바이스에서 재부팅 메서드를 수신 및 실행함에 따라 진행 상태를 추적할 수 있습니다.

이러한 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* 디바이스 쌍 및 속성: [장치 쌍 시작](iot-hub-python-twin-getstarted.md) 고 [자습서: 장치 쌍 속성 사용 방법](tutorial-device-twins.md)

* 직접 메서드: [IoT Hub 개발자 가이드-직접 메서드](iot-hub-devguide-direct-methods.md) 고 [자습서: 직접 메서드](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 솔루션 백 엔드에서 **LockDoor**를 호출할 수 있는 직접 메서드가 포함된 Python 시뮬레이션된 디바이스 앱을 만듭니다.

* 작업을 사용하여 시뮬레이션된 디바이스 앱에서 **LockDoor** 직접 메서드를 호출하고, 디바이스 작업을 사용하여 desired 속성을 업데이트하는 Python 콘솔 앱을 만듭니다.

이 자습서가 끝나면 다음과 같은 두 개의 Python 앱이 생깁니다.

**simDevice.py** - 디바이스 ID로 IoT Hub에 연결하고 **LockDoor** 직접 메서드를 수신합니다.

**scheduleJobService.py**는 시뮬레이션된 디바이스 앱에서 직접 메서드를 호출하고 작업을 사용하여 디바이스 쌍의 desired 속성을 업데이트합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Python 2.x 또는 3.x](https://www.python.org/downloads/)합니다. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [*pip* Python 패키지 관리 시스템을 설치 또는 업그레이드](https://pip.pypa.io/en/stable/installing/)해야 할 수 있습니다.

* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 [Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/confirmation.aspx?id=48145)가 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

> [!NOTE]
> **Python용 Azure IoT SDK**는 **작업** 기능을 직접 지원하지 않습니다. 이 자습서에서는 비동기 스레드 및 타이머를 사용하는 대체 솔루션을 제공합니다. 추가 업데이트는 [Python용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) 페이지에서 **Service Client SDK** 기능 목록을 참조하세요. 
>

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 클라우드에서 호출한 메서드에 응답하는 Python 콘솔 앱을 만듭니다. 이 메서드는 시뮬레이션된 **LockDoor** 메서드를 트리거합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device-client** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **simDevice.py** 파일을 만듭니다.

3. **simDevice.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다. `deviceConnectionString`을 위에서 만든 디바이스의 연결 문자열로 바꿉니다.

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 다음 함수 콜백을 추가하여 **LockDoor** 메서드를 처리합니다.

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. 디바이스 쌍 업데이트를 처리하는 다른 함수 콜백을 추가합니다.

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. 다음 코드를 추가하여 **lockDoor** 메서드에 대한 처리기를 등록합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. **simDevice.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>직접 메서드를 호출하고 디바이스 쌍의 속성을 업데이트하기 위한 작업 예약

이 섹션에서는 직접 메서드를 사용하여 디바이스에서 원격 **LockDoor**를 시작하는 Python 콘솔 앱을 만들고 디바이스 쌍의 속성을 업데이트합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-service-client** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **scheduleJobService.py** 파일을 만듭니다.

3. **scheduleJobService.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다.

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 디바이스를 조회하는 데 사용되는 다음 함수를 추가합니다.

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. 직접 메서드와 디바이스 쌍을 호출하는 작업을 실행하는 다음 메서드를 추가합니다.

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 작업을 예약하고 작업 상태를 업데이트하도록 다음 코드를 추가합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. **scheduleJobService.py** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신을 시작합니다.

    ```cmd/sh
    python simDevice.py
    ```

2. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 도어를 잠그고 쌍을 업데이트하는 작업을 트리거합니다.
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 콘솔에서 직접 메서드와 디바이스 쌍 업데이트에 대한 디바이스 응답을 확인합니다.

    ![IoT Hub Job 예제 1--장치 출력](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub 작업 2-샘플 장치 출력](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에 대한 직접 메서드를 예약하고 디바이스 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

시작 IoT Hub 및 장치 관리 패턴 같은 원격 무선 펌웨어 업데이트를 계속 하려면 참조 [펌웨어 업데이트를 수행 하는 방법을](tutorial-firmware-update.md)합니다.