---
title: Azure IoT 장치 프로 비전 서비스 MQTT 지원 이해 | Microsoft Docs
description: 개발자 가이드-MQTT 프로토콜을 사용 하 여 Azure IoT 장치 프로 비전 서비스 (DPS) 장치 지향 엔드포인트에 연결 하는 장치에 대 한 지원.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973376"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>MQTT 프로토콜을 사용 하 여 DPS와 통신

DPS를 사용 하면 다음을 사용 하 여 장치에서 DPS 장치 엔드포인트과 통신할 수 있습니다.

* 포트 8883의 [Mqtt v 3.1.1](https://mqtt.org/)
* 포트 443의 WebSocket을 통한 [Mqtt v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)

DPS는 전체 기능을 제공 하는 MQTT broker가 아니므로 MQTT v 3.1.1 표준에 지정 된 모든 동작을 지원 하지 않습니다. 이 문서에서는 장치에서 DPS와 통신 하는 데 지원 되는 MQTT 동작을 사용할 수 있는 방법을 설명 합니다.

DPS와의 모든 장치 통신은 TLS/SSL을 사용 하 여 보호 해야 합니다. 따라서 DPS는 포트 1883을 통한 비보안 연결을 지원 하지 않습니다.

 > [!NOTE] 
 > DPS는 현재 MQTT 프로토콜을 통해 TPM [증명 메커니즘](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) 을 사용 하는 장치를 지원 하지 않습니다.

## <a name="connecting-to-dps"></a>DPS에 연결

장치는 MQTT 프로토콜을 사용 하 여 다음 옵션 중 하나를 사용 하 여 DPS에 연결할 수 있습니다.

* [Azure IoT 프로 비전 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)의 라이브러리.
* MQTT 프로토콜 직접.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT 프로토콜 직접 사용(디바이스로)

디바이스가 디바이스 SDK를 사용할 수 없는 경우라도 포트 8883에서 MQTT 프로토콜을 사용하는 공용 디바이스 엔드포인트에 연결할 수 있습니다. **연결** 패킷에서 장치는 다음 값을 사용 해야 합니다.

* **ClientId** 필드의 경우 **registrationId**를 사용 합니다.

* **사용자 이름** 필드에는 `{idScope}/registrations/{registration_id}/api-version=2019-03-31`를 사용 합니다. 여기서 `{idScope}`는 DPS의 [idscope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) 입니다.

* **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTPS 및 AMQP 프로토콜에 대해 동일합니다.

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` resourceURI는 `{idScope}/registrations/{registration_id}`형식 이어야 합니다. 정책 이름은 `registration`이어야 합니다.

  > [!NOTE]
  > X.509 인증서 인증을 사용하는 경우 SAS 토큰 암호는 필요하지 않습니다.

  SAS 토큰을 생성 하는 방법에 대 한 자세한 내용은 [DPS에 대 한 액세스 제어](how-to-control-access.md#security-tokens)의 보안 토큰 섹션을 참조 하십시오.

다음은 DPS 구현 관련 동작 목록입니다.

 * DPS는 **Cleansession** 플래그가 **0**으로 설정 된 기능을 지원 하지 않습니다.

 * 장치 앱이 **QoS 2**를 사용 하 여 토픽을 구독할 때 DPS는 **suback** 패킷에서 최대 QoS level 1을 부여 합니다. 그 후 DPS는 QoS 1을 사용 하 여 장치에 메시지를 배달 합니다.

## <a name="tlsssl-configuration"></a>TLS/SSL 구성

MQTT 프로토콜을 직접 사용 하려면 클라이언트에서 TLS 1.2을 통해 연결 *해야 합니다* . 이 단계를 건너뛰려고 하면 연결 오류가 발생하여 실패합니다.


## <a name="registering-a-device"></a>디바이스 등록

DPS를 통해 장치를 등록 하려면 장치가 **토픽 필터로**`$dps/registrations/res/#`를 사용 하 여 구독 해야 합니다. 토픽 필터에 다중 레벨 와일드카드 `#`는 디바이스가 토픽 이름에 추가 속성을 수신하도록 하려는 경우에만 사용됩니다. DPS는 하위 항목 필터링에 `#` 또는 `?` 와일드 카드를 사용 하도록 허용 하지 않습니다. DPS는 일반적인 용도의 pub-sub 메시징 브로커가 아니므로 문서화 된 토픽 이름 및 토픽 필터만 지원 합니다.

장치는 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}`을 **항목 이름**으로 사용 하 여 DPS에 등록 메시지를 게시 해야 합니다. 페이로드는 JSON 형식의 [장치 등록](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) 개체를 포함 해야 합니다.
성공적인 시나리오에서 장치는 `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 항목 이름에 대 한 응답을 받게 됩니다. 여기서 x는 다시 시도-이후 값 (초)입니다. 응답의 페이로드는 JSON 형식의 [Registrationoperationstatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) 개체를 포함 합니다.

## <a name="polling-for-registration-operation-status"></a>등록 작업 상태 폴링

장치에서 서비스를 주기적으로 폴링하여 장치 등록 작업의 결과를 받아야 합니다. 위에서 설명한 것 처럼 장치가 이미 `$dps/registrations/res/#` 항목을 구독 하 고 있는 것으로 가정 하면 get operationstatus 메시지를 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 토픽 이름에 게시할 수 있습니다. 이 메시지의 작업 ID는 이전 단계의 RegistrationOperationStatus 응답 메시지에서 받은 값 이어야 합니다. 성공적으로 수행 되는 경우 서비스는 `$dps/registrations/res/200/?$rid={request_id}` 항목에서 응답 합니다. 응답의 페이로드에는 RegistrationOperationStatus 개체가 포함 됩니다. 다시 시도 기간과 같은 지연 후 응답 코드가 202 인 경우 장치는 서비스를 계속 폴링합니다. 서비스가 200 상태 코드를 반환 하는 경우 장치 등록 작업이 성공 합니다.

## <a name="connecting-over-websocket"></a>Websocket을 통한 연결
Websocket을 통해 연결 하는 경우 하위 프로토콜을 `mqtt`로 지정 합니다. [RFC 6455](https://tools.ietf.org/html/rfc6455)을 따릅니다.

## <a name="next-steps"></a>다음 단계

MQTT 프로토콜에 대 한 자세한 내용은 [mqtt 설명서](https://mqtt.org/documentation)를 참조 하세요.

DPS의 기능을 추가로 탐색 하려면 다음을 참조 하세요.

* [IoT DPS 정보](about-iot-dps.md)
