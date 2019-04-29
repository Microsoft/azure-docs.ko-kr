---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(C) | Microsoft Docs
description: C용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/25/2018
ms.author: menchi
ms.openlocfilehash: 2642d956d94a0a685d6586be1a7b0446e94f1042
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327426"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-c-backend-and-c-device"></a>C 백 엔드 및 C 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 애플리케이션에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.

이 자습서가 끝나면 다음과 같은 두 개의 C 앱이 생성됩니다.

* **CreateIdentities**: 디바이스 ID, 모듈 ID 및 관련 보안 키를 만들어 디바이스 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**: 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. (계정이 없는 경우 몇 분 안에 [Azure 체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.)
* IoT Hub
* 최신 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub에서 디바이스 ID 및 모듈 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에 디바이스 ID 및 모듈 ID를 만드는 C 앱을 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)의 **ID 레지스트리** 섹션을 참조하세요. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

C 파일에 다음 코드를 추가합니다.

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"

#include "iothub_service_client_auth.h"
#include "iothub_registrymanager.h"

static const char* hubConnectionString ="[your hub's connection string]"; // modify

static void createDevice(IOTHUB_REGISTRYMANAGER_HANDLE 
  iotHubRegistryManagerHandle, const char* deviceId)
{
    IOTHUB_REGISTRY_DEVICE_CREATE_EX deviceCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;
    
    (void)memset(&deviceCreateInfo, 0, sizeof(deviceCreateInfo));
    deviceCreateInfo.version = 1;
    deviceCreateInfo.deviceId = deviceId;
    deviceCreateInfo.primaryKey = "";
    deviceCreateInfo.secondaryKey = "";
    deviceCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_DEVICE_EX deviceInfoEx;
    memset(&deviceInfoEx, 0, sizeof(deviceInfoEx));
    deviceInfoEx.version = 1;
    
    // Create device
    result = IoTHubRegistryManager_CreateDevice_Ex(iotHubRegistryManagerHandle, 
      &deviceCreateInfo, &deviceInfoEx);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device has been created successfully: deviceId=%s, primaryKey=%s\n", deviceInfoEx.deviceId, deviceInfoEx.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice failed\n");
    }
    // You will need to Free the returned device information after it was created
    IoTHubRegistryManager_FreeDeviceExMembers(&deviceInfoEx);
}

static void createModule(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId, const char* moduleId)
{
    IOTHUB_REGISTRY_MODULE_CREATE moduleCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;    
    
    (void)memset(&moduleCreateInfo, 0, sizeof(moduleCreateInfo));
    moduleCreateInfo.version = 1;
    moduleCreateInfo.deviceId = deviceId;
    moduleCreateInfo.moduleId = moduleId;
    moduleCreateInfo.primaryKey = "";
    moduleCreateInfo.secondaryKey = "";
    moduleCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_MODULE moduleInfo;
    memset(&moduleInfo, 0, sizeof(moduleInfo));
    moduleInfo.version = 1;
    
    // Create module
    result = IoTHubRegistryManager_CreateModule(iotHubRegistryManagerHandle, &moduleCreateInfo, &moduleInfo);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module has been created successfully: deviceId=%s, moduleId=%s, primaryKey=%s\n", moduleInfo.deviceId, moduleInfo.moduleId, moduleInfo.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateModule failed\n");
    }
    // You will need to Free the returned module information after it was created
    IoTHubRegistryManager_FreeModuleMembers(&moduleInfo);
}

int main(void)
{
    (void)platform_init();

    const char* deviceId = "myFirstDevice";
    const char* moduleId = "myFirstModule";
    IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
    IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle = NULL;

    if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(hubConnectionString)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else if ((iotHubRegistryManagerHandle = IoTHubRegistryManager_Create(iotHubServiceClientHandle)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else
    {
        createDevice(iotHubRegistryManagerHandle, deviceId);
        createModule(iotHubRegistryManagerHandle, deviceId, moduleId);
    }

    if (iotHubRegistryManagerHandle != NULL)
    {
        (void)printf("Calling IoTHubRegistryManager_Destroy...\n");
        IoTHubRegistryManager_Destroy(iotHubRegistryManagerHandle);
    }

    if (iotHubServiceClientHandle != NULL)
    {
        (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
        IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
    }
    
    platform_deinit();
    return 0;
}
```

이 앱은 ID가 **myFirstDevice**인 디바이스 ID와 ID가 **myFirstModule**인 모듈 ID를 **myFirstDevice** 디바이스 아래에 만듭니다. ID 레지스트리에 해당 모듈 ID가 이미 있는 경우 코드는 기존 모듈 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 IoT Hub에 연결합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 장치별 메타데이터를 저장해야 할 경우 애플리케이션별 저장소를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)를 참조하세요.

## <a name="update-the-module-twin-using-c-device-sdk"></a>C 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 보고된 모듈 쌍 속성을 업데이트하는 C 앱을 만듭니다.

1. **모듈 연결 문자열 가져오기** - 이제 [Azure Portal](https://portal.azure.com)에 로그인합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

    ![Azure Portal 모듈 세부 정보](./media/iot-hub-c-c-module-twin-getstarted/module-detail.png)

2. **UpdateModuleTwinReportedProperties 앱 만들기** - **Program.cs** 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```C
    #include <stdio.h>
    #include <stdlib.h>

    #include "azure_c_shared_utility/crt_abstractions.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"

    #include "iothub_service_client_auth.h"
    #include "iothub_devicetwin.h"

    const char* deviceId = "bugbash-test-2";
    const char* moduleId = "module-id-1";
    static const char* hubConnectionString ="[your hub's connection string]"; // modify
    const char* testJson = "{\"properties\":{\"desired\":{\"integer_property\": b-1234, \"string_property\": \"abcd\"}}}";

    int main(void)
    {
        (void)platform_init();

        IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
        IOTHUB_SERVICE_CLIENT_DEVICE_TWIN_HANDLE iothubDeviceTwinHandle = NULL;

        if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(moduleConnectionString)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else if ((iothubDeviceTwinHandle = IoTHubDeviceTwin_Create(iotHubServiceClientHandle)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else
        {
            char *result = IoTHubDeviceTwin_UpdateModuleTwin(iothubDeviceTwinHandle, deviceId, moduleId, testJson);
            printf("IoTHubDeviceTwin_UpdateModuleTwin returned %s\n", result);
        }

        if (iothubDeviceTwinHandle != NULL)
        {
            (void)printf("Calling IoTHubDeviceTwin_Destroy...\n");
            IoTHubDeviceTwin_Destroy(iothubDeviceTwinHandle);
        }

        if (iotHubServiceClientHandle != NULL)
        {
            (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
            IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
        }
        
        platform_deinit();
        return 0;
    }
    ```

이 코드 샘플에서는 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여줍니다. 

## <a name="get-updates-on-the-device-side"></a>디바이스 쪽에서 업데이트

위의 코드 외에도 아래 코드 블록을 추가하여 디바이스에서 쌍 업데이트 메시지를 가져올 수 있습니다.

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/macro_utils.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "iothub_module_client_ll.h"
#include "iothub_client_options.h"
#include "iothub_message.h"

// The protocol you wish to use should be uncommented
//
//#define SAMPLE_MQTT
//#define SAMPLE_MQTT_OVER_WEBSOCKETS
#define SAMPLE_AMQP
//#define SAMPLE_AMQP_OVER_WEBSOCKETS
//#define SAMPLE_HTTP

#ifdef SAMPLE_MQTT
    #include "iothubtransportmqtt.h"
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    #include "iothubtransportmqtt_websockets.h"
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    #include "iothubtransportamqp.h"
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    #include "iothubtransportamqp_websockets.h"
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    #include "iothubtransporthttp.h"
#endif // SAMPLE_HTTP

/* Paste in the your iothub connection string  */
static const char* connectionString = "[Fill in connection string]";

static bool g_continueRunning;
#define DOWORK_LOOP_NUM     3

static void deviceTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
{
    (void)userContextCallback;

    printf("Device Twin update received (state=%s, size=%zu): %s\r\n", 
        ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
}

static void reportedStateCallback(int status_code, void* userContextCallback)
{
    (void)userContextCallback;
    printf("Device Twin reported properties update completed with result: %d\r\n", status_code);

    g_continueRunning = false;
}

void iothub_module_client_sample_device_twin_run(void)
{
    IOTHUB_CLIENT_TRANSPORT_PROVIDER protocol;
    IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle;
    g_continueRunning = true;

    // Select the Protocol to use with the connection
#ifdef SAMPLE_MQTT
    protocol = MQTT_Protocol;
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    protocol = MQTT_WebSocket_Protocol;
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    protocol = AMQP_Protocol;
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    protocol = AMQP_Protocol_over_WebSocketsTls;
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    protocol = HTTP_Protocol;
#endif // SAMPLE_HTTP

    if (platform_init() != 0)
    {
        (void)printf("Failed to initialize the platform.\r\n");
    }
    else
    {
        if ((iotHubModuleClientHandle = IoTHubModuleClient_LL_CreateFromConnectionString(connectionString, protocol)) == NULL)
        {
            (void)printf("ERROR: iotHubModuleClientHandle is NULL!\r\n");
        }
        else
        {
            bool traceOn = true;
            const char* reportedState = "{ 'device_property': 'new_value'}";
            size_t reportedStateSize = strlen(reportedState);

            (void)IoTHubModuleClient_LL_SetOption(iotHubModuleClientHandle, OPTION_LOG_TRACE, &traceOn);

            // Check the return of all API calls when developing your solution. Return checks ommited for sample simplification.

            (void)IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, deviceTwinCallback, iotHubModuleClientHandle);
            (void)IoTHubModuleClient_LL_SendReportedState(iotHubModuleClientHandle, (const unsigned char*)reportedState, reportedStateSize, reportedStateCallback, iotHubModuleClientHandle);

            do
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            } while (g_continueRunning);

            for (size_t index = 0; index < DOWORK_LOOP_NUM; index++)
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            }

            IoTHubModuleClient_LL_Destroy(iotHubModuleClientHandle);
        }
        platform_deinit();
    }
}

int main(void)
{
    iothub_module_client_sample_device_twin_run();
    return 0;
}
```

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)
* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)
