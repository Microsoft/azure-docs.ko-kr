---
title: 이벤트 데이터 해석
titleSuffix: Azure Digital Twins
description: 다양 한 이벤트 유형 및 다양 한 알림 메시지를 해석 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 10b74f7b795df2cf8c19d044fce44da3f798af7a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587636"
---
# <a name="understand-event-data"></a>이벤트 데이터 이해

Azure Digital Twins의 여러 이벤트는 **알림을**생성 하며,이를 통해 다양 한 작업이 수행 될 때 솔루션 백 엔드가 인식 될 수 있습니다. 그런 다음이 정보를 사용 하 여 조치를 취할 수 있는 Azure Digital Twins의 내부 및 외부의 여러 위치로 [라우팅됩니다](concepts-route-events.md) .

생성할 수 있는 여러 가지 유형의 알림이 있으며 알림 메시지는 생성 된 이벤트 유형에 따라 달라질 수 있습니다. 이 문서에서는 다양 한 유형의 메시지에 대 한 세부 정보와 표시 되는 모양을 제공 합니다.

이 차트는 다음과 같은 다양 한 알림 유형을 보여 줍니다.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

일반적으로 알림은 헤더와 본문의 두 부분으로 구성 됩니다. 

### <a name="event-notification-headers"></a>이벤트 알림 헤더

알림 메시지 헤더는 키-값 쌍으로 표현 됩니다. 사용 된 프로토콜 (MQTT, AMQP 또는 HTTP)에 따라 메시지 헤더가 다르게 serialize 됩니다. 이 섹션에서는 선택한 특정 프로토콜 및 serialization에 관계 없이 알림 메시지에 대 한 일반 헤더 정보에 대해 설명 합니다.

일부 알림은 [CloudEvents](https://cloudevents.io/) 표준을 준수 합니다. CloudEvents 준수는 다음과 같습니다.
* 장치에서 내보낸 알림은 계속 해 서 알림에 대 한 기존 사양을 따릅니다.
* IoT Hub에서 처리 하 고 내보내는 알림은 IoT Hub를 지원 하도록 선택 하는 경우를 제외 하 고는 알림에 대 한 기존 사양을 계속 따릅니다 (Event Grid 예:).
* [모델](concepts-models.md) 을 사용 하 여 [digital 쌍](concepts-twins-graph.md) 에서 내보낸 알림이 CloudEvents을 준수 합니다.
* Azure Digital Twins에서 처리 하 고 내보내는 알림이 CloudEvents을 준수 합니다.

서비스는 주문을 표시 하기 위해 모든 알림에 일련 번호를 추가 하거나 다른 방식으로 고유한 순서를 유지 해야 합니다. 

Azure Digital Twins에서 Event Grid에 내보내는 알림은 Event Grid 토픽에 정의 된 스키마 유형에 따라 자동으로 CloudEvents schema 또는 EventGridEvent 스키마로 서식 지정 됩니다. 

헤더의 확장 특성은 페이로드 내의 Event Grid 스키마에 대 한 속성으로 추가 됩니다. 

### <a name="event-notification-bodies"></a>이벤트 알림 본문

알림 메시지의 본문은 여기서 JSON에 설명 되어 있습니다. 메시지 본문에 필요한 serialization (예: JSON, CBOR, Protobuf 등)에 따라 메시지 본문이 다르게 serialize 될 수 있습니다.

본문에 포함 된 필드 집합은 알림 유형에 따라 다릅니다. 다음은 일반적으로 표시 되는 것을 이해 하 고 포함할 수 있는 두 가지 샘플 메시지 본문입니다.

원격 분석 메시지:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

수명 주기 알림 메시지:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>여러 이벤트 유형에 대 한 메시지 형식 세부 정보

이 섹션에서는 IoT Hub 및 Azure Digital Twins (또는 다른 Azure IoT 서비스)에서 내보내는 다양 한 유형의 알림에 대해 자세히 설명 합니다. 각 알림 유형을 트리거하는 것과 각 알림 본문 유형에 포함 된 필드 집합에 대해 읽어 보십시오.

### <a name="digital-twin-life-cycle-notifications"></a>디지털 쌍 수명 주기 알림

모든 [digital 쌍](concepts-twins-graph.md) 는 [Azure digital 쌍에 IoT Hub 장치](how-to-ingest-iot-hub-data.md) 를 나타내는지 여부에 관계 없이 알림을 내보냅니다. 이것은 디지털 쌍 자체에 대 한 **수명 주기 알림과**관련 된 것입니다.

수명 주기 알림은 다음과 같은 경우에 트리거됩니다.
* 디지털 쌍이 만들어집니다.
* 디지털 쌍이 삭제 됨

#### <a name="properties"></a>속성

다음은 수명 주기 알림의 본문에 있는 필드입니다.

| 속성 | 값 |
| --- | --- |
| `id` | UUID 또는 서비스에서 유지 관리 되는 카운터와 같은 알림의 식별자입니다. `source` + `id` 각 고유 이벤트에 대해 고유 합니다. |
| `source` | *Myhub.azure-devices.net* 또는 *Mydigitaltwins.westus2.azuredigitaltwins.net* 와 같은 IoT Hub 또는 Azure Digital twins 인스턴스의 이름 |
| `specversion` | *1.0*<br>이 [CloudEvents 사양](https://github.com/cloudevents/spec)버전을 준수 하는 메시지입니다. |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 디지털 쌍의 ID |
| `time` | 쌍에서 작업이 발생 한 시간에 대 한 타임 스탬프입니다. |
| `traceparent` | 이벤트에 대 한 W3C 추적 컨텍스트입니다. |

#### <a name="body-details"></a>본문 정보

본문은 영향을 받는 디지털 쌍 이며 JSON 형식으로 표시 됩니다. 이에 대 한 스키마는 *디지털 Twins 리소스 7.1*입니다.

생성 이벤트의 경우 페이로드는 리소스가 만들어진 후의 쌍 상태를 반영 하므로 호출과 마찬가지로 모든 시스템 생성 요소를 포함 해야 합니다 `GET` .

다음은 구성 요소 및 최상위 속성이 없는 [IoT 플러그 앤 플레이 (PnP)](../iot-pnp/overview-iot-plug-and-play.md) 장치에 대 한 본문의 예입니다. 장치에 적합 하지 않은 속성 (예: 보고 된 속성)은 생략 해야 합니다.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

디지털 쌍의 또 다른 예는 다음과 같습니다. 이 항목은 [모델](concepts-models.md)을 기반으로 하며 구성 요소를 지원 하지 않습니다.

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>디지털 쌍 관계 변경 알림

**관계 변경 알림은** 디지털 쌍의 관계가 생성, 업데이트 또는 삭제 될 때 트리거됩니다. 

#### <a name="properties"></a>속성

다음은에 지 변경 알림 본문의 필드입니다.

| 속성    | 값 |
| --- | --- |
| `id` | UUID 또는 서비스에서 유지 관리 되는 카운터와 같은 알림의 식별자입니다. `source` + `id` 각 고유 이벤트에 대해 고유 합니다. |
| `source` | *Mydigitaltwins.westus2.azuredigitaltwins.net* 와 같은 Azure 디지털 twins 인스턴스의 이름 |
| `specversion` | *1.0*<br>이 [CloudEvents 사양](https://github.com/cloudevents/spec)버전을 준수 하는 메시지입니다. |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | 관계의 ID (예:) `<twinID>/relationships/<relationshipID>` |
| `time` | 관계에서 작업이 발생 한 시간에 대 한 타임 스탬프입니다. |
| `traceparent` | 이벤트에 대 한 W3C 추적 컨텍스트입니다. |

#### <a name="body-details"></a>본문 정보

본문은 JSON 형식의 관계에 대 한 페이로드 이기도 합니다. `GET` [DigitalTwins API](how-to-use-apis-sdks.md)를 통한 관계 요청과 동일한 형식을 사용 합니다. 

"관계 업데이트"는 관계의 속성이 변경 되었음을 의미 합니다. 

속성을 업데이트 하기 위한 업데이트 관계 알림의 예는 다음과 같습니다.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

의 경우 `Relationship.Delete` 본문은 `GET` 요청과 같으며 삭제 전 최신 상태를 가져옵니다.

Create 또는 delete 관계 알림의 예는 다음과 같습니다.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>디지털 쌍 변경 알림

디지털 쌍 **변경 알림은** 디지털 쌍이 업데이트 될 때 트리거됩니다. 예를 들면 다음과 같습니다.
* 속성 값 또는 메타 데이터가 변경 되는 경우
* 디지털 쌍 또는 구성 요소 메타 데이터가 변경 되는 경우 이 시나리오의 예는 디지털 쌍의 모델을 변경 하는 것입니다.

#### <a name="properties"></a>속성

다음은 디지털 쌍 변경 알림 본문의 필드입니다.

| 속성    | 값 |
| --- | --- |
| `id` | UUID 또는 서비스에서 유지 관리 되는 카운터와 같은 알림의 식별자입니다. `source` + `id` 각 고유 이벤트에 대해 고유 합니다. |
| `source` | *Myhub.azure-devices.net* 또는 *Mydigitaltwins.westus2.azuredigitaltwins.net* 와 같은 IoT Hub 또는 Azure Digital twins 인스턴스의 이름
| `specversion` | *1.0*<br>이 [CloudEvents 사양](https://github.com/cloudevents/spec)버전을 준수 하는 메시지입니다. |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | 디지털 쌍의 ID |
| `time` | 디지털 쌍에서 작업이 발생 한 시간에 대 한 타임 스탬프 |
| `traceparent` | 이벤트에 대 한 W3C 추적 컨텍스트입니다. |

#### <a name="body-details"></a>본문 정보

알림의 본문은 `Twin.Update` 디지털 쌍에 대 한 업데이트를 포함 하는 JSON 패치 문서입니다.

예를 들어 다음 패치를 사용 하 여 디지털 쌍이 업데이트 되었다고 가정 합니다.

```json
[
    {
        "op": "replace",
        "value": 40,
        "path": "/Temperature"
    },
    {
        "op": "add",
        "value": 30,
        "path": "/comp1/prop1"
    }
]
```

디지털 쌍을 업데이트 하는 Azure Digital Twins와 같이 서비스에서 동기적으로 실행 하는 경우 해당 알림이 다음과 같이 나타납니다.

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>다음 단계

이벤트를 전달 하는 끝점과 경로를 만드는 방법을 참조 하세요.
* [*방법: 끝점과 경로 관리*](how-to-manage-routes-apis-cli.md)

또는 Azure Digital Twins Api 및 SDK 옵션에 대해 자세히 알아보세요.
* [*방법: Azure Digital Twins Api 및 Sdk 사용*](how-to-use-apis-sdks.md)