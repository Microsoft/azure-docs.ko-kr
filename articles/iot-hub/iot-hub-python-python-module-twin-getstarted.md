---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(Python) | Microsoft Docs
description: Python용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: f887fbd4f82e59c02d6a5b69d0d5b43b426a39bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441224"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Python 백 엔드 및 Python 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 애플리케이션에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.
>

이 자습서가 끝나면 다음과 같은 두 개의 Python 앱이 생깁니다.

* **CreateIdentities**: 디바이스 ID, 모듈 ID 및 관련 보안 키를 만들어 디바이스 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**: 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
>

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* IoT Hub

* 최신 [Python SDK](https://github.com/Azure/azure-iot-sdk-python)를 설치합니다.

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub에서 디바이스 ID 및 모듈 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에 디바이스 ID 및 모듈 ID를 만드는 Python 앱을 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 IoT Hub에 연결할 수 없습니다. 자세한 내용은 "Id 레지스트리" 섹션을 참조 합니다 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)합니다. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

Python 파일에 다음 코드를 추가합니다.

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

이 앱은 ID가 **myFirstDevice**인 디바이스 ID와 ID가 **myFirstModule**인 모듈 ID를 **myFirstDevice** 디바이스 아래에 만듭니다. ID 레지스트리에 해당 모듈 ID가 이미 있는 경우 코드는 기존 모듈 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 IoT Hub에 연결합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 장치별 메타데이터를 저장해야 할 경우 애플리케이션별 저장소를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)를 참조하세요.
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Python 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 보고된 모듈 쌍 속성을 업데이트하는 Python 앱을 만듭니다.

1. **모듈 연결 문자열 가져오기** -이제 로그인 할 경우 합니다 [Azure portal](https://portal.azure.com/)합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **UpdateModuleTwinReportedProperties 앱 만들기**

   **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다.

## <a name="get-updates-on-the-device-side"></a>디바이스 쪽에서 업데이트

위의 코드 외에도 아래 코드 블록을 추가하여 디바이스에서 쌍 업데이트 메시지를 가져올 수 있습니다.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)