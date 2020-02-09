---
title: 개발자 가이드-IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: IoT 플러그 앤 플레이 개발자를 위한 장치 모델링에 대 한 설명
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 55f3e42687c90936c33208684b58792b3e2b9f85
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905800"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드

IoT 플러그 앤 플레이 미리 보기를 통해 Azure IoT 응용 프로그램에 기능을 보급 하는 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다. IoT Central는 IoT 플러그 앤 플레이 지원 응용 프로그램의 예입니다.

IoT 플러그 앤 플레이 장치를 빌드하려면 장치 설명을 만들어야 합니다. 설명은 DTDL (Digital Twins 정의 언어) 이라는 단순한 정의 언어로 수행 됩니다.

## <a name="device-capability-model"></a>디바이스 기능 모델

DTDL을 사용 하 여 장치 구성 요소를 설명 하는 _장치 기능 모델_ 을 만듭니다. 일반적인 IoT 장치는 다음과 같이 구성 됩니다.

- 사용자 지정 파트-장치를 고유 하 게 만드는 항목입니다.
- 모든 장치에 공통적인 작업 인 표준 부분

이러한 부분은 장치 기능 모델에서 _인터페이스_ 라고 합니다. 인터페이스는 장치가 구현 하는 각 파트에 대 한 세부 정보를 정의 합니다.

다음 예제에서는 자동 온도 조절기 장치에 대 한 장치 기능 모델을 보여 줍니다.

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

기능 모델에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순한 단일 리소스 이름 형식의 고유 ID입니다.
- `@type`:이 개체가 기능 모델 임을 선언 합니다.
- `@context`: 기능 모델에 사용 되는 DTDL 버전을 지정 합니다.
- `implements`: 장치에서 구현 하는 인터페이스를 나열 합니다.

Implements 섹션에서 인터페이스 목록의 각 항목에는 다음이 포함 됩니다.

- `name`: 인터페이스의 프로그래밍 이름입니다.
- `schema`: 기능 모델이 구현 하는 인터페이스입니다.

표시 이름 및 설명과 같이 기능 모델에 세부 정보를 추가 하는 데 사용할 수 있는 추가 선택적 필드가 있습니다. 기능 모델 내에서 선언 된 인터페이스는 장치의 구성 요소로 간주할 수 있습니다. 공개 미리 보기의 경우에는 인터페이스 목록에 스키마 당 항목이 하나만 있을 수 있습니다.

## <a name="interface"></a>인터페이스

DTDL을 사용 하면 인터페이스를 사용 하 여 장치의 기능을 설명할 수 있습니다. 인터페이스는 장치에서 구현 하는 _속성_, _원격 분석_및 _명령을_ 설명 합니다.

- `Properties`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. 속성은 장치의 상태를 나타내는 데이터 필드입니다. 속성을 사용 하 여 냉각수 펌프의 꺼짐 상태와 같은 장치의 내구성이 있는 상태를 나타냅니다. 속성은 장치의 펌웨어 버전 같은 기본 장치 속성을 나타낼 수도 있습니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다.
- `Telemetry`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. 원격 분석 필드는 센서의 측정값을 나타냅니다. 장치가 센서를 사용 하는 경우에는 센서 데이터를 포함 하는 원격 분석 이벤트를 전송 해야 합니다.
- `Commands`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. 명령은 장치의 사용자가 장치에서 실행할 수 있는 메서드를 나타냅니다. 예를 들면 reset 명령 또는 팬을 전환 하는 명령입니다.

다음 예제에서는 자동 온도 조절기 장치에 대 한 인터페이스를 보여 줍니다.

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

인터페이스에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순한 단일 리소스 이름 형식의 고유 ID입니다.
- `@type`:이 개체가 인터페이스 임을 선언 합니다.
- `@context`: 인터페이스에 사용 되는 DTDL 버전을 지정 합니다.
- `contents`: 장치를 구성 하는 속성, 원격 분석 및 명령을 나열 합니다.

이 간단한 예제에는 원격 분석 필드가 하나 뿐입니다. 최소 필드 설명에는 다음이 포함 됩니다.

- `@type`: `Telemetry`, `Property`또는 `Command`기능 유형을 지정 합니다.
- `name`: 원격 분석 값의 이름을 제공 합니다.
- `schema`: 원격 분석에 대 한 데이터 형식을 지정 합니다. 이 값은 double, integer, boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식, 배열 및 맵도 지원 됩니다.

표시 이름 및 설명과 같은 기타 선택적 필드를 사용 하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

### <a name="properties"></a>속성

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 장치에서 IoT hub에 대 한 속성 값 업데이트를 보고 한다는 의미입니다. IoT hub는 읽기 전용 속성 값을 설정할 수 없습니다.

인터페이스에서 속성을 쓰기 가능으로 표시할 수도 있습니다. 장치는 IoT hub에서 쓰기 가능한 속성에 대 한 업데이트를 받을 수 있을 뿐만 아니라 허브에 대 한 속성 값 업데이트를 보고 합니다.

장치를 연결 하 여 속성 값을 설정할 필요가 없습니다. 업데이트 된 값은 다음에 장치가 허브에 연결 될 때 전송 됩니다. 이 동작은 읽기 전용 및 쓰기 가능 속성 모두에 적용 됩니다.

장치에서 원격 분석을 전송 하는 데 속성을 사용 하지 마세요. 예를 들어 `temperatureSetting=80`와 같은 readonly 속성은 장치 온도가 80로 설정 되 고 장치가이 온도를 대상으로 하는 것을 의미 합니다.

쓰기 가능한 속성의 경우 장치 응용 프로그램은 필요한 상태 코드, 버전 및 설명을 반환 하 여 속성 값을 받아서 적용 했는지 여부를 나타냅니다.

### <a name="telemetry"></a>원격 분석

기본적으로 IoT Hub는 장치의 모든 원격 분석 메시지를 [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)와 호환 되는 [기본 제공 서비스 연결 엔드포인트 (**메시지/이벤트**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 로 라우팅합니다.

[IoT Hub의 사용자 지정 엔드포인트 및 라우팅 규칙](../iot-hub/iot-hub-devguide-messages-d2c.md) 을 사용 하 여 blob storage 또는 다른 event hubs와 같은 다른 대상에 원격 분석을 보낼 수 있습니다. 라우팅 규칙은 메시지 속성을 사용 하 여 메시지를 선택 합니다.

### <a name="commands"></a>명령

명령은 동기 또는 비동기 명령입니다. 동기 명령은 기본적으로 30 초 이내에 실행 해야 하며, 명령이 도착할 때 장치를 연결 해야 합니다. 장치가 시간 내에 응답 하거나 장치가 연결 되지 않은 경우 명령이 실패 합니다.

장기 실행 작업에는 비동기 명령을 사용 합니다. 장치는 원격 분석 메시지를 사용 하 여 진행률 정보를 보냅니다. 이러한 진행 메시지의 헤더 속성은 다음과 같습니다.

- `iothub-command-name`: 명령 이름입니다 (예: `UpdateFirmware`.
- `iothub-command-request-id`: 서버 쪽에서 생성 되 고 초기 호출에서 장치로 전송 되는 요청 ID입니다.
- `iothub-interface-id`: `urn:example:AssetTracker:1`와 같이이 명령이 정의 된 인터페이스의 ID입니다.
 `iothub-interface-name`:이 인터페이스의 인스턴스 이름입니다 (예: `myAssetTracker`.
- `iothub-command-statuscode`: 장치에서 반환 된 상태 코드 (예: `202`.

## <a name="register-a-device"></a>디바이스 등록

IoT 플러그 앤 플레이를 사용 하면 장치의 기능을 쉽게 보급할 수 있습니다. IoT 플러그 앤 플레이를 사용 하 여 장치가 IoT Hub에 연결 되 면 장치 기능 모델을 등록 해야 합니다. 등록을 통해 고객은 장치의 IoT 플러그 앤 플레이 기능을 사용할 수 있습니다.

이 가이드에서는 C 용 Azure IoT 장치 SDK를 사용 하 여 장치를 등록 하는 방법을 보여 줍니다.

장치에서 구현 하는 각 인터페이스에 대해 인터페이스를 만들고 구현에 연결 해야 합니다.

앞에서 설명한 자동 온도 조절기 인터페이스의 경우 C SDK를 사용 하 여 자동 온도 조절기 인터페이스를 만들고 해당 구현에 연결 합니다.

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

장치가 구현 하는 각 인터페이스에 대해이 코드를 반복 합니다.

인터페이스를 만든 후 장치 기능 모델 및 인터페이스를 IoT hub에 등록 합니다.

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>장치 사용

IoT 플러그 앤 플레이를 사용 하면 IoT hub에 기능을 등록 한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치를 사용 하려면 IoT Hub REST API 또는 IoT 언어 Sdk 중 하나를 사용 합니다. 다음 예에서는 IoT Hub REST API를 사용 합니다.

자동 온도 조절기의 `DeviceInformation` 인터페이스에서 펌웨어 버전 (`fwVersion`)과 같은 장치 속성의 값을 가져오려면 디지털 쌍 REST API을 사용 합니다.

자동 온도 조절기 장치를 `t-123`이라고 하는 경우 REST API GET 호출을 사용 하 여 장치에서 구현 하는 모든 속성을 가져옵니다.

```REST
GET /digitalTwins/t-123/interfaces
```

보다 일반적으로이 REST API 템플릿을 사용 하 여 모든 속성에 액세스 합니다. 여기서 `{device-id}`는 장치의 식별자입니다.

```REST
GET /digitalTwins/{device-id}/interfaces
```

인터페이스의 이름을 알고 있고 해당 특정 인터페이스에 대 한 속성을 가져오려면 이름으로 특정 인터페이스에 대 한 요청의 범위를 지정 합니다.

```REST
GET /digitalTwins/t-123/interfaces/info
```

보다 일반적으로 특정 인터페이스에 대 한 속성은이 REST API 템플릿을 통해 액세스할 수 있습니다. 여기서 `device-id`는 장치의 식별자이 고 `{interface-name}`는 인터페이스의 이름입니다.

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

IoT 플러그 앤 플레이 장치 명령을 직접 호출할 수 있습니다. `t-123` 장치의 `Thermostat` 인터페이스에 `restart` 명령이 있는 경우 REST API POST 호출을 사용 하 여 호출할 수 있습니다.

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

보다 일반적으로이 REST API 템플릿을 통해 명령을 호출할 수 있습니다.

- `device-id`: 장치의 식별자입니다.
- `interface-name`: 장치 기능 모델의 implements 섹션에 있는 인터페이스의 이름입니다.
- `command-name`: 명령의 이름입니다.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL (디지털 쌍 정의 언어)](https://aka.ms/DTDL)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
