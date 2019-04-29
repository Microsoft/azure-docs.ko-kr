---
title: 고급 시뮬레이션된 디바이스 모델 만들기 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 디바이스 시뮬레이션 솔루션 가속기와 함께 사용할 고급 디바이스 모델을 만드는 방법에 대해 알아봅니다.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449059"
---
# <a name="create-an-advanced-device-model"></a>고급 디바이스 모델 만들기

이 방법 가이드에서는 사용자 지정 디바이스 모델을 정의하는 JSON 및 JavaScript 파일을 설명합니다. 이 문서는 몇 가지 샘플 디바이스 모델 정의 파일을 포함하며 이를 디바이스 시뮬레이션 인스턴스에 업로드하는 방법을 보여 줍니다. 고급 디바이스 모델을 만들어 테스트용으로 보다 현실적인 디바이스 동작을 시뮬레이션할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면 Azure 구독에서 디바이스 시뮬레이션의 배포된 인스턴스가 필요합니다.

디바이스 시뮬레이션을 아직 배포하지 않은 경우 [Azure에서 IoT 디바이스 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작을 완료해야 합니다.

### <a name="open-device-simulation"></a>디바이스 시뮬레이션 열기

브라우저에서 디바이스 시뮬레이션을 실행하려면 먼저 [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com)로 이동합니다.

Azure 구독 자격 증명을 사용하여 로그인하라는 메시지가 표시될 수 있습니다.

그런 다음, [Azure에서 IoT 디바이스 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작에서 배포한 디바이스 시뮬레이션에 대한 타일에서 **시작**을 클릭합니다.

## <a name="device-models"></a>디바이스 모델

각 시뮬레이션된 디바이스는 시뮬레이션 동작을 정의하는 특정 디바이스 모델에 속합니다. 이 동작에는 원격 분석을 보내는 빈도, 전송할 메시지 종류 및 지원되는 방법이 포함됩니다.

JSON 디바이스 정의 파일과 JavaScript 파일 집합을 사용하여 디바이스 모델을 정의합니다. 이러한 JavaScript 파일은 임의의 원격 분석 및 메서드 논리와 같은 시뮬레이션 동작을 정의합니다.

일반적인 디바이스 모델은 다음을 포함합니다.

* 디바이스 모델당 JSON 파일 하나(예: elevator.json)
* 디바이스 모델당 JavaScript 동작 스크립트 파일 하나(예: elevator-state.js)
* 디바이스 메서드당 JavaScript 메서드 스크립트 파일 하나(예: elevator-go-down.js)

> [!NOTE]
> 모든 디바이스 모델이 메서드를 정의하는 것은 아닙니다. 따라서 디바이스 모델은 메서드 스크립트를 포함하거나 포함하지 않을 수 있습니다. 그러나 모든 디바이스 모델은 동작 스크립트를 포함해야 합니다.

## <a name="device-definition-file"></a>디바이스 정의 파일

각 디바이스 정의 파일에는 다음 정보를 비롯하여 시뮬레이션된 디바이스 모델에 대한 세부 정보가 들어 있습니다.

* 디바이스 모델 이름: string
* 프로토콜: AMQP | MQTT | HTTP.
* 초기 디바이스 상태
* 디바이스 상태 새로 고침 빈도
* 디바이스 상태를 새로 고치는 데 사용할 JavaScript 파일
* 전송할 원격 분석 메시지 목록으로 각각 특정 빈도를 포함
* 수신된 원격 분석을 구문 분석하기 위해 백 엔드 애플리케이션이 사용하는 원격 분석 메시지의 스키마
* 지원되는 메서드 목록과 각 메서드를 시뮬레이션하는 데 사용할 JavaScript 파일

### <a name="file-schema"></a>파일 스키마

스키마 버전은 항상 “1.0.0”이며 이 파일의 형식에 고유합니다.

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>디바이스 모델 설명

다음 속성은 디바이스 모델을 설명합니다. 각 형식에는 고유 식별자, 의미 체계 버전, 이름 및 설명이 있습니다.

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT 프로토콜

IoT 디바이스는 다양한 프로토콜을 사용하여 연결할 수 있습니다. 시뮬레이션을 통해 **AMQP**, **MQTT** 또는 **HTTP** 중 하나를 사용할 수 있습니다.

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>시뮬레이션된 디바이스 상태

각 시뮬레이션된 디바이스는 정의해야 하는 내부 상태를 포함합니다. 또한 상태는 원격 분석에서 보고할 수 있는 속성을 정의합니다. 예를 들어 냉각기는 다음과 같은 초기 상태를 포함할 수 있습니다.

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

여러 센서와 함께 움직이는 디바이스는 다음과 같은 더 많은 속성을 포함할 수 있습니다.

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

디바이스 상태는 시뮬레이션 서비스에 의해 메모리에 유지되고 JavaScript 함수에 대한 입력으로 제공됩니다. JavaScript 함수는 다음을 결정할 수 있습니다.

* 상태를 무시하고 임의의 데이터를 생성합니다.
* 주어진 시나리오에 대해 현실적인 방법으로 디바이스 상태를 업데이트합니다.

해당 상태를 생성하는 함수는 다음을 입력으로도 받습니다.

* 디바이스 ID
* 디바이스 모델
* 현재 시간입니다. 이 값을 사용하면 디바이스 및 시간별로 다양한 데이터를 생성할 수 있습니다.

### <a name="generating-telemetry-messages"></a>원격 분석 메시지 생성

시뮬레이션 서비스는 각 디바이스에 대한 여러 가지 원격 분석 유형을 전송할 수 있습니다. 일반적으로 원격 분석은 디바이스 상태의 데이터를 포함합니다. 예를 들어, 시뮬레이션된 방은 10초마다 온도와 습도에 대한 정보를 보낼 수 있습니다. 다음 코드 조각에서 디바이스 상태 값으로 자동으로 대체되는 자리 표시자를 유의하세요.

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

자리 표시자는 특수 구문 **${NAME}** 을 사용합니다. 여기서 **NAME**은 JavaScript **main** 함수에서 반환되는 디바이스 상태 개체의 키입니다. 문자열은 따옴표로 묶어야 하지만 숫자는 따옴표로 묶으면 안 됩니다.

#### <a name="message-schema"></a>메시지 스키마

각 메시지 유형에는 잘 정의된 스키마가 있어야 합니다. 백 엔드 애플리케이션이 정보를 다시 사용하여 들어오는 원격 분석을 해석할 수 있도록 메시지 스키마가 IoT Hub에도 게시됩니다.

스키마는 JSON 형식을 지원하므로 여러 시스템과 서비스에서 구문 분석, 변환 및 분석이 용이합니다.

스키마에 나열된 필드는 다음 형식일 수 있습니다.

* 개체 - JSON을 사용하여 직렬화됨
* 이진 - base64를 사용하여 직렬화됨
* 텍스트
* BOOLEAN
* 정수 
* Double
* DateTime

### <a name="supported-methods"></a>지원되는 방식

시뮬레이션된 디바이스는 메서드 호출에도 반응할 수 있습니다. 이 경우 메서드는 일부 논리를 실행하고 응답을 제공합니다. 시뮬레이션과 마찬가지로 메서드 논리는 JavaScript 파일에 저장되며 디바이스 상태와 상호 작용할 수 있습니다. 예를 들면 다음과 같습니다.

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>디바이스 정의 파일 만들기

이 방법 가이드에서는 드론에 대한 디바이스 모델을 만드는 방법을 설명합니다. 드론은 위치와 고도를 바꿔가며 초기 좌표 집합을 무작위로 비행합니다.

다음 JSON을 텍스트 편집기에 복사하고 **drone.json**으로 저장합니다.

### <a name="device-definition-json-example"></a>디바이스 정의 JSON 예제

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>동작 스크립트 파일

동작 스크립트 파일의 코드는 드론을 움직입니다. 스크립트는 메모리 내 상태를 조작하여 드론의 상승 및 위치를 변경합니다.

JavaScript 파일에는 두 개의 매개 변수를 받아들이는 **main** 함수가 있어야 합니다.

* 다음 세 가지 속성을 포함하는 **context** 개체
    * **yyyy-MM-dd'T'HH:mm:sszzz** 형식의 문자열로 이루어진 **currentTime**
    * **deviceId**. 예를 들어 **Simulated.Elevator.123**입니다.
    * **deviceModel**. 예를 들어 **Elevator**입니다.
* 이전 호출에서 함수에 의해 반환된 값인 **state** 개체. 이 디바이스 상태는 시뮬레이션 서비스에 의해 유지 관리되며 원격 분석 메시지를 생성하는 데 사용됩니다.

**main** 함수는 새 디바이스 상태를 반환합니다. 예를 들면 다음과 같습니다.

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>동작 스크립트 파일 만들기

다음 JavaScript를 텍스트 편집기에 복사하고 **drone-state.js**로 저장합니다.

### <a name="device-model-javascript-simulation-example"></a>디바이스 모델 JavaScript 시뮬레이션 예제

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>메서드 스크립트 파일 만들기

메서드 스크립트는 동작 스크립트와 유사합니다. 디바이스 메서드에 대한 특정 클라우드를 호출할 때의 디바이스 동작을 정의합니다.

드론 리콜 스크립트는 드론이 집으로 돌아오는 것을 시뮬레이션하기 위해 드론의 좌표를 고정된 지점으로 설정합니다.

다음 JavaScript를 텍스트 편집기에 복사하고 **droneRecall-method.js**로 저장합니다.

### <a name="device-model-javascript-simulation-example"></a>디바이스 모델 JavaScript 시뮬레이션 예제

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>스크립트 파일 디버깅

실행 중인 동작 파일에 디버거를 연결할 수는 없지만 **log** 함수를 사용하여 서비스 로그에 정보를 쓸 수 있습니다. 구문 오류의 경우 인터프리터가 실패하고 예외에 대한 정보를 로그에 기록합니다.

로깅 예:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>고급 디바이스 모델 배포

고급 디바이스 모델을 배포하려면 파일을 디바이스 시뮬레이션 인스턴스에 업로드합니다.

메뉴 모음에서 **디바이스 모델**을 선택합니다. **디바이스 모델** 페이지는 디바이스 시뮬레이션의 이 인스턴스에서 사용 가능한 디바이스 모델을 나열합니다.

![디바이스 모델](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

페이지의 오른쪽 위 모서리에서 **+ 디바이스 모델 추가**를 클릭합니다.

![디바이스 모델 추가](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

**고급**을 클릭하면 고급 디바이스 모델 탭이 열립니다.

![[고급] 탭](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

**찾아보기**를 클릭하고 만든 JSON 및 JavaScript 파일을 선택합니다. 세 파일을 모두 선택해야 합니다. 하나의 파일이 누락된 경우 유효성 검사에 실패합니다.

![파일 찾아보기](media/iot-accelerators-device-simulation-advanced-device/browse.png)

파일이 유효성 검사를 통과하는 경우 **저장**을 클릭하면 시뮬레이션에서 디바이스 모델을 사용할 준비가 됩니다. 그렇지 않으면 오류를 수정하고 파일을 다시 업로드합니다.

![저장](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 디바이스 시뮬레이션에 사용된 디바이스 모델 파일과 고급 디바이스 모델을 만드는 방법에 대해 알아보았습니다. 다음으로, [Time Series Insights를 사용하여 디바이스 시뮬레이션 솔루션 가속기에서 보낸 원격 분석 시각화](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights) 방법에 대해 알아볼 수 있습니다.
