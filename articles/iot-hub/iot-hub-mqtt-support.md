---
title: Azure IoT Hub MQTT 지원 이해 | Microsoft Docs
description: 개발자 가이드 - MQTT 프로토콜을 사용하여 IoT Hub 디바이스 지향 엔드포인트에 연결하는 디바이스를 지원합니다. Azure IoT 디바이스 SDK의 기본 제공 MQTT 지원에 대한 정보를 포함합니다.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: rezas
ms.openlocfilehash: 1a0b6cf8ce272733c259283fdec9c215ac2b0fd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442565"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT 프로토콜을 사용하여 IoT 허브와 통신

IoT Hub를 사용하면 다음을 사용하여 IoT Hub 디바이스 엔드포인트와 통신할 수 있습니다.

* [MQTT v3.1.1](https://mqtt.org/) 포트 8883에서
* 포트 443에서 WebSocket을 통해 MQTT v3.1.1

IoT Hub는 모든 기능을 갖춘 MQTT broker가 아니며 MQTT v3.1.1 표준에 지정된 모든 동작을 지원하지는 않습니다. 이 문서에서는 디바이스에서 지원되는 MQTT 동작을 사용하여 IoT Hub와 통신하는 방법을 설명합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub와 통신하는 모든 디바이스는 TLS/SSL을 사용하여 보호되어야 합니다. 따라서 IoT Hub는 1883 포트를 통한 비보안 연결을 지원하지 않습니다.

## <a name="connecting-to-iot-hub"></a>IoT Hub에 연결

장치는 MQTT 프로토콜을 사용 하 여 다음 옵션 중 하나를 사용 하 여 IoT hub에 연결할 수 있습니다.

* 라이브러리에는 [Azure IoT Sdk](https://github.com/Azure/azure-iot-sdks)합니다.
* MQTT 프로토콜 직접 사용 합니다.

## <a name="using-the-device-sdks"></a>디바이스 SDK 사용

[장치 Sdk](https://github.com/Azure/azure-iot-sdks) 지 수 있는 MQTT 프로토콜에 Java, Node.js, C, 사용할 수 있는 C#, 및 Python입니다. 디바이스 SDK는 표준 IoT Hub 연결 문자열을 사용하여 IoT Hub에 대한 연결을 설정합니다. MQTT 프로토콜을 사용하려면 클라이언트 프로토콜 매개 변수를 **MQTT**에 설정해야 합니다. 기본적으로는 디바이스 SDK는 **CleanSession** 플래그가 **0**으로 설정된 IoT Hub에 연결되고 **QoS 1**을 사용하여 IoT Hub와 메시지를 교환합니다.

디바이스가 IoT Hub에 연결되면 디바이스 SDK는 IoT Hub와 메시지를 교환할 수 있게 하는 메서드를 제공합니다.

다음 테이블에는 지원되는 각 언어에 대한 코드 샘플에 대 한 링크가 있고 MQTT 프로토콜을 사용하여 IoT Hub에 연결하는 데 사용하는 매개 변수를 지정합니다.

| 언어 | 프로토콜 매개 변수 |
| --- | --- |
| [Node.JS](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>디바이스 앱을 AMQP에서 MQTT로 마이그레이션

사용 중인 경우는 [장치 Sdk](https://github.com/Azure/azure-iot-sdks), AMQP 사용에서 MQTT로 전환 하려면 앞서 설명한 것 처럼 클라이언트 초기화에서 프로토콜 매개 변수를 변경 해야 합니다.

이렇게 할 경우 다음 항목을 확인해야 합니다.

* AMQP는 많은 조건에 대한 오류를 반환하는 한편 MQTT는 연결을 종료합니다. 결과적으로 예외 처리 논리를 일부 변경해야 합니다.

* MQTT 지원 하지 않습니다 합니다 *거부* 받으면 operations [클라우드-장치 메시지](iot-hub-devguide-messaging.md)합니다. 백 엔드 앱을 장치 앱에서 응답을 수신 하는 경우 사용을 고려 [직접 메서드](iot-hub-devguide-direct-methods.md)합니다.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT 프로토콜 직접 사용(디바이스로)

디바이스가 디바이스 SDK를 사용할 수 없는 경우라도 포트 8883에서 MQTT 프로토콜을 사용하는 공용 디바이스 엔드포인트에 연결할 수 있습니다. 에 **CONNECT** 패킷의 경우 다음 값을 사용 해야 하는 장치:

* **ClientId** 필드에 **deviceId**를 사용합니다.

* **Username** 필드에 `{iothubhostname}/{device_id}/?api-version=2018-06-30`를 사용합니다. 여기서 `{iothubhostname}`은 IoT Hub의 전체 CName입니다.

    예를 들어, IoT Hub의 이름이 **contoso.azure devices.net**이고 디바이스의 이름이 **MyDevice01**이면 전체 **Username** 필드에 다음이 포함되어야 합니다.

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTPS 및 AMQP 프로토콜에 대해 동일합니다.

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509 인증서 인증을 사용하는 경우 SAS 토큰 암호는 필요하지 않습니다. 자세한 내용은 참조 하세요. [Azure IoT Hub의 X.509 보안 설정](iot-hub-security-x509-get-started.md)

  SAS 토큰을 생성 하는 방법에 대 한 자세한 내용은의 장치 섹션을 참조 하세요 [를 사용 하 여 IoT Hub 보안 토큰](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)합니다.

  테스트할 때 사용할 수도 있습니다는 플랫폼 간 [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 또는 [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) 신속 하 게 복사 하 고 자체 코드에 붙여 넣을 수 있는 SAS 토큰을 생성 하는 도구:

Azure IoT Tools:

1. Visual Studio Code의 왼쪽 아래 모퉁이에 있는 **AZURE IOT HUB 디바이스** 탭을 확장합니다.
  
2. 디바이스를 마우스 오른쪽 단추로 클릭하고 **디바이스에 대한 SAS 토큰 생성**을 선택합니다.
  
3. **만료 시간**을 설정하고 'Enter' 키를 누릅니다.
  
4. SAS 토큰이 생성되어 클립보드에 복사됩니다.

Device Explorer의 경우

1. **디바이스 탐색기**의 **관리** 탭으로 이동합니다.

2. **SAS 토큰** (오른쪽 위)을 클릭합니다.

3. **SASTokenForm**의 **DeviceID** 드롭다운에서 디바이스를 선택합니다. **TTL**을 설정합니다.

4. **생성** 을 클릭하여 토큰을 만듭니다.

   생성되는 SAS 토큰은 다음 구조를 갖습니다.

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   이 토큰에서 MQTT를 사용하여 연결할 때 **암호** 필드에서와 같이 사용하는 부분은 다음과 같습니다.

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT 연결 및 분리 패킷의 경우, IoT Hub는 **작업 모니터링** 채널의 이벤트를 발행합니다. 이 이벤트에에는 연결 문제 해결에 도움이 되는 추가 정보가 있습니다.

디바이스 앱은 **CONNECT** 패킷에 **Will** 메시지를 지정할 수 있습니다. 디바이스 앱은 `devices/{device_id}/messages/events/` 또는 `devices/{device_id}/messages/events/{property_bag}`를 **Will** 항목 이름으로 사용하여 원격 분석 메시지로서 전달할 **Will** 메시지를 정의할 수 있습니다. 이 경우 네트워크 연결이 닫혀 있지만 **DISCONNECT** 패킷이 이전에 디바이스에서 수신되지 않은 경우 IoT Hub는 **CONNECT** 패킷에 제공된 **Will** 메시지를 원격 분석 채널로 전송합니다. 원격 분석 채널은 기본 **이벤트** 엔드포인트 또는 IoT Hub 라우팅으로 정의되는 사용자 지정 엔드포인트일 수 있습니다. 메시지에는 **Will** 값이 할당된 **iothub MessageType** 속성이 지정됩니다.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>MQTT 프로토콜 직접 사용(모듈로)

모듈 ID를 사용하여 MQTT를 통해 IoT Hub에 연결하는 작업은 디바이스([위](#using-the-mqtt-protocol-directly-as-a-device)에서 설명됨)와 비슷하지만 다음을 사용해야 합니다.

* 클라이언트 ID를 `{device_id}/{module_id}`로 설정합니다.

* 사용자 이름 과 암호를 사용하여 인증하는 경우 사용자 이름을 `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30`으로 설정하고 모듈 ID와 연결된 SAS 토큰을 암호로 사용합니다.

* 원격 분석 결과를 게시하려면 `devices/{device_id}/modules/{module_id}/messages/events/`를 토픽으로 사용합니다.

* `devices/{device_id}/modules/{module_id}/messages/events/`를 Will 토픽으로 사용합니다.

* GET 및 PATCH 쌍 토픽은 모듈과 디바이스에 대해 동일합니다.

* 쌍 상태 토픽은 모듈과 디바이스에 대해 동일합니다.

### <a name="tlsssl-configuration"></a>TLS/SSL 구성

MQTT 프로토콜을 직접 사용하려면 클라이언트가 *반드시* TLS/SSL를 통해 연결되어야 합니다. 이 단계를 건너뛰려고 하면 연결 오류가 발생하여 실패합니다.

TLS 연결을 설정하려면 DigiCert Baltimore 루트 인증서를 다운로드하여 참조해야 할 수 있습니다. 이 인증서는 Azure가 연결 보호에 사용합니다. 이 인증서를 찾을 수 있습니다 합니다 [Azure iot-sdk c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) 리포지토리. 이러한 인증서에 대 한 자세한 내용은에서 확인할 수 있습니다 [Digicert의 웹 사이트](https://www.digicert.com/digicert-root-certificates.htm)합니다.

Python 버전을 사용 하 여 구현 하는 방법의 예는 [Paho MQTT 라이브러리](https://pypi.python.org/pypi/paho-mqtt) Eclipse Foundation에서 다음과 같이 표시 될 수 있습니다.

먼저, 명령줄 환경에서 Paho 라이브러리를 설치합니다.

```cmd/sh
pip install paho-mqtt
```

그런 다음, Python 스크립트로 클라이언트를 구현합니다. 다음과 같이 자리 표시자를 바꿉니다.

* `<local path to digicert.cer>`는 DigiCert Baltimore Root 인증서가 들어 있는 로컬 파일의 경로입니다. C에 대한 Azure IoT SDK에서 [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c)의 인증서 정보를 복사하여 이 파일을 만들 수 있습니다. `-----BEGIN CERTIFICATE-----` 및 `-----END CERTIFICATE-----` 줄을 포함하고 모든 줄의 시작과 끝에서 `"` 표시를 제거한 다음, 모든 줄의 마지막에서 `\r\n` 문자를 제거합니다.

* `<device id from device registry>` IoT Hub에 추가된 디바이스의 ID입니다.

* `<generated SAS token>`은 이 문서의 앞에서 설명한 디바이스에 대한 SAS 토큰입니다.

* `<iot hub name>`은 IoT Hub의 이름입니다.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>디바이스-클라우드 메시지 보내기

성공적인 연결을 구축한 후 디바이스는 `devices/{device_id}/messages/events/` 또는 `devices/{device_id}/messages/events/{property_bag}`를 **토픽 이름**으로 사용하여 IoT Hub에 메시지를 보낼 수 있습니다. `{property_bag}` 요소는 URL 인코딩 형식의 속성을 추가하여 메시지를 보내는 디바이스를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 이 `{property_bag}` 요소는 HTTPS 프로토콜의 쿼리 문자열로 동일한 인코딩을 사용 합니다.

다음은 IoT Hub 구현 관련 동작의 목록입니다.

* IoT Hub에서는 QoS 2 메시지를 지원하지 않습니다. 디바이스 앱이 **QoS 2**의 메시지를 게시하는 경우, IoT Hub는 네트워크 연결을 닫습니다.

* IoT Hub에서는 보관 메시지가 지속되지 않습니다. 장치가 **RETAIN** 플래그가 1로 설정된 메시지를 게시하는 경우, IoT Hub는 **x-opt-retain** 애플리케이션 속성을 메시지에 추가합니다. 이 경우에 IoT Hub는 보관 메시지를 유지하지 않고 백 엔드 앱에 전달합니다.

* IoT Hub는 디바이스 당 하나의 활성 MQTT 연결만을 지원합니다. 동일한 디바이스 ID를 대신하는 모든 새 MQTT 연결로 인해 IoT Hub가 기존 연결을 삭제하게 됩니다.

자세한 내용은 [메시징 개발자 가이드](iot-hub-devguide-messaging.md)합니다.

### <a name="receiving-cloud-to-device-messages"></a>클라우드-장치 메시지 수신

IoT Hub에서 메시지를 수신하려면 디바이스는 `devices/{device_id}/messages/devicebound/#`을 **토픽 필터**로 사용하여 구독해야 합니다. 토픽 필터에 다중 레벨 와일드카드 `#`는 디바이스가 토픽 이름에 추가 속성을 수신하도록 하려는 경우에만 사용됩니다. IoT Hub는 하위 토픽의 필터링을 위한 `#` 또는 `?` 와일드카드의 사용을 허용하지 않습니다. IoT Hub는 범용 발행-구독 메시징 브로커가 아니므로 문서화된 토픽 이름 및 토픽 필터만 지원합니다.

디바이스는 `devices/{device_id}/messages/devicebound/#` 항목 필터로 표시되는 디바이스 특정 엔드포인트를 성공적으로 구독하기 전에는 IoT Hub로부터 어떠한 메시지도 수신하지 않습니다 구독이 설정된 후에는 디바이스가 구독 시간 이후 전송된 클라우드-디바이스 메시지를 수신합니다. 디바이스가 **CleanSession** 플래그가 **0**으로 설정되어 연결되면 다양한 세션 간에 구독이 유지됩니다. 이 경우 다음 번에 디바이스가 **CleanSession 0**으로 연결될 때, 연결되지 않은 동안 보내진 미해결 메시지를 수신하게 됩니다. 디바이스가 **1**로 설정된 **CleanSession** 플래그를 사용하는 경우 디바이스-엔드포인트를 구독할 때까지 IoT Hub에서 어떠한 메시지도 수신하지 않습니다.

IoT Hub는 메시지 속성이 있는 경우 **토픽 이름** `devices/{device_id}/messages/devicebound/`, 또는 `devices/{device_id}/messages/devicebound/{property_bag}`와 함께 메시지를 배달합니다. `{property_bag}` 에는 메시지 속성의 URL 인코딩된 키/값 쌍이 있습니다. 애플리케이션 속성 및 사용자 설정 가능 시스템 속성(예: **messageId** 또는 **correlationId**)만 속성 모음에 포함됩니다. 시스템 속성 이름에는 접두사 **$** 가 있고, 애플리케이션 속성은 접두사가 없는 원래 속성 이름을 사용합니다.

디바이스 앱이 **QoS 2**의 토픽을 구독하는 경우, IoT Hub는 **SUBACK** 패킷에서 최대 QoS level 1을 부여합니다. 그런 다음 IoT Hub는 메시지를 QoS 1을 사용하는 디바이스에 전달합니다.

### <a name="retrieving-a-device-twins-properties"></a>디바이스 쌍 속성 검색

먼저 작업의 응답을 수신하기 위해 디바이스가 `$iothub/twin/res/#`을 구독합니다. 그런 다음 **요청 ID**에 채워진 값을 사용하여 빈 메시지를 `$iothub/twin/GET/?$rid={request id}` 항목에 보냅니다. 그러면 서비스는 요청과 동일한 **요청 ID**를 사용하여 `$iothub/twin/res/{status}/?$rid={request id}` 항목에 대한 디바이스 쌍 데이터를 포함하는 응답 메시지를 보냅니다.

요청 ID 별로 메시지 속성 값에 대 한 유효한 값을 수 있습니다 [IoT Hub 메시징 개발자 가이드](iot-hub-devguide-messaging.md), 이며 status는 정수로 확인 됩니다.

응답 본문에는 다음 응답 예제와 같이 디바이스 쌍의 속성 섹션이 포함되어 있습니다.

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

가능한 상태 코드:

|상태 | 설명 |
| ----- | ----------- |
| 204 | 성공(반환되는 콘텐츠 없음) |
| 429 | 너무 많은 요청 (제한)를 기준으로 [IoT Hub 제한](iot-hub-devguide-quotas-throttling.md) |
| 5** | 서버 오류 |

자세한 내용은 [장치 쌍 개발자 가이드](iot-hub-devguide-device-twins.md)합니다.

### <a name="update-device-twins-reported-properties"></a>디바이스 쌍의 reported 속성 업데이트

reported 속성을 업데이트하기 위해 디바이스는 지정된 MQTT 토픽에서 게시를 통해 IoT Hub에 요청을 발급합니다. IoT Hub는 요청을 처리한 후에 다른 항목에 대한 게시를 통해 업데이트 작업의 성공 또는 실패 상태를 응답합니다. 해당 쌍 업데이트 요청의 결과에 대해 알리기 위해 디바이스에서 이 토픽을 구독할 수 있습니다. MQTT에서 이 형식의 요청/응답 상호 작용을 구현하려면 해당 업데이트 요청의 디바이스에서 처음에 제공한 요청 ID(`$rid`)의 개념을 활용합니다. 이 요청 ID는 IoT Hub의 응답에도 포함되어 디바이스가 이전의 특정 요청에 대한 응답을 상호 연결하도록 합니다.

다음 시퀀스에서는 디바이스가 IoT Hub의 디바이스 쌍에서 보고된 속성을 업데이트하는 방법을 설명합니다.

1. 디바이스는 먼저 `$iothub/twin/res/#` 항목을 구독하여 IoT Hub에서 작업의 응답을 수신해야 합니다.

2. 디바이스는 디바이스 쌍 업데이트를 포함하는 메시지를 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 항목에 전송합니다. 이 메시지는 **요청 ID** 값을 포함합니다.

3. 그러면 서비스에서는 항목 `$iothub/twin/res/{status}/?$rid={request id}`에 대해 보고된 속성 컬렉션의 새 ETag 값을 포함하는 응답 메시지를 보냅니다. 이 응답 메시지는 동일한 **요청 ID**를 요청으로 사용합니다.

요청 메시지 본문은 보고된 속성에 대한 새 값을 포함하는 JSON 문서를 포함합니다. JSON 문서의 각 구성원은 디바이스 쌍의 문서에 있는 해당 구성원을 업데이트하거나 추가합니다. `null`로 설정된 구성원은 포함하는 개체에서 구성원을 삭제합니다. 예를 들면 다음과 같습니다.

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

가능한 상태 코드:

|상태 | 설명 |
| ----- | ----------- |
| 200 | 성공 |
| 400 | 잘못된 요청. 형식이 잘못된 JSON |
| 429 | 너무 많은 요청 (제한)를 기준으로 [IoT Hub 제한](iot-hub-devguide-quotas-throttling.md) |
| 5** | 서버 오류 |

아래의 Python 코드 조각은 MQTT(Paho MQTT 클라이언트 사용)를 통한 쌍 reported 속성 업데이트 프로세스를 보여줍니다.

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

위의 쌍 reported 속성 업데이트 작업이 성공하면 IoT Hub의 게시 메시지에는 다음 토픽이 포함됩니다. `$iothub/twin/res/204/?$rid=1&$version=6` 여기서 `204`는 성공을 나타내는 상태 코드이고, `$rid=1`은 해당 코드에서 디바이스에 의해 제공된 요청 ID에 해당하고, `$version`은 업데이트 후에 디바이스 쌍의 reported 속성 섹션 버전에 해당합니다.

자세한 내용은 [장치 쌍 개발자 가이드](iot-hub-devguide-device-twins.md)합니다.

### <a name="receiving-desired-properties-update-notifications"></a>desired 속성 업데이트 알림 수신

디바이스가 연결되면 IoT Hub는 `$iothub/twin/PATCH/properties/desired/?$version={new version}` 항목에 알림을 보내는데 여기에는 솔루션 백 엔드에 의해 수행된 업데이트 콘텐츠가 포함됩니다. 예를 들면 다음과 같습니다.

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

속성 업데이트의 경우 `null` 값은 JSON 개체 구성원이 삭제되고 있음을 의미합니다. 또한 `$version`은 쌍에 포함된 원하는 속성 섹션의 새 버전을 나타냅니다.

> [!IMPORTANT]
> IoT Hub는 디바이스가 연결된 경우에만 변경 알림을 생성하여 구현 해야 합니다 [장치 다시 연결 흐름](iot-hub-devguide-device-twins.md#device-reconnection-flow) 는 desired 속성이 IoT Hub 및 장치 앱 간에 동기화 되도록 합니다.

자세한 내용은 [장치 쌍 개발자 가이드](iot-hub-devguide-device-twins.md)합니다.

### <a name="respond-to-a-direct-method"></a>직접 메서드에 응답

먼저 디바이스가 `$iothub/methods/POST/#`을 구독해야 합니다. IoT Hub는 `$iothub/methods/POST/{method name}/?$rid={request id}` 항목에 유효한 JSON 또는 빈 본문으로 메서드 요청을 보냅니다.

응답하기 위해 디바이스는 올바른 JSON 또는 빈 본문이 있는 메시지를 `$iothub/methods/res/{status}/?$rid={request id}` 토픽에 보냅니다. 이 메시지에서 **요청 ID**는 요청 메시지의 것과 일치하고 **상태**는 정수여야 합니다.

자세한 내용은 [직접 메서드 개발자 가이드](iot-hub-devguide-direct-methods.md)합니다.

### <a name="additional-considerations"></a>추가 고려 사항

최종 고려 사항으로 클라우드 측에서 MQTT 프로토콜 동작을 사용자 지정 하는 경우 검토 해야 합니다 [Azure IoT 프로토콜 게이트웨이](iot-hub-protocol-gateway.md)합니다. 이 소프트웨어를 통해 IoT Hub와 직접 인터페이스되는 고성능 사용자 지정 프로토콜을 배포할 수 있습니다. Azure IoT 프로토콜 게이트웨이를 사용하면 brownfield MQTT 배포를 수용하는 디바이스 프로토콜 또는 기타 사용자 지정 프로토콜을 사용자 지정할 수 있습니다. 그렇지만 이 방법을 사용하는 경우 사용자 지정 프로토콜 게이트웨이를 실행하고 작동해야 합니다.

## <a name="next-steps"></a>다음 단계

MQTT 프로토콜에 대 한 자세한 내용은 참조는 [MQTT 설명서](https://mqtt.org/documentation)합니다.

IoT Hub 배포를 계획하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [IoT용 Azure Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)
* [추가 프로토콜 지원](iot-hub-protocol-gateway.md)
* [Event Hubs와 비교](iot-hub-compare-event-hubs.md)
* [크기 조정, HA 및 DR](iot-hub-scaling.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
