---
title: Azure IoT Hub 직접 메서드 이해 | Microsoft Docs
description: 개발자 가이드 - 직접 메서드를 사용하여 서비스 앱의 디바이스에서 코드 호출
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: d7c63ffe5a318507053f59bf3a18242ee8c327a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327757"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>IoT Hub의 직접 메서드 호출 및 이해

IoT Hub를 사용하면 클라우드의 디바이스에서 직접 메서드를 호출할 수 있습니다. 직접 메서드는 사용자가 지정한 시간 제한을 초과하는 즉시 성공하거나 실패한다는 점에서 HTTP 호출과 비슷한 디바이스와의 요청-응답 상호 작용을 나타냅니다. 이 방법은 즉각적인 조치 과정이 디바이스의 응답 여부에 따라 달라지는 시나리오에서 유용합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

각 디바이스 메서드는 단일 디바이스를 대상으로 합니다. [여러 디바이스에서 작업 예약](iot-hub-devguide-jobs.md)에서는 여러 디바이스에서 직접 메서드를 호출하고 연결되지 않은 디바이스에 대한 메서드 호출을 예약하는 방법을 보여 줍니다.

IoT Hub에 **서비스 연결** 권한만 있다면 누구든 디바이스에서 메서드를 호출할 수 있습니다.

직접 메서드는 요청-응답 패턴을 따르며, 결과를 즉각적으로 확인해야 하는 통신에 유의미합니다. 팬 켜기 등, 대화형 디바이스 제어를 예로 들 수 있습니다.

desired 속성, 직접 메서드 또는 클라우드-장치 메시지 사용에 대해 궁금한 점이 있으면 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

## <a name="method-lifecycle"></a>메서드 수명 주기

직접 메서드는 디바이스에서 구현되며, 제대로 인스턴스화하기 위해 메서드 페이로드에 0개 이상의 입력이 필요할 수 있습니다. 직접 메서드는 서비스 지향 URI를 통해 호출합니다(`{iot hub}/twins/{device id}/methods/`). 장치는 장치별 MQTT 항목(`$iothub/methods/POST/{method name}/`) 또는 AMQP 링크(`IoThub-methodname` 및 `IoThub-status` 애플리케이션 속성)를 통해 직접 메서드를 수신합니다. 

> [!NOTE]
> 디바이스에서 직접 메서드를 호출할 때 속성 이름과 값은 US-ASCII로 출력 가능한 영숫자만 포함할 수 있으며 다음 집합은 제외됩니다. ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

직접 메서드는 동기식이며 제한 시간(기본값: 30초, 최대 300초 설정 가능)이 지나면 성공하거나 실패합니다. 직접 메서드는 디바이스가 온라인 상태에서 명령을 수신하는 경우에만 작동하기를 바라는 대화형 시나리오에서 유용합니다. 예를 들어 휴대폰에서 불을 켭니다. 이러한 시나리오에서는 클라우드 서비스가 결과에 최대한 빨리 대응할 수 있도록 즉각적인 성공이나 실패를 보려고 합니다. 디바이스는 메서드의 결과로 메시지 본문을 반환할 수 있지만 메서드가 반드시 그렇게 해야 하는 것은 아닙니다. 메서드 호출의 순서 지정 또는 동시성 의미 체계에 대한 보장은 없습니다.

직접 메서드는 클라우드 쪽에서는 HTTPS 전용, 디바이스 쪽에서는 MQTT 또는 AMQP입니다.

메서드 요청 및 응답에 대한 페이로드는 최대 128KB의 JSON 문서입니다.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>백 엔드 앱에서 직접 메서드 호출

이제 백 엔드 앱에서 직접 메서드를 호출합니다.

### <a name="method-invocation"></a>메서드 호출

디바이스의 직접 메서드 호출은 다음 항목으로 구성된 HTTPS 호출입니다.

* 디바이스와 관련된 *요청 URI* 및 [API 버전](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* POST *메서드*

* *헤더*에는 권한 부여, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩이 포함됩니다.

* 다음과 같은 형식의 투명한 JSON *본문*:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

시간 제한은 초 단위입니다. 시간 제한이 설정되지 않으면 기본값이 30초로 설정됩니다.

#### <a name="example"></a>예

`curl`을 사용하는 기본 예제는 아래를 참조하세요. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>response

백 엔드 앱은 다음 항목으로 구성된 응답을 받습니다.

* *HTTP 상태 코드*는 현재 연결되지 않은 디바이스에 대한 404 오류를 비롯한 IoT Hub에서 오는 오류에 사용됩니다.

* *헤더*는 ETag, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩을 포함합니다.

* 다음과 같은 형식의 JSON *본문*:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status`와 `body`는 모두 디바이스에 의해 제공되며 디바이스 자체의 상태 코드 및/또는 설명으로 응답하는 데 사용됩니다.

### <a name="method-invocation-for-iot-edge-modules"></a>IoT Edge 모듈에 대한 메서드 호출

모듈 ID를 사용하여 직접 메서드를 호출하는 작업은 [IoT 서비스 클라이언트 C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/)에서 지원됩니다

이 작업의 경우 `ServiceClient.InvokeDeviceMethodAsync()` 메서드를 사용하고 `deviceId` 및 `moduleId`에서 매개 변수로 전달합니다.

## <a name="handle-a-direct-method-on-a-device"></a>디바이스에서 직접 메서드 처리

IoT 디바이스에서 직접 메서드를 처리하는 방법을 살펴보겠습니다.

### <a name="mqtt"></a>MQTT

다음 섹션은 MQTT 프로토콜에 대해 설명합니다.

#### <a name="method-invocation"></a>메서드 호출

디바이스는 MQTT 토픽으로 직접 메서드 요청을 수신합니다. `$iothub/methods/POST/{method name}/?$rid={request id}` 디바이스당 구독 수가 5로 제한됩니다. 따라서 각 직접 메서드를 개별적으로 구독하지 않는 것이 좋습니다. 대신 `$iothub/methods/POST/#`을 구독한 다음, 원하는 메서드 이름을 기반으로 배달된 메시지를 필터링하는 것이 좋습니다.

디바이스가 수신하는 본문은 다음과 같은 형식입니다.

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

메서드 요청은 QoS 0입니다.

#### <a name="response"></a>response

디바이스는 `$iothub/methods/res/{status}/?$rid={request id}`에 응답을 보내는데 여기서:

* `status` 속성은 디바이스가 제공하는 메서드 실행 상태입니다.

* `$rid` 속성은 IoT Hub로부터 수신한 메서드 호출의 요청 ID입니다.

본문은 디바이스에 의해 설정되며 모든 상태가 될 수 있습니다.

### <a name="amqp"></a>AMQP

다음 섹션은 AMQP 프로토콜에 대해 설명합니다.

#### <a name="method-invocation"></a>메서드 호출

디바이스는 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 주소에서 수신 링크를 만들어 직접 메서드 요청을 수신합니다.

AMQP 메시지는 메서드 요청을 나타내는 수신 링크에 도착하며 여기에는 다음 섹션이 포함됩니다.

* 해당하는 메서드 응답과 함께 다시 전달해야 하는 요청 ID가 포함된 상관 관계 ID 속성

* 호출 중인 메서드의 이름이 포함된 애플리케이션 속성 `IoThub-methodname`

* 메서드 페이로드가 JSON으로 포함된 AMQP 메시지 본문

#### <a name="response"></a>response

디바이스는 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 주소에서 메서드 응답을 반환하기 위한 전송 링크를 만듭니다.

전송 링크에서 반환되는 메서드의 응답은 다음과 같은 항목으로 구성됩니다.

* 메서드의 요청 메시지에서 전달된 요청 ID를 포함하는 상관 관계 ID 속성

* 사용자가 제공한 메서드 상태가 포함된 애플리케이션 속성 `IoThub-status`

* 메서드 응답이 JSON으로 포함된 AMQP 메시지 본문

## <a name="additional-reference-material"></a>추가 참조 자료

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)는 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 엔드포인트에 대해 설명합니다.

* [제한 및 할당량](iot-hub-devguide-quotas-throttling.md)은 IoT Hub를 사용할 때 적용되는 할당량과 예상되는 제한 동작에 대해 설명합니다.

* [Azure IoT 디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)는 IoT Hub와 상호 작용하는 디바이스 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.

* [디바이스 쌍, 작업 및 메시지 라우팅을 위한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)에서는 IoT Hub에서 디바이스 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 IoT Hub 쿼리 언어에 대해 설명합니다.

* [IoT Hub MQTT 지원](iot-hub-mqtt-support.md)은 MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

직접 메서드를 사용하는 방법에 대해 알아봤으니 다음 IoT Hub 개발자 가이드 문서를 살펴보세요.

* [여러 디바이스에서 작업 예약](iot-hub-devguide-jobs.md)

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

* [직접 메서드 사용](quickstart-control-device-node.md)
* [VS Code용 Azure IoT Tools를 사용한 디바이스 관리](iot-hub-device-management-iot-toolkit.md)
