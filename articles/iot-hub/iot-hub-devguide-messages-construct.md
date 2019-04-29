---
title: Azure IoT Hub 메시징 형식 이해 | Microsoft Docs
description: 개발자 가이드-형식 및 IoT Hub 메시지의 예상된 콘텐츠를 설명 합니다.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 49a28c120ae71224195edcdb3809335aeea1fa3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364313"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub 메시지 만들기 및 읽기

프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 디바이스 지향 프로토콜에 대한 일반적인 메시지 형식을 정의합니다. 이 메시지 형식은 [디바이스-클라우드 라우팅](iot-hub-devguide-messages-d2c.md) 및 [클라우드-디바이스](iot-hub-devguide-messages-c2d.md) 메시지에 둘 다 사용됩니다. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub는 스트리밍 메시징 패턴을 사용하여 디바이스-클라우드 메시징을 구현합니다. IoT Hub의 디바이스 - 클라우드 메시지는 여러 독자가 읽을 수 있는 서비스를 통과하는 많은 양의 이벤트가 있다는 점에서 [Service Bus](/azure/service-bus-messaging/) ‘메시지’보다는 [Event Hubs](/azure/event-hubs/) ‘이벤트’에 가깝습니다.

IoT Hub 메시지는 다음으로 구성됩니다.

* 아래에 나열된 미리 결정된 *시스템 속성* 집합

* *애플리케이션 속성*집합. 메시지 본문을 역직렬화할 필요 없이 애플리케이션이 정의하고 액세스할 수 있는 문자열 속성 사전입니다. IoT Hub는 절대로 이러한 속성을 수정하지 않습니다.

* 불투명한 이진 본문.

HTTPS 프로토콜을 사용하여 디바이스-클라우드 메시지를 보내거나 클라우드-디바이스 메시지를 보낼 때 속성 이름과 값에는 ASCII 영숫자 문자와 ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``만 포함할 수 있습니다.

IoT Hub를 사용한 디바이스-클라우드 메시징의 특징은 다음과 같습니다.

* 디바이스-클라우드 메시지는 영구적이며 IoT Hub의 기본 **messages/events** 엔드포인트에 최대 7일 동안 보관됩니다.

* 디바이스-클라우드 메시지는 최대 256KB까지 가능하며 보내기를 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. Batch는 최대 256KB가 될 수 있습니다.

* IoT Hub는 임의 분할을 허용하지 않습니다. 디바이스-클라우드 메시지는 원래 **deviceId**와 관련하여 분할됩니다.

* [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)에서 설명한 것처럼, IoT Hub는 장치 단위 인증 및 액세스 제어를 허용합니다.

다른 프로토콜을 사용하여 보낸 메시지를 인코딩하고 디코딩하는 방법에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

다음 테이블에는 IoT Hub 메시지의 시스템 속성 집합이 나열되어 있습니다.

| 자산 | 설명 | 사용자가 설정 가능한지 여부 |
| --- | --- | --- |
| message-id |사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 길이 128자)입니다. | 예 |
| sequence-number |숫자(디바이스 큐 별로 고유함)는 IoT Hub에서 각 클라우드-디바이스 메시지에 할당됩니다. | C2D 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| to |[클라우드-장치](iot-hub-devguide-c2d-guidance.md) 메시지에 지정된 대상입니다. | C2D 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| absolute-expiry-time |메시지 만료 날짜 및 시간입니다. | 예 |
| iothub-enqueuedtime |IoT Hub에서 [클라우드-장치](iot-hub-devguide-c2d-guidance.md) 메시지를 수신한 날짜 및 시간입니다. | C2D 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| correlation-id |일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. | 예 |
| user-id |메시지의 원본을 지정하는 데 사용되는 ID입니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. | 아닙니다. |
| iothub-ack |피드백 메시지 생성기입니다. 이 속성은 디바이스에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하기 위해 클라우드-디바이스 메시지에서 사용됩니다. 가능한 값: **none**(기본값): 피드백 메시지가 생성되지 않습니다. **positive**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **negative**: 디바이스에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)되면 피드백 메시지를 받습니다. **full**: positive 및 negative 모두입니다. <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | 예 |
| iothub-connection-device-id |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 **deviceId**를 포함합니다. | D2C 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| iothub-connection-auth-generation-id |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 **generationId**를 포함합니다([디바이스 ID 속성](iot-hub-devguide-identity-registry.md#device-identity-properties)당). | D2C 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| iothub-connection-auth-method |IoT Hub에서 디바이스-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 디바이스를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | D2C 메시지의 경우는 사용자가 설정할 수 없고 다른 경우는 사용자가 설정할 수 있습니다. |
| iothub-creation-time-utc | 디바이스에서 메시지를 만든 날짜 및 시간입니다. 디바이스는 명시적으로 이 값을 설정해야 합니다. | 예 |

## <a name="message-size"></a>메시지 크기

IoT Hub는 실제 페이로드만을 고려하여 프로토콜 진단 방식으로 메시지 크기를 측정합니다. 크기(바이트 단위)는 다음의 합계로 계산됩니다.

* 본문 크기(바이트)입니다.
* 모든 메시지 시스템 속성 값의 크기(바이트)입니다.
* 모든 사용자 속성 이름 및 값의 크기(바이트 단위)

속성 이름과 값은 ASCII 문자로 제한되므로 문자열의 길이는 바이트 단위의 크기와 같습니다.

## <a name="anti-spoofing-properties"></a>스푸핑 방지 속성

디바이스-클라우드 메시지에서 스푸핑된 디바이스를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

처음 두 가지는 [디바이스 ID 속성](iot-hub-devguide-identity-registry.md#device-identity-properties)에 따라 원래 디바이스의 **deviceId** 및 **generationId**를 포함합니다.

**iothub-connection-auth-method** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>다음 단계

* IoT Hub의 메시지 크기 제한에 대한 자세한 내용은 [IoT Hub 할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

* 다양한 프로그래밍 언어로 IoT Hub 메시지를 만들고 읽는 방법을 알아보려면 [빠른 시작](quickstart-send-telemetry-node.md)을 참조하세요.
