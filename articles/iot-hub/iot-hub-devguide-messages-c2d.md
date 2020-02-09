---
title: Azure IoT Hub 클라우드-디바이스 메시징 이해 | Microsoft Docs
description: 이 개발자 가이드에서는 IoT hub에서 클라우드-장치 메시징을 사용 하는 방법을 설명 합니다. 여기에는 메시지 수명 주기 및 구성 옵션에 대 한 정보가 포함 됩니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d4a51a44b48e94669e92a9d525c1b0966df53c18
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964126"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT hub에서 클라우드-장치 메시지 보내기

솔루션 백 엔드에서 장치 앱에 단방향 알림을 보내려면 IoT hub에서 장치로 클라우드-장치 메시지를 보냅니다. Azure IoT Hub에서 지 원하는 다른 클라우드-장치 옵션에 대 한 설명은 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조 하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

서비스 지향 엔드포인트 ( */messages/devicebound*)을 통해 클라우드-장치 메시지를 보냅니다. 장치는 장치 특정 엔드포인트 ( */devices/{deviceId}/messages/devicebound*)을 통해 메시지를 받습니다.

단일 장치에서 각 클라우드-장치 메시지를 대상으로 하도록 IoT hub는 **to** 속성을 */devices/{deviceId}/messages/devicebound*로 설정 합니다.

각 장치 큐는 최대 50 개의 클라우드-장치 메시지를 보유 합니다. 동일한 장치에 더 많은 메시지를 보내려고 시도 하면 오류가 발생 합니다.

## <a name="the-cloud-to-device-message-life-cycle"></a>클라우드-장치 메시지 수명 주기

메시지 배달을 한 번 이상 보장 하기 위해 IoT hub는 장치 별 큐에 클라우드-장치 메시지를 유지 합니다. IoT hub가 큐에서 메시지를 제거 하려면 장치가 *완료*를 명시적으로 승인 해야 합니다. 이 방법은 연결 및 디바이스 오류로부터 복원력을 보장합니다.

수명 주기 상태 그래프가 다음 다이어그램에 표시 됩니다.

![클라우드-장치 메시지 수명 주기](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT hub 서비스는 장치에 메시지를 보내면 서비스는 메시지 상태를 *큐*에 넣은 상태로 설정 합니다. 장치에서 메시지를 *수신* 하려고 하면 IoT hub는 상태를 *숨김*으로 설정 하 여 메시지를 *잠급니다* . 이 상태를 통해 장치의 다른 스레드가 다른 메시지 수신을 시작할 수 있습니다. 장치 스레드가 메시지 처리를 완료 하면 메시지를 *완료* 하 여 IoT hub에 알립니다. 그런 다음 IoT hub는 상태를 *완료*로 설정 합니다.

또한 디바이스는 다음을 수행할 수 있습니다.

* 메시지를 *거부* 합니다. 그러면 IoT Hub가 *배달 못* 한 편지 상태로 설정 됩니다. MQTT (메시지 큐 원격 분석 전송) 프로토콜을 통해 연결 하는 장치는 클라우드-장치 메시지를 거부할 수 없습니다.

* 메시지를 *중단* 하면 IoT hub는 상태를 큐에 넣은 상태로 설정 하 여 메시지를 큐에 다시 넣습니다. MQTT 프로토콜을 통해 연결 하는 장치는 클라우드-장치 메시지를 중단할 수 없습니다.

스레드가 IoT hub에 알리지 않고 메시지를 처리 하지 못할 수 있습니다. 이 경우 표시 제한 시간 (또는 *잠금* 제한 시간) 후 메시지가 *표시* 되지 *않는* 상태에서 *큐* 에 넣은 상태로 자동 전환 됩니다. 이 제한 시간 값은 1 분 이며 변경할 수 없습니다.

IoT hub의 **최대 배달 횟수** 속성은 *큐* 에 대기 중인 상태와 *보이지 않는* 상태 간에 메시지가 전환 될 수 있는 최대 횟수를 결정 합니다. 전환 횟수가 지나면 IoT hub는 메시지의 상태를 *배달 못 한 문자로*설정 합니다. 마찬가지로, IoT hub는 만료 시간 이후에 메시지의 상태를 *배달 못 한 문자로* 설정 합니다. 자세한 내용은 ttl ( [Time to live](#message-expiration-time-to-live))을 참조 하세요.

[IoT Hub를 사용 하 여 클라우드-장치 메시지를 보내는 방법](iot-hub-csharp-csharp-c2d.md) 문서에서는 클라우드에서 클라우드-장치 메시지를 보내고 장치에서 수신 하는 방법을 보여 줍니다.

일반적으로 장치는 메시지 손실이 응용 프로그램 논리에 영향을 주지 않는 경우 클라우드-장치 메시지를 완료 합니다. 이에 대 한 예는 장치가 메시지 콘텐츠를 로컬로 유지 하거나 작업을 성공적으로 실행 한 경우에 발생할 수 있습니다. 메시지는 응용 프로그램의 기능에 영향을 주지 않는 임시 정보를 전달할 수도 있습니다. 경우에 따라 장기 실행 작업에 다음을 수행할 수 있습니다.

* 장치가 로컬 저장소에 작업 설명을 보관 한 후 클라우드-장치 메시지를 완료 합니다.

* 작업이 진행되는 다양한 단계에서 하나 이상의 디바이스-클라우드 메시지를 사용하여 솔루션 백 엔드에 알림을 제공할 수 있습니다.

## <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))

모든 클라우드-디바이스 메시지에는 만료 시간이 있습니다. 이 시간은 다음 중 하나로 설정 됩니다.

* 서비스의 **ExpiryTimeUtc** 속성
* Iot hub는 IoT hub 속성으로 지정 된 기본 ttl ( *time to live* )을 사용 합니다.

[클라우드-디바이스 구성 옵션](#cloud-to-device-configuration-options)을 참조하세요.

메시지 만료를 활용 하 여 연결이 끊어진 장치에 메시지를 보내지 않도록 하는 일반적인 방법은 짧은 *시간을 라이브 값으로* 설정 하는 것입니다. 이 방법은 장치 연결 상태를 유지 관리 하는 것과 동일한 결과를 얻을 수 있지만 더 효율적입니다. 메시지 승인을 요청 하면 IoT hub는 다음 장치를 알립니다.

* 메시지를 받을 수 없는 경우
* 온라인 상태가 아니거나 실패한 경우

## <a name="message-feedback"></a>메시지 피드백

클라우드-장치 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대 한 메시지당 피드백 배달을 요청할 수 있습니다. 이렇게 하려면 다음 4 개 값 중 하나로 전송 되는 클라우드-장치 메시지에서 **iothub-ack** 응용 프로그램 속성을 설정 합니다.

| Ack 속성 값 | 동작 |
| ------------ | -------- |
| 없음     | IoT hub가 피드백 메시지를 생성 하지 않습니다 (기본 동작). |
| 긍정 | 클라우드-장치 메시지가 *완료* 된 상태에 도달 하면 IoT hub가 피드백 메시지를 생성 합니다. |
| 양수 | 클라우드-장치 메시지가 *배달 못* 한 메시지 상태에 도달 하면 IoT hub가 피드백 메시지를 생성 합니다. |
| 전체     | IoT hub는 어떤 경우 든 피드백 메시지를 생성 합니다. |

**Ack** 값이 *full*인 경우 피드백 메시지를 받지 못하면 피드백 메시지가 만료 되었음을 의미 합니다. 서비스는 원본 메시지에서 발생한 상황을 알지 못합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 최대 만료 시간은 2 일 이며, 오류가 발생 하는 경우 서비스를 다시 실행 하는 시간을 유지 합니다.

[엔드포인트](iot-hub-devguide-endpoints.md)에 설명 된 대로 IoT hub는 서비스 지향 엔드포인트 ( */messages/servicebound/feedback*)을 통해 피드백을 메시지로 전달 합니다. 피드백 수신을 위한 의미 체계는 클라우드-디바이스 메시지의 경우와 같습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

| 속성     | Description |
| ------------ | ----------- |
| EnqueuedTime | 허브에서 피드백 메시지를 받은 시간을 나타내는 타임 스탬프입니다. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성           | Description |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 메시지의 결과가 발생 한 시간을 나타내는 타임 스탬프입니다. 예를 들어 허브에서 피드백 메시지를 받았거나 원래 메시지는 만료 됩니다. |
| OriginalMessageId  | 이 피드백 정보가 관련 된 클라우드-장치 메시지의 *MessageId* |
| StatusCode         | IoT hub에서 생성 된 피드백 메시지에 사용 되는 필수 문자열: <br/> *성공* <br/> *종료* <br/> *DeliveryCountExceeded* <br/> *되었으므로* <br/> *삭제* |
| 설명        | *StatusCode* 에 대 한 문자열 값 |
| DeviceID           | 이 피드백 부분이 관련 된 클라우드-장치 메시지의 대상 장치에 대 한 *DeviceId* 입니다. |
| DeviceGenerationId | 이 의견의 일부가 관련 된 클라우드-장치 메시지의 대상 장치에 대 한 *DeviceGenerationId* 입니다. |

클라우드-장치 메시지에서 해당 피드백과 원래 메시지의 상관 관계를 지정 하려면 서비스에서 *MessageId*를 지정 해야 합니다.

피드백 메시지의 본문은 다음 코드에 나와 있습니다.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**삭제 된 장치에 대 한 보류 중인 피드백**

장치를 삭제 하면 보류 중인 피드백도 모두 삭제 됩니다. 장치 피드백이 일괄 처리로 전송 됩니다. 장치가 메시지 수신을 확인 하 고 다음 피드백 배치가 준비 될 때까지 좁은 기간 (종종 1 초 미만) 내에 장치를 삭제 하는 경우 피드백은 발생 하지 않습니다.

장치를 삭제 하기 전에 보류 중인 피드백이 도착할 때까지 대기 하 여이 동작을 처리할 수 있습니다. 장치가 삭제 되 면 관련 메시지 피드백이 손실 되는 것으로 간주 됩니다.

## <a name="cloud-to-device-configuration-options"></a>클라우드-디바이스 구성 옵션

각 IoT Hub는 클라우드-디바이스 메시징에 다음 구성 옵션을 노출합니다.

| 속성                  | 설명 | 범위 및 기본값 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 클라우드-장치 메시지에 대 한 기본 TTL | 최대 2 일 (최소 1 분) ISO_8601 간격 기본 1시간 |
| maxDeliveryCount          | 클라우드-장치 단위 큐의 최대 배달 횟수 | 1 ~ 100; 기본 10 |
| feedback.ttlAsIso8601     | 서비스 바인딩된 피드백 메시지의 보존 | 최대 2 일 (최소 1 분) ISO_8601 간격 기본 1시간 |
| feedback.maxDeliveryCount | 피드백 큐의 최대 배달 횟수 | 1 ~ 100; 기본 100 |

이러한 구성 옵션을 설정하는 방법에 대한 자세한 내용은 [IoT Hub 만들기](iot-hub-create-through-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

클라우드-장치 메시지를 수신 하는 데 사용할 수 있는 Sdk에 대 한 자세한 내용은 [Azure IoT sdk](iot-hub-devguide-sdks.md)를 참조 하세요.

클라우드-디바이스 메시지를 수신하려면 [클라우드-디바이스 보내기](iot-hub-csharp-csharp-c2d.md) 자습서를 참조하세요.
