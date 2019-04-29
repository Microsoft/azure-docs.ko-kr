---
title: IoT 미리 보기에 대 한 Azure Security Center 보안 메시지 보내기 | Microsoft Docs
description: IoT 용 Azure Security Center를 사용 하 여 보안 메시지를 보내는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61352046"
---
# <a name="send-security-messages-sdk"></a>보안 메시지 보내기 SDK

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 방법 가이드를 수집 하 여 IoT 에이전트에 대 한를 ASC를 사용 하지 않고 장치 보안 메시지를 보내려면 선택 하면 IoT 서비스 기능에 대 한 Azure 보안 센터 (ASC)를 설명 하 고 작업을 수행 하는 방법에 설명 합니다.  

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * C#용 보안 메시지 보내기 API 사용
> * C용 보안 메시지 보내기 API 사용

## <a name="asc-for-iot-capabilities"></a>ASC IoT 기능에 대 한

ASC IoT에 대 한 처리를 전송 된 데이터가 준수으로 모든 종류의 보안 메시지 데이터를 분석 합니다 [ASC IoT 스키마에 대 한](https://aka.ms/iot-security-schemas) 메시지 보안 메시지로 설정 됩니다.

## <a name="security-message"></a>보안 메시지

ASC IoT에 대 한 다음 조건을 사용 하 여 보안 메시지를 정의 합니다.
- Azure IoT C를 사용 하 여 메시지를 전송한 경우 /C# SDK
- 메시지가 준수 하는 경우는 [보안 메시지 스키마](https://aka.ms/iot-security-schemas)
- 메시지를 보내기 전에 보안 메시지로 설정 된 경우

각 보안 메시지 발신자의 메타 데이터를 같은 포함 `AgentId`, `AgentVersion`, `MessageSchemaVersion` 및 보안 이벤트의 목록입니다.
스키마는 이벤트 유형을 포함 하 여 보안 메시지의 유효 하 고 필요한 속성을 정의 합니다.

[!NOTE]
> 스키마를 준수하지 않은 상태로 보내는 메시지는 무시됩니다. 무시된 메시지는 현재 저장되지 않으므로 데이터 보내기를 시작하기 전에 스키마를 확인해야 합니다. 
> Azure IoT C를 사용 하 여 보안 메시지로 설정 되지 않은 메시지 전송 /C# SDK를 IoT 파이프라인에 대 한 ASC 라우팅되지 것입니다

## <a name="valid-message-example"></a>유효한 메시지 예제

아래 예제에서는 유효한 보안 메시지 개체를 보여 줍니다. 메시지 메타 데이터 및 예제에 포함 되어 `ProcessCreate` 보안 이벤트입니다.

보안 메시지 한 번 설정 하 고 보낸이 메시지를 처리할지 ASC에서 IoT에 대 한 키를 누릅니다.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>보안 메시지 보내기 

ASC를 사용 하 여 IoT 에이전트에 대 한 사용 하지 않고 보안 메시지를 전송 합니다 [Azure IoT C# 장치 SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) 또는 [Azure IoT C 장치 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)합니다.

IoT용 ASC에서 처리하기 위해 디바이스에서 디바이스 데이터를 보내려면 다음 API 중 하나를 사용하여 IoT용 ASC 처리 파이프라인으로 올바르게 라우팅하기 위한 메시지를 표시합니다. 이 방식으로 보낸 메시지는 IoT Hub 또는 Azure Security Center의 IoT용 ASC 내에서 보안 인사이트로 처리되고 표시됩니다. 

올바른 헤더로 표시된 경우에도 보내지는 모든 데이터는 [IoT용 ASC 메시지 스키마](https://aka.ms/iot-security-schemas)를 준수해야 합니다. 

### <a name="send-security-message-api"></a>보안 메시지 보내기 API

**보안 메시지 보내기** API는 현재 C 및 C#에서 사용할 수 있습니다.  

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

## <a name="next-steps"></a>다음 단계
- IoT용 ASC 서비스 [개요](overview.md)를 참조합니다.
- IoT용 ASC 서비스 [아키텍처](architecture.md)를 자세히 알아봅니다.
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- [경고](concept-security-alerts.md)를 살펴봅니다.
