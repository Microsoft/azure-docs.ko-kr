---
title: Azure Digital Twins의 송신 및 엔드포인트 | Microsoft Docs
description: Azure Digital Twins를 사용하여 엔드포인트를 만드는 방법에 대한 지침입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924808"
---
# <a name="egress-and-endpoints"></a>송신 및 엔드포인트

Azure Digital Twins *엔드포인트*는 사용자의 Azure 구독 내 메시지 또는 이벤트 브로커를 나타냅니다. 이벤트 및 메시지를 Azure Event Hubs, Azure Event Grid 및 Azure Service Bus 토픽으로 전송할 수 있습니다.

이벤트는 미리 정의된 라우팅 기본 설정에 따라 엔드포인트로 라우팅됩니다. 사용자는 각 엔드포인트가 수신할 수 있는 *이벤트 유형*을 지정합니다.

이벤트, 라우팅 및 이벤트 유형에 대해 자세히 알아보려면 [Azure Digital Twins의 라우팅 이벤트 및 메시지](./concepts-events-routing.md)를 참조하세요.

## <a name="events"></a>이벤트

이벤트는 Azure 메시지 및 이벤트 브로커에서 처리할 수 있도록 IoT 개체(예: 디바이스 및 센서)로 전송됩니다. 이벤트는 다음 [Azure Event Grid 이벤트 스키마 참조](../event-grid/event-schema.md)로 정의됩니다.

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 특성 | Type | 설명 |
| --- | --- | --- |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 제목 | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| 데이터 | object | 특정 리소스 공급자에 대한 이벤트 데이터입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |

Event Grid 이벤트 스키마에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Event Grid 이벤트 스키마 참조](../event-grid/event-schema.md)를 검토합니다.
- [Azure EventGrid Node.js SDK EventGridEvent 참조](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest)를 읽어봅니다.

## <a name="event-types"></a>일정 유형

이벤트 유형은 이벤트의 특성을 분류하며 **eventType** 필드에 설정됩니다. 사용 가능한 이벤트 유형은 다음 목록에 나와 있습니다.

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

다음 하위 섹션에서는 각 이벤트 유형의 이벤트 형식을 추가로 설명합니다.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation**은 그래프 변경 내용에 적용됩니다. **subject** 속성은 영향받는 개체의 유형을 지정합니다. 다음 유형의 개체는 이 이벤트를 트리거할 수 있습니다.

- 디바이스
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- 보고서
- RoleDefinition
- 센서
- SensorBlobMetadata
- SensorExtendedProperty
- 공백
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- 시스템
- 사용자
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>예

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom**은 UDF(사용자 정의 함수)에서 전송하는 이벤트입니다.
  
> [!IMPORTANT]  
> 이 이벤트는 UDF 자체에서 명시적으로 전송해야 합니다.

#### <a name="example"></a>예

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

### <a name="sensorchange"></a>SensorChange

**SensorChange**는 원격 분석 변경 내용을 기반으로 하는 센서의 상태 업데이트입니다.

#### <a name="example"></a>예

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

### <a name="spacechange"></a>SpaceChange

**SpaceChange**는 원격 분석 변경 내용을 기반으로 하는 공간의 상태 업데이트입니다.

#### <a name="example"></a>예

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

### <a name="devicemessage"></a>DeviceMessage

**DeviceMessage**를 사용하면 Azure Digital Twins에서도 원시 원격 분석 이벤트를 라우팅할 수 있는 **EventHub** 연결을 지정할 수 있습니다.

> [!NOTE]
> - **DeviceMessage**는 **EventHub**와만 결합할 수 있습니다. 다른 유형의 이벤트와는 **DeviceMessage**를 결합할 수 없습니다.
> - **EventHub** 또는 **DeviceMessage** 유형의 조합에 대해 하나의 엔드포인트만 지정할 수 있습니다.

## <a name="configure-endpoints"></a>엔드포인트 구성

엔드포인트 관리는 엔드포인트 API를 통해 수행합니다.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

다음 예제는 지원되는 엔드포인트를 구성하는 방법을 보여줍니다.

>[!IMPORTANT]
> **eventTypes** 특성에 유의합니다. 이 특성은 엔드포인트가 처리하는 이벤트 유형을 정의하고 해당 라우팅을 결정합니다.

인증된 HTTP POST 요청

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Service Bus 이벤트 유형 **SensorChange**, **SpaceChange** 및 **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_NAMESPACE | 엔드포인트의 네임스페이스 |
    | YOUR_PRIMARY_KEY | 인증에 사용되는 기본 연결 문자열 |
    | YOUR_SECONDARY_KEY | 인증에 사용되는 보조 연결 문자열 |
    | YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

- Event Grid 이벤트 유형 **SensorChange**, **SpaceChange** 및 **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_PRIMARY_KEY | 인증에 사용되는 기본 연결 문자열|
    | YOUR_SECONDARY_KEY | 인증에 사용되는 보조 연결 문자열 |
    | YOUR_TOPIC_NAME | 사용자 지정 항목의 이름 |

- Event Hubs 이벤트 유형 **SensorChange**, **SpaceChange** 및 **TopologyOperation**으로 라우팅합니다.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_NAMESPACE | 엔드포인트의 네임스페이스 |
    | YOUR_PRIMARY_KEY | 인증에 사용되는 기본 연결 문자열 |
    | YOUR_SECONDARY_KEY | 인증에 사용되는 보조 연결 문자열 |
    | YOUR_EVENT_HUB_NAME | 이벤트 허브의 이름 |

- Event Hubs 이벤트 유형 **DeviceMessage**로 라우팅합니다. `EntityPath`를 **connectionString**에 반드시 포함해야 합니다.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_NAMESPACE | 엔드포인트의 네임스페이스 |
    | YOUR_PRIMARY_KEY | 인증에 사용되는 기본 연결 문자열 |
    | YOUR_SECONDARY_KEY | 인증에 사용되는 보조 연결 문자열 |
    | YOUR_EVENT_HUB_NAME | 이벤트 허브의 이름 |

> [!NOTE]  
> 새 엔드포인트를 만들면 이 엔드포인트에서 이벤트 수신을 시작하는 데 최대 5~10분이 걸릴 수 있습니다.

## <a name="primary-and-secondary-connection-keys"></a>기본 및 보조 연결 키

기본 연결 키가 승인되지 않으면 시스템에서 자동으로 보조 연결 키를 시도합니다. 이렇게 하면 백업이 제공되고 엔드포인트 API를 통해 기본 키를 유연하게 인증 및 업데이트할 수 있습니다.

기본 및 보조 연결 키가 모두 승인되지 않은 경우 시스템은 최대 30분의 지수 백오프 대기 시간을 입력합니다. 트리거된 각 백오프 대기 시간에 이벤트가 삭제됩니다.

시스템이 백오프 대기 상태일 때마다 엔드포인트 API를 통해 연결 키를 업데이트하면 변경 사항이 적용되는 데 최대 30분이 걸릴 수 있습니다.

## <a name="unreachable-endpoints"></a>연결할 수 없는 엔드포인트

엔드포인트에 연결할 수 없으면 시스템은 최대 30분의 지수 백오프 대기 시간을 입력합니다. 트리거된 각 백오프 대기 시간에 이벤트가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Digital Twins Swagger를 사용하는 방법](how-to-use-swagger.md)을 알아봅니다.

- Azure Digital Twins의 [이벤트 및 메시지 라우팅](concepts-events-routing.md)에 대해 알아봅니다.
