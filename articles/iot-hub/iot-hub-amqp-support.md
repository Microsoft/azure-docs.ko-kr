---
title: AMQP 지원 Azure IoT Hub 이해 | Microsoft Docs
description: 개발자 가이드-AMQP 프로토콜을 사용 하 여 장치 지향 및 서비스 지향 엔드포인트 IoT Hub 연결 하는 장치에 대 한 지원입니다. Azure IoT 장치 Sdk의 기본 제공 AMQP 지원에 대 한 정보를 포함 합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286643"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>AMQP 프로토콜을 사용 하 여 IoT hub와 통신

Azure IoT Hub는 [AMQP (OASIS 고급 메시지 큐 프로토콜) 버전 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) 을 지원 하 여 장치 지향 및 서비스 지향 엔드포인트을 통해 다양 한 기능을 제공 합니다. 이 문서에서는 AMQP 클라이언트를 사용 하 여 IoT Hub 기능을 사용 하는 IoT hub에 연결 하는 방법을 설명 합니다.

## <a name="service-client"></a>서비스 클라이언트

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>IoT hub에 연결 및 인증 (서비스 클라이언트)

AMQP를 사용 하 여 IoT hub에 연결 하기 위해 클라이언트는 CBS ( [클레임 기반 보안](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ) 또는 [SASL (SASL(Simple Authentication and Security Layer)) 인증](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)을 사용할 수 있습니다.

서비스 클라이언트에는 다음과 같은 정보가 필요 합니다.

| 정보 | 값 |
|-------------|--------------|
| IoT hub 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 키 이름 | `service` |
| 액세스 키 | 서비스와 연결 된 기본 키 또는 보조 키 |
| 공유 액세스 서명 | @No__t-0 형식의 수명이 짧은 공유 액세스 서명입니다. 이 서명을 생성 하는 코드를 가져오려면 [IoT Hub에 대 한 액세스 제어](./iot-hub-devguide-security.md#security-token-structure)를 참조 하세요.

다음 코드 조각은 [Python의 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python) 를 사용 하 여 발신자 링크를 통해 IoT hub에 연결 합니다.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>클라우드-장치 메시지 호출 (서비스 클라이언트)

서비스와 IoT hub 간의 클라우드-장치 메시지 교환 및 장치와 IoT hub 간의 클라우드-장치 메시지 교환에 대해 알아보려면 [iot hub에서 클라우드-장치 메시지 보내기](iot-hub-devguide-messages-c2d.md)를 참조 하세요. 다음 표에서 설명 하는 것 처럼 서비스 클라이언트는 두 개의 링크를 사용 하 여 메시지를 보내고 이전에 보낸 메시지에 대 한 피드백을 수신 합니다.

| 만든 사람 | 링크 형식 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 서비스 | 보낸 사람 링크 | `/messages/devicebound` | 장치를 대상으로 하는 클라우드-장치 메시지는 서비스에 의해이 링크에 전송 됩니다. 이 링크를 통해 전송 되는 메시지의 `To` 속성은 대상 장치의 받는 사람 링크 경로 (`/devices/<deviceID>/messages/devicebound`)로 설정 됩니다. |
| 서비스 | 받는 사람 링크 | `/messages/serviceBound/feedback` | 서비스에서이 링크에 수신 된 장치에서 제공 하는 완료, 거부 및 중단 피드백 메시지입니다. 피드백 메시지에 대 한 자세한 내용은 [IoT hub에서 클라우드-장치 메시지 보내기](./iot-hub-devguide-messages-c2d.md#message-feedback)를 참조 하세요. |

다음 코드 조각에서는 [Python에서 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용 하 여 클라우드-장치 메시지를 만들고이를 장치로 보내는 방법을 보여 줍니다.

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

사용자 의견을 받으려면 서비스 클라이언트에서 받는 사람 링크를 만듭니다. 다음 코드 조각에서는 [Python에서 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용 하 여 링크를 만드는 방법을 보여 줍니다.

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

위의 코드에 표시 된 것 처럼 클라우드-장치 피드백 메시지에는 *application/vnd*의 콘텐츠 형식이 있습니다. 메시지의 JSON 본문에서 속성을 사용 하 여 원본 메시지의 배달 상태를 유추할 수 있습니다.

* 피드백 본문의 키 `statusCode`은 다음 값 중 하나입니다. *성공*, *만료 됨*, *DeliveryCountExceeded*, *거부 됨*또는 *제거*됨입니다.

* 피드백 본문의 키 `deviceId`에는 대상 장치의 ID가 있습니다.

* 피드백 본문의 키 `originalMessageId`에는 서비스에서 보낸 원래 클라우드-장치 메시지의 ID가 있습니다. 이 배달 상태를 사용 하 여 피드백을 클라우드-장치 메시지와 상호 연결할 수 있습니다.

### <a name="receive-telemetry-messages-service-client"></a>원격 분석 메시지 받기 (서비스 클라이언트)

기본적으로 IoT hub는 수집 장치 원격 분석 메시지를 기본 제공 이벤트 허브에 저장 합니다. 서비스 클라이언트는 AMQP 프로토콜을 사용 하 여 저장 된 이벤트를 받을 수 있습니다.

이러한 목적을 위해 서비스 클라이언트는 먼저 IoT hub 엔드포인트에 연결 하 고 기본 제공 event hubs에 대 한 리디렉션 주소를 수신 해야 합니다. 그러면 서비스 클라이언트는 제공 된 주소를 사용 하 여 기본 제공 이벤트 허브에 연결 합니다.

각 단계에서 클라이언트는 다음과 같은 정보를 제공 해야 합니다.

* 유효한 서비스 자격 증명 (서비스 공유 액세스 서명 토큰)입니다.

* 메시지를 검색할 소비자 그룹 파티션에 대 한 올바른 형식의 경로입니다. 지정 된 소비자 그룹 및 파티션 ID의 경우 경로는 `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (기본 소비자 그룹은 `$Default`)입니다.

* 파티션의 시작 지점을 지정 하는 선택적 필터링 조건자입니다. 이 조건자는 시퀀스 번호, 오프셋 또는 큐에 넣은 타임 스탬프의 형식이 될 수 있습니다.

다음 코드 조각은 [Python의 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python) 를 사용 하 여 이전 단계를 보여 줍니다.

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

지정 된 장치 ID의 경우 IoT hub는 장치 ID의 해시를 사용 하 여 메시지를 저장할 파티션을 결정 합니다. 위의 코드 조각에서는 단일 파티션에서 이벤트를 수신 하는 방법을 보여 줍니다. 그러나 일반적인 응용 프로그램은 모든 이벤트 허브 파티션에 저장 된 이벤트를 검색 해야 하는 경우가 많습니다.

## <a name="device-client"></a>장치 클라이언트

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>IoT hub에 연결 및 인증 (장치 클라이언트)

AMQP를 사용 하 여 IoT hub에 연결 하기 위해 장치는 [CBS (클레임 기반 보안](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ) 또는 [SASL (SASL(Simple Authentication and Security Layer))](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) 인증을 사용할 수 있습니다.

장치 클라이언트에는 다음과 같은 정보가 필요 합니다.

| 정보 | 값 |
|-------------|--------------|
| IoT hub 호스트 이름 | `<iot-hub-name>.azure-devices.net` |
| 액세스 키 | 장치와 연결 된 기본 키 또는 보조 키 |
| 공유 액세스 서명 | @No__t-0 형식의 수명이 짧은 공유 액세스 서명입니다. 이 서명을 생성 하는 코드를 가져오려면 [IoT Hub에 대 한 액세스 제어](./iot-hub-devguide-security.md#security-token-structure)를 참조 하세요.

다음 코드 조각은 [Python의 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python) 를 사용 하 여 발신자 링크를 통해 IoT hub에 연결 합니다.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

다음 링크 경로는 장치 작업으로 지원 됩니다.

| 만든 사람 | 링크 형식 | 링크 경로 | 설명 |
|------------|-----------|-----------|-------------|
| 장치 | 받는 사람 링크 | `/devices/<deviceID>/messages/devicebound` | 장치를 대상으로 하는 클라우드-장치 메시지는 각 대상 장치에 의해이 링크에 수신 됩니다. |
| 장치 | 보낸 사람 링크 | `/devices/<deviceID>/messages/events` | 장치에서 전송 되는 장치-클라우드 메시지는이 링크를 통해 전송 됩니다. |
| 장치 | 보낸 사람 링크 | `/messages/serviceBound/feedback` | 장치에서이 링크를 통해 서비스로 전송 되는 클라우드-장치 메시지 피드백입니다. |

### <a name="receive-cloud-to-device-commands-device-client"></a>클라우드-장치 명령 받기 (장치 클라이언트)

장치에 전송 되는 클라우드-장치 명령은 `/devices/<deviceID>/messages/devicebound` 링크에 도착 합니다. 장치는 이러한 메시지를 일괄 처리로 수신 하 고 메시지 데이터 페이로드, 메시지 속성, 주석 또는 메시지의 응용 프로그램 속성을 필요에 따라 사용할 수 있습니다.

다음 코드 조각은 [Python의 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python)를 사용 하 여 장치에서 클라우드-장치 메시지를 수신 합니다.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>원격 분석 메시지 보내기 (장치 클라이언트)

AMQP를 사용 하 여 장치에서 원격 분석 메시지를 보낼 수도 있습니다. 필요에 따라 장치는 응용 프로그램 속성 사전을 제공 하거나 메시지 ID와 같은 다양 한 메시지 속성을 제공할 수 있습니다.

다음 코드 조각은 [Python의 Uamqp 라이브러리](https://github.com/Azure/azure-uamqp-python) 를 사용 하 여 장치에서 장치-클라우드 메시지를 보냅니다.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>추가 참고 사항

* 네트워크 결함 또는 인증 토큰의 만료 (코드에서 생성)로 인해 AMQP 연결이 중단 될 수 있습니다. 서비스 클라이언트는 이러한 상황을 처리 하 고 필요한 경우 연결 및 링크를 다시 설정 해야 합니다. 인증 토큰이 만료 되 면 만료 되기 전에 토큰을 사전에 갱신 하 여 연결 삭제를 방지할 수 있습니다.

* 클라이언트는 때때로 링크 리디렉션을 올바르게 처리할 수 있어야 합니다. 이러한 작업을 이해 하려면 AMQP 클라이언트 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

AMQP 프로토콜에 대 한 자세한 내용은 [amqp v 1.0 사양](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)을 참조 하세요.

IoT Hub 메시징에 대해 자세히 알아보려면 다음을 참조 하세요.

* [클라우드-장치 메시지](./iot-hub-devguide-messages-c2d.md)
* [추가 프로토콜 지원](iot-hub-protocol-gateway.md)
* [MQTT (메시지 큐 원격 분석 전송) 프로토콜 지원](./iot-hub-mqtt-support.md)
