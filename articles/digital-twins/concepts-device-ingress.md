---
title: Azure Digital Twins를 사용한 디바이스 연결 및 원격 분석 수신 | Microsoft Docs
description: Azure Digital Twins를 사용한 디바이스 온보딩 개요
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 35d12d0114f9677905c85a9df94ecd074e5f8f75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926081"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>디바이스 연결 및 원격 분석 수신

디바이스 및 센서가 보낸 원격 분석 데이터는 모든 IoT 솔루션의 백본을 형성합니다. 이러한 여러 리소스를 나타내고 위치 컨텍스트 내에서 관리하는 방법은 IoT 앱 개발의 주요 고려 사항입니다. Azure Digital Twins는 디바이스와 센서를 공간 인텔리전스 그래프와 통합하여 IoT 솔루션 개발 프로세스를 단순화합니다.

시작하려면 공간 그래프의 루트에서 Azure IoT Hub 리소스를 만듭니다. IoT Hub 리소스는 루트 공간 아래의 모든 디바이스가 메시지를 보낼 수 있도록 허용합니다. IoT Hub가 만들어지면 Azure Digital Twins 내의 센서에 디바이스를 등록합니다. 디바이스는 [Azure IoT 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)를 통해 Digital Twins 서비스로 데이터를 보낼 수 있습니다.

디바이스 온보딩에 대한 단계별 가이드는 [Digital Twins 배포 및 구성 자습서](tutorial-facilities-setup.md)를 참조하세요. 간략히 보면 단계는 다음과 같습니다.

- [Azure Portal](https://portal.azure.com)에서 Digital Twins 인스턴스를 배포합니다.
- 그래프에 공간을 만듭니다.
- IoT Hub 리소스를 만들어서 그래프의 공간에 할당합니다.
- 그래프에서 디바이스와 센서를 만들고, 이전 단계에서 만든 공간에 할당합니다.
- 조건에 따라 원격 분석 메시지를 필터링하는 검사기를 만듭니다.
- [사용자 정의 함수](concepts-user-defined-functions.md)를 만들고, 원격 분석 메시지를 사용자 지정 처리할 수 있도록 그래프의 공간에 할당합니다.
- 사용자 정의 함수가 그래프 데이터에 액세스하는 것을 허용하는 역할을 할당합니다.
- Digital Twins 관리 API에서 IoT Hub 디바이스 연결 문자열을 가져옵니다.
- Azure IoT 디바이스 SDK를 사용하여 디바이스에서 디바이스 연결 문자열을 구성합니다.

다음 섹션에서는 Digital Twins 관리 API에서 IoT Hub 디바이스 연결 문자열을 가져오는 방법을 알아봅니다. IoT Hub 원격 분석 메시지 형식을 사용하여 센서 기반 원격 분석을 보내는 방법도 배웁니다. Digital Twins는 수신하는 각 원격 분석을 공간 그래프 내의 센서와 연결해야 합니다. 이는 데이터를 적절한 공간 컨텍스트 내에서 처리 및 라우팅하기 위한 요구 사항입니다.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>관리 API에서 IoT Hub 디바이스 연결 문자열 가져오기

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

IoT Hub 디바이스 연결 문자열을 가져오려면 `includes=ConnectionString` 매개 변수를 사용하여 디바이스 API에서 GET 호출을 수행합니다. 디바이스 GUID 또는 하드웨어 ID로 필터링하여 지정된 디바이스를 찾습니다.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_DEVICE_GUID* | 디바이스 ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| 매개 변수 값 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | 디바이스 하드웨어 ID |

응답 페이로드에서 디바이스의 **connectionString** 속성을 복사합니다. Digital Twins에 데이터를 보내기 위해 Azure IoT 디바이스 SDK를 호출할 때 사용합니다.

## <a name="device-to-cloud-message"></a>디바이스-클라우드 메시지

디바이스의 메시지 형식과 페이로드를 솔루션 요구 사항에 맞게 사용자 지정할 수 있습니다. [Azure IoT 디바이스 클라이언트 메시지 클래스 Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___)에서 지원하는 바이트 배열 또는 스트림으로 직렬화할 수 있는 모든 데이터 계약을 사용할 수 있습니다. 해당 사용자 정의 함수에서 데이터 계약을 디코드하는 경우, 메시지는 선택한 사용자 지정 이진 형식일 수 있습니다. 디바이스-클라우드 메시지에 대한 요구 사항은 하나밖에 없습니다. 메시지가 처리 엔진으로 적절하게 라우팅되도록 속성 집합을 유지해야 합니다.

### <a name="telemetry-properties"></a>원격 분석 속성

 **Message**의 페이로드 콘텐츠는 최대 256KB의 임의 데이터일 수 있습니다. [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) 형식의 속성에 대한 요구 사항으로 몇 가지가 예상됩니다. 이 표는 시스템에서 지원하는 필수 속성 및 선택적 속성을 보여줍니다.

| 속성 이름 | 값 | 필수 | 설명 |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | 예 | 시스템에 메시지를 식별하는 상수 값입니다. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | 예 | **Message**를 보내는 센서의 고유 식별자입니다. 이 값은 시스템에서 처리하려면 개체의 **HardwareId** 속성과 일치해야 합니다. 예: `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | 아닙니다. | 페이로드의 샘플링 시간을 식별하는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 형식의 날짜 문자열입니다. 예: `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | 아닙니다. | 시스템에서 이벤트를 추적하는 데 사용되는 UUID입니다. 예: `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Digital Twins로 메시지 보내기

Digital Twins에 메시지를 보내려면 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 또는 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 호출을 사용합니다.

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins의 데이터 처리 및 사용자 정의 함수 기능에 대해 알아보려면 [Azure Digital Twins 데이터 처리 및 사용자 정의 함수](concepts-user-defined-functions.md)를 참조하세요.
