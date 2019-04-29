---
title: Azure Digital Twins에서 사용자 정의 함수를 만드는 방법 | Microsoft Docs
description: Azure Digital Twins를 사용하여 사용자 정의 함수, 검사기 및 역할 할당을 만드는 방법입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 7208f96d99127247b51510e0c43c1733bb327dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921884"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins에서 사용자 정의 함수를 만드는 방법

[사용자 정의 함수](./concepts-user-defined-functions.md)를 사용하여 사용자가 들어오는 원격 분석 메시지 및 공간 그래프 메타데이터에 대한 사용자 지정 논리를 실행하도록 구성할 수 있습니다. 또한 사용자는 미리 정의된 [엔드포인트](./how-to-egress-endpoints.md)로 이벤트를 보낼 수 있습니다.

이 가이드에서는 수신된 온도 이벤트에서 특정 온도를 초과하는 모든 판독값을 감지하고 경고하는 방법에 설명하는 예제를 살펴봅니다.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>클라이언트 라이브러리 참조

사용자 정의 함수 런타임에서 도우미 메서드로 사용할 수 있는 함수는 [클라이언트 라이브러리 참조](./reference-user-defined-functions-client-library.md) 문서에 나와 있습니다.

## <a name="create-a-matcher"></a>검사기 만들기

검사기는 지정된 원격 분석 메시지에 대해 실행할 사용자 정의 함수를 결정하는 그래프 개체입니다.

- 유효한 검사기 조건 비교:

  - `Equals`
  - `NotEquals`
  - `Contains`

- 유효한 검사기 조건 대상:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

다음 예제 검사기에서는 `"Temperature"`를 해당 데이터 형식 값으로 사용하는 센서 원격 분석 이벤트에서 true로 평가합니다. HTTP POST 요청을 수행하여 사용자 정의 함수에서 여러 검사기를 만들 수 있습니다.

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

JSON 본문:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 인스턴스를 호스팅하는 서버 지역 |

## <a name="create-a-user-defined-function"></a>사용자 정의 함수 만들기

사용자 정의 함수 만들기에는 Azure Digital Twins 관리 API에 대한 다중 파트 HTTP 요청이 수반됩니다.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

검사기를 만든 후 다음 인증된 다중 파트 HTTP POST 요청을 사용하여 함수 코드 조각을 업로드합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

다음 본문을 사용합니다.

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 다중 파트 콘텐츠 경계 이름 |
| YOUR_SPACE_IDENTIFIER | 공간 식별자  |
| YOUR_MATCHER_IDENTIFIER | 사용하려는 검사기의 ID |

1. 헤더에 `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`가 포함되어 있는지 확인합니다.
1. 본문이 다중 파트인지 확인합니다.

   - 첫 번째 파트에는 필요한 사용자 정의 함수 메타데이터를 포함합니다.
   - 두 번째 파트에는 JavaScript 계산 논리를 포함합니다.

1. **USER_DEFINED_BOUNDARY** 섹션에서 **spaceId**(`YOUR_SPACE_IDENTIFIER`) 및 **검사기**(`YOUR_MATCHER_IDENTIFIER`) 값을 바꿉니다.
1. JavaScript 사용자 정의 함수가 `Content-Type: text/javascript`로 제공되었는지 확인합니다.

### <a name="example-functions"></a>예제 함수

`sensor.DataType`에 해당하는 **Temperature** 데이터 형식을 사용하여 센서에 대한 직접 센서 원격 분석 읽기를 설정합니다.

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**telemetry** 매개 변수는 센서에서 보낸 메시지에 해당하는 **SensorId** 및 **Message** 특성을 노출합니다. **executionContext** 매개 변수는 다음 특성을 노출합니다.

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

다음 예제에서는 센서 원격 분석 판독값이 미리 정의된 임계값을 초과하는 경우 메시지를 기록합니다. Azure Digital Twins 인스턴스에서 진단 설정을 사용하는 경우 사용자 정의 함수의 로그도 전달됩니다.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

다음 코드는 온도 수준이 미리 정의된 상수를 초과하는 경우 알림을 트리거합니다.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

더 복잡한 사용자 정의 함수 코드 예제에 대한 자세한 내용은 [사용 빠른 시작](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)을 참조하세요.

## <a name="create-a-role-assignment"></a>역할 할당 만들기

실행할 사용자 정의 함수에 대한 역할 할당을 만듭니다. 사용자 정의 함수에 대한 역할 할당이 없으면 관리 API와 상호 작용할 수 있는 적절한 권한을 갖지 못하거나 그래프 개체에서 작업을 수행할 수 있는 액세스 권한을 갖지 못합니다. 사용자 정의 함수를 통해 수행할 수 있는 작업은 Azure Digital Twins 관리 API 내 역할 기반 액세스 제어를 통해 지정 및 정의됩니다. 예를 들어 사용자 정의 함수는 특정 역할 또는 특정 액세스 제어 경로를 지정하여 범위를 제한할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어](./security-role-based-access-control.md) 설명서를 참조하세요.

1. 모든 역할에 대한 [시스템 API를 쿼리](./security-create-manage-role-assignments.md#all)하여 사용자 정의 함수에 할당할 역할 ID를 가져옵니다. 인증된 HTTP GET 요청을 수행함으로써 할 수 있습니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   원하는 역할 ID를 유지합니다. 아래와 같이 JSON 본문 특성 **roleId**(`YOUR_DESIRED_ROLE_IDENTIFIER`)로 전달됩니다.

1. **objectId**(`YOUR_USER_DEFINED_FUNCTION_ID`)는 앞서 만든 사용자 정의 함수 ID가 됩니다.
1. `fullpath`로 공백을 쿼리하여 **path**(`YOUR_ACCESS_CONTROL_PATH`) 값을 찾습니다.
1. 반환된 `spacePaths` 값을 복사합니다. 아래와 같이 이 값을 사용합니다. 인증된 HTTP GET 요청을 확인합니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_SPACE_NAME | 사용할 공간의 이름 |

1. 인증된 HTTP POST 요청을 수행하여 반환된 `spacePaths` 값을 **경로**에 붙여넣고 사용자 정의 함수 역할 할당을 만듭니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    JSON 본문:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 값 | 다음 항목으로 교체 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 원하는 역할의 식별자 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 사용하려는 사용자 정의 함수에 대한 ID |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | 사용자 정의 함수 유형을 지정하는 ID |
    | YOUR_ACCESS_CONTROL_PATH | 액세스 제어 경로 |

>[!TIP]
> 사용자 정의 함수 관리 API 조작 및 엔드포인트에 대한 자세한 내용은 [역할 할당을 만들고 관리하는 방법](./security-create-manage-role-assignments.md) 문서를 읽어보세요.

## <a name="send-telemetry-to-be-processed"></a>처리될 원격 분석 전송

공간 인텔리전스 그래프에서 정의된 센서는 원격 분석을 보냅니다. 따라서 원격 분석은 업로드된 사용자 정의 함수의 실행을 트리거합니다. 데이터 프로세스가 원격 분석 데이터를 선택합니다. 그 다음, 사용자 정의 함수 호출에 대한 실행 계획이 생성됩니다.

1. 읽기가 생성된 센서의 검사기를 검색합니다.
1. 성공적으로 평가된 검사기에 따라 연결된 사용자 정의 함수를 검색합니다.
1. 각 사용자 정의 함수를 실행합니다.

## <a name="next-steps"></a>다음 단계

- 이벤트를 보낼 [Azure Digital Twins 엔드포인트 만들기](./how-to-egress-endpoints.md)에 대한 방법을 알아봅니다.

- Azure Digital Twins의 라우팅에 대해 자세히 알아보려면 [라우팅 이벤트 및 메시지](./concepts-events-routing.md)를 확인합니다.

- [클라이언트 라이브러리 참조 설명서](./reference-user-defined-functions-client-library.md)를 검토합니다.
