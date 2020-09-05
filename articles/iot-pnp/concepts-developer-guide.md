---
title: 개발자 가이드-IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: 개발자를 위한 IoT 플러그 앤 플레이 설명
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f1860f9645a50789803d509a6a1ea98bc0ea1a9e
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950139"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT 플러그 앤 플레이 Preview 개발자 가이드

IoT 플러그 앤 플레이 미리 보기를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

이 가이드에서는 [IoT 플러그 앤 플레이 규칙](concepts-convention.md)을 따르는 장치를 만드는 데 필요한 기본 단계 및 장치와 상호 작용 하는 데 사용할 수 있는 REST api에 대해 설명 합니다.

IoT 플러그 앤 플레이 장치를 빌드하려면 다음 단계를 수행 합니다.

1. 장치가 MQTT 또는 Websocket을 통한 MQTT 프로토콜을 사용 하 여 Azure IoT Hub에 연결 하는지 확인 합니다.
1. 장치를 설명 하는 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 모델을 만듭니다. 자세히 알아보려면 [IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-components.md)를 참조 하세요.
1. 장치를 업데이트 하 여 `model-id` 장치 연결의 일부로를 발표 합니다.
1. [IoT 플러그 앤 플레이 규칙](concepts-convention.md) 을 사용 하 여 원격 분석, 속성 및 명령 구현

장치 구현이 준비 되 면 [Azure iot 탐색기](howto-use-iot-explorer.md) 를 사용 하 여 장치가 IoT 플러그 앤 플레이 규칙을 따르는지 확인 합니다.

> [!Tip]
> 이 문서의 모든 코드 조각은 c #을 사용 하지만, 개념은 C, Python, Node 및 Java에 대해 사용 가능한 모든 Sdk에 적용 됩니다.

## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리기 위해 장치는 연결 정보에 해당 ID를 포함 해야 합니다.

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

연결을 `ClientOptions` `DeviceClient` 초기화 하는 데 사용 되는 모든 메서드에서 새 오버 로드를 사용할 수 있습니다.

모델 ID 알림이 Sdk의 다음 버전에 추가 되었습니다.

|SDK|버전|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|노드|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>DPS 페이로드

[DPS (장치 프로 비전 서비스)](../iot-dps/about-iot-dps.md) 를 사용 하는 장치에는 `modelId` 다음 JSON 페이로드를 사용 하 여 프로 비전 프로세스 중에 사용할가 포함 될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>원격 분석, 속성 및 명령 구현

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-components.md)에서 설명한 대로 장치 빌더는 구성 요소를 사용 하 여 장치를 설명 하는지 결정 해야 합니다. 구성 요소를 사용 하는 경우 장치는이 섹션에 설명 된 규칙을 따라야 합니다.

### <a name="telemetry"></a>원격 분석

구성 요소가 없는 모델에는 특별 한 속성이 필요 하지 않습니다.

구성 요소를 사용 하는 경우 장치에서 구성 요소 이름으로 메시지 속성을 설정 해야 합니다.

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>읽기 전용 속성

구성 요소가 없는 모델에는 특별 한 구문이 필요 하지 않습니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

구성 요소를 사용 하는 경우 구성 요소 이름 내에 속성을 만들어야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>쓰기 가능 속성

이러한 속성은 장치에서 설정 하거나 솔루션에서 업데이트할 수 있습니다. 솔루션이 속성을 업데이트 하는 경우 클라이언트는의 콜백으로 알림을 받습니다 `DeviceClient` . IoT 플러그 앤 플레이 규칙을 따르려면 장치는 속성이 성공적으로 수신 되었음을 서비스에 알려야 합니다.

#### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

장치에서 쓰기 가능한 속성을 보고 하는 경우 규칙에 정의 된 값을 포함 해야 합니다 `ack` .

구성 요소 없이 쓰기 가능 속성을 보고 하려면 다음을 수행 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

구성 요소에서 쓰기 가능한 속성을 보고 하려면 쌍에 표식을 포함 해야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Desired 속성 업데이트를 구독 합니다.

서비스는 연결 된 장치에 대 한 알림을 트리거하는 desired 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별 하는 버전 번호를 포함 하 여 업데이트 된 desired 속성이 포함 됩니다. 장치는 보고 된 속성과 동일한 메시지를 사용 하 여 응답 해야 합니다 `ack` .

구성 요소가 없는 모델 단일 속성을 참조 하 고 수신 된 버전으로 보고 된를 만듭니다 `ack` .

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

장치 쌍은 원하는 및 보고 된 섹션의 속성을 보여 줍니다.

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

구성 요소가 있는 모델은 구성 요소 이름으로 래핑된 desired 속성을 받고 보고 된 속성을 다시 보고 해야 합니다 `ack` .

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

구성 요소에 대 한 장치 쌍은 다음과 같이 원하는 및 보고 된 섹션을 보여 줍니다.

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>명령

구성 요소가 없는 모델은 서비스에서 호출 된 명령 이름을 받습니다.

구성 요소가 있는 모델에는 구성 요소와 구분 기호가 접두사로 추가 된 명령 이름이 표시 됩니다 `*` .

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용 하 여 요청 및 응답 페이로드를 정의 합니다. 장치는 들어오는 입력 매개 변수를 deserialize 하 고 응답을 직렬화 해야 합니다. 다음 예제에서는 페이로드에 정의 된 복합 형식을 사용 하 여 명령을 구현 하는 방법을 보여 줍니다.

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

다음 코드 조각에서는 serialization 및 deserialization을 활성화 하는 데 사용 되는 형식을 포함 하 여 장치에서이 명령 정의를 구현 하는 방법을 보여 줍니다.

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 요청 및 응답 이름이 네트워크를 통해 전송 되는 직렬화 된 페이로드에 없습니다.

## <a name="interact-with-the-device"></a>장치와 상호 작용 

IoT 플러그 앤 플레이를 사용 하면 IoT hub로 모델 ID를 발표 한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치를 사용 하려면 IoT Hub REST API 또는 IoT 언어 Sdk 중 하나를 사용 합니다. 다음 예에서는 IoT Hub REST API를 사용 합니다. API의 현재 버전은 `2020-05-31-preview` 입니다. `?api-version=2020-05-31`REST PI 호출에 추가 합니다.

자동 온도 조절기 장치를 호출 하는 경우 `t-123` REST API get 호출을 사용 하 여 장치에서 구현 된 모든 인터페이스의 모든 속성을 가져옵니다.

```REST
GET /digitalTwins/t-123
```

이 호출에는 `$metadata.$model` 장치에서 발표 한 모델 ID를 포함 하는 Json 속성이 포함 됩니다.

모든 인터페이스의 모든 속성은 REST API 템플릿을 사용 하 여 액세스할 수 있습니다 `GET /DigitalTwin/{device-id}` `{device-id}` . 여기서은 장치에 대 한 식별자입니다.

```REST
GET /digitalTwins/{device-id}
```

IoT 플러그 앤 플레이 장치 명령을 직접 호출할 수 있습니다. `Thermostat`장치의 구성 요소에 명령이 있는 경우 `t-123` `restart` REST API POST 호출을 사용 하 여 호출할 수 있습니다.

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

보다 일반적으로이 REST API 템플릿을 통해 명령을 호출할 수 있습니다.

- `device-id`: 장치의 식별자입니다.
- `component-name`: 장치 기능 모델의 implements 섹션에 있는 인터페이스의 이름입니다.
- `command-name`: 명령의 이름입니다.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
