---
title: Udf를 디버그 하는 방법-Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins에서 사용자 정의 함수를 디버그 하는 데 권장 되는 방법에 대해 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511640"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins에서 사용자 정의 함수를 디버그하는 방법

이 문서에서는 Azure Digital Twins에서 사용자 정의 함수를 진단 하 고 디버깅 하는 방법을 요약 합니다. 그런 다음 디버그하는 경우에 볼 수 있는 가장 일반적인 몇 가지 시나리오를 식별합니다.

>[!TIP]
> Activity Logs, Diagnostic Logs 및 Azure Monitor를 사용하여 Azure Digital Twins에서 디버깅 도구를 설정하는 방법에 대해 자세히 알아보려면 [모니터링 및 로깅을 구성하는 방법](./how-to-configure-monitoring.md)을 참조하세요.

## <a name="debug-issues"></a>문제 디버그

Azure Digital Twins 내에서 문제를 진단 하는 방법을 알면 문제를 효과적으로 분석 하 고 문제의 원인을 파악 하 고 적절 한 솔루션을 제공할 수 있습니다.

이러한 끝에 다양 한 로깅, 분석 및 진단 도구가 제공 됩니다.

### <a name="enable-logging-for-your-instance"></a>인스턴스에 대한 로깅 사용

Azure Digital Twins는 강력한 로깅, 모니터링 및 분석을 지원합니다. 솔루션 개발자는 Azure Monitor 로그, 진단 로그, 활동 로그 및 기타 서비스를 사용 하 여 IoT 앱의 복잡 한 모니터링 요구를 지원할 수 있습니다. 로깅 옵션을 결합하여 여러 서비스의 레코드를 쿼리 또는 표시하고 여러 서비스에 대한 세밀한 로깅 범위를 제공할 수 있습니다.

* Azure Digital Twins에 특정 한 로깅 구성을 보려면 [모니터링 및 로깅을 구성 하는 방법](./how-to-configure-monitoring.md)을 참조 하세요.
* Azure Monitor를 통해 사용 하도록 설정 된 강력한 로그 설정에 대한 자세한 내용은 [Azure Monitor](../azure-monitor/overview.md) 개요를 참조 하세요.
* Azure Portal, Azure CLI 또는 PowerShell을 통해 Azure Digital Twins에서 진단 로그 설정 구성에 대한 [azure 리소스에서 로그 데이터 수집 및 사용](../azure-monitor/platform/platform-logs-overview.md) 문서를 검토 합니다.

구성 된 후에는 모든 로그 범주 및 메트릭을 선택 하 고 강력한 Azure Monitor log analytics 작업 영역을 사용 하 여 디버깅 활동을 지원할 수 있습니다.

### <a name="trace-sensor-telemetry"></a>센서 원격 분석 추적

센서 원격 분석을 추적하려면 Azure Digital Twins 인스턴스에 대해 진단 설정이 활성화되어 있는지 확인합니다. 그런 다음 원하는 모든 로그 범주가 선택되어 있는지 확합니다. 마지막으로 원하는 로그가 Azure Monitor 로그로 전송 되 고 있는지 확인 합니다.

센서 원격 분석 메시지를 해당 로그에 일치시키려면 보내는 이벤트 데이터에 대한 상관 관계 ID를 지정할 수 있습니다. 이렇게 하려면 `x-ms-client-request-id` 속성을 GUID로 설정합니다.

원격 분석을 보낸 후 set 상관 관계 ID를 사용 하 여 로그를 쿼리하려면 Azure Monitor log analytics를 엽니다.

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| 쿼리 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | 이벤트 데이터에 대해 지정된 상관 관계 ID |

모든 최신 원격 분석 로그 쿼리를 읽으려면:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

사용자 정의 함수에 대한 로깅을 사용 하도록 설정 하면 해당 로그는 `UserDefinedFunction`범주를 사용 하 여 log analytics 인스턴스에 표시 됩니다. 이를 검색 하려면 log analytics에서 다음 쿼리 조건을 입력 합니다.

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

강력한 쿼리 작업에 대한 자세한 내용은 [쿼리 시작](../azure-monitor/log-query/get-started-queries.md)을 참조하세요.

## <a name="identify-common-issues"></a>일반적인 문제 식별

솔루션 문제를 해결할 때 일반적인 문제의 진단과 식별이 모두 중요합니다. 사용자 정의 함수를 개발할 때 일반적으로 발생하는 몇 가지 문제가 다음 하위 섹션에 요약되어 있습니다.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>역할 할당이 만들어졌는지 확인

관리 API 내에서 만들어진 역할 할당이 없는 경우 사용자 정의 함수가 알림 보내기, 메타데이터 검색 및 토폴로지 내에서 계산된 값 설정 등과 같은 작업을 수행하기 위한 액세스 권한을 갖지 못하게 됩니다.

관리 API를 통해 사용자 정의 함수에 대한 역할 할당이 있는지 확인합니다.

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| 매개 변수 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | 역할 할당을 검색할 사용자 정의 함수의 ID|

역할 할당이 없는 경우 [사용자 정의 함수에 대한 역할 할당을 만드는 방법](./how-to-user-defined-functions.md)을 알아보세요.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>검사기가 센서의 원격 분석에 대해 작동하는지 확인

Azure Digital Twins 인스턴스의 관리 API에 대해 다음 호출을 사용하여 지정된 검사기가 지정된 센서에 적용되는지 결정할 수 있습니다.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | 평가하려는 검사기의 ID |
| *YOUR_SENSOR_IDENTIFIER* | 평가하려는 센서의 ID |

응답:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>센서가 트리거하는 항목 확인

Azure Digital Twins 관리 API에 대해 다음 호출을 사용하여 지정된 센서의 수신 원격 측정에서 트리거한 사용자 정의 함수의 식별자를 결정할 수 있습니다.

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | 원격 분석을 전송할 센서의 ID |

응답:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>알림 수신 문제

트리거한 사용자 정의 함수에서 알림을 수신하지 않는 경우 토폴로지 개체 유형 매개 변수가 사용 중인 식별자의 유형과 일치하는지 확인합니다.

**잘못 된** 예제:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

이 시나리오는 사용된 식별자가 지정된 토폴로지 개체 형식이 `Space`일 때 센서를 참조하기 때문에 발생합니다.

**올바른** 예제:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

이 문제에 빠져들지 않는 가장 쉬운 방법은 메타데이터 개체에 대해 `Notify` 메서드를 사용하는 것입니다.

예:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>일반적인 진단 예외

진단 설정을 사용하도록 설정하면 이러한 일반적인 예외가 발생할 수 있습니다.

1. **제한**: 사용자 정의 함수는 [서비스 제한](./concepts-service-limits.md) 문서에 요약한 실행 속도 제한을 초과하는 경우 제한됩니다. 제한 한도가 만료될 때까지 추가 작업이 성공적으로 실행되지 않습니다.

1. **데이터 없음**: 사용자 정의 함수가 존재하지 않는 메타데이터에 액세스를 시도하면 작업이 실패합니다.

1. **권한 없음**: 사용자 정의 함수가 역할 할당 집합을 포함하지 않거나 토폴로지에서 특정 메타데이터에 액세스할 권한이 부족한 경우 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins에서 [모니터링 및 로그](./how-to-configure-monitoring.md)를 사용하도록 설정하는 방법을 알아봅니다.

- 자세한 Azure 로깅 옵션은 [Azure 활동 로그 개요](../azure-monitor/platform/platform-logs-overview.md) 문서를 참조 하세요.
