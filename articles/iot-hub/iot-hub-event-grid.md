---
title: Azure IoT Hub 및 Azure Event Grid | Microsoft Docs
description: IoT Hub에서 발생하는 작업을 기반으로 프로세스를 트리거하려면 Azure Event Grid를 사용합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851703"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응

Azure IoT Hub는 이벤트 알림을 다른 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합됩니다. 안전하고 안정적이며 확장성 있는 방식으로 중요한 이벤트에 대응할 수 있도록 IoT Hub 이벤트를 수신 대기 할 수 있게 비즈니스 애플리케이션을 구성합니다. 예를 들어 데이터베이스를 업데이트 하 고, 작업 티켓을 만들고, 새 IoT 장치가 IoT hub에 등록 될 때마다 전자 메일 알림을 배달 하는 응용 프로그램을 빌드합니다.

[Azure Event Grid](../event-grid/overview.md)는 게시-구독 모델을 사용하여 완전히 관리되는 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

![Azure Event Grid 아키텍처](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>지역별 가용성

Event Grid가지 원되는 지역의 IoT Hub에 대해 Event Grid 통합을 사용할 수 있습니다. 최신 지역 목록을 보려면 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

## <a name="event-types"></a>일정 유형

IoT Hub는 다음과 같은 이벤트 유형을 게시합니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 디바이스를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 디바이스를 삭제하는 경우 게시합니다. |
| Microsoft.Devices.DeviceConnected | IoT Hub에 디바이스가 연결되는 경우 게시합니다. |
| Microsoft.Devices.DeviceDisconnected | IoT Hub와 디바이스의 연결이 해제되는 경우 게시합니다. |
| Microsoft.Devices.DeviceTelemetry | 장치 원격 분석 메시지를 IoT hub로 보낼 때 게시 |

Azure Portal 또는 Azure 명령줄 인터페이스를 사용하여 각 IoT 허브에서 어떤 이벤트를 게시할지 구성할 수 있습니다. 한 예로 자습서 [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](../event-grid/publish-iot-hub-events-to-logic-apps.md)를 시도해 봅니다.

## <a name="event-schema"></a>이벤트 스키마

IoT Hub 이벤트에는 디바이스 수명 주기 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. EventType 속성이 **Microsoft.Devices**로 시작하는지 확인하면 IoT Hub 이벤트인지 식별할 수 있습니다. Event Grid 이벤트 속성을 사용하는 방법에 대한 자세한 내용은 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.

### <a name="device-connected-schema"></a>디바이스 연결됨 스키마

다음 예제는 디바이스 연결됨 이벤트의 스키마를 보여 줍니다.

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>장치 원격 분석 스키마

장치 원격 분석 메시지는 메시지 [시스템 속성](iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType이 **application/JSON** 으로 설정 되 고 Contentencoding이 **u t f-8** 로 설정 된 유효한 JSON 형식 이어야 합니다. 이러한 속성은 모두 대/소문자를 구분 하지 않습니다. 콘텐츠 인코딩이 설정 되지 않은 경우 IoT Hub는 메시지를 base 64 인코딩 형식으로 씁니다.

Event Grid으로 엔드포인트을 선택 하 여 Event Grid에 게시 되기 전에 장치 원격 분석 이벤트를 보강할 수 있습니다. 자세한 내용은 [메시지 강화 개요](iot-hub-message-enrichments-overview.md)를 참조 하세요.

다음 예제에서는 장치 원격 분석 이벤트의 스키마를 보여 줍니다.

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

### <a name="device-created-schema"></a>스키마를 생성한 디바이스

다음 예제는 디바이스가 생성된 이벤트의 스키마를 보여줍니다.

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
      "deviceEtag":"null",
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

각 속성에 대 한 자세한 설명은 [IoT Hub에 대 한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-iot-hub.md)를 참조 하세요.

## <a name="filter-events"></a>이벤트 필터링

IoT Hub 이벤트 구독은 이벤트 유형, 데이터 콘텐츠 및 주체 (장치 이름)를 기준으로 이벤트를 필터링 할 수 있습니다.

Event Grid를 사용 하 여 이벤트 유형, 주제 및 데이터 내용에 대 한 [필터링](../event-grid/event-filtering.md) 을 수행할 수 있습니다. Event Grid 구독을 만드는 동안 선택한 IoT 이벤트를 구독 하도록 선택할 수 있습니다. Event Grid의 제목 필터는 **시작 문자**(접두사) 및 **끝 문자**(접미사) 일치를 기반으로 작동합니다. 필터는 `AND` 연산자를 사용하므로 접두사와 접미사가 둘 다 일치하는 제목의 이벤트가 구독자에게 전달됩니다.

IoT 이벤트의 제목은 다음 형식을 사용합니다.

```json
devices/{deviceId}
```

또한 Event Grid를 사용 하 여 데이터 콘텐츠를 포함 하 여 각 이벤트의 특성에 대 한 필터링을 수행할 수 있습니다. 그러면 원격 분석 메시지의 내용에 따라 전달 되는 이벤트를 선택할 수 있습니다. 예제를 보려면 [고급 필터링](../event-grid/event-filtering.md#advanced-filtering) 을 참조 하세요. 원격 분석 메시지 본문에 대 한 필터링을 위해 메시지 [시스템 속성](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)에서 contentType을 **application/Json** 으로, Contentencoding을 **u t f-8** 로 설정 해야 합니다. 이러한 속성은 모두 대/소문자를 구분 하지 않습니다.

DeviceConnected, DeviceDisconnected, DeviceCreated 및 Devicedisconnected와 같은 비 원격 분석 이벤트의 경우 구독을 만들 때 Event Grid 필터링을 사용할 수 있습니다. 원격 분석 이벤트의 경우, Event Grid 필터링 외에도 사용자는 메시지 라우팅 쿼리를 통해 장치 쌍, 메시지 속성 및 본문을 기준으로 필터링 할 수 있습니다. 

Event Grid를 통해 원격 분석 이벤트를 구독할 때 IoT Hub는 기본 메시지 경로를 만들어 데이터 원본 유형 장치 메시지를 Event Grid 보냅니다. 메시지 라우팅에 대 한 자세한 내용은 [IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 참조 하세요. 이 경로는 포털의 IoT Hub > 메시지 라우팅에서 볼 수 있습니다. 원격 분석 이벤트에 대해 생성 된 예 구독 수에 관계 없이 Event Grid 하나의 경로만 생성 됩니다. 따라서 다른 필터를 사용 하는 구독이 여러 개 필요한 경우에는 동일한 경로에 대 한 이러한 쿼리에서 또는 연산자를 사용할 수 있습니다. 경로 생성 및 삭제는 Event Grid를 통한 원격 분석 이벤트의 구독을 통해 제어 됩니다. IoT Hub 메시지 라우팅을 사용 하 여 Event Grid에 대 한 경로를 만들거나 삭제할 수 없습니다.

원격 분석 데이터를 보내기 전에 메시지를 필터링 하기 위해 [라우팅 쿼리](iot-hub-devguide-routing-query-syntax.md)를 업데이트할 수 있습니다. 본문이 JSON 인 경우에만 메시지 본문에 라우팅 쿼리를 적용할 수 있습니다. 또한 메시지 [시스템 속성](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)에서 contentType을 **application/Json** 으로, Contentencoding을 **u t f-8** 로 설정 해야 합니다.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>디바이스 연결됨 및 디바이스 연결 끊김 이벤트에 대한 제한

장치 연결 상태 이벤트를 수신 하려면 장치에서 Iot Hub를 사용 하 여 ' ' C2D Receive Message ' 작업 또는 '이 원격 분석 전송 ' 작업을 수행 해야 합니다. 그러나 장치에서 AMQP 프로토콜을 사용 하 여 Iot Hub에 연결 하는 경우에는 ' C2D Receive Message ' 작업을 수행 하는 것이 좋습니다. 그렇지 않으면 몇 분 동안 연결 상태 알림이 지연 될 수 있습니다. 디바이스가 MQTT 프로토콜을 사용하는 경우, IoT Hub는 C2D 링크를 열어둡니다. AMQP의 경우 [수신 비동기 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), IoT Hub C# SDK 또는 [amqp 용 장치 클라이언트](iot-hub-amqp-support.md#device-client)를 호출 하 여 C2D 링크를 열 수 있습니다.

원격 분석 데이터를 보내는 경우 D2C 링크가 열려 있습니다. 

장치 연결이 깜박이는 경우 (장치에서 연결 하 고 연결이 자주 끊어진 경우) 모든 단일 연결 상태를 전송 하지는 않지만 깜박임이 계속 될 때까지 주기적인 스냅숏에서 수행 된 현재 연결 상태를 게시 합니다. 서로 다른 시퀀스 번호 또는 서로 다른 연결 상태 이벤트를 사용 하 여 동일한 연결 상태 이벤트를 수신 하면 장치 연결 상태가 변경 된 것을 의미 합니다.

## <a name="tips-for-consuming-events"></a>이벤트 사용하기 위한 팁

IoT Hub 이벤트를 처리하는 애플리케이션은 다음 권장 사항을 따라야 합니다.

* 이벤트를 동일한 이벤트 처리기로 라우팅하는 여러 구독을 구성할 수 있으므로 이벤트를 특정 원본에서 가져온 것으로 가정 하지 마십시오. 항상 메시지 항목을 확인하여 예상한 IoT 허브에서 온 개체인지 확인합니다.

* 받은 모든 이벤트가 예상대로의 형식일 것이라고 가정하지 마십시오. 메시지를 처리하기 전에 eventType을 항상 확인합니다.

* 메시지는 지연 후 또는 잘못된 순서로 도착할 수 있습니다. Etag 필드를 사용 하 여 개체에 대 한 정보가 장치 생성 또는 장치 삭제 이벤트에 대해 최신 상태 인지 파악 합니다.

## <a name="next-steps"></a>다음 단계

* [IoT Hub 이벤트 자습서를 시도해 봅니다.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [디바이스 연결 및 연결 해제 이벤트를 정렬하는 방법](iot-hub-how-to-order-connection-state-events.md)에 대해 알아봅니다.

* [Event Hubs에 대한 자세한 정보](../event-grid/overview.md)

* [IoT Hub 이벤트 라우팅 및 메시지 간의 차이점 비교](iot-hub-event-grid-routing-comparison.md)

* [IoT 원격 분석 이벤트를 사용 하 여 Azure Maps를 사용 하 여 IoT 공간 분석을 구현 하는 방법을 알아봅니다.](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
