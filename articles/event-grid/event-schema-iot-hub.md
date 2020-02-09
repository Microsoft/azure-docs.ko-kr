---
title: IoT Hub에 대한 Azure Event Grid 스키마 | Microsoft Docs
description: 이 아티클에서는 Azure IoT Hub 이벤트에 대한 속성 및 스키마를 제공합니다. 사용 가능한 이벤트 유형, 예제 이벤트 및 이벤트 속성을 나열 합니다.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513034"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT Hub에 대한 Azure Event Grid 이벤트 스키마

이 아티클에서는 Azure IoT Hub 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 

샘플 스크립트 및 자습서 목록은 [IoT Hub 이벤트 원본](event-sources.md#iot-hub)을 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure IoT Hub는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | Description |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 디바이스를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 디바이스를 삭제하는 경우 게시합니다. | 
| Microsoft.Devices.DeviceConnected | IoT Hub에 디바이스가 연결되는 경우 게시합니다. |
| Microsoft.Devices.DeviceDisconnected | IoT Hub와 디바이스의 연결이 해제되는 경우 게시합니다. | 
| Microsoft.Devices.DeviceTelemetry | 원격 분석 메시지를 IoT hub로 보낼 때 게시 됩니다. |

장치 원격 분석 이벤트를 제외한 모든 장치 이벤트는 일반적으로 Event Grid에서 지 원하는 모든 지역에서 사용할 수 있습니다. 장치 원격 분석 이벤트는 공개 미리 보기 상태 이며 미국 동부, 미국 서 부, 유럽 서부, [Azure Government](../azure-government/documentation-government-welcome.md), [azure 중국 21Vianet](/azure/china/china-welcome)및 [azure 독일](https://azure.microsoft.com/global-infrastructure/germany/)을 제외한 모든 지역에서 사용할 수 있습니다.

## <a name="example-event"></a>예제 이벤트

DeviceConnected 및 DeviceDisconnected 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 연결될 때 발생하는 이벤트의 스키마를 보여 줍니다.

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry 이벤트는 원격 분석 이벤트가 IoT Hub 전송 될 때 발생 합니다. 이 이벤트에 대한 샘플 스키마는 다음과 같습니다.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

DeviceCreated 및 DeviceDeleted 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 등록될 때 발생하는 이벤트의 스키마를 보여줍니다.

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>이벤트 속성

모든 이벤트에는 동일한 최상위 수준 데이터가 포함됩니다. 

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| 데이터 | object | IoT Hub 이벤트 데이터입니다.  |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

모든 IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| hubName | 문자열 | 디바이스가 만들어지거나 삭제된 IoT Hub의 이름입니다. |
| deviceId | 문자열 | 디바이스의 고유 식별자입니다. 이 대/소문자 구분 문자열은 최대 128자까지 가능하며, ASCII 7 비트 영숫자 문자 + 다음 특수 문자 `- : . + % _ # * ? ! ( ) , = @ ; $ '`을 지원합니다. |

데이터 개체의 내용은 각 이벤트 게시자에 따라 다릅니다. 

**Device Connected** 및 **Device Disconnected** IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| moduleId | 문자열 | 모듈의 고유 식별자입니다. 이 필드는 모듈 디바이스에 대해서만 출력됩니다. 이 대/소문자 구분 문자열은 최대 128자까지 가능하며, ASCII 7 비트 영숫자 문자 + 다음 특수 문자 `- : . + % _ # * ? ! ( ) , = @ ; $ '`을 지원합니다. |
| deviceConnectionStateEventInfo | object | 디바이스 연결 상태 이벤트 정보
| sequenceNumber | 문자열 | 연결된 디바이스 또는 디바이스 분리 이벤트의 순서를 나타내는 데 도움이 되는 숫자입니다. 최신 이벤트는 이전 이벤트보다 시퀀스 번호가 높습니다. 이 숫자는 1을 초과하여 변경될 수 있지만 엄격하게 증가합니다. [시퀀스 번호 사용 방법](../iot-hub/iot-hub-how-to-order-connection-state-events.md)을 참조하세요. |

**장치 원격 분석** IoT Hub 이벤트의 경우 데이터 개체는 [IoT Hub 메시지 형식](../iot-hub/iot-hub-devguide-messages-construct.md) 에 장치-클라우드 메시지를 포함 하 고 다음과 같은 속성을 포함 합니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| 본문 | 문자열 | 장치의 메시지 내용입니다. |
| properties | 문자열 | 애플리케이션 속성은 메시지에 추가할 수 있는 사용자 정의 문자열입니다. 이러한 필드는 선택 사항입니다. |
| 시스템 속성 | 문자열 | [시스템 속성](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) 은 메시지의 내용과 출처를 식별 하는 데 도움이 됩니다. 장치 원격 분석 메시지는 메시지 시스템 속성에서 contentType이 JSON으로 설정 되 고 contentEncoding이 u t f-8로 설정 된 유효한 JSON 형식 이어야 합니다. 이 설정을 지정 하지 않으면 IoT Hub는 메시지를 base 64 인코딩 형식으로 씁니다.  |

**디바이스가 생성됨** 및 **디바이스가 삭제됨** IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| 쌍 | object | 응용 프로그램 장치 메타 데이터의 클라우드 표현인 장치 쌍에 대한 정보입니다. | 
| deviceID | 문자열 | 디바이스 쌍의 고유 식별자입니다. | 
| etag | 문자열 | 디바이스 쌍에 대한 업데이트의 일관성을 확인하는 유효성 검사기입니다. 각 etag은 디바이스 쌍마다 고유합니다. |  
| deviceEtag| 문자열 | 디바이스 레지스트리에 대한 업데이트의 일관성을 확인하는 유효성 검사기입니다. 각 deviceEtag는 디바이스 레지스트리마다 고유해야 합니다. |
| 상태 | 문자열 | 디바이스 쌍의 활성화 여부를 나타냅니다. | 
| statusUpdateTime | 문자열 | 마지막 디바이스 쌍 상태 업데이트의 ISO8601 타임 스탬프입니다. |
| connectionState | 문자열 | 디바이스의 연결 여부를 나타냅니다. | 
| lastActivityTime | 문자열 | 마지막 활동의 ISO8601 타임스탬프입니다. | 
| cloudToDeviceMessageCount | integer | 이 디바이스에 전송된 클라우드-디바이스 메시지 횟수입니다. | 
| authenticationType | 문자열 | 이 디바이스에 사용되는 인증 유형은 `SAS`, `SelfSigned` 또는 `CertificateAuthority`입니다. |
| x509Thumbprint | 문자열 | 지문은 x509 인증서에 대한 고유값으로, 인증서 저장소에서 특정 인증서를 찾는 데 주로 사용됩니다. 지문은 SHA1 알고리즘을 사용하여 동적으로 생성되며, 인증서에 실제로 존재하지는 않습니다. | 
| primaryThumbprint | 문자열 | x509 인증서에 대한 주된 지문입니다. |
| secondaryThumbprint | 문자열 | x509 인증서에 대한 보조 지문입니다. | 
| 버전 | integer | 디바이스 쌍이 업데이트될 때마다 하나씩 증가하는 정수입니다. |
| 원하는 | object | 애플리케이션 백 엔드에서만 작성할 수 있고 디바이스에서 읽을 수 있는 속성의 일부입니다. | 
| 보고된 | object | 디바이스에서만 작성할 수 있고 애플리케이션 백 엔드에서 읽을 수 있는 속성의 일부입니다. |
| lastUpdated | 문자열 | 마지막 디바이스 쌍 속성 업데이트의 ISO8601 타임 스탬프입니다. | 

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* IoT Hub 및 Event Grid와 함께 작동하는 방법에 대해 알아보려면 [동작을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md)을 참조하세요.