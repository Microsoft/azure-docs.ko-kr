---
title: IoT에 대한 Azure Security Center에 보안 메시지 보내기 | Microsoft Docs
description: IoT 용 Azure Security Center를 사용 하 여 보안 메시지를 보내는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963821"
---
# <a name="send-security-messages-sdk"></a>보안 메시지 보내기 SDK

이 방법 가이드에서는 IoT 에이전트 용 Azure Security Center를 사용 하지 않고 장치 보안 메시지를 수집 하 여 전송 하도록 선택할 때 IoT 서비스 기능에 대한 Azure Security Center 설명 하 고,이 작업을 수행 하는 방법을 설명 합니다.  

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * Azure IoT C SDK를 사용 하 여 보안 메시지 보내기
> * Azure IoT C# SDK를 사용 하 여 보안 메시지 보내기
> * Azure IoT Python SDK를 사용 하 여 보안 메시지 보내기
> * Azure IoT node.js SDK를 사용 하 여 보안 메시지 보내기
> * Azure IoT Java SDK를 사용 하 여 보안 메시지 보내기


## <a name="azure-security-center-for-iot-capabilities"></a>IoT 기능을 위한 Azure Security Center

IoT 용 Azure Security Center은 전송 된 데이터가 [IoT 스키마에 대한 Azure Security Center](https://aka.ms/iot-security-schemas) 를 준수 하 고 메시지가 보안 메시지로 설정 되어 있는 한 모든 종류의 보안 메시지 데이터를 처리 하 고 분석할 수 있습니다.

## <a name="security-message"></a>보안 메시지

IoT에 대한 Azure Security Center는 다음 조건을 사용 하 여 보안 메시지를 정의 합니다.
- Azure IoT SDK를 사용 하 여 메시지를 보낸 경우
- 메시지가 [보안 메시지 스키마](https://aka.ms/iot-security-schemas) 를 준수 하는 경우
- 메시지를 보내기 전에 보안 메시지로 설정 된 경우

각 보안 메시지에는 `AgentId`, `AgentVersion`, `MessageSchemaVersion`, 보안 이벤트 목록과 같은 보낸 사람의 메타 데이터가 포함 됩니다.
스키마는 이벤트 유형을 포함 하 여 보안 메시지의 유효한 속성 및 필수 속성을 정의 합니다.

>[!Note]
> 스키마를 준수하지 않은 상태로 보내는 메시지는 무시됩니다. 무시된 메시지는 현재 저장되지 않으므로 데이터 보내기를 시작하기 전에 스키마를 확인해야 합니다. 

>[!Note]
> Azure IoT SDK를 사용 하 여 보안 메시지로 설정 되지 않은 보낸 메시지는 IoT 파이프라인에 대한 Azure Security Center 라우팅되지 않습니다.

## <a name="valid-message-example"></a>유효한 메시지 예

아래 예제에서는 유효한 보안 메시지 개체를 보여 줍니다. 예제에는 메시지 메타 데이터와 하나의 `ProcessCreate` 보안 이벤트가 포함 되어 있습니다.

보안 메시지로 설정 하 고 전송 되 면이 메시지는 IoT의 Azure Security Center에 의해 처리 됩니다.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>보안 메시지 보내기 

[Azure Iot C 장치 sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), azure IOT [ C# 장치 sdk](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview),, [Azure IOT node.js Sdk](https://github.com/Azure/azure-iot-sdk-node), AZURE [iot Python sdk](https://github.com/Azure/azure-iot-sdk-python)또는 [azure iot Java sdk를](https://github.com/Azure/azure-iot-sdk-java) 사용 하 여 iot 에이전트에 대한 Azure Security Center를 사용 *하지 않고* 보안 메시지 보내기.

IoT에 대한 Azure Security Center에서 처리 하기 위해 장치에서 장치 데이터를 보내려면 다음 Api 중 하나를 사용 하 여 IoT 처리 파이프라인에 대한 Azure Security Center에 올바른 라우팅에 대한 메시지를 표시 합니다. 

올바른 헤더로 표시 된 경우에도 전송 되는 모든 데이터는 [IoT 메시지 스키마의 Azure Security Center](https://aka.ms/iot-security-schemas)준수 해야 합니다. 

### <a name="send-security-message-api"></a>보안 메시지 보내기 API 

**보안 메시지 보내기** API는 현재 C 및 C#, Python, node.js 및 Java에서 사용할 수 있습니다.  

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

Python API를 사용 하려면 [azure-iot-장치](https://pypi.org/project/azure-iot-device/)패키지를 설치 해야 합니다.

Python API를 사용 하는 경우 고유한 장치 또는 모듈 연결 문자열을 사용 하 여 모듈 또는 장치를 통해 보안 메시지를 보낼 수 있습니다. 다음 Python 스크립트 예제를 사용 하는 경우 장치에서 **IoTHubDeviceClient**을 사용 하 고, 모듈에 **IoTHubModuleClient**을 사용 합니다. 

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대한 Azure Security Center에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- [경고](concept-security-alerts.md)를 살펴봅니다.
